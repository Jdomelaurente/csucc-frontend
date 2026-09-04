<template>
  <div class="page">
    <header class="header">
      <div class="container header-inner" style="max-width: 100%; padding: 0 20px;">
        <div style="display: flex; align-items: center; gap: 16px;">
          <button @click="toggleSidebar" class="btn-toggle">☰</button>
          <div class="logo">CSUCC Dashboard</div>
        </div>
        <nav class="nav">
          <span class="user-name">Welcome, {{ user?.name }}</span>
          <button @click="logout" class="btn-outline">Logout</button>
        </nav>
      </div>
    </header>

    <div class="layout-body">
      <aside class="sidebar" :class="{ closed: !isSidebarOpen }">
        <nav class="sidebar-nav">
          <a href="#" :class="{ active: activeTab === 'dashboard' }" @click.prevent="activeTab = 'dashboard'">Dashboard</a>
          <a href="#" :class="{ active: activeTab === 'accounts' }" @click.prevent="activeTab = 'accounts'">Accounts</a>
          <a href="#" v-if="user?.role === 'admin'" :class="{ active: activeTab === 'users' }" @click.prevent="activeTab = 'users'">Manage Users</a>
        </nav>
      </aside>

      <main class="main-content">
        <!-- Dashboard Tab -->
        <section v-if="activeTab === 'dashboard'" class="dashboard-content container">
          <h1>Dashboard ({{ user?.role === 'admin' ? 'Admin' : 'Client' }})</h1>
          <p>Welcome to your CSUCC dashboard.</p>
          <div class="card" style="text-align: center;">
            <h2>Overview</h2>
            <p>You have logged in successfully.</p>
          </div>
        </section>

        <!-- Accounts Tab -->
        <section v-if="activeTab === 'accounts'" class="dashboard-content container">
          <h1>Accounts</h1>
          <p>Manage your account settings below.</p>
          
          <div class="card" v-if="!isEditing">
            <p><strong>Name:</strong> {{ user?.name }}</p>
            <p><strong>Email:</strong> {{ user?.email }}</p>
            <p><strong>Student ID:</strong> {{ user?.student_id }}</p>
            <p><strong>Role:</strong> <span style="text-transform: capitalize;">{{ user?.role }}</span></p>
            <div class="actions">
              <button @click="isEditing = true" class="btn-primary">Update Profile</button>
              <button @click="deleteAccount" class="btn-danger">Delete Account</button>
            </div>
          </div>

          <div class="card" v-else>
            <h2>Edit Profile</h2>
            <form @submit.prevent="updateAccount">
              <div class="form-group">
                <label>Name</label>
                <input type="text" v-model="editForm.name" required />
              </div>
              <div class="form-group">
                <label>Email</label>
                <input type="email" v-model="editForm.email" required />
              </div>
              <div class="form-group">
                <label>Student ID</label>
                <input type="text" v-model="editForm.student_id" required />
              </div>
              <!-- Role is generally not editable by the user themselves in this form -->
              <div class="actions">
                <button type="submit" class="btn-primary">Save Changes</button>
                <button type="button" @click="isEditing = false" class="btn-outline" style="color: #555; border-color: #ccc;">Cancel</button>
              </div>
            </form>
          </div>
        </section>

        <!-- Manage Users Tab (Admin Only) -->
        <section v-if="activeTab === 'users' && user?.role === 'admin'" class="dashboard-content container">
          <h1>Manage Users</h1>
          <p>This section is only visible to administrators.</p>
          <div class="card" style="max-width: 600px; text-align: center;">
            <p>Admin tools will be listed here.</p>
          </div>
        </section>
      </main>
    </div>

    <footer class="footer">
      <div class="container">
        <p>All rights reserved.</p>
      </div>
    </footer>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { useRouter } from 'vue-router'

const router = useRouter()
const user = ref(null)
const isEditing = ref(false)
const editForm = ref({ name: '', email: '', student_id: '' })
const activeTab = ref('dashboard')
const isSidebarOpen = ref(true)

function toggleSidebar() {
  isSidebarOpen.value = !isSidebarOpen.value
}

onMounted(() => {
  const userData = localStorage.getItem('user')
  if (!userData) {
    router.push('/login')
  } else {
    user.value = JSON.parse(userData)
    editForm.value = { ...user.value }
  }
})

async function updateAccount() {
  try {
    const token = localStorage.getItem('token')
    const response = await fetch(`${import.meta.env.VITE_API_URL}/api/user`, {
      method: 'PUT',
      headers: {
        'Content-Type': 'application/json',
        'Authorization': `Bearer ${token}`
      },
      body: JSON.stringify(editForm.value)
    })
    
    const data = await response.json()
    
    if (response.ok) {
      user.value = data.user
      localStorage.setItem('user', JSON.stringify(data.user))
      isEditing.value = false
      alert('Profile updated successfully!')
    } else {
      alert('Error updating profile: ' + (data.message || 'Unknown error'))
    }
  } catch (err) {
    alert('An error occurred. Please try again.')
  }
}

async function deleteAccount() {
  if (!confirm('Are you sure you want to delete your account? This action cannot be undone.')) {
    return
  }
  
  try {
    const token = localStorage.getItem('token')
    const response = await fetch(`${import.meta.env.VITE_API_URL}/api/user`, {
      method: 'DELETE',
      headers: {
        'Authorization': `Bearer ${token}`
      }
    })
    
    if (response.ok) {
      alert('Account deleted successfully.')
      localStorage.removeItem('token')
      localStorage.removeItem('user')
      router.push('/')
    } else {
      const data = await response.json()
      alert('Error deleting account: ' + (data.message || 'Unknown error'))
    }
  } catch (err) {
    alert('An error occurred. Please try again.')
  }
}

function logout() {
  localStorage.removeItem('token')
  localStorage.removeItem('user')
  router.push('/')
}
</script>

<style scoped>
* { box-sizing: border-box; margin: 0; padding: 0; }

.page {
  font-family: Arial, sans-serif;
  font-size: 15px;
  color: #222;
  background: #fff;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}

.container {
  max-width: 900px;
  margin: 0 auto;
  padding: 0 20px;
}

/* Header */
.header {
  background: #1a4a8a;
  color: #fff;
  padding: 12px 0;
}
.header-inner {
  display: flex;
  align-items: center;
  justify-content: space-between;
  width: 100%;
}
.btn-toggle {
  background: transparent;
  color: #fff;
  border: none;
  font-size: 24px;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 4px;
  border-radius: 4px;
}
.btn-toggle:hover {
  background: rgba(255,255,255,0.2);
}
.logo {
  font-size: 20px;
  font-weight: bold;
  color: #fff;
  letter-spacing: 1px;
}
.nav {
  display: flex;
  align-items: center;
  gap: 16px;
}
.user-name {
  color: #fff;
  font-size: 14px;
}

/* Buttons */
.btn-outline {
  background: #fff;
  color: #1a4a8a;
  padding: 7px 16px;
  border-radius: 4px;
  text-decoration: none;
  font-size: 14px;
  border: 1px solid #1a4a8a;
  display: inline-block;
  cursor: pointer;
}
.btn-outline:hover { background: #f0f4ff; }

/* Layout Body */
.layout-body {
  display: flex;
  flex: 1;
}

/* Sidebar */
.sidebar {
  width: 250px;
  background: #f8fafc;
  border-right: 1px solid #e2e8f0;
  padding: 20px 0;
  transition: width 0.3s ease, padding 0.3s ease;
  overflow: hidden;
  white-space: nowrap;
}
.sidebar.closed {
  width: 0;
  padding: 0;
  border-right: none;
}
.sidebar-nav {
  display: flex;
  flex-direction: column;
}
.sidebar-nav a {
  padding: 12px 24px;
  text-decoration: none;
  color: #4a5568;
  font-weight: 500;
  border-left: 4px solid transparent;
  transition: all 0.2s ease;
}
.sidebar-nav a:hover {
  background: #edf2f7;
  color: #1a4a8a;
}
.sidebar-nav a.active {
  background: #ebf4ff;
  color: #1a4a8a;
  border-left-color: #1a4a8a;
}

/* Main Content */
.main-content {
  flex: 1;
  display: flex;
  align-items: flex-start;
  justify-content: center;
  background: #fff;
}

/* Dashboard Content */
.dashboard-content {
  padding: 40px 20px;
  text-align: center;
  width: 100%;
}
.dashboard-content h1 {
  font-size: 28px;
  margin-bottom: 12px;
  color: #1a4a8a;
}
.dashboard-content p {
  color: #555;
  margin-bottom: 24px;
}

.card {
  background: #fafafa;
  border: 1px solid #ddd;
  border-radius: 6px;
  padding: 24px;
  max-width: 400px;
  margin: 0 auto;
  text-align: left;
}
.card p {
  margin-bottom: 12px;
  color: #333;
}
.card p:last-child {
  margin-bottom: 0;
}

.card h2 {
  font-size: 20px;
  margin-bottom: 15px;
  color: #1a4a8a;
}

.actions {
  display: flex;
  gap: 10px;
  margin-top: 20px;
}

.btn-primary {
  background: #1a4a8a;
  color: #fff;
  padding: 8px 16px;
  border-radius: 4px;
  border: none;
  cursor: pointer;
}
.btn-primary:hover { background: #133a6f; }

.btn-danger {
  background: #dc3545;
  color: #fff;
  padding: 8px 16px;
  border-radius: 4px;
  border: none;
  cursor: pointer;
}
.btn-danger:hover { background: #c82333; }

.form-group {
  margin-bottom: 15px;
  text-align: left;
}
.form-group label {
  display: block;
  margin-bottom: 5px;
  font-weight: bold;
}
.form-group input {
  width: 100%;
  padding: 8px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

/* Footer */
.footer {
  margin-top: auto;
  background: #f5f5f5;
  border-top: 1px solid #ddd;
  padding: 16px 0;
  text-align: center;
  font-size: 13px;
  color: #777;
}
</style>

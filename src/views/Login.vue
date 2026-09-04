<template>
  <div class="page">
    <div class="form-box">
      <div class="school-name">CSUCC</div>
      <h2>Login</h2>

      <form @submit.prevent="handleLogin">
        <div class="field">
          <label for="email">Email</label>
          <input id="email" v-model="form.email" type="email" placeholder="Enter your email" required />
        </div>

        <div class="field">
          <label for="password">Password</label>
          <input id="password" v-model="form.password" type="password" placeholder="Enter your password" required />
        </div>

        <p class="error-msg" v-if="error">{{ error }}</p>
        <button type="submit" class="btn-submit" :disabled="isLoading">
          {{ isLoading ? 'Logging in...' : 'Login' }}
        </button>
      </form>

      <p class="switch-text">
        Don't have an account? <router-link to="/register">Register here</router-link>
      </p>
      <p class="back-link"><router-link to="/">← Back to home</router-link></p>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'

const router = useRouter()
const isLoading = ref(false)
const error = ref('')
const form = ref({ email: '', password: '' })

async function handleLogin() {
  error.value = ''
  isLoading.value = true
  try {
    const res = await fetch(`${import.meta.env.VITE_API_URL}/api/login`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json', 'Accept': 'application/json' },
      body: JSON.stringify(form.value),
    })
    const data = await res.json()
    if (!res.ok) {
      error.value = data.message || data.errors?.email?.[0] || 'Login failed'
      return
    }
    localStorage.setItem('token', data.token)
    localStorage.setItem('user', JSON.stringify(data.user))
    router.push('/dashboard')
  } catch (e) {
    error.value = 'Could not connect to backend.'
  } finally {
    isLoading.value = false
  }
}
</script>

<style scoped>
* { box-sizing: border-box; margin: 0; padding: 0; }

.page {
  font-family: Arial, sans-serif;
  background: #f2f2f2;
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 20px;
}

.form-box {
  background: #fff;
  border: 1px solid #ccc;
  border-radius: 6px;
  padding: 36px 32px;
  width: 100%;
  max-width: 380px;
}

.school-name {
  text-align: center;
  font-size: 22px;
  font-weight: bold;
  color: #1a4a8a;
  margin-bottom: 4px;
  letter-spacing: 1px;
}

h2 {
  text-align: center;
  font-size: 18px;
  font-weight: normal;
  color: #333;
  margin-bottom: 24px;
}

.field {
  margin-bottom: 16px;
}

.field label {
  display: block;
  font-size: 14px;
  margin-bottom: 5px;
  color: #444;
}

.field input {
  width: 100%;
  padding: 8px 10px;
  font-size: 14px;
  border: 1px solid #bbb;
  border-radius: 4px;
  outline: none;
  font-family: Arial, sans-serif;
}

.field input:focus {
  border-color: #1a4a8a;
}

.btn-submit {
  width: 100%;
  padding: 10px;
  background: #1a4a8a;
  color: #fff;
  font-size: 15px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  margin-top: 4px;
  font-family: Arial, sans-serif;
}

.btn-submit:hover:not(:disabled) { background: #153d73; }
.btn-submit:disabled { opacity: 0.6; cursor: default; }

.switch-text {
  text-align: center;
  font-size: 13px;
  color: #555;
  margin-top: 18px;
}

.error-msg {
  color: #c0392b;
  font-size: 13px;
  text-align: center;
  margin-bottom: 10px;
}

.switch-text a, .back-link a {
  color: #1a4a8a;
  text-decoration: none;
}
.switch-text a:hover, .back-link a:hover { text-decoration: underline; }

.back-link {
  text-align: center;
  margin-top: 10px;
  font-size: 13px;
}
</style>

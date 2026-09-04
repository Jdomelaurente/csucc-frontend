<template>
  <div class="page">
    <header class="header">
      <div class="container header-inner">
        <div class="logo">CSUCC</div>
        <nav class="nav">
          <router-link to="/login">Login</router-link>
          <router-link to="/register" class="btn-primary">Register</router-link>
        </nav>
      </div>
    </header>

    <main class="main-content">
      <section class="hero container">
        <h1>Caraga State University</h1>
        <p>An online platform for students and faculty to manage courses, schedules, and campus resources.</p>
        <div class="hero-btns">
        </div>
      </section>

    </main>

    <footer class="footer">
      <div class="container">
        <p>All rights reserved.</p>
      </div>
    </footer>
  </div>
</template>

<script setup>
const features = [
  { title: 'Course Enrollment', desc: 'Browse and enroll in available courses each semester.' },
  { title: 'Class Schedule', desc: 'View your weekly class schedule in one place.' },
  { title: 'Grades', desc: 'Check your grades and academic standing anytime.' },
  { title: 'Announcements', desc: 'Stay updated with the latest school announcements.' },
]
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
.nav a {
  color: #fff;
  text-decoration: none;
  font-size: 14px;
}
.nav a:hover { text-decoration: underline; }

/* Buttons */
.btn-primary {
  background: #1a4a8a;
  color: #fff;
  padding: 7px 16px;
  border-radius: 4px;
  text-decoration: none;
  font-size: 14px;
  border: 1px solid #1a4a8a;
  cursor: pointer;
  display: inline-block;
}
.btn-primary:hover { background: #153d73; }

.btn-outline {
  background: #fff;
  color: #1a4a8a;
  padding: 7px 16px;
  border-radius: 4px;
  text-decoration: none;
  font-size: 14px;
  border: 1px solid #1a4a8a;
  display: inline-block;
}
.btn-outline:hover { background: #f0f4ff; }

/* Main Content */
.main-content {
  flex: 1;
  display: flex;
  align-items: center;
  justify-content: center;
}

/* Hero */
.hero {
  padding: 60px 20px;
  text-align: center;
}
.hero h1 {
  font-size: 28px;
  margin-bottom: 12px;
  color: #1a4a8a;
}
.hero p {
  color: #555;
  max-width: 540px;
  margin: 0 auto 24px;
  line-height: 1.6;
}
.hero-btns {
  display: flex;
  gap: 12px;
  justify-content: center;
}

/* Features */
.features {
  padding: 48px 20px;
}
.features h2 {
  font-size: 20px;
  margin-bottom: 24px;
  color: #1a4a8a;
}
.features-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 20px;
}
.feature {
  border: 1px solid #ddd;
  border-radius: 6px;
  padding: 18px;
  background: #fafafa;
}
.feature strong {
  display: block;
  margin-bottom: 6px;
  font-size: 15px;
}
.feature p {
  font-size: 14px;
  color: #555;
  line-height: 1.5;
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

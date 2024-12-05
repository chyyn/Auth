<template>
    <div class="auth-container">
      <div class="auth-wrapper">
        <div class="auth-image">
          <img src="../assets/auth-illustration.jpg" alt="Authentication Illustration">
        </div>
        <div class="auth-form-container">
          <div class="auth-form">
            <h2>{{ isLogin ? 'Welcome Back' : 'Create Account' }}</h2>
            <form @submit.prevent="handleSubmit">
              <!-- Name field (only for signup) -->
              <transition name="fade">
                <div v-if="!isLogin" class="form-group">
                  <label for="fullName">Full Name</label>
                  <input 
                    type="text" 
                    id="fullName" 
                    v-model="fullName" 
                    required 
                    placeholder="Enter your full name"
                  />
                </div>
              </transition>
  
              <!-- Username field -->
              <div class="form-group">
                <label for="username">Username</label>
                <input 
                  type="text" 
                  id="username" 
                  v-model="username" 
                  required 
                  placeholder="Enter your username"
                />
              </div>
  
              <!-- Email field (only for signup) -->
              <transition name="fade">
                <div v-if="!isLogin" class="form-group">
                  <label for="email">Email</label>
                  <input 
                    type="email" 
                    id="email" 
                    v-model="email" 
                    required 
                    placeholder="Enter your email"
                  />
                </div>
              </transition>
  
              <!-- Password field -->
              <div class="form-group">
                <label for="password">Password</label>
                <input 
                  type="password" 
                  id="password" 
                  v-model="password" 
                  required 
                  placeholder="Enter your password"
                />
              </div>
  
              <!-- Confirm Password field (only for signup) -->
              <transition name="fade">
                <div v-if="!isLogin" class="form-group">
                  <label for="confirmPassword">Confirm Password</label>
                  <input 
                    type="password" 
                    id="confirmPassword" 
                    v-model="confirmPassword" 
                    required 
                    placeholder="Confirm your password"
                  />
                </div>
              </transition>
  
              <!-- Submit Button -->
              <button type="submit" class="auth-submit">
                {{ isLogin ? 'Login' : 'Sign Up' }}
              </button>
            </form>
  
            <!-- Toggle between Login and Signup -->
            <div class="auth-toggle">
              <p>
                {{ isLogin 
                  ? "Don't have an account?" 
                  : "Already have an account?" 
                }}
              </p>
              <a @click="toggleAuthMode">
                  {{ isLogin ? 'Sign Up' : 'Login' }}
                </a>
            </div>
          </div>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  import axios from 'axios';
  import "../components/styles/Authentication.css";
  
  export default {
    name: 'AuthenticationPage',
    data() {
      return {
        isLogin: true,
        username: '',
        password: '',
        email: '',
        fullName: '',
        confirmPassword: ''
      }
    },
    methods: {
      toggleAuthMode() {
        this.isLogin = !this.isLogin;
        // Reset form fields when switching modes
        this.resetForm();
      },
      resetForm() {
        this.username = '';
        this.password = '';
        this.email = '';
        this.fullName = '';
        this.confirmPassword = '';
      },
      async handleSubmit() {
        if (this.isLogin) {
          await this.handleLogin();
        } else {
          await this.handleSignup();
        }
      },
      async handleLogin() {
        const formData = new FormData();
        formData.append('username', this.username);
        formData.append('password', this.password);
  
        try {
          const response = await axios.post('http://localhost:8080/api/login', formData, {
            headers: { 'Content-Type': 'multipart/form-data' }
          });
  
          const { access_token } = response.data;
          localStorage.setItem('token', access_token);
          localStorage.setItem('username', this.username);
  
          alert('Login successful!');
        } catch (error) {
          console.error('Login error:', error);
          alert(error.response?.data?.detail || 'Login failed');
        }
      },
      async handleSignup() {
        // Validate password match
        if (this.password !== this.confirmPassword) {
          alert('Passwords do not match');
          return;
        }
  
        const formData = new FormData();
        formData.append('username', this.username);
        formData.append('email', this.email);
        formData.append('fullname', this.fullName);
        formData.append('password', this.password);
  
        try {
          await axios.post('http://localhost:8080/api/register', formData, {
            headers: { 'Content-Type': 'multipart/form-data' }
          });
  
          alert('Registration successful! Please log in.');
          this.isLogin = true; // Switch back to login
        } catch (error) {
          console.error('Signup error:', error);
          alert(error.response?.data?.detail || 'Registration failed');
        }
      }
    }
  }
  </script>
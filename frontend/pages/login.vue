<template>
  <div class="min-h-screen relative overflow-hidden flex items-center justify-center">
    <!-- 🌌 Background Galaxy -->
    <div
      class="absolute inset-0 bg-cover bg-center animate-pan z-0 opacity-70"
      style="background-image: url('https://images.unsplash.com/photo-1581322332640-e2a2f9e90284?auto=format&fit=crop&w=1600&q=80');"
    ></div>

    <!-- 🌠 Shooting Stars -->
    <div class="absolute top-0 left-0 w-full h-full z-10 pointer-events-none">
      <div class="star" v-for="i in 15" :key="i" />
    </div>

    <!-- 💻 Login Box -->
    <div class="relative z-20 bg-white/10 backdrop-blur-md rounded-2xl shadow-xl p-6 w-80 border border-white/20 text-white hover:scale-105 transform transition duration-300">
      <form @submit.prevent="handleLogin">
        <h1 class="text-xl font-bold mb-4 text-center drop-shadow-lg">Login</h1>

        <input
          v-model="username"
          placeholder="Username"
          class="mb-3 p-2 w-full rounded hover:scale-105 transform transition duration-300 text-gray-900 bg-white/80 focus:outline-none focus:ring-2 focus:ring-sky-300"
        />

        <input
          v-model="password"
          type="password"
          placeholder="Password"
          class="mb-4 p-2 w-full rounded hover:scale-105 transform transition duration-300 text-gray-900 bg-white/80 focus:outline-none focus:ring-2 focus:ring-sky-300"
        />

        <button
          type="submit"
          class="bg-green-500 hover:bg-green-600 p-2 w-full rounded shadow-md transition hover:scale-105 transform duration-300"
        >
          Login
        </button>
      </form>

      <p class="text-center text-sm mt-3">
        Don't have an account?
        <router-link to="/register" class="text-blue-300 hover:underline">Register</router-link>
      </p>

      <p v-if="errorMsg" class="text-red-400 text-sm mt-3 text-center">
        {{ errorMsg }}
      </p>
    </div>
  </div>
</template>

<script setup>
import axios from 'axios'
import { ref } from 'vue'
import { useRouter } from 'vue-router'

const username = ref('')
const password = ref('')
const errorMsg = ref('')
const router = useRouter()

const handleLogin = async () => {
  errorMsg.value = ''
  try {
    const res = await axios.post('https://api-check-in.loeitech.org/api/login', {
      username: username.value,
      password: password.value
    })
    localStorage.setItem('token', res.data.access_token)
    router.push('/dashboard')
  } catch (err) {
    errorMsg.value = err.response?.data?.msg || 'Login failed. Please try again.'
  }
}
</script>

<style scoped>
@keyframes pan {
  0% { background-position: 0% 0%; }
  100% { background-position: 100% 100%; }
}
.animate-pan { animation: pan 60s linear infinite; }

.star {
  position: absolute;
  width: 2px;
  height: 2px;
  background: white;
  opacity: 0.8;
  border-radius: 100%;
  animation: shooting-star 5s linear infinite;
}
.star:nth-child(odd) { animation-delay: 1s; }
.star:nth-child(even) { animation-delay: 3s; }
@keyframes shooting-star {
  0% { transform: translateX(0) translateY(0) scale(1); opacity: 1; }
  100% { transform: translateX(600px) translateY(300px) scale(0.5); opacity: 0; }
}
</style>

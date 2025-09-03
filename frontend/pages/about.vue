<template>
  <div class="min-h-screen relative overflow-hidden bg-black text-white flex items-center justify-center">
    <!-- 🖼 Background Galaxy Image -->
    <div
      class="absolute inset-0 bg-cover bg-center animate-pan z-0 opacity-60"
      style="background-image: url('https://images.unsplash.com/photo-1581322332640-e2a2f9e90284?auto=format&fit=crop&w=1600&q=80');"
    ></div>

    <!-- 🌟 Shooting Stars -->
    <div class="absolute top-0 left-0 w-full h-full z-10 pointer-events-none">
      <div class="star" v-for="i in 10" :key="i" />
    </div>

    <!-- 👨‍🚀 Content -->
    <div class="relative z-20 text-center bg-white/10 backdrop-blur-md px-8 py-10 rounded-2xl shadow-xl border border-white/20">
      <h1 class="text-4xl font-extrabold text-white mb-8 drop-shadow-md tracking-widest">👨‍🚀 ผู้จัดทำ</h1>

      <transition-group name="fade" tag="div" class="space-y-4">
        <p
          v-for="(name, index) in names"
          :key="name"
          class="text-lg text-indigo-100 tracking-wider animate-fade-in"
          :style="{ animationDelay: `${index * 0.4}s` }"
        >
          🌟 {{ name }}
        </p>
      </transition-group>
    </div>
  </div>
</template>

<script setup>
const names = [
  'น.ส.ณัฐธยาน์ ฉันทะพจน์',
  'น.ภูรี โตรอด',
  'น.สุรพงษ์ พรสี'
]
</script>

<style scoped>
/* 🌌 Background pan animation */
@keyframes pan {
  0% {
    background-position: 0% 0%;
  }
  100% {
    background-position: 100% 100%;
  }
}
.animate-pan {
  animation: pan 60s linear infinite;
}

/* ✨ Fade-in effect for names */
@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
.animate-fade-in {
  animation: fadeIn 1s ease-out forwards;
  opacity: 0;
}

/* 🌠 Shooting stars */
.star {
  position: absolute;
  width: 2px;
  height: 2px;
  background: white;
  opacity: 0.8;
  border-radius: 100%;
  animation: shooting-star 6s linear infinite;
}
.star:nth-child(odd) {
  animation-delay: 2s;
}
.star:nth-child(even) {
  animation-delay: 4s;
}

@keyframes shooting-star {
  0% {
    transform: translateX(0) translateY(0) scale(1);
    opacity: 1;
  }
  100% {
    transform: translateX(600px) translateY(300px) scale(0.5);
    opacity: 0;
  }
}
</style>

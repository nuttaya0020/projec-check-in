<template>
  <div class="min-h-screen relative overflow-hidden text-white flex items-center justify-center p-6">
    <!-- 🌌 Background Galaxy -->
    <div
      class="absolute inset-0 bg-cover bg-center animate-pan z-0 opacity-70"
      style="background-image: url('https://images.unsplash.com/photo-1581322332640-e2a2f9e90284?auto=format&fit=crop&w=1600&q=80');"
    ></div>

    <!-- 🌠 Shooting Stars -->
    <div class="absolute top-0 left-0 w-full h-full z-10 pointer-events-none">
      <div class="star" v-for="i in 15" :key="i" />
    </div>

    <!-- 📝 Content -->
    <div class="relative z-20 max-w-3xl w-full bg-white/10 backdrop-blur-md rounded-2xl shadow-xl p-8 border border-white/20">
      <h1 class="text-3xl font-bold mb-6 text-center drop-shadow-lg">ระบบลงเวลา</h1>

      <!-- Input + Button -->
      <div class="flex gap-2 mb-6">
        <input
          v-model="title"
          placeholder="กรอกชื่อพนักงาน..."
          class="flex-1 px-4 py-2 rounded-xl shadow-sm text-gray-900 bg-white/80 focus:bg-white/100 focus:outline-none focus:ring-2 focus:ring-sky-300 transition duration-300"
          @keyup.enter="createNote"
        />
        <button
          @click="createNote"
          class="bg-green-500 hover:bg-green-600 text-white px-4 py-2 rounded-xl shadow-md transition hover:scale-105 transform duration-300"
        >
          ลงเวลา
        </button>
      </div>

      <!-- ไม่มีโน้ต -->
      <div v-if="notes.length === 0" class="text-center text-gray-200 italic">
        ยังไม่มีการลงเวลา
      </div>

      <!-- Notes List -->
      <div class="space-y-4">
        <NoteCard
          v-for="note in notes"
          :key="note.id"
          :note="note"
          class="animate-fade-in"
          @delete="deleteNote"
          @checkout="handleCheckout"
        />
      </div>
    </div>
  </div>
</template>

<script setup>
import axios from 'axios'
import { ref, onMounted } from 'vue'

const title = ref('')
const notes = ref([])

const fetchNotes = async () => {
  const token = localStorage.getItem('token')
  const res = await axios.get('https://api-check-in.loeitech.org/api/notes', {
    headers: { Authorization: `Bearer ${token}` }
  })
  notes.value = res.data
}

const createNote = async () => {
  const token = localStorage.getItem('token')
  const currentTime = new Date().toLocaleString()
  try {
    await axios.post('https://api-check-in.loeitech.org/api/notes', {
      title: title.value,
      content: currentTime
    }, {
      headers: { Authorization: `Bearer ${token}` }
    })
    title.value = ''
    fetchNotes()
  } catch (e) {
    console.error('Create note failed:', e)
  }
}

const deleteNote = async (id) => {
  const token = localStorage.getItem('token')
  await axios.delete(`https://api-check-in.loeitech.org/api/notes/${id}`, {
    headers: { Authorization: `Bearer ${token}` }
  })
  fetchNotes()
}

async function handleCheckout({ id, checkout }) {
  const token = localStorage.getItem('token')
  try {
    await axios.patch(`https://api-check-in.loeitech.org/api/notes/${id}/checkout`, { checkout }, {
      headers: { Authorization: `Bearer ${token}` }
    })
    const note = notes.value.find(n => n.id === id)
    if (note) note.checkout = checkout
  } catch (e) {
    console.error('Checkout update failed:', e)
  }
}

onMounted(fetchNotes)
</script>

<style scoped>
@keyframes pan { 0% { background-position: 0% 0%; } 100% { background-position: 100% 100%; } }
.animate-pan { animation: pan 60s linear infinite; }

@keyframes fadeIn { from { opacity: 0; transform: translateY(20px);} to {opacity:1; transform: translateY(0);} }
.animate-fade-in { animation: fadeIn 1s ease-out forwards; opacity:0; }

.star {
  position: absolute; width: 2px; height: 2px; background: rgb(0, 0, 0); opacity: 0.8; border-radius: 100%; animation: shooting-star 6s linear infinite;
}
.star:nth-child(odd) { animation-delay: 2s; }
.star:nth-child(even) { animation-delay: 4s; }

@keyframes shooting-star {
  0% { transform: translateX(0) translateY(0) scale(1); opacity:1;}
  100% { transform: translateX(600px) translateY(300px) scale(0.5); opacity:0;}
}
</style>

<template>
  <div class="min-h-screen relative overflow-hidden flex items-center justify-center p-6 text-white">
    <!-- Galaxy Background -->
    <div class="absolute inset-0 bg-cover bg-center animate-pan z-0 opacity-70"
      style="background-image: url('https://images.unsplash.com/photo-1581322332640-e2a2f9e90284?auto=format&fit=crop&w=1600&q=80');">
    </div>

    <!-- Shooting Stars -->
    <div class="absolute inset-0 z-10 pointer-events-none">
      <div class="star" v-for="i in 15" :key="i" />
    </div>

    <!-- Glass Content Box -->
    <div class="relative z-20 max-w-3xl w-full bg-white/10 backdrop-blur-md rounded-2xl shadow-xl p-8 border border-white/20">
      <h1 class="text-3xl font-bold mb-6 text-center drop-shadow-lg">ระบบลงเวลา</h1>

      <div class="flex gap-2 mb-6">
        <input v-model="title" placeholder="กรอกชื่อพนักงาน..." class="input-glass" @keyup.enter="createNote"/>
        <button @click="createNote"
          class="bg-white/20 hover:bg-white/30 text-white px-4 py-2 rounded-xl shadow-md hover:scale-105 transform transition duration-300">
          ลงเวลา
        </button>
      </div>

      <div v-if="notes.length===0" class="text-center text-gray-200 italic">ยังไม่มีการลงเวลา</div>

      <div class="space-y-4">
        <NoteCard v-for="note in notes" :key="note.id" :note="note" class="animate-fade-in" @delete="deleteNote" @checkout="handleCheckout"/>
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
  const res = await axios.get('https://api-check-in.loeitech.org/api/notes', { headers: { Authorization: `Bearer ${token}` } })
  notes.value = res.data
}

const createNote = async () => {
  const token = localStorage.getItem('token')
  const currentTime = new Date().toLocaleString()
  try {
    await axios.post('https://api-check-in.loeitech.org/api/notes', { title: title.value, content: currentTime }, { headers: { Authorization: `Bearer ${token}` } })
    title.value = ''
    fetchNotes()
  } catch(e) { console.error(e) }
}

const deleteNote = async (id) => {
  const token = localStorage.getItem('token')
  await axios.delete(`https://api-check-in.loeitech.org/api/notes/${id}`, { headers: { Authorization: `Bearer ${token}` } })
  fetchNotes()
}

async function handleCheckout({ id, checkout }) {
  const token = localStorage.getItem('token')
  try {
    await axios.patch(`https://api-check-in.loeitech.org/api/notes/${id}/checkout`, { checkout }, { headers: { Authorization: `Bearer ${token}` } })
    const note = notes.value.find(n=>n.id===id)
    if(note) note.checkout=checkout
  } catch(e){ console.error(e) }
}

onMounted(fetchNotes)
</script>

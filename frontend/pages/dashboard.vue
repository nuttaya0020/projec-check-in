<template>
  <div class="min-h-screen bg-gradient-to-br from-sky-100 to-white p-6">
    <div class="max-w-3xl mx-auto bg-white rounded-2xl shadow-lg p-8">
      <h1 class="text-3xl font-bold text-gray-800 mb-6 text-center">ระบบลงเวลา</h1>

      <div class="flex gap-2 mb-6">
        <input
          v-model="title"
          placeholder="กรอกชื่อพนักงาน..."
          class="flex-1 px-4 py-2 border rounded-xl shadow-sm
                transition-colors duration-300
                bg-white focus:bg-sky-100
                text-gray-800 focus:outline-none focus:ring-2 focus:ring-sky-300"
          @keyup.enter="createNote"
        />

        <button
          @click="createNote"
          :disabled="!title.trim()"
          class="bg-green-500 hover:bg-green-600 text-white px-4 py-2 rounded-xl shadow-md transition hover:scale-105 transform duration-300 disabled:opacity-50 disabled:cursor-not-allowed"
        >
          ลงเวลา
        </button>
      </div>

      <div v-if="notes.length === 0" class="text-center text-gray-500 italic">
        ยังไม่มีการลงเวลา
      </div>

      <NotesTable
        :columns="columns"
        :rows="notes"
        :page-size="10"
        @edit="editNote"
        @delete="(row) => deleteNote(row.id)"
      />
    </div>
  </div>
</template>

<script setup>
import axios from 'axios'
import { ref, onMounted } from 'vue'
import NotesTable from '~/components/NotesTable.vue'

const title = ref('')
const notes = ref([])
const columns = [
  { key: 'id', label: 'ID' },
  { key: 'title', label: 'ชื่อพนักงาน' },
  { key: 'content', label: 'เวลาเช็คอิน' },
  { key: 'checkout', label: 'เวลาเช็คเอาท์' }
]

const fetchNotes = async () => {
  const token = localStorage.getItem('token')
  const res = await axios.get('https://api-check-in.loeitech.org/api/notes', {
    headers: { Authorization: `Bearer ${token}` }
  })
  notes.value = res.data
}

const createNote = async () => {
  if (!title.value.trim()) {
    alert('กรุณากรอกชื่อพนักงานก่อนลงเวลา')
    return
  }

  const token = localStorage.getItem('token')
  const currentTime = new Date().toLocaleString() // เช่น "25/6/2025, 09:12:34"

  try {
    await axios.post(
      'https://api-check-in.loeitech.org/api/notes',
      { title: title.value, content: currentTime },
      { headers: { Authorization: `Bearer ${token}` } }
    )
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

const editNote = async (row) => {
  const token = localStorage.getItem('token')
  const newTitle = prompt('แก้ไขชื่อพนักงาน', row.title)
  if (newTitle === null) return
  const newContent = prompt('แก้ไขเวลาเช็คอิน', row.content)
  if (newContent === null) return
  await axios.put(
    `https://api-check-in.loeitech.org/api/notes/${row.id}`,
    { title: newTitle, content: newContent },
    { headers: { Authorization: `Bearer ${token}` } }
  )
  fetchNotes()
}

async function handleCheckout({ id, checkout }) {
  const token = localStorage.getItem('token')
  try {
    await axios.patch(
      `https://api-check-in.loeitech.org/api/notes/${id}/checkout`,
      { checkout },
      { headers: { Authorization: `Bearer ${token}` } }
    )
    const note = notes.value.find(n => n.id === id)
    if (note) note.checkout = checkout
  } catch (e) {
    console.error('Checkout update failed:', e)
  }
}

onMounted(fetchNotes)
</script>

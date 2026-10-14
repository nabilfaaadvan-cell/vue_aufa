
<script setup>
import { ref, onMounted } from 'vue'
import api from './api.js'

const users = ref([])
const loading = ref(false)
const error = ref('')
const message = ref('')

async function loadUsers() {
  loading.value = true
  error.value = ''
  message.value = ''

  try {
    const response = await api.get('/api/api/users')
    const result = response.data

    if (result.success === false) {
      users.value = []
      error.value = result.message || 'API gagal mengambil data pengguna.'
      return
    }

    users.value = Array.isArray(result.data) ? result.data : []

    if (users.value.length === 0) {
      message.value = 'Data pengguna tidak ditemukan.'
    }
  } catch (err) {
    error.value =
      err.response?.data?.message ||
      err.message ||
      'Gagal mengambil data pengguna.'
  } finally {
    loading.value = false
  }
}

onMounted(loadUsers)
</script>

<template>
  <div class="container">
    <h1>Aplikasi Vue Aufa</h1>
    <p class="subtitle">Data pengguna dari API Laravel</p>

    <div class="actions">
      <button
        type="button"
        @click="loadUsers"
        :disabled="loading"
      >
        {{ loading ? 'Memuat data...' : 'Muat Ulang' }}
      </button>
    </div>

    <p v-if="loading" class="status">
      Sedang mengambil data pengguna...
    </p>

    <p v-else-if="error" class="error">
      Gagal mengambil data: {{ error }}
    </p>

    <p v-else-if="message" class="status">
      {{ message }}
    </p>

    <div v-else class="table-wrapper">
      <table>
        <thead>
          <tr>
            <th>ID</th>
            <th>Nama</th>
            <th>Email</th>
          </tr>
        </thead>

        <tbody>
          <tr v-for="user in users" :key="user.id">
            <td>{{ user.id }}</td>
            <td>{{ user.name }}</td>
            <td>{{ user.email }}</td>
          </tr>
        </tbody>
      </table>

      <p class="total">
        Total pengguna: {{ users.length }}
      </p>
    </div>
  </div>
</template>

<style scoped>
.container {
  max-width: 900px;
  margin: 60px auto;
  padding: 24px;
  font-family: Arial, sans-serif;
  color: #3c0008;
}

h1 {
  color: #7c3aed;
  text-align: center;
}

.subtitle {
  color: #666;
  text-align: center;
  margin-bottom: 24px;
}

.actions {
  display: flex;
  justify-content: center;
  margin-bottom: 20px;
}

button {
  padding: 10px 18px;
  border: none;
  border-radius: 6px;
  background: #3c0008;
  color: #fdfce8;
  cursor: pointer;
}

button:disabled {
  opacity: 0.6;
  cursor: not-allowed;
}

.table-wrapper {
  overflow-x: auto;
}

table {
  width: 100%;
  border-collapse: collapse;
  background: white;
}

th,
td {
  padding: 12px;
  border: 1px solid #e5e7eb;
  text-align: left;
}

th {
  background: #3c0008;
  color: #fdfce8;
}

tbody tr:nth-child(even) {
  background: #fdfce8;
}

.status,
.error {
  text-align: center;
  padding: 16px;
}

.error {
  color: #dc2626;
}

.total {
  margin-top: 16px;
  font-weight: bold;
}
</style>

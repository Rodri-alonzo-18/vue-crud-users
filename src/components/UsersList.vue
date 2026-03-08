<template>
  <div class="usuarios-outer">
    <div class="usuarios-card">
      <h1>Usuarios</h1>
      <div class="barra-superior">
        <input v-model="search" class="input-buscar" placeholder="Buscar usuario..." />
        <button class="btn btn-primary" @click="openAddModal">Añadir usuario</button>
      </div>
      <UserModal v-if="showModal" :show="showModal" :userToEdit="userToEdit" @save="saveUser" @close="closeModal" />
      <div class="tabla-wrapper">
        <table class="usuarios-table">
          <thead>
            <tr>
              <th><span class="pi pi-user" style="margin-right: 0.3rem;"></span>Nombre</th>
              <th><span class="pi pi-id-card" style="margin-right: 0.3rem;"></span>Usuario</th>
              <th><span class="pi pi-envelope" style="margin-right: 0.3rem;"></span>Email</th>
              <th><span class="pi pi-phone" style="margin-right: 0.3rem;"></span>Teléfono</th>
              <th><span class="pi pi-cog" style="margin-right: 0.3rem;"></span>Acciones</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="user in filteredUsers" :key="user.id">
              <td>{{ user.name }}</td>
              <td>{{ user.username }}</td>
              <td>{{ user.email }}</td>
              <td>{{ user.phone }}</td>
              <td class="acciones-cell">
                <button class="btn btn-edit" @click="editUser(user)">Editar</button>
                <button class="btn btn-delete" @click="deleteUser(user.id)">Eliminar</button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
      <div v-if="loading" class="loading-indicator">
        <i class="pi pi-spin pi-spinner loading-icon"></i>
        <span>Cargando usuarios...</span>
      </div>
    </div>

    <!-- Modal de confirmación para eliminar -->
    <CustomModal
      :show="showConfirmDelete"
      title="¿Eliminar usuario?"
      message="¿Quieres eliminar a este usuario?"
      type="confirm"
      @ok="confirmDelete"
      @cancel="cancelDelete"
    />
  </div>

  <!-- Notificaciones -->
  <NotificationToast
    :show="notification.show"
    :message="notification.message"
    :type="notification.type"
    @close="hideNotification"
  />
</template>

<script setup>
import { ref, onMounted, computed, watch } from 'vue'
import UserModal from './UserModal.vue'
import CustomModal from './CustomModal.vue'
import NotificationToast from './NotificationToast.vue'


const users = ref([])
const loading = ref(true)
const showModal = ref(false)
const search = ref('')
const userToEdit = ref(null)

// Para modales personalizados
const showConfirmDelete = ref(false)
const userIdToDelete = ref(null)

// Para notificaciones
const notification = ref({
  show: false,
  message: '',
  type: 'success'
})


onMounted(async () => {
  const start = Date.now()

  // Intentar cargar usuarios desde localStorage
  const local = localStorage.getItem('users')
  let loadedFromLocal = false

  if (local) {
    try {
      const parsed = JSON.parse(local)
      if (Array.isArray(parsed) && parsed.length > 0) {
        users.value = parsed
        loadedFromLocal = true
      }
    } catch (e) {
    }
  }

  // Si no hay usuarios válidos en localStorage, cargar desde la API
  if (!loadedFromLocal) {
    try {
      const res = await fetch('https://jsonplaceholder.typicode.com/users')
      const data = await res.json()
      users.value = data.map(u => ({
        id: u.id,
        name: u.name,
        username: u.username,
        email: u.email,
        phone: u.phone
      }))
      localStorage.setItem('users', JSON.stringify(users.value))
    } catch (e) {
    }
  }

  const elapsed = Date.now() - start
  if (elapsed < 2000) {
    await new Promise(resolve => setTimeout(resolve, 2000 - elapsed))
  }

  loading.value = false
})

// Guardar en localStorage cada vez que cambia el array de usuarios
watch(users, (val) => {
  localStorage.setItem('users', JSON.stringify(val))
}, { deep: true })

const filteredUsers = computed(() => {
  if (!search.value.trim()) return users.value
  const s = search.value.trim().toLowerCase()
  return users.value.filter(u =>
    u.name.toLowerCase().includes(s) ||
    u.email.toLowerCase().includes(s) ||
    u.username?.toLowerCase().includes(s)
  )
})

function openAddModal() {
  userToEdit.value = null
  showModal.value = true
}

function showNotification(message, type = 'success') {
  notification.value = {
    show: true,
    message: message,
    type: type
  }
}

function hideNotification() {
  notification.value.show = false
}

function saveUser(userData) {
  // Validación simple de email
  const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/
  if (!emailRegex.test(userData.email)) {
    alert('Email no válido')
    return
  }
  
  if (userData.id) {
    // Editar usuario existente
    const index = users.value.findIndex(u => u.id === userData.id)
    if (index !== -1) {
      users.value[index] = userData
      showNotification('Usuario actualizado correctamente', 'success')
    }
  } else {
    // Agregar nuevo usuario
    const nextId = users.value.length ? Math.max(...users.value.map(u => u.id)) + 1 : 1
    users.value.push({
      ...userData,
      id: nextId
    })
    showNotification('Nuevo usuario creado exitosamente', 'success')
  }
  // localStorage se actualiza automáticamente por el watch
  closeModal()
}

function editUser(user) {
  userToEdit.value = user
  showModal.value = true
}

function closeModal() {
  showModal.value = false
  userToEdit.value = null
}

function deleteUser(id) {
  userIdToDelete.value = id
  showConfirmDelete.value = true
}

function confirmDelete() {
  users.value = users.value.filter(u => u.id !== userIdToDelete.value)
  showConfirmDelete.value = false
  userIdToDelete.value = null
  showNotification('Usuario eliminado exitosamente', 'danger')
}

function cancelDelete() {
  showConfirmDelete.value = false
  userIdToDelete.value = null
}
</script>



<style scoped>

h1 {
  margin-bottom: 1.5rem;
  color: #111;
  text-align: center;
}
.usuarios-outer {
  min-height: 100vh;
  background: #222;
  display: flex;
  align-items: flex-start;
  justify-content: center;
  padding: 2rem 0;
}
.usuarios-card {
  background: #fff;
  border-radius: 12px;
  box-shadow: 0 4px 24px rgba(0,0,0,0.13);
  padding: 2.5rem 2rem 2rem 2rem;
  max-width: 950px;
  width: 100%;
  margin: 0 auto;
}
.barra-superior {
  display: flex;
  gap: 1rem;
  margin-bottom: 1.5rem;
  align-items: center;
}
.input-buscar {
  flex: 1;
  padding: 0.5rem;
  border: 1px solid #ccc;
  border-radius: 4px;
  color: #111;
  background: #fff;
}
.tabla-wrapper {
  overflow-x: auto;
}
h1 {
  margin-bottom: 1.5rem;
  color: #111;
}
.barra-superior {
  display: flex;
  gap: 1rem;
  margin-bottom: 1rem;
  align-items: center;
}
.input-buscar {
  flex: 1;
  padding: 0.5rem;
  border: 1px solid #ccc;
  border-radius: 4px;
  color: #111;
  background: #fff;
}
.btn {
  min-width: 90px;
  padding: 0.5rem 1.2rem;
  border: none;
  border-radius: 4px;
  color: #fff;
  font-weight: 500;
  cursor: pointer;
  margin-right: 0.5rem;
  display: inline-block;
  text-align: center;
}
.btn-primary {
  background: #2196f3;
}
.btn-edit {
  background: #00bcd4;
}
.btn-delete {
  background: #f44336;
  margin-right: 0;
}
.usuarios-table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 0.5rem;
  background: #fff;
}
.usuarios-table th, .usuarios-table td {
  border-bottom: 1px solid #e0e0e0;
  padding: 0.7rem 0.5rem;
  text-align: center;
  color: #111;
  min-width: 110px;
}
.usuarios-table th {
  background: #f5f5f5;
  font-weight: 600;
  color: #111;
}
.usuarios-table td {
  background: #fff;
  color: #111;
}
.acciones-cell {
  display: flex;
  gap: 0.5rem;
  align-items: center;
}
hr {
  margin: 1.5rem 0;
  border: none;
  border-top: 1px solid #eee;
}

.loading-indicator {
  margin-top: 1rem;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 0.5rem;
  color: #111;
}

.loading-icon {
  font-size: 2rem;
}
</style>

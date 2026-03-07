<template>
  <div class="modal-mask">
    <div class="modal-container modal-anim user-form-modal" @click.stop>
      <h2 class="user-form-title">{{ isEditing ? 'Editar usuario' : 'Añadir usuario' }}</h2>
      <Form @submit="submit" :validation-schema="schema" class="user-form" ref="form">
        <div class="user-form-group">
          <label class="user-form-label">Nombre *</label>
          <Field 
            name="name" 
            v-model="user.name" 
            class="user-form-input" 
            placeholder="Nombre"
            :class="{ 'input-error': errors.name }"
          />
          <ErrorMessage name="name" class="error-message" />
        </div>
        
        <div class="user-form-group">
          <label class="user-form-label">Usuario *</label>
          <Field 
            name="username" 
            v-model="user.username" 
            class="user-form-input" 
            placeholder="Usuario"
            :class="{ 'input-error': errors.username }"
          />
          <ErrorMessage name="username" class="error-message" />
        </div>
        
        <div class="user-form-group">
          <label class="user-form-label">Email *</label>
          <Field 
            name="email" 
            v-model="user.email" 
            class="user-form-input" 
            placeholder="Email"
            :class="{ 'input-error': errors.email }"
          />
          <ErrorMessage name="email" class="error-message" />
        </div>
        
        <div class="user-form-group">
          <label class="user-form-label">Teléfono *</label>
          <Field 
            name="phone" 
            v-model="user.phone" 
            class="user-form-input" 
            placeholder="Teléfono"
            :class="{ 'input-error': errors.phone }"
          />
          <ErrorMessage name="phone" class="error-message" />
        </div>
        
        <div class="user-form-actions">
          <button type="button" class="user-form-cancel" @click="close">Cancelar</button>
          <button type="submit" class="user-form-submit">{{ isEditing ? 'Actualizar' : 'Guardar' }}</button>
        </div>
      </Form>
    </div>
  </div>
</template>

<script setup>
import { reactive, computed, watch, ref } from 'vue'
import { Form, Field, ErrorMessage } from 'vee-validate'
import * as yup from 'yup'

const emit = defineEmits(['save', 'close'])
const props = defineProps({
  show: Boolean,
  userToEdit: Object
})

const form = ref(null)
const user = reactive({
  id: null,
  name: '',
  username: '',
  email: '',
  phone: ''
})

const isEditing = computed(() => !!props.userToEdit?.id)

// Variable para almacenar errores
const errors = ref({})

// Esquema de validación con yup
const schema = yup.object({
  name: yup
    .string()
    .required('El nombre es obligatorio')
    .min(2, 'El nombre debe tener al menos 2 caracteres')
    .max(50, 'El nombre no puede exceder 50 caracteres'),
  
  username: yup
    .string()
    .required('El nombre de usuario es obligatorio')
    .min(3, 'El usuario debe tener al menos 3 caracteres')
    .max(30, 'El usuario no puede exceder 30 caracteres')
    .matches(/^[a-zA-Z0-9._]+$/, 'El usuario solo puede contener letras, números, puntos y guiones bajos'),
  
  email: yup
    .string()
    .required('El email es obligatorio')
    .email('Debe ser un email válido')
    .test('contains-at', 'El email debe contener "@"', (value) => {
      return value && value.includes('@')
    }),
  
  phone: yup
    .string()
    .required('El teléfono es obligatorio')
    .test('min-digits', 'El teléfono debe tener al menos 8 dígitos', (value) => {
      if (!value) return false
      // Eliminar caracteres no numéricos y contar dígitos
      const digits = value.replace(/\D/g, '')
      return digits.length >= 8
    })
    .test('valid-phone', 'El teléfono debe contener solo números, espacios, puntos, guiones o paréntesis', (value) => {
      if (!value) return false
      return /^[\d\s\-\(\)\+\.]+$/.test(value)
    })
})

// Cargar los datos del usuario si se está editando
watch(() => props.userToEdit, (newUser) => {
  if (newUser) {
    user.id = newUser.id
    user.name = newUser.name
    user.username = newUser.username
    user.email = newUser.email
    user.phone = newUser.phone
  }
}, { immediate: true })

function submit(values, { resetForm }) {
  // Si llegamos aquí, la validación ya pasó
  emit('save', { ...user })
  resetForm()
  reset()
}

function close() {
  emit('close')
  reset()
}

function reset() {
  user.id = null
  user.name = ''
  user.username = ''
  user.email = ''
  user.phone = ''
}
</script>

<style scoped>
.modal-mask {
  position: fixed;
  z-index: 9999;
  top: 0; left: 0; right: 0; bottom: 0;
  background: rgba(0,0,0,0.3);
  display: flex;
  align-items: center;
  justify-content: center;
}
.modal-container {
  background: #fff;
  padding: 2rem;
  border-radius: 8px;
  min-width: 350px;
  max-width: 90vw;
  box-shadow: 0 2px 8px rgba(0,0,0,0.15);
}
.form-group {
  margin-bottom: 1rem;
  display: flex;
  flex-direction: column;
}
label {
  font-weight: 500;
  margin-bottom: 0.2rem;
  color: #111;
}
input {
  padding: 0.5rem;
  border: 1px solid #ccc;
  border-radius: 4px;
  color: #111;
  background: #fff;
}
.modal-actions {
  display: flex;
  justify-content: flex-end;
  gap: 1rem;
}
.btn-primary {
  background: #2196f3;
  color: #fff;
  border: none;
  border-radius: 4px;
  padding: 0.5rem 1.2rem;
  font-weight: 500;
  cursor: pointer;
}
@keyframes modalIn {
  0% {
    opacity: 0;
    transform: scale(0.7);
  }
  100% {
    opacity: 1;
    transform: scale(1);
  }
}
.modal-anim {
  animation: modalIn 0.22s cubic-bezier(.4,1.3,.6,1) both;
}
/* --- Nuevo diseño tipo imagen --- */
.user-form-modal {
  background: #fff;
  border-radius: 16px;
  box-shadow: 0 8px 32px rgba(0,0,0,0.10);
  padding: 2.5rem 2.5rem 2rem 2.5rem;
  min-width: 350px;
  max-width: 420px;
  width: 100%;
}
.user-form-title {
  color: #444;
  font-size: 2rem;
  font-weight: 700;
  margin-bottom: 2rem;
  text-align: left;
}
.user-form {
  display: flex;
  flex-direction: column;
  gap: 1.3rem;
}
.user-form-group {
  display: flex;
  flex-direction: column;
  gap: 0.3rem;
}
.user-form-label {
  font-weight: 700;
  color: #444;
  font-size: 1.1rem;
  margin-bottom: 0.2rem;
  text-align: left;
}
.user-form-input {
  padding: 0.9rem 1.1rem;
  border: 1.5px solid #e0e4ea;
  border-radius: 8px;
  font-size: 1.1rem;
  color: #444;
  background: #f9fafb;
  outline: none;
  transition: border 0.2s;
  box-shadow: 0 2px 8px rgba(0,0,0,0.03);
}
.user-form-input:focus {
  border: 1.5px solid #2bb6a8;
  background: #fff;
}
.user-form-actions {
  display: flex;
  justify-content: flex-start;
  align-items: center;
  gap: 1.5rem;
  margin-top: 1.2rem;
}
.user-form-submit {
  background: #2bb6a8;
  color: #fff;
  font-weight: 700;
  font-size: 1.1rem;
  border: none;
  border-radius: 7px;
  padding: 0.8rem 2.2rem;
  cursor: pointer;
  box-shadow: 0 2px 8px rgba(43,182,168,0.08);
  transition: background 0.18s;
}
.user-form-submit:hover {
  background: #229e92;
}
.user-form-cancel {
  background: #e0e4ea;
  color: #444;
  font-weight: 700;
  font-size: 1.1rem;
  border: none;
  border-radius: 7px;
  padding: 0.8rem 2.2rem;
  cursor: pointer;
  transition: background 0.18s;
}
.user-form-cancel:hover {
  background: #cfd4db;
}

/* Estilos para mensajes de error */
.error-message {
  color: #f44336;
  font-size: 0.875rem;
  margin-top: 0.25rem;
  font-weight: 500;
}

.input-error {
  border-color: #f44336 !important;
  background-color: #fff5f5 !important;
}

.input-error:focus {
  border-color: #f44336 !important;
  box-shadow: 0 0 0 2px rgba(244, 67, 54, 0.2) !important;
}
</style>

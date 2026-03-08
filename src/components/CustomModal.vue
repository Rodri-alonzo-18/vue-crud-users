<template>
  <div v-if="show" class="custom-modal-overlay">
    <div class="custom-modal custom-modal-anim" @click.stop>
      <template v-if="type === 'confirm'">
        <div style="display: flex; flex-direction: column; align-items: center;">
          <span class="pi pi-exclamation-triangle" style="font-size: 2.5rem; color: #f44336; margin-bottom: 0.5rem;"></span>
          <h2 v-if="title">{{ title }}</h2>
        </div>
      </template>
      <h2 v-else-if="title">{{ title }}</h2>
      <div class="custom-modal-content">
        <slot>{{ message }}</slot>
      </div>
      <div class="custom-modal-actions">
        <button v-if="type === 'confirm'" class="btn btn-cancel" @click="$emit('cancel')">Cancelar</button>
        <button
          class="btn"
          :class="type === 'confirm' ? 'btn-delete' : 'btn-primary'"
          @click="$emit('ok')"
        >
          {{ type === 'confirm' ? 'Eliminar' : 'Aceptar' }}
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
const props = defineProps({
  show: Boolean,
  title: String,
  message: String,
  type: {
    type: String,
    default: 'info' // 'info' o 'confirm'
  }
})
</script>

<style scoped>
.custom-modal-overlay {
  position: fixed;
  top: 0; left: 0; right: 0; bottom: 0;
  background: rgba(0,0,0,0.35);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
}
.custom-modal {
  background: #fff;
  border-radius: 10px;
  box-shadow: 0 4px 24px rgba(0,0,0,0.18);
  padding: 2rem 1.5rem 1.5rem 1.5rem;
  min-width: 320px;
  max-width: 400px;
  width: 100%;
  text-align: center;
}
.custom-modal h2 {
  margin-bottom: 1rem;
  color: #222;
}
.custom-modal-content {
  margin-bottom: 1.5rem;
  color: #444;
  font-size: 1.1rem;
}
.custom-modal-actions {
  display: flex;
  justify-content: center;
  gap: 1rem;
}
.btn {
  min-width: 90px;
  padding: 0.5rem 1.2rem;
  border: none;
  border-radius: 4px;
  color: #fff;
  font-weight: 500;
  cursor: pointer;
  display: inline-block;
  text-align: center;
}
.btn-primary {
  background: #2196f3;
}
.btn-cancel {
  background: #aaa;
}
.btn-delete {
  background: #f44336;
}
</style>

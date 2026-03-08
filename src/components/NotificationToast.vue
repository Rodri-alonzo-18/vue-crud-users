<template>
  <div v-if="show" class="notification-toast" :class="type">
    <div class="notification-content">
      <span class="notification-icon" :class="iconClass"></span>
      <span class="notification-message">{{ message }}</span>
    </div>
    <button class="notification-close" @click="close">&times;</button>
  </div>
</template>

<script setup>
import { ref, watch, onMounted, computed } from 'vue'

const props = defineProps({
  show: Boolean,
  message: String,
  type: {
    type: String,
    default: 'success', // success, error, warning, info, danger
    validator: (value) => ['success', 'error', 'warning', 'info', 'danger'].includes(value)
  },
  duration: {
    type: Number,
    default: 3000 // 3 segundos
  }
})

const emit = defineEmits(['close'])

let timeout = null

const iconClass = computed(() => {
  const icons = {
    success: 'pi pi-check-circle',
    error: 'pi pi-times-circle',
    warning: 'pi pi-exclamation-triangle',
    info: 'pi pi-info-circle',
    danger: 'pi pi-check-circle'
  }
  return icons[props.type] || 'pi pi-info-circle'
})

function close() {
  emit('close')
  if (timeout) {
    clearTimeout(timeout)
  }
}

watch(() => props.show, (newShow) => {
  if (newShow && props.duration > 0) {
    timeout = setTimeout(() => {
      close()
    }, props.duration)
  }
})

onMounted(() => {
  if (props.show && props.duration > 0) {
    timeout = setTimeout(() => {
      close()
    }, props.duration)
  }
})
</script>

<style scoped>
.notification-toast {
  position: fixed;
  top: 20px;
  right: 20px;
  background: white;
  border-radius: 8px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
  padding: 16px 20px;
  min-width: 300px;
  max-width: 400px;
  z-index: 9999;
  display: flex;
  align-items: center;
  justify-content: space-between;
  animation: slideIn 0.3s ease-out;
}

.notification-content {
  display: flex;
  align-items: center;
  gap: 12px;
}

.notification-icon {
  font-size: 20px;
}

.notification-message {
  font-size: 14px;
  font-weight: 500;
  color: #000000;
}

.notification-close {
  background: none;
  border: none;
  font-size: 20px;
  cursor: pointer;
  color: #666;
  padding: 0;
  width: 24px;
  height: 24px;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 4px;
  transition: all 0.2s;
}

.notification-close:hover {
  background: rgba(0, 0, 0, 0.1);
  color: #333;
}

/* Colores por tipo */
.notification-toast.success {
  border-left: 4px solid #22c55e;
  background: #f0fdf4;
}

.notification-toast.success .notification-icon {
  color: #22c55e;
}

.notification-toast.error {
  border-left: 4px solid #ef4444;
  background: #fef2f2;
}

.notification-toast.error .notification-icon {
  color: #ef4444;
}

.notification-toast.danger {
  border-left: 4px solid #ef4444;
  background: #fef2f2;
}

.notification-toast.danger .notification-icon {
  color: #ef4444;
}

.notification-toast.warning {
  border-left: 4px solid #f59e0b;
  background: #fffbeb;
}

.notification-toast.warning .notification-icon {
  color: #f59e0b;
}

.notification-toast.info {
  border-left: 4px solid #3b82f6;
  background: #eff6ff;
}

.notification-toast.info .notification-icon {
  color: #3b82f6;
}

@keyframes slideIn {
  from {
    transform: translateX(100%);
    opacity: 0;
  }
  to {
    transform: translateX(0);
    opacity: 1;
  }
}
</style>

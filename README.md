# vue-crud-users

Aplicación web desarrollada en **Vue 3 + Vite** que implementa un **CRUD de usuarios**
consumiendo la API pública:

`https://jsonplaceholder.typicode.com/users`

El objetivo es cumplir los requisitos del ejercicio técnico propuesto por la empresa:

- Consumir una API pública de usuarios.
- Mostrar el listado en una tabla con la información básica.
- Permitir crear, editar y eliminar usuarios trabajando sobre estado local en el frontend.

La lógica del CRUD se maneja íntegramente en el cliente: los datos se traen una sola vez desde
la API y, a partir de ahí, todas las operaciones se realizan sobre un array local sincronizado
con `localStorage`.

---

## ⚙️ Tecnologías

- **Vue.js 3** (Composition API)
- **Vite** como herramienta de desarrollo y build
- **CSS** simple para estilos
- **Fetch API** para consumo de la API de usuarios
- **localStorage** para almacenar el listado localmente

---

## 🚀 Instalación y ejecución

Dentro de la carpeta del proyecto:

```bash
# Instalar dependencias
npm install

# Ejecutar en modo desarrollo
npm run dev
```

La aplicación se servirá en `http://localhost:5173` (o el puerto que indique Vite).

---

## 📋 Funcionalidad del ejercicio

Este proyecto se centra exclusivamente en los requisitos indicados en el enunciado, sin añadir
capas extra de complejidad (router, store global, etc.), para que el revisor pueda ver de forma
directa la implementación del CRUD con Vue 3.

### 1. Listado de usuarios

- Al iniciar, la app hace una petición `GET` a:

  `https://jsonplaceholder.typicode.com/users`

- Los usuarios obtenidos se guardan en:
  - Un array local en memoria.
  - `localStorage`, para no depender siempre de la API.
- Se muestra una **tabla** donde cada fila incluye:
  - `name`, `username`, `email`, `phone`.
- Mientras se obtienen los datos se muestra un **mensaje de carga** (loading).

### 2. Agregar usuario

- Botón **“Añadir usuario”** que abre un **modal** con un formulario.
- Campos:
  - Nombre, Usuario, Email, Teléfono (todos obligatorios).
- Se realiza una validación básica del email.
- El nuevo usuario:
  - Se agrega solo al **array local** (no se envía a la API).
  - El campo `id` se genera automáticamente siguiendo la secuencia de ids existentes.

### 3. Editar usuario

- Cada fila tiene un botón **“Editar”**.
- Al hacer clic:
  - Se abre un modal con los datos actuales del usuario cargados en el formulario.
- Al guardar:
  - Se actualiza el usuario correspondiente en el array local según su `id`.

### 4. Eliminar usuario

- Cada fila tiene un botón **“Eliminar”**.
- Antes de eliminar:
  - Se muestra una **ventana de confirmación** (modal).
- Si se confirma:
  - El usuario se elimina del array local.
  - El cambio se refleja en la tabla.

---

## 🧱 Estructura simple del proyecto

- `src/App.vue`  
  Renderiza el componente principal de la aplicación.

- `src/components/UsersList.vue`  
  Lógica principal del CRUD y tabla de usuarios.

- `src/components/UserModal.vue`  
  Modal para crear y editar usuarios.

- `src/components/CustomModal.vue`  
  Modal de confirmación para eliminar usuarios.

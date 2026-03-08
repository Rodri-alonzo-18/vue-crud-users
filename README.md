# vue-crud-users

Aplicación web desarrollada en **Vue 3 + Vite** que implementa un **CRUD completo de usuarios**
consumiendo la API pública:

`https://jsonplaceholder.typicode.com/users`

El objetivo es gestionar usuarios (listar, crear, editar y eliminar) utilizando estado local,
validaciones de formularios y una experiencia de usuario cómoda y clara.

***

## 🧩 Tecnologías principales

- **Vue.js 3** (Composition API con `<script setup>`)
- **Vite** como herramienta de desarrollo y build
- **Vuetify 4** como framework de UI base
- **PrimeIcons** para iconos (incluyendo el spinner de carga)
- **VeeValidate 4 + Yup** para validación de formularios
- **localStorage** para persistencia local del CRUD

***

## ⚙️ Requisitos previos

- Node.js 18+ (recomendado)
- npm (gestor de paquetes)

***

## 🚀 Instalación y ejecución

Clonar el repositorio y entrar en la carpeta del proyecto:

```bash
git clone <https://github.com/Rodri-alonzo-18/vue-crud-users.git>
cd vue-crud-users
```

Instalar dependencias:

```bash
npm install
```

Ejecutar en modo desarrollo:

```bash
npm run dev
```

La aplicación se servirá en `http://localhost:5173` (o el puerto que indique Vite).

Build para producción:

```bash
npm run build
```

Previsualizar el build:

```bash
npm run preview
```

***

## 📋 Funcionalidades principales

### Listado de usuarios

- Consumo de `https://jsonplaceholder.typicode.com/users` al iniciar.
- Normalización de datos al modelo:
  - `id`, `name`, `username`, `email`, `phone`.
- Almacenamiento de los usuarios en:
  - Un array reactivo local.
  - `localStorage` para persistir entre recargas.
- Tabla con columnas:
  - Nombre, Usuario, Email, Teléfono y Acciones.
- Filtro de búsqueda por nombre, usuario y email.
- Indicador de carga:
  - Spinner de PrimeIcons (`pi pi-spin pi-spinner`) y texto “Cargando usuarios…”.

### Crear usuario

- Botón “Añadir usuario” que abre un **modal** con formulario.
- Campos: Nombre, Usuario, Email, Teléfono (todos obligatorios).
- Validación con **VeeValidate + Yup**:
  - Email:
    - Formato válido y obligatoriedad de `@`.
  - Usuario:
    - Longitud mínima/máxima, permite letras, números, puntos y guion bajo.
  - Teléfono:
    - Mínimo 8 dígitos (ignorando símbolos).
    - Permite números, espacios, puntos, guiones, paréntesis y `+`.
- El nuevo usuario se agrega solo al array local y se persiste en `localStorage`.
- El `id` se genera automáticamente a partir del máximo `id` existente.

### Editar usuario

- Botón “Editar” en cada fila de la tabla.
- Modal reutilizado con los datos actuales del usuario precargados.
- Al guardar:
  - Se actualiza el usuario correspondiente en el array local según su `id`.
  - Se sincroniza `localStorage`.

### Eliminar usuario

- Botón “Eliminar” en cada fila.
- Modal de confirmación antes de eliminar.
- Si se confirma:
  - Se elimina el usuario del array local.
  - `localStorage` se actualiza automáticamente.

***

## ✨ Detalles de UX y validación

- Formulario gestionado con **VeeValidate + Yup** y mensajes de error claros.
- Campos con error se resaltan visualmente.
- **Notificaciones tipo toast** para:
  - Usuario creado.
  - Usuario actualizado.
  - Usuario eliminado (versión especial en rojo).
- Loader visible al menos 2 segundos al cargar datos, para una mejor percepción de estado.

***

## 🧱 Estructura de componentes

- `App.vue`: punto de entrada de la vista principal.
- `components/UsersList.vue`: listado, búsqueda y lógica del CRUD.
- `components/UserModal.vue`: formulario de alta/edición de usuario.
- `components/CustomModal.vue`: modal genérico de confirmación.
- `components/NotificationToast.vue`: componente para notificaciones.

***

## 📌 Posibles mejoras futuras

- Añadir paginación para listas largas de usuarios.
- Añadir tests unitarios y de integración.
- Internacionalización (i18n).
- Integración de Vue Router para futuras vistas adicionales.


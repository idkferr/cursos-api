# 📚 API Cursos

API REST para la gestión de cursos académicos. Construida con **Node.js**, **Express** y **SQLite**, documentada con **Swagger** y desplegada en **Render**.

🔗 **API en producción:** https://cursos-api-0s42.onrender.com
📖 **Documentación interactiva:** https://cursos-api-0s42.onrender.com/docs

---

## 🚀 Tecnologías

- **Node.js** — entorno de ejecución JavaScript
- **Express** — framework web minimalista
- **better-sqlite3** — base de datos SQL local, sin servidor
- **Swagger (swagger-ui-express + swagger-jsdoc)** — documentación interactiva
- **Render** — plataforma de despliegue continuo

---

## 📋 Endpoints

| Método   | Ruta            | Descripción                       |
| -------- | --------------- | --------------------------------- |
| `GET`    | `/cursos`       | Lista todos los cursos            |
| `POST`   | `/cursos`       | Crea un nuevo curso               |
| `PUT`    | `/cursos/:id`   | Modifica un curso existente       |
| `DELETE` | `/cursos/:id`   | Elimina un curso                  |
| `GET`    | `/docs`         | Documentación Swagger interactiva |

### Modelo de datos — `Curso`

```json
{
  "id": 1,
  "nombre": "Calculo Diferencial",
  "instructor": "Dra. Ana Perez",
  "creditos": 4
}
```

| Campo        | Tipo     | Descripción                              |
| ------------ | -------- | ---------------------------------------- |
| `id`         | integer  | Identificador único (autogenerado)       |
| `nombre`     | string   | Nombre del curso                         |
| `instructor` | string   | Nombre del docente a cargo               |
| `creditos`   | integer  | Cantidad de créditos académicos          |

---

## 💻 Ejecutar localmente

### Requisitos
- Node.js v18 o superior
- Git

### Pasos

```bash
# Clonar el repositorio
git clone https://github.com/idkferr/cursos-api.git
cd cursos-api

# Instalar dependencias
npm install

# Levantar el servidor
npm start
```

La API quedará disponible en `http://localhost:3000` y la documentación en `http://localhost:3000/docs`.

---

## 🧪 Ejemplos de uso

### Crear un curso

```bash
curl -X POST https://cursos-api-0s42.onrender.com/cursos \
  -H "Content-Type: application/json" \
  -d '{
    "nombre": "Calculo Diferencial",
    "instructor": "Dra. Ana Perez",
    "creditos": 4
  }'
```

### Listar cursos

```bash
curl https://cursos-api-0s42.onrender.com/cursos
```

### Actualizar un curso

```bash
curl -X PUT https://cursos-api-0s42.onrender.com/cursos/1 \
  -H "Content-Type: application/json" \
  -d '{
    "nombre": "Calculo Diferencial Avanzado",
    "instructor": "Dr. Carlos Ruiz",
    "creditos": 5
  }'
```

### Eliminar un curso

```bash
curl -X DELETE https://cursos-api-0s42.onrender.com/cursos/1
```

---

## 📁 Estructura del proyecto

```
cursos-api/
├── index.js           # Servidor Express + endpoints + Swagger
├── db.js              # Configuración de SQLite y schema
├── package.json       # Dependencias y scripts
├── .gitignore         # Archivos excluidos del repo
└── README.md          # Este archivo
```

---

## ☁️ Despliegue

La API se despliega automáticamente en **Render** con cada `push` a la rama `main`.

> ⏳ **Nota sobre el plan gratuito de Render:** el servicio entra en reposo después de 15 minutos sin actividad. La primera petición tras un periodo de inactividad puede tardar hasta 30 segundos en responder.

---

## 📝 Licencia

Proyecto académico desarrollado en el marco del curso de **Proyectos de Software** — Universidad de Valparaíso.

# 📚 API Cursos

API REST para la gestión de cursos académicos. Construida con **Node.js**, **Express** y **SQLite**, documentada con **Swagger** y desplegada en **Render**.

🔗 **API en producción:** https://cursos-api-0s42.onrender.com/cursos
📖 **Documentación interactiva:** https://cursos-api-0s42.onrender.com/docs

---

## 📑 Contenido

- [🚀 Tecnologías](#-tecnologías)
- [📋 Endpoints](#-endpoints)
- [🛠️ Parte 1: Cómo construir esta API desde cero](#️-parte-1-cómo-construir-esta-api-desde-cero)
- [🔄 Parte 2: Flujo de Gestión de la Configuración](#-parte-2-flujo-de-gestión-de-la-configuración-swebok--github-flow)
- [📊 Evidencia del flujo aplicado](#-evidencia-del-flujo-de-gestión-de-la-configuración-aplicado)
- [💻 Ejecutar localmente](#-ejecutar-localmente-versión-rápida)
- [🧪 Probar la API con Thunder Client](#-probar-la-api-con-thunder-client)

---

## 🚀 Tecnologías

- **Node.js v18+** — entorno de ejecución JavaScript
- **Express** — framework web minimalista
- **better-sqlite3** — base de datos SQL local, sin servidor
- **Swagger (swagger-ui-express + swagger-jsdoc)** — documentación interactiva
- **Render** — plataforma de despliegue continuo
- **GitHub Flow** — flujo de trabajo con Issues, Branches, Pull Requests y Releases

---

## 📋 Endpoints

| Método   | Ruta            | Descripción                       |
| -------- | --------------- | --------------------------------- |
| `GET`    | `/cursos`       | Lista todos los cursos            |
| `GET`    | `/cursos/:id`   | Obtiene un curso por su ID        |
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

---

# 🛠️ Parte 1: Cómo construir esta API desde cero

Esta guía te lleva paso a paso desde un computador vacío hasta tener la API funcionando local y deployada en Render.

## Requisitos previos

Instala estas herramientas una sola vez:

- **Node.js v18+** → https://nodejs.org (elige la versión LTS, deja todas las opciones por defecto)
- **Git** → https://git-scm.com
- **VS Code** → https://code.visualstudio.com
- **Cuenta en GitHub** → https://github.com

Verifica en una terminal:
```bash
node --version
git --version
```

## Paso 1 — Crear el repositorio en GitHub

1. Entra a https://github.com → botón **`+`** arriba derecha → **New repository**
2. **Repository name:** `cursos-api`
3. Marca **Public** ✅
4. Marca **Add a README file** ✅
5. Click en **Create repository**
6. Botón verde **Code** → copia la URL HTTPS

## Paso 2 — Clonar el repo

```bash
cd Desktop
git clone https://github.com/tu-usuario/cursos-api.git
cd cursos-api
code .
```

## Paso 3 — Inicializar el proyecto Node

Dentro de VS Code, abre la terminal (`` Ctrl + ` ``):

```bash
npm init -y
npm install express better-sqlite3 swagger-ui-express swagger-jsdoc
```

## Paso 4 — Crear `db.js`

Archivo nuevo en la raíz → **`db.js`**:

```javascript
const Database = require('better-sqlite3');
const db = new Database('datos.db');

db.exec(`
  CREATE TABLE IF NOT EXISTS cursos (
    id         INTEGER PRIMARY KEY AUTOINCREMENT,
    nombre     TEXT NOT NULL,
    instructor TEXT NOT NULL,
    creditos   INTEGER NOT NULL
  )
`);

module.exports = db;
```

## Paso 5 — Crear `index.js`

Archivo nuevo en la raíz → **`index.js`**:

```javascript
const express      = require('express');
const db           = require('./db');
const swaggerUi    = require('swagger-ui-express');
const swaggerJsdoc = require('swagger-jsdoc');

const app = express();
app.use(express.json());

const swaggerSpec = swaggerJsdoc({
  definition: {
    openapi: '3.0.0',
    info: {
      title: 'API Cursos',
      version: '1.0.0',
      description: 'API para gestionar cursos academicos'
    }
  },
  apis: ['./index.js']
});
app.use('/docs', swaggerUi.serve, swaggerUi.setup(swaggerSpec));

/**
 * @swagger
 * /cursos:
 *   get:
 *     summary: Lista todos los cursos
 *     responses:
 *       200:
 *         description: Array de cursos
 */
app.get('/cursos', (req, res) => {
  res.json(db.prepare('SELECT * FROM cursos').all());
});

/**
 * @swagger
 * /cursos/{id}:
 *   get:
 *     summary: Obtiene un curso por su ID
 *     parameters:
 *       - in: path
 *         name: id
 *         required: true
 *         schema: { type: integer }
 *     responses:
 *       200:
 *         description: Curso encontrado
 *       404:
 *         description: Curso no encontrado
 */
app.get('/cursos/:id', (req, res) => {
  const curso = db.prepare('SELECT * FROM cursos WHERE id = ?').get(req.params.id);
  if (!curso) return res.status(404).json({ error: 'Curso no encontrado' });
  res.json(curso);
});

/**
 * @swagger
 * /cursos:
 *   post:
 *     summary: Crea un nuevo curso
 *     requestBody:
 *       required: true
 *       content:
 *         application/json:
 *           schema:
 *             type: object
 *             properties:
 *               nombre:     { type: string }
 *               instructor: { type: string }
 *               creditos:   { type: integer }
 *     responses:
 *       201:
 *         description: Curso creado
 */
app.post('/cursos', (req, res) => {
  const { nombre, instructor, creditos } = req.body;
  const r = db.prepare(
    'INSERT INTO cursos (nombre, instructor, creditos) VALUES (?, ?, ?)'
  ).run(nombre, instructor, creditos);
  res.status(201).json({ id: r.lastInsertRowid, nombre, instructor, creditos });
});

/**
 * @swagger
 * /cursos/{id}:
 *   put:
 *     summary: Modifica un curso
 *     parameters:
 *       - in: path
 *         name: id
 *         required: true
 *         schema: { type: integer }
 *     requestBody:
 *       required: true
 *       content:
 *         application/json:
 *           schema:
 *             type: object
 *             properties:
 *               nombre:     { type: string }
 *               instructor: { type: string }
 *               creditos:   { type: integer }
 *     responses:
 *       200:
 *         description: Curso actualizado
 *       404:
 *         description: No encontrado
 */
app.put('/cursos/:id', (req, res) => {
  const { nombre, instructor, creditos } = req.body;
  const i = db.prepare(
    'UPDATE cursos SET nombre=?, instructor=?, creditos=? WHERE id=?'
  ).run(nombre, instructor, creditos, req.params.id);
  if (i.changes === 0) return res.status(404).json({ error: 'Curso no encontrado' });
  res.json({ mensaje: 'Curso actualizado' });
});

/**
 * @swagger
 * /cursos/{id}:
 *   delete:
 *     summary: Elimina un curso
 *     parameters:
 *       - in: path
 *         name: id
 *         required: true
 *         schema: { type: integer }
 *     responses:
 *       200:
 *         description: Curso eliminado
 *       404:
 *         description: No encontrado
 */
app.delete('/cursos/:id', (req, res) => {
  const i = db.prepare('DELETE FROM cursos WHERE id=?').run(req.params.id);
  if (i.changes === 0) return res.status(404).json({ error: 'Curso no encontrado' });
  res.json({ mensaje: 'Curso eliminado' });
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => console.log(`API corriendo en puerto ${PORT}`));
```

> 🔑 La línea `const PORT = process.env.PORT || 3000` es **clave** para que la API funcione tanto local como en Render.

## Paso 6 — Crear `.gitignore`

Archivo nuevo en la raíz → **`.gitignore`**:

```
node_modules/
datos.db
```

> 💡 `node_modules/` se regenera con `npm install`, no se versiona. `datos.db` es estado runtime, tampoco.

## Paso 7 — Agregar script `start` en `package.json`

```json
"scripts": {
  "start": "node index.js"
}
```

## Paso 8 — Probar localmente

```bash
node index.js
```

En el navegador:
- **http://localhost:3000/cursos** → debe mostrar `[]`
- **http://localhost:3000/docs** → Swagger UI con los 5 endpoints

Detén el servidor con `Ctrl + C`.

## Paso 9 — Primer commit y push a GitHub

Si nunca habías usado Git:
```bash
git config --global user.name "Tu Nombre"
git config --global user.email "tu-correo@uv.cl"
```

Subir el código:
```bash
git add .
git commit -m "feat: api crud de cursos con sqlite y swagger"
git push origin main
```

> 🔑 Si GitHub te pide credenciales y rechaza tu contraseña, necesitas un Personal Access Token: https://github.com/settings/tokens → Generate new token (classic) → marca `repo` → genera y úsalo como contraseña.

## Paso 10 — Deploy en Render

1. https://render.com → **Get Started for Free** → **Sign up with GitHub**
2. Dashboard → **New +** → **Web Service**
3. Selecciona tu repo `cursos-api`
4. Configura:
   - **Name:** `cursos-api`
   - **Branch:** `main`
   - **Runtime:** `Node`
   - **Build Command:** `npm install`
   - **Start Command:** `npm start`
   - **Instance Type:** `Free`
5. **Create Web Service** y espera 2-4 minutos

Cuando termine, Render te da una URL pública.

> ⏳ La primera carga puede tardar 30 seg si el servicio estuvo dormido.

---

# 🔄 Parte 2: Flujo de Gestión de la Configuración (SWEBOK + GitHub Flow)

Esta sección demuestra cómo aplicar los conceptos de **SWEBOK Cap. 8 (Software Configuration Management)** usando **GitHub Flow**.

## ⚙️ Paso 0 — Configurar protección de `main`

1. Repo en GitHub → **Settings** → menú lateral **Branches**
2. Click en **Add branch ruleset**
3. **Ruleset Name:** `Proteger main`
4. **Enforcement status:** Active
5. **Target branches:** Add target → **Include default branch**
6. En **Rules**, marca **Require a pull request before merging** → **Required approvals: 1**
7. Click **Create**

## 📝 Paso 1 — Crear el Issue (Solicitud de Cambio)

1. Repo → pestaña **Issues** → **New issue**
2. **Título descriptivo** y **descripción detallada** con criterios de aceptación
3. **Panel derecho:** asignar Assignee y Label `enhancement`
4. **Submit new issue**

## 🌿 Paso 2 — Crear una rama desde `main`

```bash
git checkout main
git pull origin main
git checkout -b feature/1-get-curso-por-id
```

> Convención: `tipo/numero-issue-descripcion-corta`

## 💻 Paso 3 — Implementar el cambio

Modifica `index.js` agregando el nuevo endpoint con su anotación Swagger.

Prueba localmente:
```bash
node index.js
```

## 💾 Paso 4 — Commit con referencia al Issue

```bash
git add index.js
git commit -m "feat: agregar endpoint GET /cursos/:id (#1)"
```

> El `(#1)` enlaza el commit al Issue automáticamente.

## 🚀 Paso 5 — Publicar la rama en GitHub

```bash
git push origin feature/1-get-curso-por-id
```

## 🔀 Paso 6 — Abrir el Pull Request

1. En GitHub → banner amarillo **Compare & pull request**
2. **Título** y **descripción detallada** (qué cambia, por qué, cómo probar, impacto)
3. Termina la descripción con: `Closes #1`
4. **Panel derecho:** asignar Reviewer, Assignee, Label
5. **Create pull request**

## 👀 Paso 7 — Code Review

El revisor:
1. Abre el PR → pestaña **Files changed**
2. Lee el diff línea por línea
3. Deja **al menos un comentario sustantivo**
4. **Review changes** → **Approve** ✅ → **Submit review**

> Para invitar a un colaborador: Settings → Collaborators → Add people.

## ✅ Paso 8 — Fusionar el PR

Una vez aprobado:
1. Click en **Merge pull request**
2. **Create a merge commit**
3. **Confirm merge**

Automáticamente: cambios a `main`, Issue cerrado, Render redeploya.

## 🗑️ Paso 9 — Eliminar la rama

```bash
git checkout main
git pull origin main
git branch -d feature/1-get-curso-por-id
```

## 🏷️ Paso 10 — Crear tag de versión

```bash
git tag -a v1.1.0 -m "feat: agregar endpoint GET /cursos/:id"
git push origin v1.1.0
```

> **SemVer:** MAYOR.MENOR.PARCHE — funcionalidad nueva compatible → sube MENOR.

## 📦 Paso 11 — Publicar Release

1. Repo → panel derecho → **Releases** → **Draft a new release**
2. **Choose a tag:** `v1.1.0`
3. **Release title** y notas detalladas
4. ✅ **Set as the latest release**
5. **Publish release**

## 🎯 Mapeo SWEBOK → GitHub Flow

| Concepto SWEBOK Cap. 8 | GitHub Flow |
|---|---|
| Elemento de Configuración (2.1.2) | Archivo rastreado en Git |
| Línea Base (2.3) | `main` protegida + tag de versión |
| Sucesión (2.5) | Historial de commits |
| Dependencia (2.5) | `package.json` + lockfile |
| Solicitud de Cambio (3.1) | Issue de GitHub |
| Comité de Control (3.1.1) | Reviewers + branch protection |
| Implementar cambio (3.2) | Merge del PR a `main` |
| Auditoría en proceso (5.3) | Code review en el PR |
| Verificación (5.1) | Status checks / CI |
| Gestión de versiones (6.2) | Tag semántico |
| Biblioteca de software (2.6) | GitHub Releases |

---

## 📊 Evidencia del flujo de Gestión de la Configuración aplicado

Este repositorio fue construido siguiendo el flujo de **GitHub Flow** aplicando los procesos de **Software Configuration Management (SWEBOK Cap. 8)**.

### Iteración v1.1.0 — Endpoint GET por ID

| Etapa | Responsable | Evidencia |
|---|---|---|
| Solicitud de Cambio | @idkferr | [Issue #1](https://github.com/idkferr/cursos-api/issues/1) |
| Implementación | @idkferr | Rama `feature/1-get-curso-por-id` |
| Pull Request | @idkferr | PR con descripción detallada y `Closes #1` |
| Code Review y Aprobación | @Uribe22 | Comentario y aprobación formal en el PR |
| Merge a `main` | @idkferr | Tras aprobación formal de la revisora |
| Versionado | @idkferr | Tag `v1.1.0` (Semantic Versioning) |
| Release | @idkferr | [Release v1.1.0](https://github.com/idkferr/cursos-api/releases) |

### 📌 Nota sobre el ejecutor del merge

En el flujo aplicado, **el merge fue ejecutado por la autora del PR (@idkferr) tras recibir la aprobación formal de la revisora (@Uribe22)**. Esta es una práctica aceptada en muchos equipos ágiles, donde la **aprobación del revisor** constituye la garantía de control de calidad, independientemente de quién ejecute el merge técnico.

El registro de la aprobación queda permanentemente en el historial del Pull Request, cumpliendo con el principio de trazabilidad de **SWEBOK 5.3 (Auditoría en proceso)** y **SWEBOK 3.1.1 (Comité de Control)**: ningún cambio entró a la línea base sin revisión y aprobación de otra persona del equipo.

En contextos donde se requiera estricta separación de roles (autor ≠ ejecutor del merge), GitHub permite configurar la regla de protección de rama con la opción **"Restrict who can push to matching branches"** para que solo los revisores autorizados puedan ejecutar el merge.

---

## 💻 Ejecutar localmente (versión rápida)

```bash
git clone https://github.com/idkferr/cursos-api.git
cd cursos-api
npm install
npm start
```

- API: http://localhost:3000/cursos
- Docs: http://localhost:3000/docs

---

## 🧪 Probar la API con Thunder Client

[Thunder Client](https://www.thunderclient.com) es una extensión de VS Code que funciona como Postman pero dentro del editor.

### Instalación

1. VS Code → `Ctrl + Shift + X`
2. Buscar **Thunder Client** (autor: *Ranga Vadhineni*)
3. Click en **Install**
4. Aparecerá un ícono de rayo ⚡ en la barra lateral

### Cómo usarlo

1. Click en **New Request**
2. Seleccionar método HTTP (GET, POST, PUT, DELETE)
3. Pegar la URL: `http://localhost:3000/cursos`
4. Para POST y PUT: pestaña **Body** → **JSON** → pegar el contenido
5. Click en **Send**

### Body de ejemplo (POST)

```json
{
  "nombre": "Calculo Diferencial",
  "instructor": "Dra. Ana Perez",
  "creditos": 4
}
```

---

## 📁 Estructura del proyecto

```
cursos-api/
├── index.js           # Servidor Express + endpoints + Swagger
├── db.js              # Configuración de SQLite y schema
├── package.json       # Dependencias y scripts
├── package-lock.json  # Versiones exactas de dependencias
├── .gitignore         # node_modules y datos.db excluidos
└── README.md          # Este archivo
```

---

## ☁️ Despliegue continuo

La API se despliega automáticamente en **Render** con cada `push` a la rama `main`. Cada vez que se fusiona un Pull Request, Render detecta el cambio y redeploya en 2-3 minutos.

> ⏳ **Nota sobre Render Free:** el servicio entra en reposo tras 15 min sin actividad. La primera petición tras un periodo de inactividad puede tardar hasta 30 segundos.

---

## 📝 Licencia

Proyecto académico desarrollado en el curso de **Proyectos de Software** — Universidad de Valparaíso.

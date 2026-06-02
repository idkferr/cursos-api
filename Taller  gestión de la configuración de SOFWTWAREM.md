# 🛠️ Taller de Gestión de la Configuración de Software (SCM)

Este repositorio contiene la API de Cursos Académicos basada en Node.js, Express y SQLite, utilizada como proyecto práctico para implementar los procesos formales de **Gestión de la Configuración de Software (SCM)** definidos en el **SWEBOK Capítulo 8**, utilizando el flujo de trabajo **GitHub Flow**.

---

## 📚 Marco Teórico: SWEBOK ↔️ GitHub Flow

En este taller se mapearon los conceptos teóricos de ingeniería de software con las herramientas prácticas de desarrollo:

| Concepto SWEBOK (Cap. 8) | Implementación Práctica (GitHub Flow) | Función en el Proyecto |
| :--- | :--- | :--- |
| **Elemento de Configuración (2.1.2)** | Archivos rastreados por Git (`index.js`, `package.json`, etc.) | Componentes que afectan el comportamiento del sistema y deben controlarse. |
| **Línea Base (2.3)** | Rama `main` protegida + Etiqueta de versión | Estado estable, aprobado y verificado del código del cual parten los cambios. |
| **Sucesión (2.5)** | Historial de commits (`git log`) | Registro histórico ordenado de las diferentes versiones de un archivo. |
| **Dependencia (2.5)** | `package.json` y `package-lock.json` | Relación entre módulos. Asegura que todos tengan las mismas versiones de librerías. |
| **Solicitud de Cambio (3.1)** | **GitHub Issues** | Documentación ágil de la necesidad de una mejora o corrección de un bug. |
| **Comité de Control (3.1.1)** | Reglas de Protección de Ramas + Revisores del PR | Entidad/mecanismo que evalúa, comenta y aprueba la integración de cambios. |
| **Auditoría en Proceso (5.3)** | **Code Review** (Revisión de Código en el PR) | Evaluación detallada del código por parte de otro compañero antes del merge. |
| **Gestión de Versiones (6.2)** | Versión Semántica (**Tags**) + **GitHub Releases** | Identificación formal y empaquetamiento del estado del software para su entrega. |

---

## 🚀 Guía Paso a Paso para Replicar el Taller

Sigue de forma estricta este flujo para proponer, implementar y liberar un nuevo cambio en la API sin alterar directamente la Línea Base (`main`).

### 🛡️ Paso 0: Configurar la Protección de la Línea Base
Antes de programar, el administrador del repositorio debe bloquear los cambios directos en `main`:
1. En GitHub, ve a **Settings** > **Branches**.
2. Presiona **Add branch ruleset**.
3. Nómbralo como `Proteger main` y cambia el estado a **Active**.
4. En *Target branches*, añade la opción **Include default branch**.
5. En *Rules*, marca **Require a pull request before merging** y define **Required approvals: 1**.
6. Guarda los cambios.

---

### 📝 Paso 1: Crear la Solicitud de Cambio (Issue)
Todo cambio o nueva funcionalidad debe estar justificada y documentada.
1. Ve a la pestaña **Issues** en GitHub y haz clic en **New issue**.
2. Define un título descriptivo (Ej: `Agregar endpoint GET /cursos/:id para obtener un curso por ID`).
3. En la descripción detalla: **Objetivo**, **Criterios de Aceptación** y **Evaluación de Impacto** (archivos que se modificarán).
4. En el panel derecho, asígnate el Issue (**Assignees**) y colócale la etiqueta correspondiente (**Labels**, ej: `enhancement`).
5. **Anota el número de tu Issue** (ej: `#1`).

---

### 🌿 Paso 2: Crear una Rama de Trabajo Aislada
Nunca se trabaja directamente sobre `main`. Muévete a una rama local limpia conectada con tu Issue.

```bash
# 1. Asegúrate de estar en main y con el código actualizado
git checkout main
git pull origin main

# 2. Crea y muévete a tu nueva rama descriptiva (tipo/numero-issue-descripcion)
git checkout -b feature/1-get-curso-por-id
bash```
```
### Paso 3: Implementación y Pruebas Locales
Realiza los cambios de código correspondientes en tu editor. Para este ejemplo, se añade el endpoint solicitado junto con su documentación interactiva en el archivo index.js:

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


Levanta el servidor localmente para verificar que todo responda con éxito:

Bash
node index.js
Pruebe en el navegador que http://localhost:3000/cursos/1 devuelva el JSON del curso.

Verifique en http://localhost:3000/docs que la interfaz de Swagger UI renderice correctamente los 5 endpoints.

💾 Paso 4: Registrar la Sucesión (Commit) y Publicar Rama
Haz el commit utilizando Conventional Commits y referencia el número de tu Issue para asegurar la trazabilidad.

Bash
# Agrega el archivo modificado al área de preparación
git add index.js

# Registra el commit enlazando el Issue usando (#N)
git commit -m "feat: agregar endpoint GET /cursos/:id y documentar POST en Swagger (#1)"

# Sube la rama local al repositorio remoto en GitHub
git push origin feature/1-get-curso-por-id
🔀 Paso 5: Abrir el Pull Request (PR)
Ve a tu repositorio en GitHub. Verás un banner amarillo; haz clic en Compare & pull request.

Verifica que la fusión apunte correctamente: base: main ⬅️ compare: feature/1-get-curso-por-id.

En el cuerpo del texto describe los cambios realizados, cómo probarlos e incluye la palabra clave de cierre automático al final: Closes #1 (reemplaza por el número de tu Issue).

En el panel lateral derecho, ve a Reviewers y asigna a tu compañero/a de equipo para que actúe como Comité de Control.

Haz clic en Create pull request.

👀 Paso 6: Auditoría en Proceso (Code Review)
Este paso lo realiza el compañero asignado como Revisor:

Entra al PR en GitHub y dirígete a la pestaña Files changed.

Analiza el código línea por línea. Haz clic en el símbolo + azul en cualquier línea para dejar comentarios sustantivos (sugerencias de optimización, preguntas de formato, etc.).

Al finalizar, haz clic en Review changes en la esquina superior derecha, selecciona la opción Approve y envía la revisión (Submit review).

🏁 Paso 7: Integración a la Línea Base y Cierre
Una vez que el PR está aprobado y libre de conflictos:

El autor del cambio hace clic en el botón verde Merge pull request y confirma la acción (Confirm merge).

Presiona Delete branch directamente en la interfaz de GitHub para mantener limpio el repositorio remoto.

Comprueba que el Issue original se haya movido automáticamente al estado Closed gracias al tag vinculante.

Sincroniza tu entorno local desde la terminal de comandos:

Bash
# Regresa a main y descarga lo que tu compañero aprobó
git checkout main
git pull origin main

# Elimina de forma segura la rama local que ya fue integrada
git branch -d feature/1-get-curso-por-id
🏷️ Paso 8: Versionamiento Semántico y Entrega Formal (Release)
Para finalizar el proceso SCM, se establece una nueva Línea Base de entrega utilizando el estándar de Versionamiento Semántico (MAJOR.MINOR.PATCH). Como se agregó una funcionalidad compatible hacia atrás, se incrementa el dígito Minor:

Bash
# Crea una etiqueta anotada con metadatos del autor y descripción del hito
git tag -a version1.1.0 -m "feat: agregar endpoint GET /cursos/:id"

# Sube la etiqueta creada a GitHub
git push origin version1.1.0
En la página principal de tu repositorio en GitHub, haz clic en Releases (menú de la derecha) > Draft a new release.

Vincula el tag version1.1.0 que acabas de subir.

Define un título representativo (ej: v1.1.0 - Endpoint GET por ID).

Redacta las notas del release detallando las nuevas características incorporadas, compatibilidad y haciendo mención a la revisión exitosa del equipo.

Presiona Publish release.
🤝 Compromisos de Configuración del Equipo
Para garantizar la estabilidad del software y mantener el orden formal a lo largo de todo el ciclo de desarrollo, nos comprometemos a adoptar las siguientes prácticas obligatorias:

La rama main es intocable: Queda estrictamente prohibido realizar git push directo a la rama principal. Todo cambio, por pequeño que sea, se procesa vía PR.

Sin Issue no hay Código: Ningún integrante del equipo puede codificar o abrir una rama sin antes haber documentado y justificado la tarea mediante una Solicitud de Cambio (Issue).

Revisión Humana Cruzada: Ningún Pull Request se auto-aprueba. Siempre se requerirá la firma, revisión honesta y aprobación formal de otro desarrollador del equipo antes de la integración.

Historial Trazable: Se exige el uso estricto de mensajes claros (convención Conventional Commits) enlazando explícitamente el ID del Issue afectado en cada confirmación.

Entregas Etiquetadas: Al cierre de cada hito o funcionalidad clave incorporada con éxito, se generará el Tag semántico y se publicará su respectivo Release con la documentación de soporte.

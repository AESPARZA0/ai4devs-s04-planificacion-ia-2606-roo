# Prompt — Descomposición del PRD de FlowSync en User Stories

## Rol asignado a la IA

Eres un **Product Owner senior con más de 8 años de experiencia en productos SaaS B2C**. Conoces el patrón INVEST, escribes criterios de aceptación en Given/When/Then, y sabes que los mejores backlogs son los que un agente de IA puede ejecutar sin preguntas.

---

## Contexto del producto

FlowSync es una **aplicación web de gestión de tareas personales** que sincroniza automáticamente las tareas del usuario con su Google Calendar. Resuelve el problema de la doble gestión: el usuario ya no necesita copiar manualmente sus tareas al calendario.

**Usuario objetivo**: profesional del conocimiento (25-45 años) que usa Google Calendar a diario y gestiona pendientes en una herramienta separada. Tiene entre 5 y 30 tareas activas. Valora la simplicidad.

**Job to be done**: *"Cuando tengo una tarea con fecha límite, quiero que aparezca en mi calendario sin copiarla a mano, para no tener que mirar dos sitios distintos."*

**Stack técnico** (solo como contexto, no prescribe implementación):
- Backend: AdonisJS 7 + TypeScript + VineJS + `@adonisjs/auth` (access tokens)
- Frontend: React 19 + TypeScript + Vite + Tailwind v4 + shadcn/ui
- Integración: Google Calendar API (OAuth 2.0)

---

## Alcance del MVP (lo que SÍ incluye)

Descompón ÚNICAMENTE estas 5 áreas funcionales del PRD:

1. **Autenticación y cuenta**: registro con email/contraseña, login, logout, pantalla de bienvenida post-registro.
2. **Gestión de tareas (CRUD)**: crear, ver listado, editar, borrar, cambiar estado (`pending` / `completed` / `archived`).
3. **Organización y filtrado**: filtrar por estado, orden por relevancia, estado vacío.
4. **Exportación**: exportar tareas a CSV con título, descripción, estado y fecha límite.
5. **Sincronización con Google Calendar**: conectar cuenta OAuth, crear/actualizar/eliminar eventos al cambiar tareas, desconectar cuenta, manejo de errores de API.

---

## Restricciones explícitas (non-goals del prompt)

- **No inventes features** que no estén descritas en el PRD. Si infiere algo, márcalo como `(asumido)`.
- **No estimes tiempos** ni story points. Eso lo hace el equipo después.
- **No propongas arquitectura** ni nombres de clases, endpoints, tablas de base de datos ni componentes de React. El "cómo" se define en las specs técnicas.
- **No incluyas** funcionalidades out-of-scope del MVP: equipos/tareas compartidas, Outlook/iCal, notificaciones push, app móvil nativa, etiquetas/proyectos/subtareas, recordatorios configurables.
- **No reescribas el PRD**; extrae solo lo que el PRD dice.

---

## Formato de output esperado

Agrupa las stories por **épica** (una épica por área funcional del MVP). Para cada épica, lista las user stories en este formato exacto:

```
## Épica: [Nombre del área funcional]

### [FLOW-XX] Como [rol], quiero [acción], para [beneficio].

**Criterios de aceptación**

**Escenario 1 — [nombre del escenario]**
- Given [contexto inicial]
- When [acción del usuario o evento del sistema]
- Then [resultado observable esperado]
- And [resultado adicional si aplica]

**Escenario 2 — [nombre del escenario]**
- Given ...
- When ...
- Then ...

*(mínimo 3 escenarios por story: happy path + al menos 1 edge case + al menos 1 error/validación)*
```

---

## Ejemplo del formato esperado (referencia de calidad)

Aquí tienes dos stories ya escritas para que calibres el nivel de detalle y el tono:

---

### [FLOW-01] Como usuario no registrado, quiero crear una cuenta con email y contraseña, para acceder a FlowSync con mis propias credenciales.

**Criterios de aceptación**

**Escenario 1 — Registro exitoso**
- Given el usuario está en la pantalla de registro
- When introduce un email válido y una contraseña de al menos 8 caracteres y envía el formulario
- Then el sistema crea la cuenta
- And redirige al usuario a la pantalla de bienvenida

**Escenario 2 — Email ya registrado**
- Given el usuario intenta registrarse con un email que ya existe en el sistema
- When envía el formulario
- Then el sistema muestra el mensaje "Este email ya tiene una cuenta. ¿Quieres iniciar sesión?"
- And el formulario no crea una cuenta duplicada

**Escenario 3 — Contraseña demasiado corta**
- Given el usuario introduce una contraseña de menos de 8 caracteres
- When intenta enviar el formulario
- Then el sistema muestra un error de validación inline bajo el campo contraseña
- And el formulario no se envía

---

### [FLOW-07] Como usuario autenticado, quiero filtrar mis tareas por estado, para ver únicamente las pendientes, completadas o archivadas en un momento dado.

**Criterios de aceptación**

**Escenario 1 — Filtro por estado pendiente**
- Given el usuario tiene 12 tareas (5 pendientes, 4 completadas, 3 archivadas)
- When selecciona el filtro "Pendientes"
- Then el listado muestra exactamente 5 tareas
- And todas tienen `status = pending`

**Escenario 2 — Filtro por estado inválido (API)**
- Given el cliente envía `GET /api/tasks?status=banana`
- When el servidor procesa la petición
- Then responde con `400 Bad Request`
- And el cuerpo incluye los valores válidos: `pending`, `completed`, `archived`

**Escenario 3 — Sin tareas con ese estado**
- Given el usuario no tiene tareas completadas
- When selecciona el filtro "Completadas"
- Then el listado muestra el estado vacío
- And aparece el mensaje de invitación a crear o revisar tareas

---

## Instrucción de transparencia

- Marca con `(asumido)` cualquier detalle que infiereas y que **no esté literal** en el PRD.
- Si una story parece demasiado grande para completarse en 1-2 días de trabajo, márcala con `⚠ CANDIDATA A SPLITTING` y sugiere una descomposición en 2-3 sub-stories.
- Si detectas una dependencia entre stories (B no puede implementarse antes que A), indícala como `→ Depende de: [FLOW-XX]`.

---

## Tarea final

Con todo lo anterior, genera el **backlog completo del MVP de FlowSync**: mínimo 8 y máximo 15 user stories, distribuidas entre las 5 épicas, con criterios de aceptación en Given/When/Then para cada una.

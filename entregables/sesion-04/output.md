# Output — Backlog FlowSync MVP

> Output crudo generado por Claude (claude-sonnet-4-6) ejecutando el prompt de `prompt.md`
> contra el PRD de FlowSync (`docs/PRD.md`). Sin retoques posteriores.

---

## Épica 1: Autenticación y gestión de cuenta

---

### [FLOW-01] Como visitante, quiero crear una cuenta con email y contraseña, para acceder a FlowSync con mis propias credenciales.

**Criterios de aceptación**

**Escenario 1 — Registro exitoso**
- Given el visitante está en la pantalla de registro
- When introduce un email con formato válido y una contraseña de al menos 8 caracteres y envía el formulario
- Then el sistema crea la cuenta
- And redirige al usuario a la pantalla de bienvenida

**Escenario 2 — Email ya registrado**
- Given el visitante introduce un email que ya existe en el sistema
- When envía el formulario
- Then el sistema muestra el mensaje "Este email ya tiene una cuenta. ¿Quieres iniciar sesión?"
- And no crea una cuenta duplicada

**Escenario 3 — Contraseña demasiado corta**
- Given el visitante introduce una contraseña de menos de 8 caracteres
- When intenta enviar el formulario
- Then el sistema muestra un error de validación inline bajo el campo contraseña
- And el formulario no se envía al servidor

**Escenario 4 — Email con formato inválido**
- Given el visitante introduce un valor sin formato de email (ej: "noesuno")
- When intenta enviar el formulario
- Then el sistema muestra un error de validación inline bajo el campo email
- And el formulario no se envía

---

### [FLOW-02] Como usuario registrado, quiero iniciar sesión con mi email y contraseña, para acceder a mis tareas.

**Criterios de aceptación**

**Escenario 1 — Login exitoso**
- Given el usuario tiene una cuenta registrada en FlowSync
- When introduce el email y la contraseña correctos y envía el formulario
- Then el sistema genera un token de acceso válido
- And redirige al usuario al listado de tareas

**Escenario 2 — Credenciales incorrectas**
- Given el usuario introduce una contraseña incorrecta para su email
- When envía el formulario
- Then el sistema muestra "Email o contraseña incorrectos"
- And no genera ningún token de acceso

**Escenario 3 — Email no registrado**
- Given el usuario introduce un email que no existe en el sistema
- When envía el formulario
- Then el sistema muestra "Email o contraseña incorrectos" (sin revelar qué campo es incorrecto)
- And no genera token

---

### [FLOW-03] Como usuario autenticado, quiero cerrar sesión, para que nadie más pueda acceder a mis tareas desde este dispositivo.

**Criterios de aceptación**

**Escenario 1 — Cierre de sesión exitoso**
- Given el usuario está autenticado en FlowSync
- When hace clic en "Cerrar sesión"
- Then el sistema invalida el token de acceso actual
- And redirige al usuario a la pantalla de login

**Escenario 2 — Acceso con token invalidado**
- Given el usuario ha cerrado sesión
- When intenta acceder a una ruta protegida (ej: `/tasks`) directamente
- Then el sistema responde con redirección a login
- And no devuelve ningún dato del usuario

---

### [FLOW-04] Como usuario recién registrado, quiero ver una pantalla de bienvenida que explique qué hace FlowSync, para entender cómo empezar sin ayuda externa.

**Criterios de aceptación**

**Escenario 1 — Pantalla de bienvenida tras registro**
- Given el usuario acaba de completar el registro exitosamente
- When es redirigido a la app por primera vez
- Then ve una pantalla con al menos una frase que explica el propósito de FlowSync
- And un botón o CTA visible que le invita a crear su primera tarea

**Escenario 2 — La bienvenida no bloquea el flujo**
- Given el usuario está en la pantalla de bienvenida
- When hace clic en "Crear mi primera tarea"
- Then llega directamente al formulario de creación de tarea sin pasos intermedios

**Escenario 3 — La bienvenida no reaparece en logins posteriores (asumido)**
- Given el usuario ya pasó por la pantalla de bienvenida en su primer acceso
- When cierra sesión y vuelve a iniciarla
- Then es redirigido directamente al listado de tareas, no a bienvenida

---

## Épica 2: Gestión de tareas (CRUD)

---

### [FLOW-05] Como usuario autenticado, quiero crear una tarea indicando al menos un título, para registrar un pendiente en FlowSync.

**Criterios de aceptación**

**Escenario 1 — Creación con solo título**
- Given el usuario está en el formulario de creación de tarea
- When introduce solo un título y guarda
- Then el sistema crea la tarea con `status=pending`, sin descripción ni fecha límite
- And la tarea aparece en el listado de tareas

**Escenario 2 — Creación con todos los campos**
- Given el usuario introduce título, descripción y fecha límite
- When guarda la tarea
- Then la tarea queda almacenada con todos los campos correctamente
- And aparece en el listado

**Escenario 3 — Título vacío**
- Given el usuario deja el campo título en blanco
- When intenta guardar
- Then el sistema muestra un error de validación en el campo título
- And no crea la tarea

**Escenario 4 — Estado inicial siempre es `pending`**
- Given el usuario crea una tarea con cualquier combinación de campos válidos
- When se guarda
- Then el `status` de la tarea es `pending`, independientemente de si se envió otro valor (asumido)

---

### [FLOW-06] Como usuario autenticado, quiero ver el listado de mis tareas, para tener visibilidad de todos mis pendientes.

**Criterios de aceptación**

**Escenario 1 — Listado con tareas existentes**
- Given el usuario tiene 5 tareas creadas
- When accede al listado
- Then ve las 5 tareas ordenadas por relevancia para hoy (asumido: tareas con fecha límite más próxima aparecen primero)
- And cada tarea muestra al menos: título, estado y fecha límite si existe

**Escenario 2 — Listado vacío**
- Given el usuario no tiene ninguna tarea creada
- When accede al listado
- Then ve un estado vacío con un mensaje que le invita a crear su primera tarea

**Escenario 3 — Aislamiento entre usuarios**
- Given los usuarios A y B tienen tareas propias
- When el usuario A accede a su listado
- Then solo ve sus propias tareas, nunca las del usuario B

**Escenario 4 — Rendimiento con 200 tareas**
- Given el usuario tiene 200 tareas activas
- When accede al listado
- Then la respuesta llega en menos de 1 segundo

---

### [FLOW-07] Como usuario autenticado, quiero editar cualquier campo de una tarea existente, para actualizar su información.

**Criterios de aceptación**

**Escenario 1 — Edición exitosa**
- Given el usuario tiene una tarea con título "Revisar presupuesto"
- When la edita y cambia el título a "Revisar presupuesto Q3" y guarda
- Then el sistema actualiza la tarea
- And el listado muestra el nuevo título

**Escenario 2 — Agregar fecha límite a tarea sin fecha**
- Given la tarea no tenía fecha límite
- When el usuario la edita y agrega una fecha límite
- Then la tarea queda actualizada con la nueva fecha
- And si el usuario tiene Google Calendar conectado, se dispara la sincronización para crear el evento (asumido → depende de FLOW-12)

**Escenario 3 — Titulo vacío al editar**
- Given el usuario elimina el contenido del campo título al editar
- When intenta guardar
- Then el sistema muestra validación de campo requerido
- And no actualiza la tarea

**Escenario 4 — Editar tarea de otro usuario (seguridad)**
- Given el usuario B intenta editar una tarea del usuario A vía `PATCH /api/tasks/:id`
- When envía el request con un token válido de usuario B
- Then el servidor responde `403 Forbidden`
- And la tarea del usuario A no se modifica

---

### [FLOW-08] Como usuario autenticado, quiero borrar una tarea, para eliminar pendientes que ya no son relevantes.

**Criterios de aceptación**

**Escenario 1 — Borrado exitoso**
- Given el usuario tiene una tarea
- When la borra y confirma la acción
- Then la tarea desaparece del listado
- And si tenía un evento en Google Calendar, el evento se elimina (asumido → depende de FLOW-13)

**Escenario 2 — Confirmación antes de borrar (asumido)**
- Given el usuario hace clic en la acción de borrar una tarea
- When aparece un diálogo de confirmación
- Then el borrado solo ocurre si el usuario confirma explícitamente

**Escenario 3 — Borrar tarea ajena (seguridad)**
- Given el usuario B intenta borrar una tarea del usuario A vía `DELETE /api/tasks/:id`
- When envía el request con token válido de usuario B
- Then el servidor responde `403 Forbidden`
- And la tarea del usuario A no se elimina

---

### [FLOW-09] Como usuario autenticado, quiero cambiar el estado de una tarea, para reflejar su progreso.

**Criterios de aceptación**

**Escenario 1 — Marcar tarea como completada**
- Given el usuario tiene una tarea con `status=pending`
- When la marca como completada
- Then el sistema actualiza `status=completed`
- And si tenía evento en Google Calendar, el evento se elimina o marca según corresponda (asumido → depende de FLOW-13)

**Escenario 2 — Archivar tarea**
- Given el usuario tiene una tarea con cualquier estado
- When la archiva
- Then el sistema actualiza `status=archived`

**Escenario 3 — Estado inválido vía API**
- Given el cliente envía `PATCH /api/tasks/:id` con `status="banana"`
- When el servidor procesa la petición
- Then responde `400 Bad Request`
- And el cuerpo del error incluye los valores válidos: `pending`, `completed`, `archived`

---

## Épica 3: Organización y filtrado

---

### [FLOW-10] Como usuario autenticado, quiero filtrar mis tareas por estado, para ver únicamente las pendientes, completadas o archivadas en un momento dado.

**Criterios de aceptación**

**Escenario 1 — Filtro por estado `pending`**
- Given el usuario tiene 12 tareas: 5 pendientes, 4 completadas, 3 archivadas
- When selecciona el filtro "Pendientes"
- Then el listado muestra exactamente 5 tareas
- And todas tienen `status=pending`

**Escenario 2 — Filtro sin resultados**
- Given el usuario no tiene tareas completadas
- When selecciona el filtro "Completadas"
- Then el listado muestra el estado vacío con un mensaje apropiado

**Escenario 3 — Filtro inválido vía API**
- Given el cliente envía `GET /api/tasks?status=banana`
- When el servidor procesa la petición
- Then responde `400 Bad Request`
- And el cuerpo incluye los valores válidos: `pending`, `completed`, `archived`

**Escenario 4 — Vista sin filtro activo (por defecto)**
- Given el usuario accede al listado sin aplicar filtro
- When carga la vista
- Then ve todas sus tareas (todos los estados) ordenadas por relevancia para hoy

---

## Épica 4: Exportación

---

### [FLOW-11] Como usuario autenticado, quiero exportar mis tareas a un archivo CSV, para llevarme mis datos o analizarlos fuera de FlowSync.

**Criterios de aceptación**

**Escenario 1 — Exportación exitosa con tareas**
- Given el usuario tiene 8 tareas de distintos estados
- When solicita exportar a CSV
- Then el navegador descarga un archivo con extensión `.csv`
- And el archivo contiene una fila por tarea con al menos: título, descripción, estado y fecha límite

**Escenario 2 — Encabezados del CSV**
- Given el usuario exporta sus tareas
- When abre el archivo en un editor o hoja de cálculo
- Then la primera fila contiene los nombres de columnas en texto legible (asumido: en español o inglés, no IDs de base de datos)

**Escenario 3 — Exportación con lista vacía**
- Given el usuario no tiene ninguna tarea
- When solicita exportar
- Then el sistema genera un archivo CSV solo con la fila de encabezados, sin filas de datos

**Escenario 4 — Las tareas exportadas son solo del usuario autenticado**
- Given hay múltiples usuarios en el sistema
- When un usuario exporta
- Then el CSV contiene únicamente sus propias tareas

---

## Épica 5: Sincronización con Google Calendar

> ⚠ **CANDIDATA A SPLITTING** — Esta épica contiene la funcionalidad de mayor riesgo técnico del MVP. Se recomienda un spike antes de comprometer las stories de sincronización inversa. Las stories [FLOW-13] y [FLOW-14] son candidatas a desglosarse en sub-stories tras el spike.

---

### [FLOW-12] Como usuario autenticado, quiero conectar mi cuenta de Google a FlowSync mediante OAuth, para habilitar la sincronización con mi Google Calendar.

**Criterios de aceptación**

**Escenario 1 — Conexión OAuth exitosa**
- Given el usuario no tiene cuenta de Google conectada
- When inicia el flujo de conexión y autoriza en la pantalla de Google
- Then FlowSync almacena el token de acceso de forma segura (no expuesto en respuestas de API al cliente)
- And el usuario ve confirmación de que su Google Calendar está conectado con su email de Google visible

**Escenario 2 — Usuario cancela la autorización en Google**
- Given el usuario inicia el flujo OAuth
- When cancela en la pantalla de Google sin autorizar
- Then FlowSync muestra un mensaje informativo sin considerar esto un error crítico
- And no almacena ningún token
- And el usuario puede reintentar la conexión

**Escenario 3 — Cuenta ya conectada**
- Given el usuario ya tiene una cuenta de Google conectada
- When accede a la sección de integraciones
- Then ve el estado "Conectado" con el email de Google asociado
- And tiene la opción visible de desconectar su cuenta

**Escenario 4 — Token seguro y aislado**
- Given el sistema almacena el token OAuth del usuario A
- When otro usuario intenta acceder a ese token vía API
- Then el sistema devuelve `403 Forbidden`

---

### [FLOW-13] Como usuario autenticado con Google Calendar conectado, quiero que mis tareas con fecha límite se reflejen como eventos en mi calendario, para verlos sin salir de Google Calendar.

→ Depende de: FLOW-12

> ⚠ **CANDIDATA A SPLITTING** — Esta story cubre creación, actualización y eliminación de eventos. Se puede dividir en tres sub-stories si el equipo lo considera demasiado amplio para un sprint.

**Criterios de aceptación**

**Escenario 1 — Crear tarea con fecha crea evento**
- Given el usuario tiene Google Calendar conectado
- When crea una tarea con fecha límite
- Then FlowSync crea un evento en Google Calendar con el título de la tarea
- And la fecha del evento corresponde a la fecha límite (asumido: hora por definir en spike de zonas horarias)

**Escenario 2 — Crear tarea sin fecha no crea evento**
- Given el usuario tiene Google Calendar conectado
- When crea una tarea sin fecha límite
- Then no se crea ningún evento en Google Calendar

**Escenario 3 — Cambiar fecha actualiza evento**
- Given el usuario tiene una tarea con evento en Google Calendar
- When edita la fecha límite de la tarea
- Then el evento en Google Calendar se actualiza a la nueva fecha

**Escenario 4 — Completar o borrar tarea elimina evento**
- Given el usuario tiene una tarea con un evento en Google Calendar
- When completa o borra la tarea
- Then el evento correspondiente se elimina de Google Calendar

**Escenario 5 — Fallo de la API de Google**
- Given la API de Google no está disponible al momento de sincronizar
- When el usuario crea, edita o completa una tarea
- Then la operación en FlowSync se guarda exitosamente de todas formas
- And el sistema registra el fallo en los logs de sincronización
- And reintenta la sincronización más tarde (asumido: mecanismo de retry a definir en spike técnico)

---

### [FLOW-14] Como usuario autenticado con Google Calendar conectado, quiero poder desconectar mi cuenta de Google, para detener la sincronización sin perder mis tareas en FlowSync.

→ Depende de: FLOW-12

**Criterios de aceptación**

**Escenario 1 — Desconexión exitosa**
- Given el usuario tiene Google Calendar conectado
- When hace clic en "Desconectar Google Calendar" y confirma la acción
- Then FlowSync revoca y elimina el token OAuth almacenado
- And el estado de integración cambia a "No conectado"
- And todas las tareas existentes en FlowSync se mantienen intactas

**Escenario 2 — La sincronización se detiene tras desconectar**
- Given el usuario ha desconectado su Google Calendar
- When crea o edita una tarea con fecha límite
- Then FlowSync no intenta ninguna llamada a la API de Google
- And el usuario no ve ningún mensaje de error relacionado con el calendario

**Escenario 3 — Los eventos previos en Google no se borran al desconectar (asumido)**
- Given el usuario tenía eventos creados por FlowSync en su Google Calendar
- When desconecta su cuenta de Google
- Then esos eventos permanecen en Google Calendar tal como estaban
- And FlowSync no envía ninguna petición de borrado al desconectar

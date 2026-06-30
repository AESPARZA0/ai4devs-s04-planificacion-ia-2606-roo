# Poke-holes — FLOW-13

> Story analizada: **[FLOW-13] Sincronización de tareas con eventos de Google Calendar**
> Patrón aplicado: "AI as poke-holes" — buscar gaps, supuestos implícitos,
> escenarios faltantes y dependencias no mencionadas. No se reescribió la story.

---

## Hallazgo 1 — ¿Qué pasa con las tareas existentes cuando el usuario conecta Google Calendar por primera vez?

**Tipo**: escenario faltante  
**Dónde**: entre FLOW-12 (conectar Google) y FLOW-13 (sincronizar tareas)

FLOW-13 cubre el caso "el usuario crea una tarea con fecha límite y Google ya está conectado". Pero no existe ningún escenario que cubra esto:

> El usuario tiene 10 tareas con fecha límite creadas *antes* de conectar su cuenta de Google. Cuando conecta el calendario, ¿FlowSync retroactivamente crea 10 eventos? ¿O solo sincroniza las tareas creadas/editadas a partir de ese momento?

Las dos respuestas son válidas desde producto, pero tienen consecuencias técnicas distintas (un job de backfill vs. sincronización solo hacia adelante). Si el equipo no decide esto explícitamente, el agente va a elegir una de las dos sin avisar.

---

## Hallazgo 2 — El estado `archived` no está cubierto

**Tipo**: gap en lógica de negocio  
**Dónde**: Escenario 4 de FLOW-13

El escenario 4 dice: *"Si el usuario completa o borra la tarea, el evento se elimina."*

Pero el sistema tiene tres estados: `pending`, `completed`, **`archived`**. Archivar una tarea no es borrarla ni completarla; es un estado propio. La story no dice qué debe pasar con el evento en Google Calendar cuando una tarea pasa a `archived`.

Opciones no decididas: ¿se elimina el evento? ¿se mantiene? ¿se mueve a otro calendario? Cualquiera de las tres es razonable; ninguna está en los AC.

---

## Hallazgo 3 — El usuario puede borrar el evento en Google Calendar manualmente

**Tipo**: edge case de sistema externo  
**Dónde**: Escenarios 3 y 4 de FLOW-13

Los AC asumen que el evento en Google Calendar siempre existe cuando FlowSync intenta actualizarlo o borrarlo. Pero el usuario puede entrar a Google Calendar y borrar el evento a mano. Cuando FlowSync luego intente actualizar la fecha o borrar el evento tras completar la tarea, la API de Google devolverá `404 Not Found`.

El AC dice "el evento se actualiza / se elimina", pero no cubre el caso en que Google responda que ese evento ya no existe. ¿Error silencioso? ¿Notificación al usuario? ¿Intento de recrearlo?

---

## Hallazgo 4 — La fecha límite no tiene hora: ¿all-day event o evento con hora por defecto?

**Tipo**: supuesto implícito crítico de UX  
**Dónde**: Escenario 1 de FLOW-13, marcado como `(asumido)`

El campo `fecha límite` de una tarea en el PRD es solo una fecha (sin hora). Un evento de Google Calendar puede ser:
- **All-day event**: aparece como bloque de día completo, sin hora específica.
- **Timed event**: aparece a una hora concreta (ej: 09:00).

La story lo marca como "asumido: hora por definir en spike", lo que es correcto, pero tiene consecuencia directa en el UX: si se usa hora por defecto (ej: 00:00 o 09:00), el usuario ve el evento en un horario que él nunca eligió. Si se usa all-day event, el título y la posición en el calendario son distintos. Esto debe decidirse antes de implementar el escenario 1, no durante.

---

## Hallazgo 5 — Cambio de título de la tarea no está cubierto como trigger de actualización

**Tipo**: escenario faltante  
**Dónde**: Escenario 3 de FLOW-13

El escenario 3 dice: *"Si el usuario cambia la fecha límite de la tarea, el evento en Google Calendar se actualiza."*

Pero no hay ningún escenario que cubra: *"Si el usuario cambia el título de la tarea, ¿se actualiza el título del evento en Google Calendar?"*

Si el evento se crea con el título de la tarea (escenario 1), y el usuario luego renombra la tarea, las dos fuentes quedan desincronizadas: el evento sigue teniendo el título antiguo mientras que la tarea tiene el nuevo. Esto puede ser confuso precisamente en el caso de uso principal del producto (el usuario mira Google Calendar y ve un evento que ya no corresponde a cómo llama a esa tarea).

# Reflexión — Ejercicio pre-sesión S4

## ¿Qué me sorprendió del output de la IA?

La cantidad y consistencia del output fue mayor de lo esperado: 14 stories con 55 escenarios Given/When/Then en una sola ejecución. Lo que más llamó la atención fue que el modelo respetó casi sin desviaciones las restricciones del prompt (no inventó features out-of-scope, marcó los supuestos con `(asumido)` y señaló dependencias entre stories). La homogeneidad del formato fue total, lo cual —como advierte el material— es en sí misma una señal de alerta: todas las stories tenían el mismo peso visual aunque la complejidad real era muy distinta.

## ¿Qué falló o tuve que corregir?

El modelo no cubrió el estado `archived` en los escenarios de sincronización con Google Calendar (FLOW-13), a pesar de que ese estado sí está definido en el PRD y aparece en las stories de CRUD. Tampoco planteó el escenario de backfill al conectar Google por primera vez. Ambos gaps eran previsibles leyendo el PRD con atención, pero pasaron desapercibidos en el output porque el formato uniforme de los AC daba sensación de exhaustividad. Es el efecto "false completeness" del que habla el material.

## ¿El patrón poke-holes encontró algo que yo no había visto?

Sí. El hallazgo más útil fue el #3: que el usuario puede borrar manualmente un evento en Google Calendar, dejando a FlowSync con una referencia al evento que ya no existe. Ese escenario no estaba en el PRD, no estaba en los AC del output, y es exactamente el tipo de fallo que aparece en producción y no en los tests. El patrón forzó pensar en el sistema externo como un actor que puede cambiar estado de forma independiente, no solo como un receptor pasivo de llamadas.

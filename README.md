# Ejercicio pre-sesión · Sesión 4 — Planificación efectiva con IA

## Objetivo

Llegar a la sesión con un backlog inicial de FlowSync ya generado: convertir el PRD del proyecto en un conjunto de user stories con criterios de aceptación, usando IA como herramienta — pero **diseñando tú el prompt** y **revisando críticamente la salida**.
---

## Prerrequisitos

- [ ] Haber consumido el material asíncrono de la S4, en especial la página 2 (anatomía de un backlog AI-ready) y el patrón "AI as poke-holes".
- [ ] Tener **[`docs/PRD.md`](docs/PRD.md)** de FlowSync accesible. Sin el PRD no puedes empezar; es prerrequisito.
- [ ] Tener acceso a un **copiloto de IA** de tu elección (Claude Code recomendado, es lo que usaremos en la demo; Cursor, Claude.ai web, ChatGPT o Gemini también sirven — el ejercicio es agnóstico al modelo).

---

## El ejercicio

### Parte 1 — Diseña tu prompt de descomposición (15-20 min)

Escribe un prompt para pedirle a una IA que extraiga user stories del PRD de FlowSync. El prompt debe cumplir, **al menos**, estos cinco requisitos:

1. **Contexto**: dar a la IA la información necesaria para entender qué es FlowSync, para quién es, y cuál es el alcance del MVP.
2. **Formato exacto**: las stories deben venir en el formato `Como [rol], quiero [acción], para [beneficio]`.
3. **Alcance acotado**: solo funcionalidades del MVP (la sección correspondiente del PRD); sin inventar features que no estén en el documento.
4. **AC en Given/When/Then**: cada story con 3-5 criterios de aceptación verificables, no genéricos.
5. **Agrupación**: las stories agrupadas por módulo, caso de uso o épica, según tenga más sentido para FlowSync.

Te recomiendo que tu prompt incluya:

- ✅ Una sección de "rol" para la IA (eres un Product Owner senior con experiencia en aplicaciones SaaS).
- ✅ Restricciones explícitas (non-goals: no inventar features, no estimar tiempos, no proponer arquitectura).
- ✅ Un ejemplo del formato de output esperado (1-2 stories ya escritas a mano sirven).
- ✅ Una instrucción de "marca con `(asumido)` lo que infieras y no esté literal en el PRD".

> 💡 **Pista (no es obligación)**: el patrón **estructura → restricciones → ejemplo → instrucción de transparencia** suele dar resultados notablemente mejores que prompts narrativos sueltos. Lo viste aplicado en la página 2 del asíncrono.

### Parte 2 — Ejecuta el prompt (5-10 min)

Lanza el prompt contra el PRD usando el copiloto que prefieras. Guarda el **output crudo**, exactamente como te lo devuelve la IA. No retoques nada todavía — el output sin pulir es parte de lo que analizaremos.

### Parte 3 — Aplica el patrón "AI as poke-holes" (10-15 min)

Esta es la parte más valiosa, y la que casi nadie hace cuando trabaja con IA por primera vez.

Toma **una de las stories que generó la IA** (la que veas con AC más completos a primera vista) y pídele explícitamente que la critique:

```
Aquí tienes esta user story con sus criterios de aceptación:
[pega la story aquí]

Tu tarea: identifica edge cases, supuestos implícitos,
escenarios faltantes, y dependencias o riesgos no
mencionados. No reescribas la story, solo lista lo
que falta o lo que asumiste.
```

Anota **3-5 hallazgos genuinos** (descarta el ruido). Estos son los que vamos a comentar en la sesión en vivo.

### Parte 4 — Reflexión breve (5 min)

Cierra con 4-6 líneas de reflexión honesta:

- ¿Qué te sorprendió del output de la IA?
- ¿Qué falló o tuviste que corregir?
- ¿El patrón poke-holes te encontró algo que tú no habías visto?

Respuestas honestas y específicas valen más que respuestas elaboradas y genéricas.

---

## Qué entregar

Sube a tu rama `alumno/<nombre-apellido>` del repo del programa una carpeta `entregables/sesion-04/` con:

```
entregables/sesion-04/
├── prompt.md         # Tu prompt completo
├── output.md         # El output crudo de la IA (sin retocar)
├── poke-holes.md     # Los 3-5 hallazgos de la Parte 3
└── reflexion.md      # 4-6 líneas de la Parte 4
```

> 📌 **No hace falta perfección.** Lo importante es el proceso, no la pulcritud. La sesión en vivo se basa en lo que aprendiste haciéndolo, incluyendo lo que falló.

---

## Criterio de completitud

Sabes que terminaste cuando:

- [ ] Tienes un `prompt.md` que cubre los 5 requisitos de la Parte 1.
- [ ] Tienes `output.md` con al menos 8-12 user stories generadas, agrupadas, con AC en Given/When/Then.
- [ ] Tienes `poke-holes.md` con 3-5 hallazgos no triviales (gaps, supuestos, edge cases reales).
- [ ] Tienes `reflexion.md` con tu reflexión honesta.
- [ ] Has commiteado y pusheado a tu rama `alumno/<tu-nombre>` con el directorio `entregables/sesion-04/`.

---

## Pasos para entregar mediante Pull Request

Template del ejercicio: `https://github.com/LIDR-academy/ai4devs-s04-planificacion-ia`

1. Haz un **fork** del repositorio usando el botón ubicado arriba a la derecha (o trabaja sobre tu rama `alumno/<nombre-apellido>` si la cohorte usa el repo compartido).
2. Clona tu fork en tu computadora.
3. Completa el ejercicio dentro de `entregables/sesion-04/`:
   - Añade `prompt.md`, `output.md`, `poke-holes.md` y `reflexion.md`.
4. Crea una rama para tu solución:

```bash
git checkout -b alumno/nombre-apellido
```

5. Haz commit de tus cambios:

```bash
git add entregables/sesion-04/
git commit -m "S4: backlog inicial FlowSync + poke-holes"
```

6. Sube tu rama:

```bash
git push origin alumno/nombre-apellido
```

7. Crea el Pull Request hacia el repositorio original. Esa será tu entrega final.

Si tu output está en una herramienta externa, asegúrate de que tu TA tenga acceso de lectura al enlace que envíes.

---

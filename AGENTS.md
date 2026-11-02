# AGENTS.md — Instrucciones para agentes

Este archivo es un punto de entrada, no una fuente de verdad. No repite las reglas de la Constitución.

## Antes de empezar

1. Lee `constitution.md` completo.
2. Lee `specs/001-mvp/spec.md`, `specs/001-mvp/plan.md` y la tarea actual en `specs/001-mvp/tasks.md`.
3. Identifica el id de tarea (T0–T9) y su rama `task/001-Tx-...`.

## Reglas de trabajo

- Una tarea a la vez. No mezcles tareas ni adelantes la siguiente.
- Si hay conflicto, manda `constitution.md`.
- Si el spec, el plan o la tarea no coinciden con la realidad del código o de las APIs, **detente** y propón un cambio de spec (issue `type:spec-change`). No lo apliques por tu cuenta.
- No cambies la arquitectura, las dependencias permitidas ni los criterios de aceptación sin aprobación.
- Tests primero. No modifiques tests ya aprobados sin autorización explícita.
- Nada de secretos, llaves, tokens ni contraseñas reales en el repositorio, en logs ni en mensajes de error.
- Ninguna dependencia nueva fuera de las listadas en `plan.md`.
- No inventes APIs, flags ni librerías: si no existen en la versión fijada, detente y repórtalo.

## Entrega

- Un pull request por tarea hacia la rama del spec.
- Incluye el reporte `/verify`: archivos tocados, comandos ejecutados, resultados y desvíos.
- Si tocas criptografía o capabilities, pide la segunda revisión.

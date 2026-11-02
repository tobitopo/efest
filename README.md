# Efest

Administrador de contraseñas **local**: sin nube, sin suscripción y sin telemetría. La bóveda vive en tu equipo, cifrada, y la aplicación no hace peticiones de red.

> **Estado: pre-alfa.** Todavía no hay código ni auditoría. **No uses contraseñas reales** ni datos que te importen.

## Stack previsto

- Rust + [Tauri](https://tauri.app/) v2
- Svelte 5 + TypeScript + Vite
- Criptografía con `dryoc` (Argon2id + XChaCha20-Poly1305), detrás de `efest-core::crypto`

## Documentación

| Documento | Contenido |
|---|---|
| [constitution.md](constitution.md) | Reglas no negociables del proyecto (fuente de verdad) |
| [specs/001-mvp/spec.md](specs/001-mvp/spec.md) | Qué hace el MVP y sus criterios de aceptación |
| [specs/001-mvp/plan.md](specs/001-mvp/plan.md) | Cómo se construye: formato, contrato IPC y dependencias |
| [specs/001-mvp/tasks.md](specs/001-mvp/tasks.md) | Tareas T0–T9 |
| [CONTRIBUTING.md](CONTRIBUTING.md) | Ramas, commits, PRs y revisiones |
| [SECURITY.md](SECURITY.md) | Cómo reportar vulnerabilidades |

## Cómo trabajamos

Una rama por spec (`spec/001-mvp`), una tarea a la vez y un pull request por tarea. `main` solo recibe cambios por PR y con revisión. La criptografía y las capabilities de Tauri requieren una segunda revisión.

Este repositorio es la fuente de verdad. Si el código y el spec no coinciden, se detiene el trabajo y se propone un cambio de spec.

## Licencia

[GPL-3.0-or-later](LICENSE).

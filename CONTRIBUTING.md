# Cómo contribuir

## Reglas de oro

1. Lee [constitution.md](constitution.md) antes de tocar nada. Es la fuente de verdad.
2. Trabaja en **una tarea a la vez** de [specs/001-mvp/tasks.md](specs/001-mvp/tasks.md).
3. No inventes APIs, flags ni librerías: si no existen en la versión fijada, detente y repórtalo.
4. Ningún secreto, llave, token ni contraseña real en el repositorio, en los logs ni en los mensajes de error.

## Ramas

- `main` está protegida: solo se actualiza por pull request, con revisión.
- `spec/NNN-nombre` es la rama de trabajo de un spec. Para este MVP: `spec/001-mvp`.
- `task/NNN-Tx-descripcion` sale de la rama del spec y su pull request va **hacia esa misma rama**, no hacia `main`. Ejemplo: `task/001-T1-key-derivation`.
- Cuando el spec está aceptado, se abre un PR de `spec/...` a `main`.
- No se permite `git push --force`, reescribir historial ni borrar ramas con trabajo sin fusionar.

## Commits

Usa [Conventional Commits](https://www.conventionalcommits.org/) e incluye el id de tarea:

```text
feat(core): T1 key derivation
test(core): T2 authenticated encryption vectors
docs: T9 update spec change log
```

Tipos habituales: `feat`, `fix`, `test`, `docs`, `refactor`, `chore`, `ci`. Un commit debe poder explicarse en una frase.

## Pull requests

- Un pull request por tarea, con la plantilla del repositorio.
- Título con el id: `T1 — Derivación de clave`.
- Incluye el **reporte `/verify`**: archivos tocados, comandos ejecutados, resultados y desvíos respecto del spec.
- El merge es por **squash**. La rama se elimina automáticamente al fusionar.
- No se fusiona un PR con conversaciones sin resolver ni con el check `repo-sanity` en rojo.

## Revisiones

- `main` y las ramas `spec/**` requieren **1 aprobación de una persona distinta del autor** y revisión de code owners.
- **Segunda revisión obligatoria** para cambios en `crates/efest-core/src/crypto/` y `src-tauri/capabilities/`. Quien implementa no aprueba su propio cambio.
- Los tests criptográficos y sus vectores los escribe o revisa el verificador. **El implementador no modifica tests aprobados**; si hace falta un cambio, se aprueba explícitamente en el PR.

## Cambios de spec

Si el spec no coincide con la realidad, **no lo cambies por tu cuenta**. Abre un issue con la plantilla **Cambio de spec** (`type:spec-change`): qué choca, propuesta e impacto. El cambio se aprueba y se registra en el apartado «Registro de cambios del spec» antes de implementarlo.

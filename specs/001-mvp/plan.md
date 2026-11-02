# Plan 001 — Cómo se construye

> Fuente de verdad una vez dentro del repositorio. Copia de la Documentación Maestra v1.0, §7 (y ADR-001 de §4.3).

## ADR-001 — Biblioteca criptográfica

- **Decisión:** `dryoc` 1.x, implementación en Rust puro compatible con libsodium, detrás de `efest-core::crypto`.
- **Primitivas:** Argon2id (perfil MODERATE de libsodium: 3 pasadas y 256 MiB, guardado en la cabecera) y XChaCha20-Poly1305 como cifrado autenticado, con la cabecera completa como datos asociados. Si dryoc no expone ese AEAD, se usa su `secretstream` XChaCha20-Poly1305 con la cabecera como dato asociado. `secretbox` (XSalsa20-Poly1305) solo si la cabecera se duplica dentro de la carga cifrada y se compara al descifrar.
- **Riesgo:** la documentación de dryoc indica que no ha tenido auditoría de terceros, y su API cambió entre 0.x y 1.0 (los ejemplos antiguos no sirven). Mitigaciones: vectores de prueba generados de forma independiente con libsodium real, versión fijada, y el resto del código solo ve nuestra interfaz.
- **Gatillo de revisión:** los vectores no coinciden, el AEAD necesario no existe, o aparece una auditoría relevante.

## Formato del archivo `efest.vault` (v1)

Cabecera de 60 bytes, enteros en big-endian. Toda la cabecera se usa como datos asociados del cifrado.

| Campo | Bytes | Valor |
|---|---|---|
| Firma | 4 | `EFST` |
| Versión del formato | 2 | 1 |
| Identificador del KDF | 1 | 1 = Argon2id |
| Pasadas (`ops_limit`) | 4 | 3 |
| Memoria en KiB | 4 | 262144 |
| Salt | 16 | aleatorio |
| Identificador del cifrado | 1 | 1 = XChaCha20-Poly1305 |
| Nonce | 24 | aleatorio por cada escritura |
| Longitud del cifrado | 4 | máximo 64 MiB |

Luego va la carga cifrada (contenido más etiqueta de 16 bytes). Longitud mínima válida: 76 bytes.

Contenido descifrado (JSON v1): `{"schema":1,"credentials":[{"id","title","username","password","created_at","updated_at"}]}`. Los campos futuros se añaden como opcionales con valor por defecto. El `id` es un UUID v4 y las fechas son marcas Unix.

**Límites de parámetros aceptados al abrir:** pasadas entre 2 y 10, memoria entre 64 MiB y 1 GiB.

## Dependencias permitidas

- **Rust:** `tauri` 2.x, `dryoc` 1.x (requiere una versión de Rust reciente; fijarla en `rust-toolchain.toml`), `zeroize`, `serde`, `serde_json`, `uuid`, `thiserror`, `unicode-normalization`. De desarrollo: `tempfile`, `proptest`.
- **Frontend:** `svelte` 5, `vite`, `typescript`, `tailwindcss`, `lucide-svelte`, `@tauri-apps/api`, `vitest`, `@testing-library/svelte`, `eslint` con el plugin de Svelte.
- Cualquier otra requiere aprobación.

## Contrato IPC

| Comando | Entrada | Salida | Errores |
|---|---|---|---|
| `vault_exists` | — | booleano | `IO_ERROR` |
| `create_vault` | `masterPassword` | — | `INVALID_INPUT`, `VAULT_EXISTS`, `IO_ERROR` |
| `unlock_vault` | `masterPassword` | lista de `CredentialSummary` | `UNLOCK_FAILED`, `UNSUPPORTED_FORMAT`, `VAULT_NOT_FOUND`, `IO_ERROR` |
| `add_credential` | título, usuario, contraseña | `CredentialSummary` | `VAULT_LOCKED`, `INVALID_INPUT`, `LIMIT_REACHED`, `IO_ERROR` |
| `reveal_password` | `id` | texto | `VAULT_LOCKED`, `NOT_FOUND` |
| `lock_vault` | — | — | ninguno (idempotente) |

`CredentialSummary` = `{ id, title, username }`. Los errores se serializan como `{ code, message }` y los mensajes nunca contienen secretos. Los comandos pesados se ejecutan fuera del hilo de la interfaz.

## Seguridad de la capa Tauri

- Capabilities mínimas: ninguna de archivos, shell, HTTP ni portapapeles para el frontend.
- CSP restrictiva sin contenido remoto; los valores exactos del canal IPC se validan en T7 contra la documentación de la versión instalada.
- Herramientas de desarrollo deshabilitadas en las compilaciones de producción.

## Frontend

- Pantallas: `SETUP`, `UNLOCK` y `VAULT` (variable única de estado). Al iniciar, siempre `UNLOCK` o `SETUP`.
- El store solo contiene `CredentialSummary`. El secreto revelado vive en el estado local del componente y se descarta al soltar el botón y al bloquear.
- `lock()` ejecuta, en orden: `lock_vault`, purga del store, descarte de cualquier secreto local y vuelta a `UNLOCK`.
- Componente `PasswordField`: oculto por defecto, se revela mientras se mantiene presionado y vuelve a ocultarse al soltar o al salir del botón.

## Estructura del repositorio

Workspace de Cargo con `crates/efest-core` (criptografía, formato, modelo y almacenamiento, sin Tauri), `src-tauri` (capa delgada de comandos) y `src` (Svelte). Ver §4.2 de la Documentación Maestra.

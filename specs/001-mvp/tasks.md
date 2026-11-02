# Tareas del Spec 001

> Fuente de verdad una vez dentro del repositorio. Copia de la Documentación Maestra v1.0, §8.

Cada tarea tiene sus pruebas escritas antes del código. Los tests criptográficos y sus vectores los escribe o revisa el verificador o una persona; el implementador no los modifica una vez aprobados. Ninguna tarea se da por terminada sin el reporte de `/verify` (agente `verifier`, solo lectura, modelo de otro proveedor). Un pull request por tarea. Contexto de cada sesión: `constitution.md` + `spec.md` + `plan.md` + la tarea actual.

**Plantilla de reporte del agente:** archivos tocados, comandos ejecutados, resultados, dudas o desvíos respecto del spec (si los hay, detenerse y proponer un cambio al spec).

**T0 — Andamiaje.** Workspace de Cargo, `efest-core` con un test trivial, plantilla Tauri v2 con Svelte y TypeScript, pnpm, `rust-toolchain.toml`, `#![forbid(unsafe_code)]`, `scripts/verify.sh` y `scripts/check-forbidden.sh`, CI mínimo, `CODEOWNERS` y `.opencode/agents/verifier.md`. *Verificación:* `./scripts/verify.sh` en verde en CI (Linux y Windows).

**T1 — Derivación de clave.** `derive_key(password, salt, params)` con Argon2id y el tipo `Key` con zeroize. *Tests primero:* vector conocido generado con libsodium real (script en `tools/vectors/`), determinismo, contraseñas distintas dan claves distintas, salts distintos dan claves distintas, longitud de 32 bytes, parámetros fuera de límites rechazados, `Debug` sin el secreto.

**T2 — Cifrado autenticado.** `seal` y `open` con la cabecera como datos asociados. Primero se confirma qué expone dryoc 1.x y se reporta (ADR-001). *Tests primero:* ida y vuelta, clave incorrecta falla, alterar cualquier byte de cabecera, nonce o carga falla, dos cifrados del mismo texto difieren, vector conocido generado con libsodium.

**T3 — Formato del archivo.** Codificar y decodificar la cabecera de 60 bytes y la carga. *Tests primero:* ida y vuelta, firma errónea, versión desconocida, truncado en cada posición posible, longitud declarada excesiva, parámetros fuera de límites. Objetivo de `cargo-fuzz` sobre el decodificador.

**T4 — Modelo y serialización.** `Credential`, `VaultData`, `CredentialSummary` y errores tipados. *Tests primero:* ida y vuelta con serde, campos desconocidos tolerados, `Debug` sin secretos, rechazo de campos obligatorios vacíos.

**T5 — Almacenamiento.** Trait `VaultStorage` y `FileStorage` con escritura atómica (archivo temporal, sincronización, renombrado) y respaldo `.bak`. *Tests primero (con directorios temporales):* crear falla si existe, un temporal huérfano no corrompe el archivo vigente, el respaldo se conserva, permisos restrictivos en Linux.

**T6 — Sesión de bóveda.** Estados bloqueada y desbloqueada, con crear, desbloquear, agregar, revelar y bloquear. La contraseña maestra se normaliza a NFKC y se exige un mínimo de 12 caracteres. La derivación corre en un hilo aparte. *Tests primero:* error genérico ante fallos de autenticación, operaciones sin desbloquear fallan, bloquear destruye clave y datos, segunda credencial rechazada, el archivo no contiene texto en claro (AC1).

**T7 — Capa Tauri.** Los seis comandos del contrato IPC, estado compartido, capabilities y CSP mínimas. *Verificación:* tests de la lógica de comandos con estado simulado, revisión de capabilities (sin permisos de archivos, shell, HTTP ni portapapeles) y compilación en modo de depuración.

**T8 — Frontend.** Pantallas `SETUP`, `UNLOCK` y `VAULT`, store y `PasswordField`, textos en el diccionario de cadenas. Se prueba primero Open Design con los mockups; si no sirve, el implementador maqueta con Tailwind. *Tests primero (vitest):* `lock()` deja el store vacío y la pantalla en `UNLOCK`, `PasswordField` oculta por defecto y al soltar. *Verificación:* `pnpm check`, `pnpm lint`, `pnpm test`, `pnpm build` y `check-forbidden.sh`.

**T9 — Aceptación.** Lista manual con `pnpm tauri dev`: crear, cerrar y reabrir; contraseña incorrecta; alterar un byte con un editor hexadecimal; bloquear y comprobar el estado; sesión completa con Wireshark sin tráfico; tiempo de desbloqueo (AC8). Se completa el registro de cambios del spec y se fusiona la rama.

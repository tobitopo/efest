# Constitución de Efest

> Fuente de verdad una vez dentro del repositorio. Copia de la Documentación Maestra v1.0, §3.
> Se inyecta al inicio de cada sesión con un agente. Una enmienda requiere acuerdo de las dos personas del equipo y se registra al final.

**Art. 1 — Stack fijo.** Rust (versión fijada en `rust-toolchain.toml`), Tauri v2, Svelte 5 con TypeScript y Vite como SPA (sin SvelteKit), TailwindCSS, Lucide y pnpm. Las versiones las fija el lockfile y no se cambian sin aprobación.

**Art. 2 — Aislamiento.** Toda la criptografía y todo acceso a archivos viven en Rust (`efest-core`). El frontend no accede a disco, red, shell ni portapapeles; su único canal es `invoke` hacia comandos Tauri con capabilities mínimas. El frontend solo recibe lo estrictamente necesario: los secretos se entregan bajo demanda y por identificador.

**Art. 3 — Criptografía.** Prohibida la criptografía propia. Solo se usan las primitivas del ADR-001 a través de `dryoc` y detrás de la interfaz interna `efest-core::crypto`. Los parámetros se guardan en la cabecera del archivo y la cabecera se autentica. Cada cifrado usa un nonce aleatorio nuevo. Todo componente criptográfico exige tests con vectores de prueba conocidos.

**Art. 4 — Secretos en memoria.** Todo tipo que contenga un secreto implementa `Zeroize` y `ZeroizeOnDrop` (preferir los tipos de memoria protegida de dryoc). Su `Debug` está redactado. Los secretos nunca van a logs, `println!`, mensajes de error ni a almacenamiento del navegador. Al bloquear, el estado de Rust se destruye y la interfaz se purga.

**Art. 5 — Red cero.** La aplicación no hace peticiones de red. No hay telemetría ni íconos remotos. Cualquier función de red futura es opt-in, tiene su propio spec y requiere aprobación. La CSP es restrictiva y no hay contenido remoto.

**Art. 6 — Código y convenciones.**
- Identificadores en inglés; textos de interfaz en español, en un único diccionario de cadenas.
- Rust: `rustfmt`, `clippy -D warnings`, `#![forbid(unsafe_code)]` en nuestros crates, errores tipados con `thiserror`, sin `unwrap()` ni `expect()` fuera de tests.
- TypeScript: modo estricto, sin `any`, sin `{@html}`, sin `localStorage`, `sessionStorage`, `indexedDB` ni `navigator.clipboard`.
- El acceso a datos pasa solo por el trait `VaultStorage`.
- Ninguna dependencia nueva sin estar listada en el plan. Solo se usan APIs que existan en la documentación de la versión fijada; si la API no existe, el agente se detiene y lo reporta. Nada de inventar librerías ni firmas.

**Art. 7 — Proceso y prohibiciones.** Se trabaja en una tarea a la vez, con TDD y verificación independiente. La IA no cambia la arquitectura ni los specs por su cuenta. Los cambios en `efest-core::crypto` y en `src-tauri/capabilities` requieren revisión de la otra persona. **Está prohibido:** sincronización en la nube en la v1, telemetría, íconos remotos, almacenar la contraseña maestra, mensajes de error que distingan "contraseña incorrecta" de "archivo dañado", actualizaciones silenciosas, y guardar llaves, tokens o secretos de firma en el repositorio o en la máquina de desarrollo.

## Registro de enmiendas

(vacío)

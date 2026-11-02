# Spec 001 — Bóveda mínima (el átomo)

> Fuente de verdad una vez dentro del repositorio. Copia de la Documentación Maestra v1.0, §6.
> Rama: `spec/001-mvp`. Un pull request por tarea (ver `tasks.md`).

**Objetivo.** Probar de punta a punta el núcleo seguro: crear una bóveda cifrada con contraseña maestra, guardar una credencial y volver a abrirla.

## Funciones

- **F1 Crear bóveda:** el usuario define la contraseña maestra (con confirmación). Se genera un salt aleatorio de 16 bytes, se deriva la clave con Argon2id y se escribe `efest.vault` con cero credenciales.
- **F2 Guardar una credencial:** título, usuario y contraseña. El átomo admite una sola; una segunda se rechaza (el CRUD llega en el Spec 002).
- **F3 Desbloquear, mostrar y bloquear:** se pide la contraseña maestra y se muestran título y usuario. La contraseña se revela solo mientras se mantiene presionado el botón. Un botón bloquea y purga todo.

## Fuera de alcance

Edición y borrado, categorías, búsqueda, generador, portapapeles, autobloqueo por inactividad, importar y exportar, TOTP, actualizador, instaladores, red.

## Casos límite

- Contraseña maestra incorrecta o archivo alterado: mismo error genérico (`UNLOCK_FAILED`), sin pánico y sin datos parciales.
- Contraseña maestra de menos de 12 caracteres: se rechaza antes de derivar.
- Normalización: la contraseña se normaliza a Unicode NFKC antes de derivar, para que el mismo texto dé la misma clave en cualquier sistema o teclado.
- Crear con una bóveda ya existente: error `VAULT_EXISTS`; nunca se sobrescribe.
- Abrir sin archivo: la interfaz vuelve a la pantalla de creación.
- Operaciones sin desbloquear (`add_credential`, `reveal_password`): error `VAULT_LOCKED`.
- Archivo truncado, firma errónea o versión de formato desconocida: error `UNSUPPORTED_FORMAT` y nunca un pánico.
- Parámetros de Argon2 fuera de los límites aceptados en la cabecera: se rechazan antes de derivar.
- Escritura interrumpida (corte de energía o cierre): el archivo anterior queda intacto (escritura atómica con respaldo).
- La derivación tarda cerca de un segundo: nunca bloquea la interfaz y se muestra un estado de progreso.
- Los campos obligatorios vacíos (título, contraseña de la credencial) se rechazan.

## Criterios de aceptación

- **AC1:** tras crear la bóveda existe el archivo con firma `EFST` y ningún texto de la contraseña, el título ni el usuario aparece en claro en sus bytes.
- **AC2:** cerrar y abrir la aplicación y desbloquear devuelve la misma credencial.
- **AC3:** con contraseña incorrecta se obtiene `UNLOCK_FAILED`, sin pánico.
- **AC4:** alterar un solo byte en cualquier punto del archivo hace fallar el desbloqueo.
- **AC5:** al bloquear, el estado de Rust queda destruido y la interfaz vuelve a la pantalla de desbloqueo con el store vacío.
- **AC6:** durante una sesión completa la aplicación no abre ninguna conexión de red (comprobado con Wireshark o el Monitor de recursos).
- **AC7:** `scripts/verify.sh` pasa en CI (Linux y Windows).
- **AC8:** el desbloqueo toma como máximo 1,5 s en el equipo de referencia y la interfaz no se congela.

## Registro de cambios del spec

(vacío)

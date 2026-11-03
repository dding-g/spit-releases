# spit

[English](../README.md) · [한국어](README.ko.md) · [日本語](README.ja.md) · [简体中文](README.zh-CN.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Português](README.pt.md)

Una pequeña aplicación de bandeja para Windows y macOS que guarda notas rápidas de una línea en Markdown local. El editor sencillo permanece precargado; los archivos se escriben en segundo plano. La guía en inglés es la referencia.

El repositorio de código fuente es privado. El [repositorio de distribución público](https://github.com/dding-g/spit-releases) solo contiene archivos oficiales de instalación, actualizaciones y guías de usuario.

## Instalación y uso

Descarga un archivo publicado para tu sistema y CPU desde [Releases](https://github.com/dding-g/spit-releases/releases) y descomprímelo. [Sitio web](https://spit-ashy.vercel.app). En macOS 12 o posterior, copia `spit.app` a Applications. En Windows, ejecuta `spit.exe` desde la carpeta que prefieras; requiere Microsoft WebView2 Runtime. No hay instalador ni inicio automático de sesión. macOS solo lleva firma ad-hoc, **sin Developer ID ni notarización**; Windows no lleva firma Authenticode. Gatekeeper o SmartScreen pueden advertir o bloquear la ejecución. Abre solo archivos de confianza.

Elige `macos-arm64` para Apple silicon o `macos-amd64` para Intel; en Windows, `windows-amd64` para x64 o `windows-arm64` para ARM64. **No se ha verificado en Windows la ejecución ni la actualización/reinicio**; compilar de forma cruzada no verifica la ejecución.

Al iniciarse por primera vez, spit detecta las bóvedas registradas de Obsidian sin recorrer todo el disco y elige la usada más recientemente. Si no hay ninguna, selecciona una carpeta en Ajustes. Sirve cualquier carpeta existente; Obsidian no es obligatorio.

- `Cmd + \` en macOS / `Ctrl + \` en Windows: mostrar u ocultar el editor rápido.
- `Enter`: ocultar de inmediato y enviar para guardar en segundo plano. No envía durante la composición con un IME.
- `Esc`: ocultar y conservar el borrador sin enviar **solo durante esta sesión**. No se recupera tras salir y abrir normalmente; la excepción es el reinicio explícito para actualizar descrito abajo.
- Al pegar varias líneas se unen en una. Se ignoran las notas vacías; el máximo es de 10.000 unidades de código UTF-16.
- Un clic izquierdo en el icono de la bandeja/barra de menús abre o cierra el **historial de solo lectura**, inicialmente en el día de hoy. Puedes elegir cualquier fecha guardada, ir a la anterior/siguiente fecha con notas o actualizar. No permite editar, borrar ni buscar, y no es un widget permanente del escritorio o del sistema.
- El menú de la bandeja también abre el historial, Nueva nota y los ajustes de carpeta e idioma. Usa Nueva nota si no se puede registrar el atajo global. Cerrar una ventana no cierra la aplicación; usa Salir.

El idioma puede **detectarse automáticamente según el sistema/navegador** o elegirse manualmente y conservarse: inglés, coreano, japonés, chino simplificado, español, francés, alemán o portugués. La elección manual tiene prioridad; los idiomas no compatibles y las traducciones que falten recurren al inglés. Cambiar el idioma conserva el borrador actual.

## Actualizaciones y privacidad

spit busca actualizaciones en GitHub Releases y las descarga automáticamente en segundo plano. Ajustes muestra el estado, los errores, la comprobación manual y el reinicio cuando está listo. **Solo se instala y reinicia al pulsar Reiniciar**; nunca interrumpe la captura automáticamente. Espera los guardados en curso y la persistencia de la cola, y rechaza el reinicio durante la composición IME. Conserva el borrador sin enviar para restaurarlo tras la actualización; esta copia exclusiva de actualización no hace persistentes los borradores de sesiones normales.

Antes de instalar, verifica SHA-256 y la firma Ed25519 con la clave pública fijada en la aplicación; si falla, bloquea la instalación. Esta firma no equivale a firma del sistema, notarización de Apple ni confianza del editor en Windows. Las comprobaciones y descargas acceden a GitHub y sus servidores de descarga; no suben notas ni archivos de la bóveda. La sincronización de la bóveda depende de otros programas.

## Archivos y recuperación

Si eliges `chosenRoot`, se escribe en `chosenRoot/admo/YYYY-MM-DD.md`. Si la carpeta ya se llama `admo`, se usa directamente, sin crear `admo/admo`. Las notas diarias existentes en la raíz no se mueven. Cada nota conserva la fecha y hora local del envío, aunque se guarde más tarde:

```markdown
- 09:12:04 Comprar leche <!-- admo:UUID -->
```

El comentario HTML oculto identifica la nota para evitar duplicados al recuperar; sigue visible en el código fuente. El texto se escapa para no interpretarlo como Markdown/HTML y se conserva el contenido previo del archivo. El historial lee los archivos con fecha de la carpeta efectiva `admo`, no toda la bóveda, y reconoce los marcadores antiguos compatibles.

Primero se conserva la nota en una cola local en `state.json`; después se añade y sincroniza en Markdown y se elimina de la cola. Si falla la escritura, permanece pendiente, se muestra un error en Ajustes y en la bandeja y se reintenta cada **5 segundos**, también al reiniciar. Si no se puede conservar en la propia cola local, el texto vuelve al borrador.

Las notas pendientes mantienen su destino original; cambiar de carpeta solo afecta a las nuevas. La cola se procesa en orden: un destino anterior no disponible bloquea las notas posteriores. Restaura la carpeta o los permisos para permitir el reintento. El estado dañado **no se reinicia automáticamente**: conserva el archivo indicado en el error antes de repararlo.

Estado local, independiente de la carpeta Markdown:

- macOS: `~/Library/Application Support/spit/state.json`
- Windows: `%AppData%\spit\state.json`

En la ubicación predeterminada, si no existe estado de `spit`, se migra el antiguo de `admo` y después el de `adhd-memo`, sin sobrescribir el nuevo. La carpeta Markdown y los marcadores existentes siguen siendo `admo` por compatibilidad: no se renombran, mueven ni borran carpetas de la bóveda y las notas pendientes mantienen su destino.

Notas y estado son **texto sin cifrar**. Ocultar el editor no confirma un guardado duradero: un cierre forzado o corte eléctrico antes de persistir la cola puede perder esa entrada. Una escritura interrumpida puede dejar líneas parciales; la recuperación las conserva y añade la nota completa. No se garantizan fallos del sistema de archivos/dispositivo ni cambios simultáneos de editores o sincronizadores.

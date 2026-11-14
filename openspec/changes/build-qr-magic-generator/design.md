## Context

`qr-magic` es un proyecto greenfield (el repositorio no tiene commits ni specs previos). La aplicación debe cumplir un requisito duro: **un único archivo HTML autocontenido** (`index.html`) que funcione íntegramente en el navegador, sin backend, sin build step y sin enviar datos a terceros.

Restricciones confirmadas contra las librerías reales:

- `qr-code-styling@1.9.2` se distribuye como UMD (`window.QRCodeStyling`), 48 KB, servible desde `https://cdn.jsdelivr.net/npm/qr-code-styling@1.9.2/lib/qr-code-styling.min.js`.
  - Opciones relevantes verificadas en el código: `dotsOptions{type,color,gradient}`, `cornersSquareOptions{type,color,gradient}`, `cornersDotOptions{type,color,gradient}`, `backgroundOptions{color,gradient,round}`, `imageOptions{imageSize,margin,hideBackgroundDots,crossOrigin,saveAsBlob}`, `qrOptions{errorCorrectionLevel,mode,typeNumber}`.
  - `dotsOptions.type` admite `square | dots | rounded | extra-rounded | classy | classy-rounded`.
  - `cornersSquareOptions.type` admite `square | extra-rounded | dot`; `cornersDotOptions.type` admite `square | dot`.
  - `gradient = { type: 'linear'|'radial', rotation (RADIANTES), colorStops: [{offset,color}] }`.
  - El fondo transparente se consigue con `backgroundOptions.color = 'transparent'`.
  - API pública: `update(options)`, `download({name, extension})`, `getRawData(extension)`, `append(container)`.
- `@tailwindcss/browser@4.3.5` se sirve desde `https://cdn.jsdelivr.net/npm/@tailwindcss/browser@4` y soporta un bloque `<style type="text/tailwindcss">` con `@theme` para tokens de diseño.

Público objetivo: usuarios no técnicos que necesitan un QR personalizado para imprimir (tarjetas, menús, redes sociales). Toda la interfaz está **en español** y el branding visible es `qr-magic`.

## Goals / Non-Goals

**Goals:**

- Una sola página `index.html` que abre con doble clic (protocolo `file://`) y funciona sin servidor.
- Vista previa que se actualiza en vivo con cada cambio de control, sin botón "Generar".
- Cuatro formatos de contenido estándar (URL/Texto, Wi-Fi, vCard, Email) con payload correcto según especificación.
- Personalización visual profunda: formas de módulo, degradados, esquinas independientes, fondo transparente, logo/icono central.
- Exportación a PNG/SVG/JPEG con resolución seleccionable (300, 500, 800, 1000, 1500 o 2000 px) y copia al portapapeles.
- Advertencia de legibilidad por contraste cuando la configuración arriesga el escaneo.
- Layout responsive: dos columnas en escritorio (`≥1024px`), columna única apilada en móvil.

**Non-Goals:**

- Persistencia en servidor, cuentas de usuario, analítica, backend o API propia.
- Colección/biblioteca de QRs guardados, historial, QR dinámicos con parámetros de tracking.
- Generación por lotes, plantilla de impresión (CMYK, sangrado, PDF de producción).
- Modo offline completo sin CDN (el CDN es una dependencia asumida; sí habrá estilos inline de respaldo para el layout crítico).
- Soporte de formatos adicionales (SMS, Geolocalización, evento de calendario, Bluetooth, redes sociales con deep-link).

## Decisions

### D1 — Un único `index.html` con tres capas (Tailwind CDN + librería UMD + script inline)

La página tendrá: `<script src="tailwind-browser">`, `<script src="qr-code-styling">` (ambos con `defer`) y un `<script>` inline con toda la lógica, más un bloque `<style>` con `@theme` de Tailwind v4 y un `<style>` inline de respaldo.

**Alternativa descartada:** un proyecto con bundler (Vite) o varios archivos. Contradice el requisito "único archivo HTML autocontenido" y añade fricción para el usuario final.

**Consecuencia:** el archivo requiere red para cargar Tailwind y `qr-code-styling` la primera vez. El `<style>` de respaldo garantiza que la página no quede ilegible sin red.

### D2 — Modelo de estado único y declarativo (`state` + `render()`)

Todo el estado vive en un objeto plano `state`. Cada control HTML lleva un `data-bind` (`data-bind="dots.type"`), y un único listener delegado (`input` + `change`) escribe en `state` y dispara `render()`. No hay estado duplicado en el DOM.

**Alternativa descartada:** actualizar la librería directamente desde cada listener de control. Produce duplicación y deriva entre la vista previa y los controles.

`render()` hace siempre lo mismo, en orden: (1) construir el payload desde la pestaña activa, (2) validarlo, (3) derivar el objeto de opciones de `qr-code-styling`, (4) `preview.update(options)`, (5) recalcular el contraste y refrescar el indicador, (6) refrescar el volcado de texto del payload.

Los campos de texto se actualizan con un debounce de ~150 ms; sliders, selectores de color y toggles se aplican de inmediato.

### D3 — Dos instancias de `QRCodeStyling`: vista previa (SVG) y exportación (canvas)

- **Vista previa:** una instancia con `type: 'svg'`, tamaño de presentación (por ejemplo 420×420). SVG se ve nítido a cualquier zoom y no-pixeliza en pantallas HiDPI.
- **Exportación:** una segunda instancia con `type: 'canvas'`, creada bajo demanda con `width = height = resolución elegida`, de la que se llama `getRawData()` / `download()`.

**Alternativa descartada:** redimensionar la instancia de la vista previa para exportar. Produce saltos visuales y obliga a restaurar el tamaño; además el `download()` de la librería usa el `width`/`height` de la instancia.

**Consecuencia:** el SVG exportado no tiene resolución intrínseca (es vectorial, se escala sin pérdida), lo cual es lo deseable; el PNG/JPEG respetan la opción seleccionada (300, 500, 800, 1000, 1500 o 2000 px).

### D4 — Constructores de payload como funciones puras

`buildUrlPayload`, `buildWifiPayload`, `buildVCardPayload`, `buildEmailPayload` son funciones puras sin acceso al DOM, con una forma de objeto de entrada → string de salida. El escape de caracteres especiales vive en funciones auxiliares (`escapeWifiValue`, `escapeVCardValue`).

Reglas de formato:

| Formato | Formato de salida |
|---|---|
| Wi-Fi | `WIFI:T:<tipo>;S:<ssid>[;P:<contraseña>][;H:true];;` — `T` ∈ `WPA/WPA2`, `WEP`, `nopass`. Escape de `\ ; , : "` con backslash. El campo `H:true` solo se añade si la red es oculta. `P` se omite únicamente para `nopass`; WPA/WPA2 y WEP requieren contraseña no vacía. |
| vCard | `BEGIN:VCARD` / `VERSION:3.0` / `END:VCARD`; `N:` y `FN:` cuando haya nombre o apellidos; `ORG:` / `TITLE:` / `TEL;TYPE=CELL:` / `EMAIL:` / `URL:` solo cuando sus valores estén presentes. CRLF y escape de `, ; \` en los valores. |
| Email | `mailto:<destinatario>?subject=<...>&body=<...>` con `encodeURIComponent` en asunto y cuerpo. |

**Alternativa descartada:** construir los payloads con template literals inline en los listeners. Impide reutilizar y testear la lógica.

### D5 — Validación por campo con estados de error en línea

Cada campo tiene un estado (`vacío | válido | error`) y un mensaje en español bajo el input. Reglas mínimas: Wi-Fi exige SSID y, para WPA/WPA2 o WEP, una contraseña no vacía; vCard exige al menos nombre o correo; Email exige destinatario con formato válido. Con payload inválido la vista previa muestra un estado vacío explicativo en lugar de un QR roto.

### D6 — Verificador de contraste basado en WCAG 2.1

Se implementa luminancia relativa WCAG:

```
L = 0.2126·R + 0.7152·G + 0.0722·B   (canal linealizado: c/255, si c ≤ 0.03928 → c/12.92, si no ((c+0.055)/1.055)^2.4)
ratio = (L_máx + 0.05) / (L_mín + 0.05)
```

- Con colores de puntos sólidos: se compara ese color contra el fondo. También se comparan contra el fondo los colores propios del marco exterior y del punto interior de las esquinas; el indicador usa el peor valor entre esas áreas.
- Con degradado lineal o radial en los módulos de datos: se interpolan los dos colores elegidos en sRGB y se calculan sus contrastes contra el fondo en 101 posiciones equidistantes, incluidos ambos extremos. Se usa el peor resultado.
- El indicador evalúa los módulos de datos y los colores configurados para los marcos y puntos interiores de las esquinas; usa el peor contraste entre esas áreas. Es una señal orientativa de contraste, no una garantía de escaneabilidad: tamaño de módulos, zona silenciosa, corrección de errores, calidad de impresión y soporte también afectan la lectura.
- Con fondo transparente: el fondo efectivo se asume `#FFFFFF` (la cuadrícula de la vista previa es clara) y se muestra un aviso adicional indicando que el contraste real depende del soporte donde se imprima.

Escala orientativa: `ratio ≥ 7` → excelente; `≥ 4.5` → bueno; `≥ 3` → bajo (aviso ámbar); `< 3` → contraste bajo (alerta roja con recomendación de oscurecer los puntos o aclarar el fondo). Estos umbrales describen solo el contraste medido y no predicen por sí solos si un QR podrá escanearse.

**Alternativa descartada:** permitir que el usuario introduzca el contraste a mano. El cálculo automático es el valor diferencial y es determinista.

### D7 — Presets como datos, no como código imperativo

`PRESETS` es un array de objetos `{ id, nombre, descripcion, opciones }`. Un botón por preset hace `Object.assign` de las subsecciones relevantes del estado (puntos, esquinas, fondo, logo) y llama a `render()`. Los presets de la especificación: *Clásico*, *Moderno Azul*, *Neón Oscuro*, *Elegante Dorado*, *Minimalista*.

### D8 — Logo: data URL en base64, nunca `blob:` URL

Los ficheros subidos se leen con `FileReader.readAsDataURL` y los iconos de la galería son SVG embebidos convertidos a `data:image/svg+xml;base64,…` con codificación UTF-8 segura (`btoa(unescape(encodeURIComponent(svg)))`).

**Alternativa descartada:** `URL.createObjectURL(file)`. La librería, con `imageOptions.saveAsBlob: true`, hace un `XMLHttpRequest` GET sobre la URL de la imagen; con `blob:` URLs el comportamiento no es uniforme entre navegadores y el SVG dibujado en canvas puede quedar tainted.

Validaciones: tipo `image/png`, `image/jpeg`, `image/svg+xml`; tamaño máximo 2 MB; mensajes de error en español. Al añadir un logo se fuerza `errorCorrectionLevel = 'H'` y se avisa al usuario si vuelve a bajarlo. `imageOptions.hideBackgroundDots` se mantiene en `true` para que el logo "flote" sobre los módulos.

### D9 — Exportación: `getRawData` en vez de manipulating canvas a mano

- **PNG:** `exportInstance.getRawData('png')` → `Blob` → `URL.createObjectURL` → `<a download="qr-magic.png">`.
- **JPEG:** `exportInstance.getRawData('jpeg')`. Si el fondo está transparente, JPEG no soporta transparencia: en ese caso la instancia de exportación se construye con fondo blanco y la interfaz lo indica.
- **SVG:** `exportInstance.getRawData('svg')` → `Blob` de tipo `image/svg+xml`, con la cabecera `<?xml …?>` que añade la librería.
- **Portapapeles:** `navigator.clipboard.write([new ClipboardItem({'image/png': blob})])`, que requiere contexto seguro y gesto de usuario. El PNG copiado usa la resolución seleccionada. El botón se deshabilita solo si la API o `ClipboardItem` no están disponibles; si el intento falla (por ejemplo, por contexto no seguro o permiso denegado), se informa del problema y se ofrece descargar el PNG manualmente.

**Alternativa descartada:** `canvas.toDataURL()` sobre el SVG serializado a mano. Duplica la lógica de la librería y pierde el soporte de `saveAsBlob` para SVG.

### D10 — Copy al portapapeles con degradación elegante

`navigator.clipboard.write` suele requerir un contexto seguro (HTTPS o `localhost`) y un gesto del usuario. Como la disponibilidad y el permiso efectivo pueden conocerse solo al intentar copiar, el botón se deshabilita únicamente cuando la API o `ClipboardItem` no existen. Si el intento falla, se muestra un mensaje en español con la descarga PNG como alternativa; no se presupone el permiso antes de la acción.

### D11 — Un solo archivo, sin ficheros extra

El favicon se define como SVG embebido en un `<link rel="icon" href="data:image/svg+xml,…">` para no crear un segundo fichero. `qr-magic` aparece en: `<title>`, encabezado, subtítulo, placeholders (`https://qr-magic.app` por defecto), textos de ayuda y nombres de archivo exportados (`qr-magic.png`, `qr-magic.svg`, `qr-magic.jpeg`).

### D12 — Accesibilidad y responsive como requisitos, no como extras

- Pestañas con `role="tablist"` / `role="tab"` / `role="tabpanel"`, `aria-selected`, navegación con flechas y `aria-controls`.
- Todos los controles con `<label for>` asociado, `aria-describedby` para mensajes de validación, foco visible con `focus-visible:ring`.
- Layout `lg:grid-cols-[minmax(0,1fr)_420px]` con la columna derecha `lg:sticky lg:top-6`; en `<1024px` se apila en una columna.
- Contraste de la propia UI (texto gris-600 sobre blanco) verificado para cumplir WCAG AA en los textos de la interfaz.

## Risks / Trade-offs

- **[El CDN de Tailwind no carga → la página pierde el diseño] →** Se incluye un bloque `<style>` inline de respaldo con el layout crítico (grid de dos columnas, tarjetas, tipografía base) y las clases de estado propia; el JS muestra un aviso si `window.tailwind` no está disponible.
- **El CDN de `qr-code-styling` no carga → la app no renderiza nada** →** Se comprueba `typeof QRCodeStyling === 'undefined'` al inicio y se muestra un mensaje de error en español explicando que hace falta conexión para la primera carga.
- **[Degradado angular con valores fuera de rango] →** La librería exige `colorStops` no vacío y convierte `rotation` a número; se normaliza el ángulo en grados (0–360) a radianes y se fuerza `offset` en [0,1] antes de pasar la opción.
- **[Un logo grande o con nivel de corrección bajo impide el escaneo] →** Se limita `imageSize` a un rango seguro (0.10–0.40) y se fuerza nivel `H` al añadir logo, avisando al usuario si lo reduce.
- **[Exportar JPEG con fondo transparente produce un fondo negro] →** Al exportar JPEG se sustituye el fondo transparente por blanco y se informa en la interfaz.
- **[Copiar al portapapeles falla en `file://`] →** Si la API no está disponible, el botón queda deshabilitado con explicación; si la API existe pero el intento falla, se muestra el error y se ofrece descargar el PNG.
- **[El SVG embebido como logo puede no renderizar en canvas por caracteres especiales] →** Se codifica en UTF-8 antes de `btoa` y se usa `data:image/svg+xml;base64,`.
- **[Un archivo HTML de gran tamaño dificulta la edición manual] →** El archivo se organiza en secciones claramente delimitadas y comentadas (`ESTADO`, `PAYLOADS`, `VALIDACIÓN`, `CONTRASTE`, `PRESETS`, `ICONOS`, `RENDER`, `EXPORTACIÓN`, `EVENTOS`).
- **[Re-render en cada pulsación de teclado puede ir lento] →** Debounce de 150 ms para campos de texto y actualización inmediata para controles gráficos; el render es O(1) en la librería.

## Migration Plan

No hay migración: proyecto greenfield sin datos ni usuarios previos.

Plan de despliegue: el usuario abre `index.html` en el navegador (doble clic) o lo publica tal cual en cualquier hosting estático (GitHub Pages, Netlify, S3). No hay build, ni variables de entorno, ni pasos de compilación.

**Rollback:** eliminar `index.html` (o revertir el commit) restituye el estado anterior. Al ser el único artefacto del proyecto, no hay estado parcial que limpiar.

## Open Questions

- ¿Deberá la aplicación incluir una opción de "guardar configuración" (exportar/importar JSON de los ajustes)? Queda fuera del alcance actual; se puede añadir más adelante serializando `state` a JSON.
- ¿Se requiere un modo de impresión con fondo blanco forzado y margen de sécurité para uso profesional? No está en el alcance de este cambio.
- ¿El ancho de la columna derecha (420 px) debe ser configurable o basta con un valor fijo responsive? Se asume fijo con punto de quiebre `lg`.

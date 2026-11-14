## 1. Andamiaje del documento y layout base

- [ ] 1.1 Crear `index.html` en la raíz del repositorio con el esqueleto completo: `<head>` con `lang="es"`, `<meta charset>`, `<meta viewport>`, `<title>qr-magic — Generador de códigos QR</title>`, favicon SVG embebido en `data:` URI y `<link rel="preconnect">` a jsDelivr.
- [ ] 1.2 Cargar las dependencias por CDN con `defer`: `https://cdn.jsdelivr.net/npm/@tailwindcss/browser@4` y `https://cdn.jsdelivr.net/npm/qr-code-styling@1.9.2/lib/qr-code-styling.min.js`.
- [ ] 1.3 Añadir el bloque `<style type="text/tailwindcss">` con `@theme` que defina la paleta índigo/violeta, la escala de grises, la familia tipográfica Inter (con `font-family` de respaldo del sistema) y el radio de borde de las tarjetas.
- [ ] 1.4 Añadir un bloque `<style>` inline de respaldo con el layout crítico (grid de dos columnas en `min-width:1024px`, tarjetas, `position:sticky` de la columna derecha, botones y campos) para que la página siga siendo utilizable si Tailwind no carga.
- [ ] 1.5 Construir la estructura HTML del encabezado (marca `qr-magic` + título + subtítulo) y el layout de dos columnas: `lg:grid-cols-[minmax(0,1fr)_420px]`, con la columna derecha `lg:sticky lg:top-6`.
- [ ] 1.6 Crear el esqueleto de la columna derecha: tarjeta de vista previa con contenedor de cuadrícula de fondo (patrón CSS `repeating-linear-gradient`), hueco para el QR, indicador de contraste, volcado del payload y bloque de exportación.
- [ ] 1.7 Crear el esqueleto de la columna izquierda con las tarjetas de control: contenido (pestañas), plantillas rápidas, estilo de puntos, esquinas, fondo, logo y iconos.
- [ ] 1.8 Verificar que la página se abre con doble clic (`file://`) sin errores en la consola y que la cuadrícula de la vista previa se ve.

## 2. Modelo de estado y motor de render

- [ ] 2.1 Definir el objeto `state` con las secciones `activeTab`, `content` (url/wifi/vcard/email), `dots`, `cornersSquare`, `cornersDot`, `background`, `logo` y `export`, con los valores por defecto definidos en el diseño (URL `https://qr-magic.app`, nivel de corrección `Q`, resolución de exportación por defecto).
- [ ] 2.2 Implementar el binding declarativo: atributo `data-bind` en cada control, listener delegado `input`/`change`/`click` que escribe en `state` usando la ruta del binding, y función `setState(path, value)`.
- [ ] 2.3 Implementar `render()` con el orden definido en el diseño: construir payload → validar → derivar opciones → `preview.update(options)` → recalcular contraste → refrescar volcado del payload.
- [ ] 2.4 Implementar `buildOptions()` que traduzca `state` al objeto de opciones de `qr-code-styling` (dotsOptions, cornersSquareOptions, cornersDotOptions, backgroundOptions, imageOptions, qrOptions) incluyendo la conversión del ángulo de degradado de grados a radianes y el clamping de los `colorStops`.
- [ ] 2.5 Crear la instancia de vista previa con `type: 'svg'` y tamaño de presentación, e instanciarla en el contenedor de la vista previa.
- [ ] 2.6 Implementar el debounce de ~150 ms para los campos de texto y la actualización inmediata para sliders, selectores de color e interruptores.
- [ ] 2.7 Implementar `syncControlsFromState()` para que los presets y cualquier cambio programático se reflejen visualmente en los controles.

## 3. Contenido: pestañas, payloads y validación

- [ ] 3.1 Implementar la navegación de pestañas con `role="tablist"`/`tab`/`tabpanel`, `aria-selected`, `aria-controls` y soporte de flechas izquierda/derecha, sincronizando `state.activeTab`.
- [ ] 3.2 Construir el panel «URL / Texto» con un campo de texto cuyo valor por defecto sea `https://qr-magic.app`.
- [ ] 3.3 Implementar `buildUrlPayload(texto)` devolviendo el texto recortado.
- [ ] 3.4 Construir el panel «Wi-Fi» con campos SSID, contraseña, selector de tipo (`WPA/WPA2`, `WEP`, `Ninguna`) y casilla «Red oculta».
- [ ] 3.5 Implementar `buildWifiPayload({ssid, password, encryption, hidden})` con los campos opcionales explícitos: omitir `P` solo para `nopass`, exigir contraseña no vacía para WPA/WPA2 y WEP, emitir `H:true` solo para redes ocultas y escapar `\ ; , : "` con backslash.
- [ ] 3.6 Construir el panel «vCard (Contacto)» con campos nombre, apellidos, teléfono, email, empresa, cargo y sitio web.
- [ ] 3.7 Implementar `buildVCardPayload(...)` con `BEGIN:VCARD`, `VERSION:3.0` y `END:VCARD`; emitir `N:` y `FN:` si hay nombre/apellidos, permitir vCard de solo correo y omitir los demás campos opcionales vacíos; usar saltos CRLF y `escapeVCardValue`.
- [ ] 3.8 Construir el panel «Email» con campos destinatario, asunto y cuerpo.
- [ ] 3.9 Implementar `buildEmailPayload(...)` con `mailto:` y `encodeURIComponent` en asunto y cuerpo, omitiendo parámetros vacíos.
- [ ] 3.10 Implementar la validación por campo: SSID obligatorio; contraseña obligatoria para WPA/WPA2 y WEP; email con formato válido; vCard con nombre o correo. Marcar el campo y enlazar el mensaje de error en español mediante `aria-describedby`.
- [ ] 3.11 Implementar el estado vacío de la vista previa: cuando el payload sea inválido, sustituir el QR por un mensaje en español que indique qué falta, sin renderizar un QR roto.
- [ ] 3.12 Añadir el volcado de texto del payload junto a la vista previa, sincronizado con cada render.
- [ ] 3.13 Verificar manualmente los cuatro formatos: comprobar que el QR generado se escanea con un lector y que el texto codificado coincide exactamente con el volcado.

## 4. Apariencia: presets, puntos, degradados, esquinas y fondo

- [ ] 4.1 Definir el array `PRESETS` con al menos «Clásico», «Moderno Azul», «Neón Oscuro», «Elegante Dorado» y «Minimalista», cada uno con su objeto completo de opciones.
- [ ] 4.2 Renderizar los botones de preset y conectar su clic con la aplicación del preset sobre `state` más `render()` y `syncControlsFromState()`.
- [ ] 4.3 Construir el selector de forma de módulo con `square`, `dots`, `rounded`, `extra-rounded`, `classy` y `classy-rounded`.
- [ ] 4.4 Construir el selector de modo de color de puntos (Sólido / Degradado) con dos selectores de color, tipo de degradado (lineal / radial) y control de ángulo en grados.
- [ ] 4.5 Implementar la construcción del objeto `gradient` para los puntos, con `colorStops` normalizados y `rotation` en radianes, incluyendo la traducción al objeto `dotsOptions`.
- [ ] 4.6 Construir el bloque de esquinas con el selector de estilo del marco exterior (`square`, `extra-rounded`, `dot`) y su selector de color propio.
- [ ] 4.7 Construir el bloque de esquinas con el selector de estilo del punto interior (`square`, `dot`) y su selector de color propio.
- [ ] 4.8 Construir el bloque de fondo con selector de color e interruptor «Fondo transparente», deshabilitando el selector de color cuando el interruptor está activo.
- [ ] 4.9 Verificar visualmente cada forma de módulo, cada tipo de degradado y cada estilo de esquina, confirmando que los colores de las esquinas son independientes de los de los puntos.

## 5. Logo central, iconos y corrección de errores

- [ ] 5.1 Implementar la carga de ficheros con `FileReader.readAsDataURL`, validación de tipo MIME (PNG/JPG/SVG) y de tamaño máximo (2 MB), con mensajes de error en español.
- [ ] 5.2 Construir la zona de arrastre y soltar con realce visual durante el `dragover` y procesamiento del `drop` con la misma validación que el selector de archivos.
- [ ] 5.3 Implementar la galería de iconos SVG embebidos (al menos Wi-Fi, ubicación, estrella y corazón) como cadenas SVG codificadas en `data:image/svg+xml;base64,` con soporte UTF-8.
- [ ] 5.4 Implementar la selección de un icono de la galería como logo activo, sustituyendo el logo cargado previamente.
- [ ] 5.5 Añadir el control deslizante de tamaño del logo (rango 0,10–0,40) y el de margen en píxeles, conectados a `imageOptions.imageSize` e `imageOptions.margin`.
- [ ] 5.6 Implementar el selector de corrección de errores (`L`, `M`, `Q`, `H`), la activación automática de `H` al añadir logo y el aviso en español cuando el usuario lo reduzca con un logo activo.
- [ ] 5.7 Configurar `imageOptions.hideBackgroundDots: true` y mostrar una miniatura del logo activo con su nombre y un botón para quitarlo.
- [ ] 5.8 Verificar que el QR con logo sigue siendo escaneable y que el tamaño máximo del logo no compromete la legibilidad.

## 6. Verificador de contraste

- [ ] 6.1 Implementar la conversión de color hexadecimal a RGB y el cálculo de luminancia relativa según WCAG 2.1 (linealización de canales y pesos 0,2126 / 0,7152 / 0,0722).
- [ ] 6.2 Calcular el contraste `(L1 + 0.05) / (L2 + 0.05)` entre fondo y módulos de datos, evaluar 101 posiciones equidistantes de la interpolación sRGB de cada degradado y tomar el peor caso entre esos puntos y los colores de marcos y puntos interiores de esquinas.
- [ ] 6.3 Implementar el indicador orientativo de contraste en la vista previa: ratio numérica, «Excelente» ≥ 7, «Bueno» ≥ 4,5, «Bajo» ≥ 3 y «Contraste bajo» < 3; aclarar que no garantiza escaneabilidad.
- [ ] 6.4 Implementar la alerta destacada para ratios inferiores a 4,5:1: advertencia visual entre 3 y 4,5, error visual por debajo de 3, recomendación en español y desaparición automática al corregir colores.
- [ ] 6.5 Tratar el fondo transparente tomando el blanco como fondo efectivo y mostrando un aviso adicional sobre la dependencia del soporte de impresión.
- [ ] 6.6 Verificar que el indicador se actualiza al aplicar un preset con contraste reducido.

## 7. Exportación y portapapeles

- [ ] 7.1 Construir el selector de resolución con los valores 300, 500, 800, 1000, 1500 y 2000 px, y un valor por defecto de 1000 px.
- [ ] 7.2 Implementar `createExportInstance(opciones, tamaño)` que cree una instancia `qr-code-styling` de tipo `canvas` bajo demanda con el tamaño elegido.
- [ ] 7.3 Implementar la descarga en PNG con `getRawData('png')`, `URL.createObjectURL` y un `<a download="qr-magic.png">`, revocando la URL tras la descarga.
- [ ] 7.4 Implementar la descarga en SVG con `getRawData('svg')` y `<a download="qr-magic.svg">`, comprobando que conserva degradados y logo.
- [ ] 7.5 Implementar la descarga en JPEG con `getRawData('jpeg')` y `<a download="qr-magic.jpeg">`, forzando fondo blanco cuando el fondo es transparente e informando al usuario.
- [ ] 7.6 Copiar al portapapeles un PNG en la resolución seleccionada; deshabilitar el botón solo si la API o `ClipboardItem` no están disponibles. Si un intento falla por contexto o permiso, informar en español y dejar disponible la descarga PNG.
- [ ] 7.7 Verificar que los tres ficheros descargados tienen el nombre correcto, la resolución correcta y el contenido esperado.

## 8. Accesibilidad, responsive y branding

- [ ] 8.1 Verificar que todos los controles tienen `<label for>` asociado, que los mensajes de validación usan `aria-describedby` y que el foco es visible mediante `focus-visible`.
- [ ] 8.2 Verificar la navegación por teclado de las pestañas, del deslizador de logos y de los interruptores.
- [ ] 8.3 Verificar el layout en 390 px, 768 px, 1024 px y 1440 px: sin desbordamiento horizontal, columna única en móvil y dos columnas desde 1024 px.
- [ ] 8.4 Verificar que la columna derecha permanece visible (`sticky`) al hacer scroll en escritorio.
- [ ] 8.5 Comprobar que el nombre `qr-magic` aparece en el título, el encabezado, los placeholders, los textos de ayuda y los nombres de archivo exportados.
- [ ] 8.6 Revisar que todos los textos visibles están en español, incluidos los mensajes de error de la librería y los avisos de degradación.
- [ ] 8.7 Añadir el aviso de fallo de carga de `qr-code-styling` en español y comprobar que se muestra cuando la librería no está disponible.

## 9. Verificación final

- [ ] 9.1 Abrir `index.html` en Chrome, Safari y Firefox y comprobar que no hay errores en la consola y que la vista previa se renderiza.
- [ ] 9.2 Recorrer los cinco presets y verificar que el QR resultante es escaneable.
- [ ] 9.3 Escanear con el móvil un QR exportado en PNG, SVG y JPEG con logo, degradado, esquinas personalizadas y fondo transparente, y confirmar la lectura correcta.
- [ ] 9.4 Confirmar que ningún dato del usuario sale del navegador (sin peticiones de red más allá de los dos CDN).
- [ ] 9.5 Revisar el tamaño final del fichero y la legibilidad del código (secciones comentadas) para facilitar el mantenimiento.

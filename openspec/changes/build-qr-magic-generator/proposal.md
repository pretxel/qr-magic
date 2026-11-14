## Why

Los generadores de códigos QR online suelen obligar al usuario a crear una cuenta, suben los datos a servidores de terceros y ofrecen un control visual limitado (un único color de puntos, sin degradados, sin esquinas independientes, sin logo real). `qr-magic` resuelve esto con una aplicación web 100% local, en un único archivo HTML autocontenido, que permite diseñar y exportar códigos QR altamente personalizados (degradados, marcos redondeados, logo central, fondos transparentes) sin enviar nada a ningún servidor.

## What Changes

- Se añade `index.html`: aplicación web autocontenida (Tailwind CSS vía CDN + `qr-code-styling`) que funciona íntegramente en el navegador, sin backend ni build step.
- La interfaz se escribe **en español** y sigue una estética SaaS moderna (tonos claros, tarjetas con bordes suaves, tipografía Inter, acentos índigo/violeta), con layout responsive de dos columnas en escritorio: panel de controles a la izquierda y vista previa sticky a la derecha.
- Se añaden pestañas de contenido con cuatro formatos estándar: URL/Texto, Wi-Fi, vCard y Email, cada una con sus campos y su formateador de payload.
- Se añade un panel de personalización visual: plantillas rápidas (presets), estilo de puntos (sólido o degradado lineal/radial con ángulo), estilo y color del marco exterior y del punto interior de las esquinas, color de fondo y modo de fondo transparente.
- Se añade soporte de logo central: subida por selector o arrastrar y soltar (PNG/JPG/SVG), galería de iconos SVG integrados, sliders de tamaño y margen, y control de corrección de errores que auto-activa nivel `H` al añadir un logo.
- Se añade un verificador de contraste automático que calcula la relación de contraste fondo/puntos y alerta visualmente cuando el escaneo puede verse comprometido.
- Se añade exportación en PNG, SVG y JPEG con selector de resolución (300×300, 500×500, 800×800, 1000×1000, 1500×1500 o 2000×2000) más copia al portapapeles.
- Todo el branding visible (título, encabezado, placeholders, nombres de archivo exportados) usa el nombre `qr-magic`.

## Capabilities

### New Capabilities
<!-- Capabilities being introduced. Replace <name> with kebab-case identifier (e.g., user-auth, data-export, api-rate-limiting). Each creates specs/<name>/spec.md -->
- `qr-content-payloads`: Generación y formateo del contenido codificado en el QR para los cuatro formatos soportados (URL/Texto, Wi-Fi, vCard, Email), incluyendo la selección de pestaña, la validación de campos y la normalización de escapes.
- `qr-visual-styling`: Personalización del aspecto del QR: presets de un clic, estilo y color de los módulos de datos (sólido o degradado lineal/radial con ángulo), estilo y color independientes del marco exterior y del punto interior de las esquinas, y configuración del fondo (color o transparente).
- `qr-logo-embedding`: Inserción de un logo o icono en el centro del QR, con carga local (selector y drag & drop), galería de iconos SVG integrados, control de tamaño y margen, y gestión del nivel de corrección de errores.
- `qr-contrast-validation`: Cálculo del contraste relativo entre el fondo y los puntos del QR con indicador de legibilidad en la vista previa y alerta visible ante relaciones insuficientes.
- `qr-export-download`: Exportación del QR en PNG, SVG y JPEG con resolución configurable, nombres de archivo con marca `qr-magic` y copia de la imagen renderizada al portapapeles.
- `qr-magic-app-shell`: Estructura y comportamiento de la aplicación: layout responsive de dos columnas, vista previa en tiempo real con fondo de cuadrícula, navegación por pestañas, sistema de presets y branding `qr-magic` en toda la interfaz.

### Modified Capabilities
<!-- Existing capabilities whose REQUIREMENTS are changing (not just implementation).
     Only list here if spec-level behavior changes. Each needs a delta spec file.
     Use existing spec names from openspec/specs/. Leave empty if no requirement changes. -->
- Ninguna (el proyecto no tiene specs previos).

## Impact

- **Archivos nuevos**: `index.html` en la raíz del repositorio (único artefacto ejecutable) y un favicon `qr-magic.svg` opcional referenciado desde la misma página.
- **Dependencias externas (CDN, sin bundle)**: Tailwind CSS (Play CDN) y `qr-code-styling` (UMD). Ninguna llamada a servidor de back-end: el contenido del QR se procesa íntegramente en el cliente.
- **Dependencias del sistema**: Tailwind Play CDN exige conexión a Internet para cargar los estilos; la aplicación sigue siendo funcional con estilos inline de respaldo para los elementos críticos si el CDN no responde.
- **Sin cambios de API, base de datos ni migraciones**: proyecto greenfield, sin commits previos en `main`.

## Why

`qr-magic` ya está desplegado en Vercel, pero no hay forma de saber cuántas personas lo usan, desde dónde llegan ni qué páginas visitan. Añadir Google Tag Manager (contenedor `GTM-T6FDLJ4F`) da una base de medición gestionable desde la consola de GTM, sin tener que volver a tocar el código cada vez que se quiera añadir o ajustar una etiqueta (GA4, conversiones, etc.).

## What Changes

- Se añade a `index.html` el snippet oficial de Google Tag Manager para el contenedor `GTM-T6FDLJ4F`:
  - el `<script>` de carga asíncrona lo más arriba posible dentro de `<head>`;
  - el `<noscript><iframe>` de respaldo inmediatamente después de la apertura de `<body>`.
- Se inicializa `window.dataLayer` antes de cargar GTM, de modo que futuras etiquetas puedan leer eventos.
- Garantía explícita de privacidad: la aplicación **no** envía al `dataLayer` ni a GTM el contenido de los códigos QR (URLs, credenciales Wi-Fi, vCard, email) ni el logo subido. GTM solo mide la navegación/visita.
- Se ajusta el texto del badge del encabezado (`100 % local · sin registro · sin servidor`) si deja de ser preciso, para no prometer "nada sale del navegador" cuando ahora se cargan scripts de analítica de terceros.
- Sin cambios en `vercel.json`: actualmente no hay `Content-Security-Policy`, así que no hace falta permitir dominios de Google.

## Capabilities

### New Capabilities
- `site-analytics`: carga de Google Tag Manager (`GTM-T6FDLJ4F`) en la página, respaldo `noscript`, inicialización del `dataLayer` y garantía de que los datos introducidos por el usuario nunca se envían a la analítica.

### Modified Capabilities
<!-- No hay specs en openspec/specs/ todavía; el cambio de copy del badge no altera requisitos funcionales. -->

## Impact

- **Código**: `index.html` (bloque `<head>`, inicio de `<body>` y, posiblemente, el texto del badge del header).
- **Dependencias externas**: nueva carga en tiempo de ejecución de `https://www.googletagmanager.com/gtm.js` (y lo que el contenedor configure, p. ej. GA4).
- **Privacidad / legal**: el sitio pasa a usar analítica de terceros; puede requerir aviso de cookies/consentimiento según la audiencia (fuera del alcance de este cambio, se anota como riesgo).
- **Rendimiento**: script asíncrono; impacto mínimo en la carga inicial.
- **Despliegue**: sin cambios en la configuración de Vercel.

## ADDED Requirements

### Requirement: Carga de Google Tag Manager

La página SHALL cargar el contenedor de Google Tag Manager `GTM-T6FDLJ4F` mediante el snippet oficial: un `<script>` dentro de `<head>`, situado antes de cualquier otro `<script>`, que inicializa `window.dataLayer` y solicita de forma asíncrona `https://www.googletagmanager.com/gtm.js?id=GTM-T6FDLJ4F`.

#### Scenario: Carga en una visita normal

- **WHEN** un usuario abre la aplicación en un navegador con JavaScript habilitado
- **THEN** el navegador solicita `https://www.googletagmanager.com/gtm.js?id=GTM-T6FDLJ4F`
- **AND** `window.dataLayer` existe y contiene un evento con `event: 'gtm.js'`

#### Scenario: Script no bloqueante

- **WHEN** se carga la página
- **THEN** el script de GTM se inserta con el atributo `async` y no retrasa el render inicial ni la generación del QR

### Requirement: Respaldo noscript

La página SHALL incluir, como primer elemento dentro de `<body>`, un bloque `<noscript>` con un `<iframe>` oculto que apunte a `https://www.googletagmanager.com/ns.html?id=GTM-T6FDLJ4F`.

#### Scenario: JavaScript deshabilitado

- **WHEN** un usuario abre la página con JavaScript deshabilitado
- **THEN** el navegador carga el `<iframe>` de `ns.html?id=GTM-T6FDLJ4F` sin que sea visible ni ocupe espacio en el layout

### Requirement: Datos del usuario fuera de la analítica

La aplicación MUST NOT enviar a `window.dataLayer` ni a ningún endpoint de analítica el contenido introducido por el usuario: URL o texto, credenciales Wi-Fi, campos de vCard, campos de email, ni el logo subido.

#### Scenario: Generación de un QR Wi-Fi

- **WHEN** el usuario introduce un SSID y una contraseña Wi-Fi y genera el QR
- **THEN** ninguna entrada de `window.dataLayer` contiene el SSID ni la contraseña

#### Scenario: Subida de logo

- **WHEN** el usuario sube una imagen como logo
- **THEN** la imagen no se transmite a ningún dominio externo

### Requirement: Funcionamiento independiente de la analítica

La aplicación SHALL funcionar por completo aunque GTM no cargue (bloqueador de anuncios, red caída o dominio bloqueado).

#### Scenario: GTM bloqueado

- **WHEN** la petición a `www.googletagmanager.com` es bloqueada
- **THEN** la vista previa, la personalización y la descarga del QR siguen funcionando y no aparecen errores de la aplicación en consola

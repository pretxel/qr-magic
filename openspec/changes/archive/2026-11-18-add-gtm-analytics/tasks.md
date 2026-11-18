## 1. Integración de GTM

- [x] 1.1 Insertar el snippet `<script>` oficial de GTM (`GTM-T6FDLJ4F`) en `index.html`, dentro de `<head>`, justo después de las metas `charset`/`viewport` y antes de los `<script>` de Tailwind y `qr-code-styling`
- [x] 1.2 Insertar el bloque `<noscript><iframe src="https://www.googletagmanager.com/ns.html?id=GTM-T6FDLJ4F" ...></noscript>` como primer hijo de `<body>`
- [x] 1.3 Confirmar que ningún código de la app hace `dataLayer.push` con datos del formulario ni del logo

## 2. Copy de privacidad

- [x] 2.1 Actualizar el badge del header (`100 % local · sin registro · sin servidor`) a un texto que mantenga la promesa sobre los datos del QR sin negar la analítica (p. ej. `Tus datos no salen del navegador · sin registro`)
- [x] 2.2 Revisar el comentario del bloque `<script>` principal ("Ningún dato del...") y alinearlo con el nuevo comportamiento

## 3. Verificación

- [x] 3.1 Servir en local (`localhost`) y comprobar en DevTools → Network la petición a `gtm.js?id=GTM-T6FDLJ4F`
- [x] 3.2 Comprobar en consola que `window.dataLayer` contiene el evento `gtm.js` y ningún dato del usuario tras generar QRs de cada tipo (URL, Wi-Fi, vCard, Email)
- [x] 3.3 Bloquear `www.googletagmanager.com` en DevTools y verificar que la app sigue generando y descargando QRs sin errores
- [x] 3.4 Verificar que el `<iframe>` noscript no altera el layout (escritorio 1440 px y móvil 390 px)
- [ ] 3.5 Desplegar preview en Vercel y validar el contenedor con GTM Vista previa / Tag Assistant

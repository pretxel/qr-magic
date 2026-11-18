## Context

`qr-magic` es un único `index.html` estático (Tailwind vía CDN + `qr-code-styling`) desplegado en Vercel sin build step. Hoy no tiene analítica. El encabezado promete `100 % local · sin registro · sin servidor` y el código indica que ningún dato del usuario sale del navegador. `vercel.json` define cabeceras de seguridad (`X-Frame-Options: DENY`, `Referrer-Policy`, `Permissions-Policy`) pero **no** una `Content-Security-Policy`.

Se quiere integrar el contenedor de Google Tag Manager `GTM-T6FDLJ4F`.

## Goals / Non-Goals

**Goals:**
- Cargar GTM en todas las visitas con el snippet oficial, sin afectar al render ni a la generación del QR.
- Disponer de `window.dataLayer` listo para que el contenedor publique etiquetas (GA4, etc.) desde la consola de GTM.
- Mantener la promesa de privacidad sobre el contenido del QR: nada de lo que el usuario escribe o sube llega a la analítica.

**Non-Goals:**
- Configurar etiquetas, activadores o GA4 dentro del contenedor (se hace en la consola de GTM).
- Banner de consentimiento de cookies / Consent Mode v2 (se anota como pregunta abierta).
- Eventos personalizados de producto (p. ej. "QR descargado"); podrán añadirse en un cambio posterior.

## Decisions

1. **Snippet oficial de GTM, inline en `index.html`.**
   - `<script>` en `<head>`, lo más arriba posible (justo después de `<meta charset>`/`viewport`), que inicializa `window.dataLayer`, empuja `{'gtm.start': ..., event: 'gtm.js'}` e inyecta `https://www.googletagmanager.com/gtm.js?id=GTM-T6FDLJ4F` con `async`.
   - `<noscript><iframe src="https://www.googletagmanager.com/ns.html?id=GTM-T6FDLJ4F" height="0" width="0" style="display:none;visibility:hidden"></iframe></noscript>` como primer hijo de `<body>`.
   - *Alternativa descartada*: cargar `gtag.js` (GA4) directamente. GTM permite gestionar etiquetas sin redeploy, que es lo pedido.
   - *Alternativa descartada*: archivo JS externo propio. Añade una petición y no aporta nada en un sitio de un solo archivo.

2. **ID del contenedor hardcodeado.** Sitio estático sin build ni variables de entorno; el ID de GTM es público por naturaleza. Se declara una sola vez dentro del snippet.

3. **Sin cambios en `vercel.json`.** No hay CSP que bloquee `googletagmanager.com`. `X-Frame-Options: DENY` solo afecta a que *nuestra* página sea enmarcada, no al `<iframe>` de `ns.html` que nosotros incrustamos. Si en el futuro se añade una CSP, deberá permitir `www.googletagmanager.com` (y los dominios de las etiquetas que publique el contenedor).

4. **Frontera de privacidad.** El código de la app no hará `dataLayer.push` con datos del formulario (URL, SSID/contraseña Wi-Fi, vCard, email, logo). Solo GTM empuja sus eventos automáticos. Las etiquetas del contenedor no deben usar activadores que lean campos de formulario (documentado como guía para quien gestione el contenedor).

5. **Ajuste de copy del badge.** "sin servidor" sigue siendo cierto (no hay backend), pero "100 % local" ya no describe la página completa. Se propone cambiar a algo como `Tus datos no salen del navegador · sin registro`, que mantiene la promesa real (los datos del QR) sin negar la analítica.

## Risks / Trade-offs

- [Bloqueadores de anuncios impiden cargar GTM] → Aceptado; la app funciona igual porque nada depende de GTM.
- [GTM inyecta scripts arbitrarios configurados en la consola] → Restringir acceso al contenedor; revisar cada publicación del contenedor.
- [Requisitos legales de consentimiento (RGPD/ePrivacy) si hay usuarios en la UE] → Fuera de alcance; ver Open Questions.
- [Impacto en rendimiento] → Script `async`; GTM no bloquea el render ni la inicialización de `qr-code-styling`.
- [Una etiqueta del contenedor captura valores de inputs] → Guía de privacidad (Decisión 4) y no exponer datos en `dataLayer`.

## Migration Plan

1. Insertar snippets en `index.html` y ajustar el badge.
2. Probar en local (`localhost`) que se solicita `gtm.js?id=GTM-T6FDLJ4F` y que `window.dataLayer` contiene `gtm.js`.
3. Desplegar preview en Vercel y verificar con el modo Vista previa de GTM (Tag Assistant).
4. Promocionar a producción.
5. **Rollback**: eliminar los dos snippets y redeplegar (o pausar/despublicar el contenedor desde la consola de GTM sin tocar código).

## Open Questions

- ¿Se necesita banner de consentimiento / Consent Mode v2 antes de activar GA4 en el contenedor?
- ¿Se quieren eventos personalizados (descargar PNG/SVG, copiar al portapapeles) en un cambio posterior?

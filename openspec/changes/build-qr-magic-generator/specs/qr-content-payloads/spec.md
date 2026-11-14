## ADDED Requirements

### Requirement: Pestañas de tipo de contenido

La aplicación SHALL presentar cuatro pestañas de tipo de contenido — «URL / Texto», «Wi-Fi», «vCard (Contacto)» y «Email» — con exactamente una activa en todo momento. La aplicación SHALL numerar las pestañas con el patrón `role="tablist"` / `role="tab"` / `role="tabpanel"` y SHALL mantener `aria-selected` sincronizado con la pestaña visible.

#### Scenario: Pestaña inicial

- **WHEN** el usuario abre la aplicación por primera vez
- **THEN** la pestaña «URL / Texto» aparece activa, su panel de campos es visible y los paneles de las demás pestañas están ocultos

#### Scenario: Cambio de pestaña

- **WHEN** el usuario pulsa la pestaña «Wi-Fi»
- **THEN** la pestaña «Wi-Fi» queda marcada como activa, el panel de campos de Wi-Fi se muestra y el panel de «URL / Texto» se oculta

#### Scenario: Navegación con teclado

- **WHEN** el usuario navega por las pestañas usando las flechas izquierda/derecha
- **THEN** el foco se mueve a la pestaña siguiente y esa pestaña se activa

### Requirement: Contenido predeterminado de la pestaña URL / Texto

La pestaña «URL / Texto» SHALL exponer un campo de texto libre cuyo valor predeterminado SHALL ser `https://qr-magic.app`.

#### Scenario: Valor inicial

- **WHEN** la aplicación se carga sin interacción previa
- **THEN** el campo de la pestaña «URL / Texto» contiene el texto `https://qr-magic.app` y la vista previa muestra un QR con ese contenido

#### Scenario: Texto arbitrario

- **WHEN** el usuario escribe `Hola qr-magic` en el campo
- **THEN** la vista previa se regenera con el payload exacto `Hola qr-magic`

### Requirement: Payload Wi-Fi

La aplicación SHALL construir el payload Wi-Fi con `WIFI:T:<tipo>;S:<ssid>[;P:<contraseña>][;H:true];;`, usando `T:WPA` para `WPA/WPA2`, `T:WEP` para `WEP` y `T:nopass` para `Ninguna`. El campo `P` SHALL omitirse únicamente para `T:nopass`; las redes `WPA/WPA2` y `WEP` SHALL requerir contraseña no vacía. El campo `H:true` SHALL emitirse únicamente cuando la casilla «Red oculta» está marcada. Los valores SHALL escapar con backslash los caracteres `\`, `;`, `,`, `:` y `"`.

#### Scenario: Red WPA con contraseña

- **WHEN** el usuario introduce SSID `MiRed`, contraseña `clave123`, tipo `WPA/WPA2` y deja sin marcar «Red oculta»
- **THEN** el payload es `WIFI:T:WPA;S:MiRed;P:clave123;;`

#### Scenario: Red sin contraseña

- **WHEN** el usuario introduce SSID `RedAbierta`, deja la contraseña vacía y selecciona el tipo `Ninguna`
- **THEN** el payload es `WIFI:T:nopass;S:RedAbierta;;` y no contiene el campo `P`

#### Scenario: Contraseña obligatoria para red protegida

- **WHEN** el usuario selecciona `WPA/WPA2` o `WEP` y deja la contraseña vacía
- **THEN** la contraseña se marca como obligatoria, se muestra un error en español bajo ese campo enlazado mediante `aria-describedby`, y no se genera un payload Wi-Fi ni un QR

#### Scenario: Red oculta

- **WHEN** el usuario marca la casilla «Red oculta»
- **THEN** el payload contiene `H:true`, por ejemplo `WIFI:T:WPA;S:MiRed;P:clave123;H:true;;`

#### Scenario: Caracteres especiales en el SSID

- **WHEN** el usuario introduce el SSID `Café;Red:5G`
- **THEN** el payload escapa los caracteres especiales con backslash y el valor original es recuperable al deshacer el escape

### Requirement: Payload vCard

La aplicación SHALL construir un vCard válido con `BEGIN:VCARD`, `VERSION:3.0` y `END:VCARD`, usando saltos de línea CRLF y escapando con backslash las comas, punto y comas y barras invertidas de los valores. SHALL incluir `N:<apellidos>;<nombre>` cuando haya nombre o apellidos, e `FN:<nombre completo>` cuando haya nombre o apellidos. SHALL incluir `ORG:<empresa>`, `TITLE:<cargo>`, `TEL;TYPE=CELL:<teléfono>`, `EMAIL:<correo>` y `URL:<sitio web>` cuando sus valores no estén vacíos; SHALL omitir esos campos si están vacíos. Un vCard con solo correo es válido y no requiere `N` ni `FN`.

#### Scenario: Contacto sin nombre

- **WHEN** el usuario introduce solo `ana@example.com`
- **THEN** el payload incluye `EMAIL:ana@example.com`, omite `N:` y `FN:`, y es válido

#### Scenario: Contacto completo

- **WHEN** el usuario introduce nombre `Ana`, apellidos `García`, teléfono `+34600000000`, correo `ana@example.com`, empresa `Acme` y cargo `CEO`
- **THEN** el payload comienza por `BEGIN:VCARD`, contiene `N:García;Ana`, `FN:Ana García`, `ORG:Acme`, `TITLE:CEO`, `TEL;TYPE=CELL:+34600000000`, `EMAIL:ana@example.com` y termina en `END:VCARD`

#### Scenario: Campos opcionales vacíos

- **WHEN** el usuario deja vacíos empresa, cargo y sitio web
- **THEN** el payload no contiene las líneas `ORG:`, `TITLE:` ni `URL:`

#### Scenario: Separador de campos

- **WHEN** el nombre contiene una coma, por ejemplo `Ana, Pérez`
- **THEN** el valor aparece escapado con backslash y la estructura del vCard no se rompe

### Requirement: Payload Email

La aplicación SHALL construir el payload de email con el formato `mailto:<destinatario>?subject=<asunto>&body=<cuerpo>`, aplicando codificación percent a los valores de asunto y cuerpo, y omitiendo `subject` y `body` cuando estén vacíos.

#### Scenario: Email completo

- **WHEN** el usuario introduce destinatario `hola@example.com`, asunto `Presupuesto` y cuerpo `Adjunto el presupuesto 2026.`
- **THEN** el payload es `mailto:hola@example.com?subject=Presupuesto&body=Adjunto%20el%20presupuesto%202026.`

#### Scenario: Solo destinatario

- **WHEN** el usuario deja vacíos el asunto y el cuerpo
- **THEN** el payload es `mailto:hola@example.com` sin signos de interrogación

### Requirement: Validación de campos de contenido

Cada campo SHALL mostrar un mensaje de validación en español cuando su valor sea inválido, y la aplicación SHALL bloquear el renderizado del QR y toda exportación cuando el payload de la pestaña activa sea inválido, mostrando en su lugar un estado vacío que explique qué falta.

#### Scenario: SSID de Wi-Fi obligatorio

- **WHEN** el usuario borra el SSID en la pestaña «Wi-Fi»
- **THEN** aparece el mensaje de error bajo el campo indicando que el nombre de la red es obligatorio y la vista previa muestra un estado vacío en lugar de un QR

#### Scenario: Exportación bloqueada con contenido inválido

- **WHEN** el payload de la pestaña activa es inválido y el usuario intenta descargar o copiar el QR
- **THEN** no se crea ni exporta una imagen y la interfaz mantiene el estado vacío con el mensaje de validación

#### Scenario: Destinatario de email inválido

- **WHEN** el usuario escribe `no-es-un-correo` en el destinatario de email
- **THEN** el campo se marca como error con un mensaje en español y la vista previa no muestra un QR

#### Scenario: vCard con al menos nombre o correo

- **WHEN** el usuario deja vacíos todos los campos de la pestaña «vCard»
- **THEN** la vista previa muestra un estado vacío indicando que se necesita al menos un nombre o un correo

### Requirement: Actualización en vivo de la vista previa

La aplicación SHALL regenerar la vista previa automáticamente ante cualquier cambio en el contenido o en las opciones de apariencia, sin requerir una acción explícita de «Generar».

#### Scenario: Cambio de contenido

- **WHEN** el usuario modifica el texto de la pestaña activa
- **THEN** el QR de la vista previa se actualiza en un plazo razonable sin pulsar ningún botón

#### Scenario: Cambio de estilo

- **WHEN** el usuario cambia la forma de los puntos del QR
- **THEN** la vista previa refleja el nuevo estilo de módulo

### Requirement: Visualización del payload generado

La aplicación SHALL mostrar, junto a la vista previa, el payload de texto exacto que se está codificando, actualizado junto con la vista previa.

#### Scenario: Sincronización del volcado

- **WHEN** el usuario edita el SSID de la red Wi-Fi
- **THEN** el texto de payload mostrado refleja el nuevo SSID y coincide con el contenido codificado en el QR

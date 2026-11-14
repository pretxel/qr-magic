## ADDED Requirements

### Requirement: Selector de resolución de exportación

La aplicación SHALL ofrecer un selector de resolución con estos valores: 300×300, 500×500, 800×800, 1000×1000, 1500×1500 y 2000×2000 px. SHALL exportar el QR a la resolución seleccionada.

#### Scenario: Resolución mínima

- **WHEN** el usuario selecciona 300×300 y descarga el PNG
- **THEN** el fichero PNG descargado tiene 300 px de ancho y 300 px de alto

#### Scenario: Resolución máxima

- **WHEN** el usuario selecciona 2000×2000 y descarga el PNG
- **THEN** el fichero PNG descargado tiene 2000 px de ancho y 2000 px de alto

### Requirement: Descarga en PNG

La aplicación SHALL ofrecer un botón de descarga del QR en PNG y SHALL generar el fichero a partir del estado actual de la vista previa, respetando el tamaño, el logo y el fondo configurados.

#### Scenario: Descarga en PNG

- **WHEN** el usuario pulsa el botón de descarga PNG
- **THEN** el navegador descarga un fichero PNG con el QR tal y como aparece en la vista previa

#### Scenario: Respeto de la configuración

- **WHEN** el usuario ha configurado un logo y un fondo transparente y descarga el PNG
- **THEN** el PNG descargado contiene el logo y el fondo del QR es transparente

### Requirement: Descarga en SVG

La aplicación SHALL ofrecer un botón de descarga del QR en SVG y SHALL generar un fichero vectorial que conserve las formas, los degradados y el logo del QR.

#### Scenario: Descarga vectorial

- **WHEN** el usuario pulsa el botón de descarga SVG
- **THEN** el navegador descarga un fichero SVG que puede escalarse sin pérdida de nitidez

#### Scenario: Vectorial con degradado

- **WHEN** el QR usa un degradado lineal y el usuario descarga el SVG
- **THEN** el SVG conserva la definición de degradado en lugar de aplanarla a un color sólido

### Requirement: Descarga en JPEG

La aplicación SHALL ofrecer un botón de descarga del QR en JPEG. Si el fondo del QR es transparente, la aplicación SHALL exportar el JPEG sobre fondo blanco e informar al usuario de ello, ya que el formato JPEG no soporta transparencia.

#### Scenario: Descarga en JPEG

- **WHEN** el usuario pulsa el botón de descarga JPEG con fondo opaco
- **THEN** el navegador descarga un fichero JPEG con el QR sobre su fondo configurado

#### Scenario: JPEG con fondo transparente

- **WHEN** el usuario tiene el fondo transparente y pulsa el botón de descarga JPEG
- **THEN** el fichero JPEG se genera con fondo blanco y la aplicación informa de que el fondo transparente no es compatible con JPEG

### Requirement: Copia de la imagen al portapapeles

La aplicación SHALL ofrecer un botón para copiar al portapapeles un PNG del QR en la resolución seleccionada. SHALL deshabilitar el botón solo si `navigator.clipboard.write` o `ClipboardItem` no están disponibles, mostrando una explicación en español y ofreciendo la descarga PNG como alternativa. Si la API está disponible pero la solicitud falla (por ejemplo, permiso denegado o contexto no seguro), SHALL mostrar un mensaje en español y mantener disponible la descarga PNG; no SHALL presuponer el resultado del permiso antes de intentar copiar.

#### Scenario: Copia con éxito

- **WHEN** el usuario pulsa el botón de copiar en un contexto seguro y concede el permiso
- **THEN** el portapapeles del sistema contiene la imagen PNG del QR

#### Scenario: Portapapeles no disponible

- **WHEN** `navigator.clipboard.write` o `ClipboardItem` no están disponibles
- **THEN** el botón aparece deshabilitado con una explicación en español que indica que la función requiere un contexto compatible, como HTTPS o localhost

#### Scenario: Permiso de portapapeles denegado

- **WHEN** el usuario intenta copiar y la solicitud falla por permiso denegado
- **THEN** la aplicación informa del fallo en español, conserva habilitada la descarga PNG y no afirma que el botón estaba deshabilitado previamente

### Requirement: Nombres de archivo con la marca qr-magic

La aplicación SHALL nombrar los ficheros exportados con la marca `qr-magic`, concretamente `qr-magic.png`, `qr-magic.svg` y `qr-magic.jpeg`.

#### Scenario: Nombre del fichero PNG

- **WHEN** el usuario descarga el QR en PNG
- **THEN** el nombre del fichero descargado es `qr-magic.png`

#### Scenario: Nombre del fichero SVG

- **WHEN** el usuario descarga el QR en SVG
- **THEN** el nombre del fichero descargado es `qr-magic.svg`

#### Scenario: Nombre del fichero JPEG

- **WHEN** el usuario descarga el QR en JPEG
- **THEN** el nombre del fichero descargado es `qr-magic.jpeg`

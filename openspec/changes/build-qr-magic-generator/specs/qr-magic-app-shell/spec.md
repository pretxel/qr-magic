## ADDED Requirements

### Requirement: Layout responsive de dos columnas

La aplicación SHALL presentar en pantallas de al menos 1024 px de ancho un layout de dos columnas: una columna izquierda con los controles y una columna derecha con la vista previa. Por debajo de 1024 px SHALL apilarse en una única columna con los controles primero y la vista previa después.

#### Scenario: Escritorio

- **WHEN** el usuario abre la aplicación en una pantalla de 1440 px de ancho
- **THEN** los controles ocupan la columna izquierda y la vista previa la columna derecha, en paralelo

#### Scenario: Móvil

- **WHEN** el usuario abre la aplicación en una pantalla de 390 px de ancho
- **THEN** el contenido se apila en una sola columna sin desbordamiento horizontal

### Requirement: Vista previa fija (sticky)

La columna de la vista previa SHALL permanecer visible al desplazarse por los controles en pantallas de escritorio.

#### Scenario: Desplazamiento largo

- **WHEN** el usuario hace scroll hacia abajo en la columna de controles
- **THEN** la vista previa sigue visible en la pantalla

### Requirement: Fondo de cuadrícula en la vista previa

La vista previa SHALL mostrarse sobre un fondo de patrón de cuadrícula que permita apreciar las zonas transparentes del QR.

#### Scenario: Fondo transparente visible

- **WHEN** el QR tiene el fondo transparente
- **THEN** la cuadrícula del fondo de la vista previa se ve a través del QR

### Requirement: Estética de interfaz SaaS

La interfaz SHALL seguir una estética moderna tipo SaaS: fondo claro, tarjetas con bordes suaves y sombras sutiles, tipografía limpia (Inter), y acentos en índigo y violeta aplicados a elementos activos como pestañas, botones principales y controles.

#### Scenario: Tarjetas de control

- **WHEN** el usuario recorre los controles
- **THEN** cada bloque de controles está contenido en una tarjeta con bordes redondeados y sombra suave

#### Scenario: Elementos activos

- **WHEN** una pestaña o un botón principal está activo
- **THEN** se destaca con la paleta índigo/violeta de la aplicación

### Requirement: Interfaz íntegramente en español

Todos los textos visibles de la interfaz —etiquetas, botones, mensajes de error, avisos, placeholders y textos de ayuda— SHALL estar redactados en español.

#### Scenario: Mensajes del sistema

- **WHEN** la aplicación muestra un error de validación, un aviso de contraste o un error de carga
- **THEN** el mensaje está en español

### Requirement: Branding qr-magic

El nombre `qr-magic` SHALL aparecer en el título de la página, en el encabezado visible, en los placeholders y textos de ayuda, y en los nombres de los ficheros exportados.

#### Scenario: Título y encabezado

- **WHEN** el usuario abre la aplicación
- **THEN** el título de la pestaña del navegador y el encabezado visible incluyen el nombre `qr-magic`

#### Scenario: Placeholder

- **WHEN** el usuario enfoca el campo de la pestaña «URL / Texto»
- **THEN** el texto de ayuda o placeholder hace referencia a `qr-magic`

### Requirement: Accesibilidad de los controles

Todos los controles SHALL tener una etiqueta asociada, los mensajes de validación SHALL estar enlazados al campo correspondiente y los elementos interactivos SHALL mostrar un indicador de foco visible. Los interruptores y casillas SHALL ser operables con teclado.

#### Scenario: Navegación por teclado

- **WHEN** el usuario navega con la tecla Tab por la interfaz
- **THEN** el foco es visible en cada control y el control enfocado se anuncia con su etiqueta

#### Scenario: Asociación de mensajes de error

- **WHEN** un campo muestra un mensaje de validación
- **THEN** ese mensaje está enlazado programáticamente al campo mediante `aria-describedby`

### Requirement: Dependencias externas y degradación

La aplicación SHALL cargar Tailwind CSS y la librería `qr-code-styling` desde CDN, sin servidor propio ni proceso de compilación. Si la librería de generación no está disponible, la aplicación SHALL mostrar un mensaje de error en español en lugar de fallar en silencio.

#### Scenario: Aplicación sin conexión tras la primera carga

- **WHEN** el navegador no puede acceder a la red y la librería no está cargada
- **THEN** la aplicación muestra un aviso en español indicando que hace falta conexión para cargar la librería de generación de QR

#### Scenario: Apertura directa del fichero

- **WHEN** el usuario abre `index.html` con doble clic desde el disco, sin servidor web
- **THEN** la aplicación se carga y funciona sin errores de CORS

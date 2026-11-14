## ADDED Requirements

### Requirement: Plantillas rápidas de estilo

La aplicación SHALL ofrecer botones de plantilla de un clic que apliquen un conjunto completo de opciones de apariencia a la vista previa. La aplicación SHALL incluir al menos las plantillas «Clásico», «Moderno Azul», «Neón Oscuro», «Elegante Dorado» y «Minimalista».

#### Scenario: Aplicación de una plantilla

- **WHEN** el usuario pulsa la plantilla «Neón Oscuro»
- **THEN** la vista previa adopta simultáneamente el color de puntos, el estilo de esquinas y el fondo definidos por esa plantilla

#### Scenario: Sincronización de los controles

- **WHEN** el usuario ha aplicado una plantilla y después modifica un control individual
- **THEN** ese control refleja el valor de la plantilla y su modificación manual se aplica sobre el resto de valores de la plantilla

### Requirement: Estilo de los módulos de datos

La aplicación SHALL ofrecer un selector de forma de módulo con los valores `square`, `dots`, `rounded`, `extra-rounded`, `classy` y `classy-rounded`, y SHALL aplicarlo a los módulos de datos del QR.

#### Scenario: Selección de forma

- **WHEN** el usuario selecciona `dots`
- **THEN** los módulos de datos del QR se renderizan como círculos

#### Scenario: Forma classy

- **WHEN** el usuario selecciona `classy-rounded`
- **THEN** los módulos de datos adoptan la forma clásica con esquinas redondeadas

### Requirement: Color de los módulos de datos

La aplicación SHALL permitir elegir el color de los módulos de datos mediante un selector de color, y SHALL ofrecer un modo alternativo de degradado con dos colores y un tipo —lineal o radial— más un ángulo de rotación.

#### Scenario: Color sólido

- **WHEN** el usuario elige el modo «Sólido» y un color de puntos
- **THEN** todos los módulos de datos se renderizan con ese color

#### Scenario: Degradado lineal con ángulo

- **WHEN** el usuario elige el modo «Degradado», el tipo `lineal`, dos colores y un ángulo de 45°
- **THEN** los módulos de datos se renderizan con una transición lineal entre ambos colores orientada según ese ángulo

#### Scenario: Degradado radial

- **WHEN** el usuario elige el modo «Degradado» y el tipo `radial`
- **THEN** los módulos de datos se renderizan con una transición radial entre los dos colores

#### Scenario: Interpolación de los puntos de color

- **WHEN** el usuario define un degradado
- **THEN** la transición se realiza de forma continua entre los dos colores elegidos, sin bandas visibles

### Requirement: Estilo del marco exterior de las esquinas

La aplicación SHALL permitir configurar de forma independiente el estilo del marco exterior de las esquinas del QR, con las opciones `square`, `extra-rounded` y `dot`, y SHALL ofrecer un selector de color propio para ese marco.

#### Scenario: Marco redondeado con color propio

- **WHEN** el usuario selecciona `extra-rounded` para el marco exterior y un color distinto al de los puntos
- **THEN** el marco exterior de las tres esquinas se renderiza con esa forma y ese color, mientras los módulos de datos mantienen su color

### Requirement: Estilo del punto interior de las esquinas

La aplicación SHALL permitir configurar de forma independiente el punto interior de las esquinas del QR, con las opciones `square` y `dot`, y SHALL ofrecer un selector de color propio para ese punto.

#### Scenario: Punto interior circular con color propio

- **WHEN** el usuario selecciona `dot` para el punto interior y un color distinto al del marco exterior
- **THEN** el punto interior de las tres esquinas se renderiza como círculo con ese color

### Requirement: Configuración del fondo

La aplicación SHALL ofrecer un selector de color de fondo y una opción para activar el fondo transparente. Con el fondo transparente activado, el fondo del QR SHALL ser transparente y los controles de color de fondo SHALL quedar deshabilitados.

#### Scenario: Fondo de color

- **WHEN** el usuario elige un color de fondo distinto del blanco
- **THEN** el fondo del QR se renderiza con ese color

#### Scenario: Fondo transparente

- **WHEN** el usuario activa el interruptor «Fondo transparente»
- **THEN** el QR se renderiza sin fondo y el selector de color de fondo queda deshabilitado

#### Scenario: Desactivar el fondo transparente

- **WHEN** el usuario desactiva el interruptor «Fondo transparente»
- **THEN** el fondo vuelve a pintarse con el color seleccionado y el selector vuelve a estar habilitado

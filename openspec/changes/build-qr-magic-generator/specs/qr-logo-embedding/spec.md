## ADDED Requirements

### Requirement: Subida de logo mediante selector de archivo

La aplicación SHALL permitir cargar un logo desde el dispositivo mediante un selector de archivos, aceptando imágenes PNG, JPG y SVG. La aplicación SHALL validar el tipo MIME y el tamaño del fichero, y SHALL informar en español cuando el fichero no sea válido.

#### Scenario: Carga válida

- **WHEN** el usuario selecciona un fichero PNG válido
- **THEN** el logo aparece centrado en el QR y la vista previa muestra una miniatura del fichero cargado con su nombre

#### Scenario: Tipo de fichero no admitido

- **WHEN** el usuario intenta cargar un fichero que no es PNG, JPG ni SVG
- **THEN** la aplicación muestra un mensaje de error en español y no modifica el logo actual

#### Scenario: Fichero demasiado grande

- **WHEN** el usuario carga un fichero que supera el tamaño máximo admitido
- **THEN** la aplicación muestra un mensaje de error en español y no modifica el logo actual

### Requirement: Subida de logo mediante arrastrar y soltar

La aplicación SHALL permitir arrastrar un fichero de imagen sobre una zona de arrastre en la zona de controles, con realce visual durante el arrastre, y SHALL procesar el fichero soltado con la misma validación que el selector de archivos.

#### Scenario: Arrastre sobre la zona

- **WHEN** el usuario arrastra un fichero de imagen sobre la zona de arrastre
- **THEN** la zona cambia de aspecto para indicar que puede soltar el fichero

#### Scenario: Soltar un fichero válido

- **WHEN** el usuario suelta un fichero PNG válido sobre la zona
- **THEN** el logo se aplica al QR exactamente igual que si se hubiera elegido con el selector de archivos

#### Scenario: Soltar un fichero no válido

- **WHEN** el usuario suelta sobre la zona un fichero de tipo no admitido
- **THEN** la aplicación muestra un mensaje de error en español y el logo no cambia

### Requirement: Galería de iconos SVG integrados

La aplicación SHALL incluir una galería de iconos SVG embebidos en el propio archivo, listos para usar como logo, que incluya al menos los iconos de Wi-Fi, ubicación, estrella y corazón. Al elegir un icono, este SHALL aplicarse como logo del QR.

#### Scenario: Selección de un icono

- **WHEN** el usuario pulsa el icono de Wi-Fi en la galería
- **THEN** ese icono aparece como logo centrado en el QR

#### Scenario: Sustitución de un logo cargado por un icono

- **WHEN** el usuario ha cargado un fichero propio y después pulsa un icono de la galería
- **THEN** el icono sustituye al fichero cargado como logo activo

### Requirement: Controles de tamaño y margen del logo

La aplicación SHALL ofrecer un control deslizante para el tamaño del logo dentro del rango 0,10–0,40 del ancho del QR y un control deslizante para el margen alrededor del logo en píxeles. Ambos valores SHALL reflejarse de inmediato en la vista previa.

#### Scenario: Ajuste de tamaño

- **WHEN** el usuario arrastra el control de tamaño al máximo
- **THEN** el logo ocupa el 40 % del ancho del QR

#### Scenario: Ajuste de margen

- **WHEN** el usuario aumenta el control de margen
- **THEN** el logo se separa del resto de módulos y el espacio de separación aumenta

### Requirement: Nivel de corrección de errores

La aplicación SHALL ofrecer un selector de nivel de corrección de errores con los valores `L`, `M`, `Q` y `H`. Al añadir un logo, la aplicación SHALL activar automáticamente el nivel `H`, y si el usuario reduce el nivel manualmente con un logo presente, la aplicación SHALL advertirle en español del riesgo para la legibilidad.

#### Scenario: Nivel por defecto sin logo

- **WHEN** la aplicación se carga sin logo
- **THEN** el nivel de corrección de errores activo es `Q`

#### Scenario: Activación automática de H con logo

- **WHEN** el usuario añade un logo o un icono
- **THEN** el nivel de corrección de errores cambia automáticamente a `H`

#### Scenario: Reducción manual del nivel con logo

- **WHEN** el usuario tiene un logo activo y cambia el nivel de corrección de errores a `L`
- **THEN** la aplicación muestra un aviso indicando que el logo puede dejar de ser legible con ese nivel

#### Scenario: Cambio de nivel sin logo

- **WHEN** el usuario cambia el nivel de corrección de errores a `M` sin tener logo
- **THEN** el QR se regenera con ese nivel y no se muestra ningún aviso sobre el logo

## ADDED Requirements

### Requirement: Cálculo automático del contraste

La aplicación SHALL calcular una relación orientativa de contraste entre el fondo y los módulos de datos y las áreas de las esquinas del QR usando luminancia relativa según WCAG 2.1. Para degradados SHALL interpolar los colores de inicio y fin en sRGB y evaluar 101 posiciones equidistantes, incluidos los extremos, contra el fondo; el resultado SHALL ser el peor contraste medido entre todos esos colores y los colores configurados para las esquinas. SHALL recalcularlo automáticamente ante cualquier cambio de color, degradado o fondo. El indicador SHALL aclarar que esta medición no garantiza escaneabilidad, que también depende del tamaño de módulos, zona silenciosa, corrección de errores, calidad de impresión y soporte.

#### Scenario: Colores sólidos

- **WHEN** los puntos son negros sobre fondo blanco
- **THEN** la relación de contraste calculada es 21:1

#### Scenario: Degradado

- **WHEN** los puntos usan un degradado
- **THEN** la aplicación evalúa 101 posiciones equidistantes de la interpolación sRGB entre ambos colores, compara cada posición contra el fondo y muestra el peor contraste medido

#### Scenario: Fondo transparente

- **WHEN** el fondo del QR es transparente
- **THEN** la aplicación calcula el contraste tomando el blanco como fondo efectivo y advierte de que el resultado real depende del soporte donde se use el QR

### Requirement: Indicador orientativo de contraste en la vista previa

La aplicación SHALL mostrar junto a la vista previa un indicador orientativo de contraste, con la relación numérica y una etiqueta cualitativa: «Excelente» para ≥7:1, «Bueno» para ≥4,5:1, «Bajo» para ≥3:1 y «Contraste bajo» para <3:1. El indicador SHALL identificar que estos valores describen contraste y no garantizan escaneabilidad.

#### Scenario: Contraste excelente

- **WHEN** la relación de contraste es de 21:1
- **THEN** el indicador muestra la etiqueta «Excelente» con un tratamiento visual de éxito

#### Scenario: Contraste insuficiente

- **WHEN** la relación de contraste es inferior a 3:1
- **THEN** el indicador muestra la etiqueta «Contraste bajo» con un tratamiento visual de error

### Requirement: Alerta visual por contraste insuficiente

Cuando la relación de contraste sea inferior a 4.5:1, la aplicación SHALL mostrar una alerta visual destacada en español con una recomendación accionable para oscurecer los módulos o aclarar el fondo. Para ratios de 3:1 o superiores la alerta SHALL usar un tratamiento de advertencia; por debajo de 3:1 SHALL usar un tratamiento de error. El mensaje SHALL describir el contraste medido como orientativo y no prometer ni descartar que el QR pueda escanearse.

#### Scenario: Aviso de baja legibilidad

- **WHEN** el usuario fija un color de puntos gris medio sobre fondo blanco
- **THEN** aparece una alerta visible de advertencia que indica que el contraste medido es bajo, aclara que no garantiza el resultado de escaneo y sugiere cómo oscurecer los puntos o aclarar el fondo

#### Scenario: Alerta de error para contraste muy bajo

- **WHEN** la relación es inferior a 3:1
- **THEN** la alerta usa el tratamiento visual de error y conserva la recomendación de corregir el contraste

#### Scenario: Alerta que desaparece al corregir

- **WHEN** el usuario corrige los colores y la relación de contraste vuelve a ser de 4.5:1 o superior
- **THEN** la alerta de contraste desaparece de la interfaz

### Requirement: Consistencia entre indicador y configuración mostrada

El indicador de contraste SHALL reflejar en todo momento los valores de color y fondo activos en la vista previa, incluidos los colores seleccionados mediante los presets.

#### Scenario: Aplicación de un preset

- **WHEN** el usuario aplica un preset con contraste reducido, incluidos colores propios de esquinas
- **THEN** el indicador se actualiza inmediatamente usando el peor contraste de módulos de datos y esquinas, sin necesidad de interactuar con ningún otro control

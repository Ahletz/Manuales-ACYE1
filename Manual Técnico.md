# Manual Técnico del Código ARM Assembly

## Introducción

Este manual técnico describe en detalle el código ensamblador ARM proporcionado. El código implementa un programa que simula una especie de hoja de cálculo con operaciones aritméticas y lógicas, permite importar datos desde un archivo CSV, manipular valores de celdas y realizar diversas operaciones como suma, resta, multiplicación, división y operaciones lógicas.

El objetivo de este manual es explicar la estructura del programa, las secciones de datos y código, las macros utilizadas y las funciones principales, para facilitar la comprensión y mantenimiento del código.

## Tabla de Contenidos

- [Sección de Datos](#sección-de-datos)
  - [Datos Inicializados (.data)](#datos-inicializados-data)
  - [Datos No Inicializados (.bss)](#datos-no-inicializados-bss)
- [Sección de Código (.text)](#sección-de-código-text)
  - [Macros](#macros)
  - [Funciones](#funciones)
    - [atoi2](#atoi2)
    - [proc_import](#proc_import)
    - [import_data](#import_data)
    - [readCSV](#readcsv)
    - [openFile](#openfile)
    - [closeFile](#closefile)
    - [imprimirCeldas](#imprimirceldas)
    - [limpiarParametro](#limpiarparametro)
    - [posicionCelda](#posicioncelda)
    - [atoi](#atoi)
    - [verificarParametro](#verificarparametro)
    - [verificarComando](#verificarcomando)
    - [verificarPalabraIntermedia](#verificarpalabra-intermedia)
    - [getComando](#getcomando)
    - [getNumero](#getnumero)
    - [itoa](#itoa)
    - [parametroNumero](#parametronumero)
    - [posicionACelda](#posicionacelda)
    - [llenarFilaColumna](#llenarfilacolumna)
  - [Punto de Entrada (\_start)](#punto-de-entrada-_start)
- [Flujo del Programa](#flujo-del-programa)
- [Conclusión](#conclusión)

## Sección de Datos

El programa define varias secciones de datos, incluyendo datos inicializados y no inicializados. Estas secciones se utilizan para almacenar cadenas, variables y estructuras necesarias para el funcionamiento del programa.

### Datos Inicializados (.data)

En la sección `.data`, se definen las cadenas y variables con valores iniciales. A continuación, se describen los principales elementos:

#### Mensajes y Cadenas de Texto

- **`clear`**: Cadena para limpiar la pantalla usando secuencias de escape ANSI.

  ```assembly
  clear:
      .asciz "\x1B[2J\x1B[H"
      lenClear = . - clear
  ```

- **`encabezado`**: Cadena que contiene el encabezado con información institucional.

  ```assembly
  encabezado:
      .asciz "===================================================\n"
      .asciz "UNIVERSIDADA DE SAN CARLOS DE GUATEMALA\n"
      .asciz "FACULTAD DE INGENIERIA\n"
      .asciz "ESCUELA DE CIENCIAS Y SISTEMAS\n"
      .asciz "ARQUITECTURA DE COMPUTADORES Y ENSAMBLADORES 1\n"
      .asciz "SECCION A\n"
      .asciz "LUDWING ALEXANDER LOPEZ ORTIZ\n"
      .asciz "201907608\n"
      .asciz "===================================================\n"
      lenEncabezado = . - encabezado
  ```

- **`encabezado_columnas`**: Cadena que representa las letras de las columnas en el tablero.

  ```assembly
  encabezado_columnas:
      .asciz "      A          B          C          D          E          F          G          H          I          J          K   \n"
      lenEncabezadoColumnas = . - encabezado_columnas
  ```

- **`value`**: Cadena de 9 caracteres inicializada con ceros, utilizada para representar los valores de las celdas.

  ```assembly
  value:  
      .asciz "000000000"
      lenValue = . - value  // 9 espacios para el valor de cada celda
  ```

- **`ingresoComando`**: Cadena con dos puntos ":" para solicitar el ingreso de un comando.

  ```assembly
  ingresoComando:
      .asciz ":"
      lenIngresoComando = . - ingresoComando
  ```

- **`msgIngresarValor`**: Mensaje que solicita al usuario ingresar un valor para una celda específica.

  ```assembly
  msgIngresarValor:
      .asciz "Ingresar un valor para @@@:"
      lenMsgIngresarValor = . - msgIngresarValor
  ```

- **Otros Mensajes**:

  - `errorDivMsg`: "Error: División por cero".
  - `errorImport`: "Error En El Comando De Importación".
  - `errorOpenFile`: "Error al abrir el archivo".
  - `readSuccess`: "El Archivo Se Ha Leído Correctamente".

#### Variables para Almacenamiento de Parámetros

- **`fila`** y **`letra`**: Utilizadas para representar la posición de una celda.

  ```assembly
  fila:  
      .asciz "00"

  letra:  
      .asciz "A"
  ```

- **Buffers y Variables Auxiliares**:

  ```assembly
  buffer:  
      .asciz "0\n"

  prueba:  
      .asciz "9223372036854775807\0"
  ```

### Datos No Inicializados (.bss)

En la sección `.bss`, se reservan espacios de memoria sin inicializar:

- **`tablero`**: Espacio para almacenar el tablero de celdas (253 * 8 bytes).

  ```assembly
  tablero:
      .skip 253 * 8
  ```

- **Buffers y Variables Auxiliares**:

  ```assembly
  num:
      .space 19  // Espacio para almacenar números como cadenas

  comando:
      .zero 50   // Espacio para almacenar el comando ingresado

  cualquier_numero:
      .skip 20

  tipo_comando: 
      .zero 1

  num64:
      .skip 8         // Espacio para números de 64 bits

  param1:
      .skip 8        // Parámetro 1

  posicion_param1:
      .skip 1

  param2:
      .skip 8         // Parámetro 2

  posicion_param2:
      .skip 2

  posicion_param3:
      .skip 3

  fila64:
      .skip 8      // Espacio para el número de fila como entero de 64 bits

  rreglo:
      .rept 36
      .hword 0
      .endr

  val:
      .space 2

  bufferComando:
      .zero 50

  filename:
      .space 100

  buffer2:
      .zero 1024

  fileDescriptor:
      .space 8

  listIndex:
      .zero 6

  num2:
      .space 8

  col_imp:
      .space 1

  character:
      .space 2

  count:
      .zero 8   
  ```

## Sección de Código (.text)

La sección `.text` contiene el código ejecutable del programa. Incluye macros, funciones y el punto de entrada principal.

### Macros

El código define varias macros para simplificar operaciones comunes:

#### `mPrint`

Macro para imprimir cadenas utilizando el servicio del sistema operativo.

```assembly
.macro mPrint reg, len
    MOV x0, 1
    LDR x1, =\reg
    MOV x2, \len
    MOV x8, 64
    SVC 0
.endm
```

#### `mImprimirValores`

Macro para imprimir valores de celdas.

```assembly
.macro mImprimirValores
    MOV x9, 0
    imprimir_valores:
        LDR x1, =value
        MOV w7, 9 
        mLimpiarValue
        MOV x0, #-1
        LDR x1, =value
        MOV X2, 9
        BL itoa
        mPrint value, 9
        ADD x9, x9, 1
    end_mImprimirValores:
.endm
```

#### `mLimpiarValue`

Macro para limpiar el buffer `value` llenándolo con caracteres '0'.

```assembly
.macro mLimpiarValue
    MOV w6, 48
    1:
        STRB w6, [x1], 1
        SUB w7, w7, 1
        CBNZ w7, 1b
.endm
```

#### `mRead`

Macro para leer datos desde la entrada estándar.

```assembly
.macro mRead stdin, buffer, len
    MOV x0, \stdin
    LDR x1, =\buffer
    MOV x2, \len
    MOV x8, 63
    SVC 0
.endm
```

#### `print` y `read`

Macros para simplificar llamadas al sistema de impresión y lectura.

```assembly
.macro print stdout, reg, len
    MOV x0, \stdout
    LDR x1, =\reg
    MOV x2, \len
    MOV x8, 64
    SVC 0
.endm

.macro read stdin, reg, len
    MOV x0, \stdin
    LDR x1, =\reg
    MOV x2, \len
    MOV x8, 63
    SVC 0
.endm
```

### Funciones

A continuación, se describen las funciones principales del programa:

#### `atoi2`

Convierte una cadena de caracteres que representa un número en su valor entero correspondiente. Esta función se utiliza específicamente para convertir cadenas leídas desde archivos.

- **Parámetros**:
  - `x5`, `x8`: Dirección inicial y actual del buffer.

- **Descripción**:
  - Recorre la cadena hasta encontrar un carácter nulo o un salto de línea.
  - Convierte cada carácter numérico en su valor entero, acumulando el resultado.
  - Soporta números negativos.

#### `proc_import`

Procesa el comando de importación ingresado por el usuario.

- **Descripción**:
  - Verifica si el comando ingresado es "IMPORTAR".
  - Si el comando es correcto, extrae el nombre del archivo a importar.
  - Maneja errores si el comando no es válido.

#### `import_data`

Importa datos desde un archivo CSV especificado por el usuario.

- **Descripción**:
  - Abre el archivo utilizando `openFile`.
  - Lee los encabezados del archivo y solicita al usuario la columna a utilizar.
  - Llama a `readCSV` para leer los datos del archivo.

#### `readCSV`

Lee los datos del archivo CSV y los almacena en el tablero.

- **Descripción**:
  - Lee números separados por comas y saltos de línea.
  - Utiliza `atoi2` para convertir cadenas numéricas en valores enteros.
  - Almacena los valores en el tablero en las posiciones correspondientes.

#### `openFile`

Abre un archivo y almacena su descriptor.

- **Descripción**:
  - Utiliza la llamada al sistema `open` para abrir el archivo en modo lectura.
  - Verifica si el archivo se abrió correctamente.
  - Maneja errores de apertura de archivo.

#### `closeFile`

Cierra el archivo abierto.

- **Descripción**:
  - Utiliza la llamada al sistema `close` para cerrar el archivo utilizando el descriptor almacenado.

#### `imprimirCeldas`

Imprime el tablero de celdas en pantalla.

- **Descripción**:
  - Imprime el encabezado de columnas.
  - Recorre el tablero y muestra los valores de cada celda.
  - Utiliza `itoa` para convertir los valores numéricos a cadenas de caracteres.

#### `limpiarParametro`

Limpia el buffer `num` estableciendo su contenido a cero.

#### `posicionCelda`

Calcula la posición en memoria de una celda basada en su referencia (por ejemplo, "A1").

- **Descripción**:
  - Convierte la letra de columna a un índice numérico.
  - Convierte el número de fila a un índice numérico.
  - Calcula la posición lineal en el arreglo `tablero`.

#### `atoi`

Convierte una cadena de caracteres que representa un número en su valor entero.

- **Descripción**:
  - Similar a `atoi2`, pero se utiliza para conversiones generales en el programa.
  - Soporta números negativos.

#### `verificarParametro`

Verifica y analiza los parámetros ingresados por el usuario.

- **Descripción**:
  - Determina si el parámetro es una celda, un número o un archivo.
  - Actualiza la variable `w4` para indicar el tipo de parámetro.

#### `verificarComando`

Verifica el comando ingresado por el usuario y determina la operación a realizar.

- **Descripción**:
  - Compara el comando ingresado con las palabras clave reconocidas (e.g., "GUARDAR", "IMPORTAR").
  - Establece el tipo de comando en `w4` para su uso posterior.

#### `verificarPalabraIntermedia`

Verifica palabras intermedias en comandos compuestos.

- **Descripción**:
  - Reconoce palabras como "EN", "HASTA", "SEPARADO POR", "Y".
  - Utiliza esta información para interpretar correctamente comandos más complejos.

#### `getComando`

Solicita al usuario que ingrese un comando.

- **Descripción**:
  - Muestra el prompt `:` y lee la entrada del usuario.
  - Almacena el comando en el buffer `comando`.

#### `getNumero`

Solicita al usuario que ingrese un número.

- **Descripción**:
  - Muestra un mensaje solicitando un valor para una celda específica.
  - Lee el número ingresado y lo almacena.

#### `itoa`

Convierte un número entero en una cadena de caracteres.

- **Descripción**:
  - Soporta números negativos y positivos.
  - Utiliza división y módulo para extraer dígitos y convertirlos a caracteres ASCII.
  - Almacena el resultado en el buffer proporcionado.

#### `parametroNumero`

Interpreta y procesa los parámetros ingresados según su tipo.

- **Descripción**:
  - Llama a funciones específicas dependiendo del tipo de parámetro (número, celda, texto).
  - Convierte cadenas a valores numéricos o calcula posiciones en el tablero.

#### `posicionACelda`

Convierte una posición lineal en el tablero a su referencia de celda (e.g., de índice a "A1").

- **Descripción**:
  - Calcula la letra de columna y el número de fila.
  - Actualiza el mensaje `msgIngresarValor` para indicar al usuario la celda específica.

#### `llenarFilaColumna`

Llena un rango de celdas en el tablero con valores ingresados por el usuario.

- **Descripción**:
  - Itera sobre filas o columnas según los parámetros proporcionados.
  - Solicita al usuario valores para cada celda en el rango.
  - Utiliza `getNumero` y `atoi` para obtener y convertir los valores ingresados.

### Punto de Entrada (`_start`)

El punto de entrada `_start` es donde comienza la ejecución del programa.

- **Descripción**:
  - Limpia la pantalla y muestra el encabezado institucional.
  - Imprime el tablero inicial de celdas.
  - Solicita al usuario ingresar un comando.
  - Procesa el comando ingresado y llama a las funciones correspondientes.
  - Implementa un bucle principal para permitir al usuario realizar múltiples operaciones hasta que decida salir.

## Flujo del Programa

1. **Inicialización**:
   - Limpia la pantalla y muestra el encabezado.
   - Imprime el tablero vacío de celdas.

2. **Entrada del Usuario**:
   - Solicita al usuario ingresar un comando.

3. **Procesamiento del Comando**:
   - Verifica el comando ingresado utilizando `verificarComando`.
   - Dependiendo del comando, realiza diferentes operaciones:
     - **IMPORTAR**: Llama a `proc_import` e `import_data` para cargar datos desde un archivo CSV.
     - **GUARDAR**: Guarda un valor en una celda específica.
     - **OPERACIONES ARITMÉTICAS**: Realiza operaciones como suma, resta, multiplicación, división, etc.
     - **LLENAR DESDE**: Llena un rango de celdas con valores proporcionados por el usuario.

4. **Actualización del Tablero**:
   - Después de cada operación, actualiza e imprime el tablero de celdas.

5. **Bucle Principal**:
   - El programa vuelve a solicitar un comando, permitiendo al usuario realizar múltiples operaciones.

6. **Finalización**:
   - El programa continúa en el bucle hasta que el usuario decida salir.

## Conclusión

Este código ensamblador ARM implementa un programa interactivo que permite al usuario manipular valores en un tablero de celdas, similar a una hoja de cálculo básica. A través de comandos específicos, el usuario puede importar datos, realizar operaciones aritméticas y lógicas, y gestionar los valores de las celdas.

El programa hace uso intensivo de llamadas al sistema para entrada y salida, y utiliza diversas funciones para manejar la conversión de datos y la lógica de operaciones. La comprensión detallada de este código requiere familiaridad con el lenguaje ensamblador ARM y los conceptos de arquitectura de computadores.

Esperamos que este manual técnico facilite la comprensión y el mantenimiento del código proporcionado.

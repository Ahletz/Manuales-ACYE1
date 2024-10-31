# Manual de Usuario del Programa de Hoja de Cálculo en ARM Assembly

## Introducción

Este manual de usuario está diseñado para guiarte en el uso del programa de hoja de cálculo implementado en lenguaje ensamblador ARM. El programa permite manipular un tablero de celdas similar a una hoja de cálculo, donde puedes realizar operaciones aritméticas y lógicas, importar datos desde un archivo CSV y gestionar los valores de las celdas.

## Requisitos del Sistema

- **Arquitectura ARM**: El programa está escrito en lenguaje ensamblador para arquitectura ARM de 64 bits.
- **Ensambaldor y Compilador**: Necesitarás un ensamblador y compilador compatible con ARM, como `as` y `ld` de GNU.
- **Sistema Operativo**: Linux u otro sistema operativo que soporte llamadas al sistema compatibles con ARM.

## Instalación y Compilación

### Paso 1: Guardar el Código Fuente

Copia el código fuente del programa y guárdalo en un archivo con extensión `.s`, por ejemplo, `hoja_calculo.s`.

### Paso 2: Ensamblar el Código

Abre una terminal y navega hasta el directorio donde guardaste el archivo. Ejecuta el siguiente comando para ensamblar el código:

```bash
as -o hoja_calculo.o hoja_calculo.s
```

Este comando generará un archivo objeto `hoja_calculo.o`.

### Paso 3: Enlazar el Programa

Ahora, enlaza el archivo objeto para crear el ejecutable:

```bash
ld -o hoja_calculo hoja_calculo.o
```

Esto generará un ejecutable llamado `hoja_calculo`.

## Ejecución del Programa

Para ejecutar el programa, simplemente escribe en la terminal:

```bash
./hoja_calculo
```

Al iniciar, el programa mostrará una pantalla con el encabezado institucional y un tablero de celdas vacío.

## Uso del Programa

El programa funciona mediante la introducción de comandos en una interfaz de línea de comandos. A continuación, se detallan los comandos disponibles y cómo utilizarlos.

### Comandos Disponibles

1. **IMPORTAR**

   Permite importar datos desde un archivo CSV.

   **Sintaxis**:

   ```
   IMPORTAR nombre_de_archivo SEPARADO POR COMA
   ```

   **Ejemplo**:

   ```
   IMPORTAR datos.csv SEPARADO POR COMA
   ```

   **Pasos**:

   - Después de ingresar el comando, el programa solicitará que asignes cada columna del archivo CSV a una columna en el tablero.
   - Ingresa la letra de la columna en el tablero para cada columna del archivo.

2. **GUARDAR**

   Guarda un valor en una celda específica.

   **Sintaxis**:

   ```
   GUARDAR A1 EN 123
   ```

   **Pasos**:

   - Reemplaza `A1` con la celda donde deseas guardar el valor.
   - Reemplaza `123` con el valor que deseas almacenar.

3. **LLENAR DESDE**

   Llena un rango de celdas con valores proporcionados por el usuario.

   **Sintaxis**:

   ```
   LLENAR DESDE A1 HASTA A5
   ```

   **Pasos**:

   - Especifica el rango desde la celda inicial hasta la celda final.
   - El programa te pedirá que ingreses los valores para cada celda en el rango.

4. **Operaciones Aritméticas**

   - **SUMA**

     **Sintaxis**:

     ```
     SUMA A1 Y A2
     ```

     **Descripción**: Suma los valores de las celdas A1 y A2, y almacena el resultado en una celda o variable.

   - **RESTA**

     **Sintaxis**:

     ```
     RESTA A1 Y A2
     ```

     **Descripción**: Resta el valor de A2 al valor de A1.

   - **MULTIPLICACION**

     **Sintaxis**:

     ```
     MULTIPLICACION A1 Y A2
     ```

     **Descripción**: Multiplica los valores de A1 y A2.

   - **DIVIDIR**

     **Sintaxis**:

     ```
     DIVIDIR A1 Y A2
     ```

     **Descripción**: Divide el valor de A1 entre el valor de A2.

5. **Operaciones Lógicas**

   - **Y LÓGICO**

     **Sintaxis**:

     ```
     Y LOGICO A1 Y A2
     ```

     **Descripción**: Realiza una operación lógica AND entre los valores de A1 y A2.

   - **O LÓGICO**

     **Sintaxis**:

     ```
     O LOGICO A1 Y A2
     ```

     **Descripción**: Realiza una operación lógica OR entre los valores de A1 y A2.

   - **NO LÓGICO**

     **Sintaxis**:

     ```
     NO LOGICO A1
     ```

     **Descripción**: Realiza una operación lógica NOT al valor de A1.

6. **POTENCIAR**

   **Sintaxis**:

   ```
   POTENCIAR A1 ELEVADO A A2
   ```

   **Descripción**: Eleva el valor de A1 a la potencia del valor de A2.

### Notas Importantes

- **Referencias a Celdas**: Las celdas se refieren mediante una letra (columna) y un número (fila), por ejemplo, `A1`, `B3`, `K10`.
- **Valores Numéricos**: Puedes utilizar números enteros directamente en lugar de referencias a celdas.
- **Visualización del Tablero**: Después de cada operación, el tablero se actualizará y mostrará los valores actuales de las celdas.

## Ejemplos de Uso

### Importar Datos desde un Archivo CSV

1. Ejecuta el comando:

   ```
   IMPORTAR datos.csv SEPARADO POR COMA
   ```

2. Asigna las columnas del archivo a las columnas del tablero cuando se te solicite.

3. El programa mostrará un mensaje confirmando la importación exitosa.

### Guardar un Valor en una Celda

1. Ejecuta el comando:

   ```
   GUARDAR B2 EN 500
   ```

2. El valor `500` se almacenará en la celda `B2`.

### Realizar una Suma entre Dos Celdas

1. Asegúrate de que las celdas `C1` y `C2` tienen valores.

2. Ejecuta el comando:

   ```
   SUMA C1 Y C2
   ```

3. El resultado se mostrará y puede almacenarse en otra celda si se especifica.

### Llenar un Rango de Celdas

1. Ejecuta el comando:

   ```
   LLENAR DESDE D1 HASTA D5
   ```

2. El programa te pedirá que ingreses los valores para `D1`, `D2`, `D3`, `D4` y `D5`.

## Manejo de Errores

- **Comando Inválido**: Si ingresas un comando no reconocido, el programa mostrará un mensaje de error. Verifica la sintaxis y vuelve a intentarlo.
- **División por Cero**: Si intentas dividir entre cero, el programa mostrará un mensaje de error.
- **Archivo No Encontrado**: Si el archivo especificado en el comando `IMPORTAR` no existe o no puede abrirse, se mostrará un mensaje de error.

## Salir del Programa

Para salir del programa, puedes presionar `Ctrl + C` en cualquier momento o ingresar un comando específico si el programa lo soporta (por ejemplo, `SALIR`), aunque este último debe ser implementado en el código.

## Consejos y Buenas Prácticas

- **Guardar Trabajo**: Si estás trabajando con datos importantes, considera implementar una función de guardado o modificar el código para que soporte persistencia.
- **Verificar Datos**: Siempre verifica que los valores en las celdas sean los esperados antes de realizar operaciones.
- **Respaldar Archivos**: Si importas datos desde archivos, asegúrate de tener una copia de seguridad de los mismos.

## Soporte y Contacto

Este programa fue desarrollado por **Ludwing Alexander López Ortiz**. Para soporte o consultas adicionales, puedes contactarte a través de los canales institucionales o educativos correspondientes.

## Conclusión

Este programa es una herramienta educativa que demuestra cómo se pueden implementar funcionalidades complejas en lenguaje ensamblador ARM. Aprovecha las funcionalidades ofrecidas y experimenta con las operaciones disponibles para comprender mejor el funcionamiento interno y la manipulación de datos a bajo nivel.

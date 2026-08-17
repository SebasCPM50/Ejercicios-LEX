# Ejemplos 1.3 y 1.4 - Flex and Bison

Ejercicios realizados a partir de los ejemplos del libro **Flex & Bison**.

## Ejemplo 1.3

El ejemplo 1.3 implementa un scanner sencillo utilizando Flex para reconocer números enteros y operadores de una calculadora.

El scanner reconoce:

* `+` como `PLUS`
* `-` como `MINUS`
* `*` como `TIMES`
* `/` como `DIVIDE`
* `|` como `ABS`
* Números enteros como `NUMBER`
* Saltos de línea como `NEWLINE`
* Espacios y tabulaciones son ignorados
* Los caracteres no reconocidos se muestran como `Mystery character`

### Prueba 1

**Entrada:**

```text
12+34
```

**Salida:**

```text
NUMBER 12
PLUS
NUMBER 34
NEWLINE
```

Esta prueba demuestra que el scanner puede reconocer números de varios dígitos y el operador de suma.

<img width="862" height="125" alt="image" src="https://github.com/user-attachments/assets/78e67fdd-9840-4e80-a9bd-288e096aed0a" />


<!-- Inserta aquí la captura de pantalla de la Prueba 1 del Ejemplo 1.3 -->

---

### Prueba 2

**Entrada:**

```text
5-6*7/8|9
```

**Salida:**

```text
NUMBER 5
MINUS
NUMBER 6
TIMES
NUMBER 7
DIVIDE
NUMBER 8
ABS
NUMBER 9
NEWLINE
```

Esta prueba permite comprobar el reconocimiento de los diferentes operadores definidos en el scanner.

<img width="869" height="201" alt="image" src="https://github.com/user-attachments/assets/73828251-149a-4c19-b18f-ed96caa545f0" />


---

### Prueba 3

**Entrada:**

```text
10 + 20 q
```

**Salida:**

```text
NUMBER 10
PLUS
NUMBER 20
Mystery character q
NEWLINE
```

Los espacios son ignorados por el scanner.

La letra `q` no corresponde a ninguna de las reglas principales, por lo que es reconocida por la regla general `.` y se muestra como un carácter desconocido.

<img width="884" height="131" alt="image" src="https://github.com/user-attachments/assets/1c7b3157-1e89-4e23-bb22-e6f05e55d66d" />


---

## Ejemplo 1.4

El ejemplo 1.4 modifica el funcionamiento del scanner para que, en lugar de imprimir directamente el nombre del elemento reconocido, retorne un número correspondiente a cada token.

Los tokens utilizados son:

```text
258 = NUMBER
259 = ADD
260 = SUB
261 = MUL
262 = DIV
263 = ABS
264 = EOL
```

Para los números se utiliza la variable `yylval`, que permite almacenar el valor numérico reconocido.

### Prueba 1

**Entrada:**

```text
34+35
```

**Salida:**

```text
258 = 34
259
258 = 35
264
```

El token `258` indica que se reconoció un número y `yylval` almacena su valor.

El token `259` corresponde al operador de suma y `264` representa el final de línea.

<img width="889" height="120" alt="image" src="https://github.com/user-attachments/assets/82d45b5b-5a42-432e-8f85-8aba1baa4229" />


---

### Prueba 2

**Entrada:**

```text
a / 34 + |45
```

**Salida:**

```text
Mystery character a
262
258 = 34
259
263
258 = 45
264
```

La letra `a` no corresponde a ningún token definido, por lo que se reporta como un carácter desconocido.

El scanner continúa procesando el resto de la entrada y reconoce `/`, los números, `+` y `|`.

Los espacios son ignorados.

<img width="886" height="165" alt="image" src="https://github.com/user-attachments/assets/a8f06499-41f1-48fc-af07-c312dd23cdef" />


---

### Prueba 3

**Entrada:**

```text
10-2*3
```

**Salida:**

```text
258 = 10
260
258 = 2
261
258 = 3
264
```

Esta prueba comprueba el reconocimiento de los tokens `SUB` y `MUL`.

<img width="881" height="151" alt="image" src="https://github.com/user-attachments/assets/bd49997c-17e2-4b91-ab0d-316416889cfc" />


---

## Diferencia entre los ejemplos 1.3 y 1.4

En el ejemplo 1.3 las acciones de Flex imprimen directamente el tipo de elemento encontrado, por ejemplo `NUMBER`, `PLUS` o `MINUS`.

En el ejemplo 1.4 el scanner funciona como una corrutina y utiliza `return` para devolver tokens al programa que llama a `yylex()`.

Además, el ejemplo 1.4 utiliza `yylval` para conservar el valor de los números reconocidos.

Este comportamiento permite que posteriormente un parser, como Bison, pueda recibir los tokens producidos por Flex y utilizarlos para realizar el análisis sintáctico.

## Compilación

En el entorno utilizado se generaron los scanners con Flex utilizando:

```bash
flex --noyywrap fb1-3.1
flex --noyywrap fb1-4.1
```

Para el ejemplo 1.3 se utilizó un archivo `main.c` para iniciar el scanner:

```bash
gcc lex.yy.c main.c -o fb1-3
```

Para el ejemplo 1.4:

```bash
gcc lex.yy.c -o fb1-4
```

Los ejecutables se probaron correctamente en MSYS2 UCRT64 sobre Windows.

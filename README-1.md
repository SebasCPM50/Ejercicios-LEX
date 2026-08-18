# Ejemplo 1.1 — Contador de caracteres, palabras y líneas

## Descripción

En este ejercicio se implementó y ejecutó el **Ejemplo 1.1 de Flex**, basado en un analizador léxico sencillo que funciona de manera similar al comando `wc` de Unix.

El programa recibe texto desde la entrada estándar y mantiene tres contadores:

- **Líneas:** cantidad de saltos de línea detectados.
- **Palabras:** cantidad de secuencias formadas por letras de `A` a `Z` o de `a` a `z`.
- **Caracteres:** cantidad de caracteres procesados.

El objetivo de la práctica fue familiarizarse con la estructura básica de un archivo de Flex, generar el analizador léxico, compilarlo con GCC y observar cómo las reglas definidas reaccionan ante diferentes entradas.

---

## Enfoque del ejercicio

El ejercicio está enfocado en observar cómo **Flex reconoce patrones mediante expresiones regulares y ejecuta una acción asociada a cada patrón**.

El archivo `.l` contiene tres partes principales:

1. **Código C inicial**, delimitado por `%{` y `%}`.
2. **Reglas léxicas**, ubicadas entre `%%` y `%%`.
3. **Código C adicional**, donde se encuentra `main()` y se inicia el análisis mediante `yylex()`.

Las reglas utilizadas fueron:

```text
[a-zA-Z]+    → cuenta una palabra y suma sus caracteres.
\n           → cuenta una línea y un carácter.
.            → cuenta cualquier otro carácter.
```

---

## Código utilizado

El archivo fuente utilizado para este ejercicio es `fb1-1.l`.

```c
/* just like Unix wc */
%{
int chars = 0;
int words = 0;
int lines = 0;
%}

%%
[a-zA-Z]+ { words++; chars += strlen(yytext); }
\n        { chars++; lines++; }
.         { chars++; }
%%

main(int argc, char **argv)
{
    yylex();
    printf("%8d%8d%8d\n", lines, words, chars);
}
```

> **Nota:** Este README documenta el código utilizado durante la práctica y sus resultados de ejecución.

---

## Procedimiento realizado

El ejercicio se trabajó desde **MSYS2 UCRT64**.

### Ubicación del proyecto

El proyecto se organizó dentro de:

```text
Documents/Flexbison/ejemplo-1-1
```

En MSYS2, la ubicación correspondiente se mostró como:

```text
/c/Users/apove/Documents/Flexbison/ejemplo-1-1
```

### Generación del código C mediante Flex

Se ejecutó:

```bash
flex fb1-1.l
```

Esto generó el archivo:

```text
lex.yy.c
```

### Compilación

El archivo generado se compiló con GCC:

```bash
gcc lex.yy.c -o ejemplo-1-1.exe
```

### Ejecución

Finalmente se ejecutó:

```bash
./ejemplo-1-1.exe
```

El programa quedó esperando texto introducido desde la terminal. Para finalizar la entrada en MSYS2/Windows se utilizó:

```text
Ctrl + Z
Enter
```

Después de recibir el final de la entrada, el programa mostró los tres contadores.

---

# Pruebas realizadas

## Prueba 1 — Una palabra

### Entrada

```text
Hello
```

### Resultado obtenido

```text
1 1 6
```

### Captura de prueba

> **Captura de la Prueba 1**
> <img width="1547" height="595" alt="image" src="https://github.com/user-attachments/assets/661dea5f-5459-4ea7-b53c-db6c82cf14a5" />


### Análisis

La entrada contiene una palabra formada únicamente por letras:

```text
Hello
```

La regla:

```text
[a-zA-Z]+
```

la reconoce como una palabra, por lo que el contador de palabras aumenta en `1`.

La palabra tiene cinco letras, pero también se procesa el salto de línea introducido al terminar la entrada. Por ello, el contador de caracteres mostrado es `6`.

El salto de línea activa además la regla:

```text
\n
```

por lo que el contador de líneas aumenta en `1`.

Resultado:

```text
Líneas = 1
Palabras = 1
Caracteres = 6
```

---

## Prueba 2 — Varias líneas y palabras

### Entrada

```text
Hola Mundo
Esto es Flex
Estoy probando el ejemplo uno
```

### Resultado obtenido

```text
3 10 54
```

### Captura de prueba

> **Captura de la Prueba 2**
> <img width="1555" height="290" alt="image" src="https://github.com/user-attachments/assets/fdecad5c-d4ae-4f68-b30a-a922df27892a" />


### Análisis

La entrada contiene tres líneas, por lo que se detectan tres saltos de línea.

Las palabras reconocidas son:

```text
Hola
Mundo
Esto
es
Flex
Estoy
probando
el
ejemplo
uno
```

En total:

```text
10 palabras
```

Los caracteres se contabilizan considerando tanto los caracteres que forman las palabras como los espacios, saltos de línea y demás caracteres procesados por las reglas correspondientes.

El resultado obtenido fue:

```text
Líneas = 3
Palabras = 10
Caracteres = 54
```

---

# Resultados obtenidos

Las pruebas confirmaron que el analizador responde de acuerdo con los patrones definidos.

| Prueba | Líneas | Palabras | Caracteres |
|---|---:|---:|---:|
| `Hello` | 1 | 1 | 6 |
| Tres líneas de texto | 3 | 10 | 54 |

El comportamiento observado permite comprobar que Flex:

- reconoce secuencias de letras como palabras;
- reconoce los saltos de línea;
- procesa los demás caracteres mediante la regla general `.`;
- ejecuta las acciones C asociadas a cada patrón;
- permite mantener variables y realizar operaciones sobre ellas durante el análisis.

---

# Análisis general del comportamiento

El programa no interpreta el texto como una oración completa, sino que lo procesa mediante **patrones léxicos**.

Cuando Flex encuentra una secuencia de letras, aplica:

```text
[a-zA-Z]+
```

y aumenta el contador de palabras.

Cuando encuentra un salto de línea, aplica:

```text
\n
```

y aumenta el contador de líneas y caracteres.

Para los demás caracteres se utiliza:

```text
.
```

que incrementa únicamente el contador de caracteres.

De esta manera, el ejercicio demuestra la idea fundamental de un analizador léxico: **recibir una secuencia de caracteres, identificar patrones y ejecutar acciones asociadas a dichos patrones**.

---

# Conclusión

El Ejemplo 1.1 permitió comprobar de forma práctica el funcionamiento básico de Flex mediante un contador de líneas, palabras y caracteres.

Se realizó el proceso completo:

```text
Archivo .l
   ↓
flex
   ↓
lex.yy.c
   ↓
gcc
   ↓
ejemplo-1-1.exe
   ↓
Pruebas desde la terminal
```

Las pruebas realizadas produjeron los resultados esperados y permitieron observar cómo cada regla léxica contribuye al conteo final.


---

# Ejemplo 1.2 — Transformación de palabras

## Descripción

En este ejercicio se implementó y ejecutó el **Ejemplo 1.2 de Flex**, cuyo propósito es transformar determinadas palabras de una entrada de texto.

El programa recibe texto desde la entrada estándar y busca palabras específicas. Cuando encuentra una de las palabras definidas en las reglas, imprime una sustitución. Los caracteres que no coinciden con las reglas específicas se imprimen sin modificación.

El ejercicio permite observar de manera sencilla cómo Flex puede utilizarse para **reconocer patrones y producir una salida transformada**.

---

## Enfoque del ejercicio

El ejercicio está enfocado en el reconocimiento de palabras mediante reglas léxicas.

Las transformaciones definidas son:

| Entrada | Salida |
|---|---|
| `colour` | `color` |
| `flavour` | `flavor` |
| `clever` | `smart` |
| `smart` | `elegant` |
| `conservative` | `liberal` |

Además, existe una regla general:

```text
. { printf("%s", yytext); }
```

Esta regla permite imprimir los caracteres que no fueron transformados por las reglas anteriores.

---

## Código utilizado

El archivo fuente utilizado para este ejercicio es `fb1-2.l`.

```c
/* English -> American */
%%
"colour" { printf("color"); }
"flavour" { printf("flavor"); }
"clever" { printf("smart"); }
"smart" { printf("elegant"); }
"conservative" { printf("liberal"); }

. { printf("%s", yytext); }
%%
```

> **Nota:** Este README documenta el código utilizado durante la práctica y los resultados obtenidos en las pruebas realizadas.

---

## Procedimiento realizado

El ejercicio se trabajó desde **MSYS2 UCRT64**.

### Ubicación del proyecto

El proyecto se organizó dentro de:

```text
Documents/Flexbison/ejemplo-1-2
```

En MSYS2, la ubicación correspondiente se mostró como:

```text
/c/Users/apove/Documents/Flexbison/ejemplo-1-2
```

### Generación del código C mediante Flex

Se ejecutó:

```bash
flex fb1-2.l
```

Esto generó:

```text
lex.yy.c
```

### Compilación

Se compiló el archivo generado con GCC:

```bash
gcc lex.yy.c -o ejemplo-1-2.exe
```

### Ejecución

Se ejecutó:

```bash
./ejemplo-1-2.exe
```

El programa quedó esperando texto desde la terminal.

Para finalizar la entrada en MSYS2/Windows se utilizó:

```text
Ctrl + Z
Enter
```

---

# Pruebas realizadas

## Prueba 1 — Una transformación

### Entrada

```text
colour
```

### Resultado obtenido

```text
color
```

### Captura de prueba

> **Captura de la Prueba 1**
> <img width="1549" height="221" alt="image" src="https://github.com/user-attachments/assets/e7d2ba7b-6480-444f-aa39-a995c120373e" />


### Análisis

La palabra:

```text
colour
```

coincide con la regla:

```c
"colour" { printf("color"); }
```

Por esta razón, Flex no imprime la palabra original, sino que ejecuta la acción asociada y produce:

```text
color
```

Esta prueba permite comprobar directamente el funcionamiento de una regla de sustitución.

---

## Prueba 2 — Todas las transformaciones

### Entrada

```text
colour flavour clever smart conservative
```

### Resultado obtenido

```text
color flavor smart elegant liberal
```

### Captura de prueba

> **Captura de la Prueba 2**
> <img width="1525" height="193" alt="image" src="https://github.com/user-attachments/assets/77a7a314-6541-4fba-8b5e-8d6515b19df3" />


### Análisis

En esta prueba se utilizaron las cinco palabras que tienen una transformación específica.

Las sustituciones observadas fueron:

```text
colour        → color
flavour       → flavor
clever        → smart
smart         → elegant
conservative  → liberal
```

La prueba demuestra que cada patrón puede tener una acción diferente.

También permite observar una característica importante del ejemplo: la palabra `smart` que aparece como entrada se transforma directamente en `elegant`, mientras que la salida producida por `clever` (`smart`) no vuelve a ser procesada por la regla `"smart"` durante la misma coincidencia. El resultado observado fue:

```text
color flavor smart elegant liberal
```

---

## Prueba 3 — Texto normal con una palabra transformable

### Entrada

```text
I like the colour of this car.
```

### Resultado obtenido

```text
I like the color of this car.
```

### Captura de prueba

> **Captura de la Prueba 3**
> <img width="1529" height="222" alt="image" src="https://github.com/user-attachments/assets/a18f1258-3d09-4cc4-a4af-f2059d80f16e" />


### Análisis

Esta prueba permite observar que el programa no reemplaza todo el texto.

Solamente la palabra:

```text
colour
```

coincide con una regla específica y se transforma en:

```text
color
```

El resto de los caracteres se imprime mediante la regla general:

```c
. { printf("%s", yytext); }
```

Por ello, la estructura general de la frase permanece igual.

---

## Prueba 4 — Mayúsculas y minúsculas

### Entrada

```text
Colour COLOUR colour
```

### Resultado obtenido

```text
Colour COLOUR color
```

### Captura de prueba

> **Captura de la Prueba 4**
> <img width="1525" height="226" alt="image" src="https://github.com/user-attachments/assets/1321f39f-e79e-4c9c-82c4-558f82278b22" />


### Análisis

Esta prueba permitió comprobar que la regla:

```c
"colour"
```

coincide con la forma escrita en minúsculas.

Por lo tanto:

```text
Colour
COLOUR
```

no coinciden con esa regla y se conservan, mientras que:

```text
colour
```

sí coincide y se transforma en:

```text
color
```

Resultado:

```text
Colour COLOUR color
```

---

# Resultados obtenidos

Las pruebas confirmaron el comportamiento esperado del analizador.

| Prueba | Entrada | Resultado |
|---|---|---|
| 1 | `colour` | `color` |
| 2 | Cinco palabras transformables | `color flavor smart elegant liberal` |
| 3 | `I like the colour of this car.` | `I like the color of this car.` |
| 4 | `Colour COLOUR colour` | `Colour COLOUR color` |

Los resultados muestran que:

- las palabras definidas explícitamente son transformadas;
- el resto de caracteres se conserva;
- las reglas son sensibles a la forma exacta de las palabras;
- las mayúsculas no coinciden con la regla escrita en minúsculas;
- el programa puede transformar texto mientras lo procesa.

---

# Análisis general del comportamiento

El analizador funciona mediante una serie de reglas léxicas.

Por ejemplo:

```c
"colour" { printf("color"); }
```

indica que cuando Flex reconoce exactamente el patrón `colour`, ejecuta `printf("color")`.

De manera similar:

```c
"smart" { printf("elegant"); }
```

hace que la palabra de entrada `smart` produzca `elegant`.

Finalmente, la regla:

```c
. { printf("%s", yytext); }
```

se utiliza para conservar los caracteres que no fueron reemplazados por las reglas específicas.

El ejercicio demuestra así que Flex no solamente puede contar o reconocer elementos, sino que también puede utilizar las coincidencias léxicas para **generar una salida diferente a partir de la entrada recibida**.

---

# Conclusión

El Ejemplo 1.2 permitió comprobar el uso de Flex para realizar transformaciones sobre texto mediante reglas léxicas.

Se realizó el proceso completo:

```text
Archivo .l
   ↓
flex
   ↓
lex.yy.c
   ↓
gcc
   ↓
ejemplo-1-2.exe
   ↓
Pruebas desde la terminal
   ↓
Salida transformada
```

Las cuatro pruebas realizadas permitieron comprobar tanto las sustituciones directas como el comportamiento de los caracteres que no coinciden con las reglas y la diferencia entre palabras escritas en minúsculas y mayúsculas.


---

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

---

# Laboratorio Flex & Bison

# Ejemplos 1-5 y 1-6 (Calculadora simple)

**Fuente:** *Flex & Bison*, John Levine (O'Reilly). Ejemplo 1-5 (`fb1-5.y`, pág. 31) y Ejemplo 1-6 (`fb1-5.l`, pág. 33), capítulo 1.

## Archivos

**`fb1-5.y`** (bison)
```c
%{
#include <stdio.h>
int yylex(void);
void yyerror(const char *s);
%}

%token NUMBER
%token ADD SUB MUL DIV ABS
%token EOL

%%

calclist: /* nothing */
 | calclist exp EOL { printf("= %d\n", $2); }
 ;

exp: factor
 | exp ADD factor { $$ = $1 + $3; }
 | exp SUB factor { $$ = $1 - $3; }
 ;

factor: term
 | factor MUL term { $$ = $1 * $3; }
 | factor DIV term {
     if ($3 == 0) {
         yyerror("division por cero");
         $$ = 0;
     } else {
         $$ = $1 / $3;
     }
   }
 ;

term: NUMBER
 | ABS term { $$ = $2 >= 0 ? $2 : -$2; }
 ;

%%

int main(int argc, char **argv) {
    yyparse();
    return 0;
}

void yyerror(const char *s) {
    fprintf(stderr, "error: %s\n", s);
}
```

**`fb1-5.l`** (flex)
```c
%{
# include "fb1-5.tab.h"
%}

%%
"+"      { return ADD; }
"-"      { return SUB; }
"*"      { return MUL; }
"/"      { return DIV; }
"|"      { return ABS; }
[0-9]+   { yylval = atoi(yytext); return NUMBER; }
\n       { return EOL; }
[ \t]    { /* ignore whitespace */ }
.        { printf("Mystery character %c\n", *yytext); }
%%
```

> **Nota (errata del libro):** en el texto impreso, la acción de `calclist exp EOL` usa `$1` en vez de `$2`.

## Compilación

```bash
bison -d fb1-5.y      # genera fb1-5.tab.c y fb1-5.tab.h
flex fb1-5.l           # genera lex.yy.c
cc -o fb1-5 fb1-5.tab.c lex.yy.c -lfl
./fb1-5
```

## Corrección: división por cero

El libro no valida el divisor. `10 / 0` sin corregir produce `SIGFPE` (el sistema mata el proceso). Se agregó la validación mostrada arriba en la regla `factor DIV term`: ahora imprime `error: division por cero` y el programa sigue corriendo.

## Evidencia de ejecución

<img width="348" height="360" alt="Pruebas 1 5-1 6" src="https://github.com/user-attachments/assets/dd7e1f3e-fc5c-4a4c-bd13-cafecafe0f1e" />

## Pruebas y resultados

| Prueba | Entrada | Resultado |
|---|---|---|
| Precedencia | `2 + 3 * 4` | `= 14` |
| Precedencia | `20 - 4 / 2` | `= 18` |
| División por cero | `10 / 0` | `error: division por cero` (no se cierra) |
| Carácter desconocido | `3 & 2` | `Mystery character &` + `error: syntax error` |
| División entera | `7 / 2` | `= 3` (trunca, no da 3.5) |
| ABS con negativo | `|-5 + 3` | `error: syntax error` |
| ABS válido | `|5 + 3` | `= 8` |

## Análisis y conclusiones

- **Precedencia:** la gramática en tres niveles (`exp → factor → term`) hace que `*`/`/` se resuelvan antes que `+`/`-`, sin declarar `%left`/`%right`.
- **División entera:** `yylval` y los valores semánticos son `int`, así que la división trunca decimales (comportamiento normal de C, no un bug).
- **División por cero:** el libro no la valida (SIGFPE); se corrigió agregando el chequeo en la regla `DIV`.
- **Operador `|` (ABS):** no soporta números negativos con `-` porque la gramática no tiene regla de "menos unario"; sin el signo funciona bien.
- **Caracteres desconocidos:** el escáner los reporta pero no detiene el programa; el error real ocurre después, en el parser, porque ese carácter nunca es un token válido.
- flex y bison se complementan: flex hace el análisis léxico (texto → tokens) y bison el sintáctico/semántico (tokens → resultado).

Presentado por: Alejandro Poveda Sandoval - Juan Pablo Bejarano Arévalo - Sebastián Chaux Palencia

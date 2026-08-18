# Ejemplo 1.2 — Transformación de palabras

## 1. Descripción

En este ejercicio se implementó y ejecutó el **Ejemplo 1.2 de Flex**, cuyo propósito es transformar determinadas palabras de una entrada de texto.

El programa recibe texto desde la entrada estándar y busca palabras específicas. Cuando encuentra una de las palabras definidas en las reglas, imprime una sustitución. Los caracteres que no coinciden con las reglas específicas se imprimen sin modificación.

El ejercicio permite observar de manera sencilla cómo Flex puede utilizarse para **reconocer patrones y producir una salida transformada**.

---

## 2. Enfoque del ejercicio

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

## 3. Código utilizado

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

## 4. Procedimiento realizado

El ejercicio se trabajó desde **MSYS2 UCRT64**.

### 4.1. Ubicación del proyecto

El proyecto se organizó dentro de:

```text
Documents/Flexbison/ejemplo-1-2
```

En MSYS2, la ubicación correspondiente se mostró como:

```text
/c/Users/apove/Documents/Flexbison/ejemplo-1-2
```

### 4.2. Generación del código C mediante Flex

Se ejecutó:

```bash
flex fb1-2.l
```

Esto generó:

```text
lex.yy.c
```

### 4.3. Compilación

Se compiló el archivo generado con GCC:

```bash
gcc lex.yy.c -o ejemplo-1-2.exe
```

### 4.4. Ejecución

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

# 5. Pruebas realizadas

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

# 6. Resultados obtenidos

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

# 7. Análisis general del comportamiento

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

# 8. Conclusión

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


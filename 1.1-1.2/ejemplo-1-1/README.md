# Ejemplo 1.1 — Contador de caracteres, palabras y líneas

## 1. Descripción

En este ejercicio se implementó y ejecutó el **Ejemplo 1.1 de Flex**, basado en un analizador léxico sencillo que funciona de manera similar al comando `wc` de Unix.

El programa recibe texto desde la entrada estándar y mantiene tres contadores:

- **Líneas:** cantidad de saltos de línea detectados.
- **Palabras:** cantidad de secuencias formadas por letras de `A` a `Z` o de `a` a `z`.
- **Caracteres:** cantidad de caracteres procesados.

El objetivo de la práctica fue familiarizarse con la estructura básica de un archivo de Flex, generar el analizador léxico, compilarlo con GCC y observar cómo las reglas definidas reaccionan ante diferentes entradas.

---

## 2. Enfoque del ejercicio

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

## 3. Código utilizado

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

## 4. Procedimiento realizado

El ejercicio se trabajó desde **MSYS2 UCRT64**.

### 4.1. Ubicación del proyecto

El proyecto se organizó dentro de:

```text
Documents/Flexbison/ejemplo-1-1
```

En MSYS2, la ubicación correspondiente se mostró como:

```text
/c/Users/apove/Documents/Flexbison/ejemplo-1-1
```

### 4.2. Generación del código C mediante Flex

Se ejecutó:

```bash
flex fb1-1.l
```

Esto generó el archivo:

```text
lex.yy.c
```

### 4.3. Compilación

El archivo generado se compiló con GCC:

```bash
gcc lex.yy.c -o ejemplo-1-1.exe
```

### 4.4. Ejecución

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

# 5. Pruebas realizadas

## Prueba 1 — Una palabra

### Entrada

```text
Hello
```

### Resultado obtenido

```text
1 1 6
```

### Espacio para captura

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

### Espacio para captura

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

# 6. Resultados obtenidos

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

# 7. Análisis general del comportamiento

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

# 8. Conclusión

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


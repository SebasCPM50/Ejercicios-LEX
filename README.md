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

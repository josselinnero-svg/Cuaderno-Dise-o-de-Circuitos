# Diseño de Circuitos · Unidad 1: Lógica combinacional con diseño clásico

Resumen didáctico semana a semana (Semanas 1 a 4).

Resultado de aprendizaje R1: diseñar e implementar circuitos combinacionales básicos con técnicas de diseño clásico.

> Nota sobre las imágenes: las referencias del tipo `![...](img/archivo.png)` son marcadores. Exporta la diapositiva indicada como imagen y guárdala en la carpeta img/. Cada esquema clave también tiene su versión en ASCII para que el README se entienda sin imágenes.

## Índice

1. [Semana 1: Sistemas numéricos, códigos y compuertas](#semana-1-sistemas-numéricos-códigos-y-compuertas)
2. [Semana 2: Álgebra de Boole y formas canónicas](#semana-2-álgebra-de-boole-y-formas-canónicas)
3. [Semana 3: Mapas de Karnaugh, indiferentes y NAND/NOR](#semana-3-mapas-de-karnaugh-indiferentes-y-nandnor)
4. [Semana 4: Circuitos aritméticos](#semana-4-circuitos-aritméticos)
5. [Mapa conceptual del bloque](#mapa-conceptual-del-bloque)

---


# Semana 3: Mapas de Karnaugh, indiferentes y NAND/NOR

## 3.1 El problema del mínimo y el teorema de adyacencia

Reto: simplificar $F(A,B,C) = \Sigma m(0,2,4,5,6)$ con álgebra. La pregunta es cómo saber que la respuesta es la mínima.

El mapa de Karnaugh resuelve esto visualmente con la clave del **teorema de adyacencia**:

$$XY + XY' = X$$

Si dos minitérminos difieren en una sola variable, esa variable desaparece.

```text
 m4 = A B' C'  = 1 0 0
 m5 = A B' C   = 1 0 1      difieren solo en C
 ───────────────────────
 suma          = A B'       (C desaparece)
```

## 3.2 ¿Por qué el orden 00, 01, 11, 10? Código Gray

![Binario vs Gray](img/s3_gray_vs_binario.png)

En el binario natural 01 a 10 cambia 2 bits. En Gray cada celda es vecina lógica de la siguiente (cambia un solo bit), y la última también es vecina de la primera.

**Analogía:** el mapa es como las mesas de un comedor circular donde solo puedes saludar al vecino inmediato. Para que "vecino" signifique "difiere en un bit", las mesas deben ir en orden Gray.

## 3.3 Mapa de 3 variables

```text
  A \ BC | 00 | 01 | 11 | 10
  -------+----+----+----+----
    0    | m0 | m1 | m3 | m2
    1    | m4 | m5 | m7 | m6
```

- Filas: la variable A. Columnas: BC en código Gray.
- Cada celda es un minitérmino (8 celdas = 8 filas de la tabla).
- Atención: m3 va antes que m2.

## 3.4 Reglas para agrupar

1. **Tamaño $2^k$:** 1, 2, 4, 8 o 16 celdas. Un grupo de $2^k$ celdas elimina $k$ variables.
2. **Rectangular:** sin formas en L ni diagonales.
3. **Lo más grande posible:** grupos grandes dan términos más cortos.
4. **El mapa se enrolla:** los bordes opuestos y las esquinas son vecinos.

![Agrupaciones válidas y no válidas](img/s3_reglas_agrupar.png)

Ejemplo no válido: un grupo de 3 en forma de L.

**Cómo leer un grupo:** se conservan solo las variables que no cambian. Constante en 1 se escribe A, constante en 0 se escribe A', y si cambia desaparece.

### Reto inicial resuelto en el mapa

$F = \Sigma m(0,2,4,5,6)$:

```text
  A \ BC | 00 | 01 | 11 | 10
  -------+----+----+----+----
    0    | 1  | 0  | 0  | 1
    1    | 1  | 1  | 0  | 1
```

- Grupo de 4 (columnas 00 y 10, que son vecinas por el borde): C = 0 en todo el grupo, queda $C'$.
- Grupo de 2 (m4 y m5): A = 1, B = 0, C cambia, queda $AB'$.

$$F = C' + AB'$$

## 3.5 Mapa de 4 variables

```text
  AB \ CD | 00 | 01 | 11 | 10
  --------+----+----+----+----
    00    |  0 |  1 |  3 |  2
    01    |  4 |  5 |  7 |  6
    11    | 12 | 13 | 15 | 14
    10    |  8 |  9 | 11 | 10
```

Filas AB y columnas CD, ambas en código Gray.

**Bordes y esquinas también son vecinos.** Imagina el mapa enrollado como un toroide: el borde superior toca al inferior y el izquierdo al derecho. Las 4 esquinas (m0, m2, m8, m10) forman un grupo con B = 0 y D = 0, es decir $B'D'$.

![Mapa de 4 variables: las 4 esquinas](img/s3_esquinas.png)

## 3.6 Implicantes primos y esenciales

- **Implicante:** cualquier grupo válido de 1 (o X). Es un término producto que solo vale 1 donde F vale 1.
- **Implicante primo:** un grupo que ya no se puede agrandar más.
- **Primo esencial:** el único primo que cubre algún 1, y debe estar en la solución.

Solución mínima = primos esenciales + los primos mínimos necesarios para cubrir el resto.

## 3.7 Procedimiento para la SOP mínima

1. **Colocar** los 1, 0 y X en sus celdas.
2. **Agrupar** los grupos más grandes.
3. **Elegir** los primos esenciales.
4. **Cubrir** los 1 restantes con pocos grupos.
5. **Leer** un término por grupo.

## 3.8 Ejemplo guiado: $F = \Sigma m(0,1,2,5,8,9,10)$

![Mapa de Karnaugh con tres grupos](img/s3_ejemplo_guiado.png)

```text
  AB \ CD | 00 | 01 | 11 | 10
  --------+----+----+----+----
    00    | 1  | 1  | 0  | 1
    01    | 0  | 1  | 0  | 0
    11    | 0  | 0  | 0  | 0
    10    | 1  | 1  | 0  | 1
```

| Grupo | Celdas | Término |
|---|---|---|
| Bordes superior e inferior | m0, m1, m8, m9 | $B'C'$ |
| 4 esquinas | m0, m2, m8, m10 | $B'D'$ |
| Par vertical | m1, m5 | $A'C'D$ |

$$F_{SOP} = B'C' + B'D' + A'C'D \qquad (3\text{ términos}, 7\text{ literales})$$

### La misma función en POS: se agrupan los 0 y se lee invertido

Lectura invertida: un 0 se escribe A y un 1 se escribe A'.

| Grupo de ceros | Celdas | Suma |
|---|---|---|
| Fila AB = 11 | 12, 13, 14, 15 | $(A' + B')$ |
| Columna CD = 11 | 3, 7, 11, 15 | $(C' + D')$ |
| B = 1, D = 0 (bordes izquierdo y derecho) | 4, 6, 12, 14 | $(B' + D)$ |

$$F_{POS} = (A' + B')(B' + D)(C' + D') \qquad (6\text{ literales})$$

**¿SOP o POS?** Se construyen ambas formas y se elige la de menor costo en literales, compuertas y circuitos integrados. Aquí la POS (6 literales) gana a la SOP (7 literales).

## 3.9 Taller y diagnóstico de errores (resueltos)

- **T1:** $F = \Sigma m(1,3,4,6)$ da $A'C + AC' = A \oplus C$.
- **T2:** $F = \Sigma m(0,1,4,5,6,7,14,15)$ da $F = A'C' + BC$. El primo $A'B$ (m4 a m7) sobra: sus celdas ya están cubiertas por los otros dos.
- **T3:** $F = \Sigma m(0,2,5,8,10,13)$ da $F = B'D' + BC'D$. Las 4 esquinas dan $B'D'$, y m5 con m13 dan $BC'D$.
- **T4 (mapa con errores):** el estudiante obtuvo $F = A'C' + BC + A'B + A'B'C'$ para la función de T2. Los 3 errores son:
  1. El encabezado de columnas está como 00, 01, 10, 11 y debería ser 00, 01, 11, 10 (Gray). Con ese orden las adyacencias no son reales.
  2. $A'B'C'$ es un grupo no máximo: está contenido en $A'C'$, así que sobra.
  3. $A'B$ es un primo redundante: todas sus celdas ya las cubren $A'C'$ y $BC$.

  Expresión correcta: $F = A'C' + BC$.
- **Ticket de salida:** $F = \Sigma m(0,2,5,7,8,10,13,15)$ da $F = B'D' + BD$. Es una compuerta **XNOR** entre B y D.

## 3.10 Condiciones indiferentes (Don't Care, X)

Un dígito BCD usa 4 bits, pero solo 10 de las 16 combinaciones (0000 a 1001). Las combinaciones 1010 a 1111 nunca ocurren. ¿Qué debe hacer el circuito con ellas? Lo que más nos convenga.

**Analogía:** es como una pizarra con casillas que nadie va a mirar. Puedes rellenarlas como más te ayude a escribir menos.

Cómo usar las X en el mapa:

- **Como 1:** si agranda un grupo, se incluye y el término queda más corto.
- **Como 0:** si no ayuda, se ignora. Ninguna X está obligada a quedar cubierta.
- **Nunca sola:** un grupo formado solo por X agrega un término inútil.

Notación: $F = \Sigma m(\text{minitérminos}) + d(\text{indiferentes})$.

### Ejemplo guiado: dígito BCD mayor o igual a 5

$$F = \Sigma m(5,6,7,8,9) + d(10,11,12,13,14,15)$$

![Mapa con y sin usar las X](img/s3_indiferentes_bcd.png)

```text
  Sin usar las X                 Con las X
  AB\CD 00 01 11 10              AB\CD 00 01 11 10
   00    0  0  0  0               00    0  0  0  0
   01    0  1  1  1               01    0  1  1  1
   11    X  X  X  X               11    X  X  X  X
   10    1  1  X  X               10    1  1  X  X

  F = AB'C' + A'BD + A'BC        F = A + BD + BC
        9 literales                    5 literales
```

Con las X, el grupo de 8 celdas (filas 11 y 10 completas) da simplemente $A$.

## 3.11 Universalidad de NAND y NOR

Con NAND se construye todo:

| Función | Con solo NAND | Con solo NOR |
|---|---|---|
| NOT | $(A \cdot A)'$ | $(A + A)'$ |
| AND | $((AB)')'$ | $(A' + B')'$ |
| OR | $(A' \cdot B')'$ | $((A + B)')'$ |

Si NOT, AND y OR se construyen con NAND, cualquier función también (y lo mismo con NOR).

**Analogía:** NAND y NOR son como una pieza de LEGO universal. Con un solo tipo de pieza se arma cualquier figura, y en el almacén solo guardas una caja de piezas.

- SOP mínima corresponde a **NAND-NAND**.
- POS mínima corresponde a **NOR-NOR**.
- La forma mínima elegida decide la familia que conviene.

### De SOP a NAND-NAND en cuatro pasos

Con el ejemplo $F = A + BD + BC$:

1. $F = A + BD + BC$ (SOP mínima).
2. $F = ((A + BD + BC)')'$ (doble negación: no cambia nada).
3. $F = (A' \cdot (BD)' \cdot (BC)')'$ (De Morgan sobre la negación interna).
4. $F = \text{NAND}(A',\ \text{NAND}(B,D),\ \text{NAND}(B,C))$ (cada paréntesis es una NAND).

```text
 Nivel 1 (antes AND)          Nivel 2 (antes OR)

 A ── NAND(inversor) ── A' ──┐
 B ──┐                       │
     NAND ── (BD)' ──────────┼── NAND ── F
 D ──┘                       │
 B ──┐                       │
     NAND ── (BC)' ──────────┘
 C ──┘
```

- El literal que entra solo (A) se complementa: la NAND funciona como inversor. Por eso A entra complementada y B, C y D no.
- Son dos niveles, así que el retardo es el mismo que el de AND-OR.
- Es una sola familia: toda la red sale de chips 7400 y 7410.

### ¿Cuántos circuitos integrados cuesta?

| Implementación | Chips | Total |
|---|---|---|
| Sin usar las X | 7404 + 7411 + 7432 | 3 CI |
| Con X, AND-OR | 7408 + 7432 | 2 CI |
| Con X, solo NAND | 7400 + 7410 | 2 CI |

El ahorro vino de las X. La NAND no agrega chips y deja una sola familia en el inventario.

**Piensa, pareja, comparte:** convertir $F = A'C' + BC$ (taller T2) a NAND-NAND. Resultado: $F = ((A'C')' \cdot (BC)')'$. Se necesitan 2 NAND como inversores (A' y C') más 3 NAND de 2 entradas, es decir 5 NAND, y como cada 7400 trae 4, hacen falta **2 chips 7400**.

## Resumen de la Semana 3

- **Gray:** el orden 00-01-11-10 hace visibles las adyacencias.
- **Grupos:** $2^k$ celdas, rectangulares, lo más grandes posible, con bordes enrollados.
- **SOP y POS:** SOP agrupa los 1 y POS agrupa los 0 y se lee invertido.
- **Mínimo:** primos esenciales primero, y la solución mínima no siempre es única.
- **Indiferentes:** valen 1 si agrandan un grupo y 0 si no, y nunca se agrupan solas.
- **Conversión:** SOP a NAND-NAND y POS a NOR-NOR, y el literal solo se complementa.
- **Costo:** se compara en literales y en chips, y el mínimo algebraico no siempre es el más barato.

---

# Semana 4: Circuitos aritméticos

## 4.1 Sumar un solo bit: solo 4 casos

| Operación | Resultado | S (escribo) | C (me llevo) |
|---|---|---|---|
| 0 + 0 | 0 | 0 | 0 |
| 0 + 1 | 1 | 1 | 0 |
| 1 + 0 | 1 | 1 | 0 |
| 1 + 1 | 2 = 10 en binario | 0 | 1 |

- S = Suma, el dígito que se escribe.
- C = Carry (acarreo), el que "me llevo".

**Analogía:** en decimal, 7 + 5 = 12, escribo 2 y llevo 1. En binario ocurre con 1 + 1 = 10.

## 4.2 Ejemplo columna por columna: 0110 + 0111

```text
 acarreo  1 1 0 0
          0 1 1 0   ( 6)
        + 0 1 1 1   ( 7)
        ─────────
          1 1 0 1   (13)
```

| Columna | Suma | Escribo | Llevo |
|---|---|---|---|
| 0 | 0 + 1 = 1 | 1 | 0 |
| 1 | 1 + 1 = 2 | 0 | 1 |
| 2 | 1 + 1 + 1 = 3 | 1 | 1 |
| 3 | 0 + 0 + 1 = 1 | 1 | 0 |

El acarreo puede aparecer en cualquier columna y un acarreo provoca otro, por eso se encadenan. Ejemplo: 0111 + 0001 = 1000, donde el acarreo se propaga por tres columnas.

## 4.3 Dos tipos de columna, dos circuitos

- **Columna 0:** suma 2 bits (A y B), pues a su derecha no hay nada. Usa un **semisumador**.
- **Columnas 1, 2, 3, ...:** suman 3 bits (A, B y el acarreo que llega, Cin). Usan un **sumador completo**.

## 4.4 Semisumador

| A | B | C | S |
|---|---|---|---|
| 0 | 0 | 0 | 0 |
| 0 | 1 | 0 | 1 |
| 1 | 0 | 0 | 1 |
| 1 | 1 | 1 | 0 |

- Columna S: vale 1 cuando A y B son distintos, es decir una **XOR**.
- Columna C: vale 1 solo cuando A y B son ambos 1, es decir una **AND**.

$$S = A \oplus B \qquad C = A \cdot B$$

```text
 A ──┬──── XOR ──── S
     │      │
 B ──┼──────┘
     │
     └──── AND ──── C
```

Es "semi" porque no puede recibir acarreo, así que solo sirve para la columna 0.

## 4.5 Sumador completo: el truco de contar unos

Se cuenta cuántos unos hay en la fila y se escribe el total en binario: ese binario es (Cout, S).

| A | B | Cin | Número de 1 | Binario | Cout | S |
|---|---|---|---|---|---|---|
| 0 | 0 | 0 | 0 | 00 | 0 | 0 |
| 0 | 0 | 1 | 1 | 01 | 0 | 1 |
| 0 | 1 | 0 | 1 | 01 | 0 | 1 |
| 0 | 1 | 1 | 2 | 10 | 1 | 0 |
| 1 | 0 | 0 | 1 | 01 | 0 | 1 |
| 1 | 0 | 1 | 2 | 10 | 1 | 0 |
| 1 | 1 | 0 | 2 | 10 | 1 | 0 |
| 1 | 1 | 1 | 3 | 11 | 1 | 1 |

En palabras:

- **S = 1** cuando hay un número impar de unos (1 o 3).
- **Cout = 1** cuando hay al menos dos unos (la mayoría).

**Analogía:** tres amigos votan a mano alzada. S dice si la cantidad de manos levantadas es impar. Cout dice si hubo mayoría (al menos 2 de 3).

### Mapa de Karnaugh de S: el "tablero de ajedrez"

```text
  A \ BCin | 00 | 01 | 11 | 10
  ---------+----+----+----+----
     0     | 0  | 1  | 0  | 1
     1     | 1  | 0  | 1  | 0
```

Los unos están alternados y ningún 1 tiene vecino, así que no hay grupos. Es el patrón de la XOR:

$$S = A \oplus B \oplus C_{in}$$

### Mapa de Karnaugh de Cout: tres grupos de dos

```text
  A \ BCin | 00 | 01 | 11 | 10
  ---------+----+----+----+----
     0     | 0  | 0  | 1  | 0
     1     | 0  | 1  | 1  | 1
```

- Columna 11: $B \cdot C_{in}$.
- Fila A = 1, columnas 11 y 10: $A \cdot B$.
- Fila A = 1, columnas 01 y 11: $A \cdot C_{in}$.

$$C_{out} = AB + A\,C_{in} + B\,C_{in}$$

### ¿Hay que memorizar las fórmulas? No

Procedimiento: (1) tabla de verdad contando los unos, (2) K-map (agrupar, y si sale ajedrez es XOR), (3) expresión y su lectura en palabras. Lo que se recuerda: S = impar de unos, Cout = al menos dos unos.

## 4.6 Sumador de 4 bits en cadena (ripple carry)

![Cuatro sumadores completos en cadena](img/s4_ripple_carry.png)

```text
        A3 B3        A2 B2        A1 B1        A0 B0
         │  │         │  │         │  │         │  │
   ┌─────▼──▼──┐ ┌────▼──▼───┐ ┌───▼──▼───┐ ┌───▼──▼───┐
 ◄─┤ Sumador 3 │◄┤ Sumador 2 │◄┤ Sumador 1│◄┤ Sumador 0│◄── Cin (0)
Cout└─────┬─────┘ └─────┬─────┘ └────┬─────┘ └────┬─────┘
            S3           S2           S1           S0
```

Regla única: el Cout de un sumador va al Cin del siguiente.

**Por qué se llama "ripple" (ola):** cada sumador espera el acarreo del anterior, así que el resultado se completa de derecha a izquierda, como una ola.

**Analogía:** es una fila de personas pasándose un balde de agua. La última no puede empezar hasta que le llega el balde de la anterior.

### Estimación del retardo

Supuestos: cada compuerta tarda aproximadamente 10 ns y el acarreo cruza aproximadamente 2 compuertas por etapa.

$$t \approx 2 \cdot n \cdot 10\text{ ns}$$

| Bits | Compuertas en la ruta | Tiempo aproximado |
|---|---|---|
| 4 | 8 | 80 ns |
| 16 | 32 | 320 ns |
| 32 | 64 | 640 ns |

Más bits implican más lentitud. El retardo crece linealmente con $n$.

## 4.7 Restar es sumar el negativo

$$A - B = A + (-B) \qquad -B = \text{invertir los bits de } B \text{ y sumar } 1$$

```text
 B   = 0011 (3)
 inv = 1100
 +1  = 1101 (-3)

   0110  (6)
 + 1101  (-3)
 ───────
 1 0011  → 0011 = 3  ✔   (el 1 que sobra a la izquierda se descarta)
```

### Sumador/restador unificado con XOR

La XOR actúa como un inversor controlado por una señal M.

| M | B | B xor M |
|---|---|---|
| 0 | 0 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 0 |

- M = 0: B pasa igual, entonces **suma**.
- M = 1: B se invierte, entonces **resta**.
- M también se conecta al Cin del sumador 0, y así aporta el "+1".

```text
        M ───────────────────────────────┐ (a Cin del sumador 0)
        │                                │
 B3..B0 ─ XOR ─ XOR ─ XOR ─ XOR ──► B' ──┼──►┌───────────────┐
                                         │   │  Sumador de   ├──► S3..S0
 A3..A0 ─────────────────────────────────┴──►│    4 bits     │
                                             └───────────────┘
```

Un solo circuito suma o resta según M.

## 4.8 Overflow: cuando el resultado no cabe

Con 4 bits con signo el rango es de −8 a +7.

```text
   0101  (+5)
 + 0100  (+4)
 ───────
   1001  → se lee -7  ✗   (el resultado real, +9, no cabe)
```

**Regla:** hay overflow si el acarreo que entra a la última etapa es distinto del que sale.

$$V = C_3 \oplus C_4$$

Donde $C_3$ es el acarreo que entra al bit más significativo y $C_4$ es el acarreo de salida (Cout final).

**Atención:** Cout = 1 no significa error. En números con signo lo que cuenta es $V$. En números sin signo, el Cout = 1 sí indica que el resultado no cabe.

## 4.9 Comparador de magnitud

- **¿A = B?** Se usa una XNOR en cada bit (1 si son iguales) y una AND de todas.
- **¿A > B?** Se mira desde la izquierda: el primer bit en que difieren decide.

Para 2 bits: $A > B$ si A gana en el bit alto, o empatan ahí y A gana en el bajo:

$$A > B = A_1 B_1' + (A_1 \odot B_1)\,A_0 B_0'$$

**Analogía:** es como comparar dos números escritos en papel, de izquierda a derecha. Al primer dígito distinto ya sabes quién es mayor, y no hace falta seguir leyendo.

Integrado comercial: 7485.

## 4.10 De aquí sale la ALU

La unidad aritmético-lógica (ALU) de un procesador combina estos bloques:

```text
 ┌──────────────────┐
 │ Sumador/restador │──┐
 ├──────────────────┤  │
 │   Comparador     │──┤
 ├──────────────────┤  ├──► Multiplexor (Semana 5) ──► Resultado de la instrucción
 │ AND / OR / XOR   │──┘
 └──────────────────┘
```

## 4.11 Demostración: 0101 + 0011 en el circuito

| Etapa | A | B | Cin | Número de 1 | S | Cout |
|---|---|---|---|---|---|---|
| 0 | 1 | 1 | 0 | 2 | 0 | 1 |
| 1 | 0 | 1 | 1 | 2 | 0 | 1 |
| 2 | 1 | 0 | 1 | 2 | 0 | 1 |
| 3 | 0 | 0 | 1 | 1 | 1 | 0 |

Resultado: 1000 = 8.

- Sin signo: correcto (5 + 3 = 8).
- Con signo: hay overflow, porque $C_3 = 1$ y $C_4 = 0$, así que $V = 1 \oplus 0 = 1$ (8 no cabe en el rango −8 a +7).

## 4.12 Taller dirigido (resuelto)

1. **1011 + 0110:** acarreos por columna 0, 1, 1, 1 y Cout = 1. Resultado 10001 = 17 (11 + 6 = 17, sin signo).
2. **0110 − 0011 con M = 1:** B invertida es 1100 y Cin = 1, entonces 0110 + 1100 + 1 = 1 0011. Se descarta el 1 y queda 0011 = 3.
3. **Retardo de un sumador de 16 bits:** $2 \cdot 16 \cdot 10\text{ ns} = 320\text{ ns}$.
4. **A = 10 y B = 01 (2 bits):** A en decimal vale 2 y B vale 1, así que A > B es verdadero y A = B es falso.

## Resumen de la Semana 4

| Tema | Idea clave |
|---|---|
| S y C | S se escribe, C es el acarreo ("me llevo") |
| Semisumador | $S = A \oplus B$, $C = A \cdot B$, solo para la columna 0 |
| Sumador completo | S = impar de unos, Cout = al menos dos unos |
| Ripple | El acarreo avanza etapa por etapa, el retardo crece con $n$ |
| Resta | M = 1: la XOR invierte B y Cin = 1 |
| Overflow | $V = C_3 \oplus C_4$, no el Cout |
| Comparador | XNOR por bit, y el primer bit distinto decide |

---

# Mapa conceptual del bloque

```text
 Semana 1                Semana 2               Semana 3                  Semana 4
 Bits, bases, C2,   ──►  Boole, De Morgan,  ──► K-maps, indiferentes, ──► Sumadores, resta,
 códigos, compuertas     Σm / ΠM, costo         NAND/NOR                  overflow, comparador
       │                       │                        │                        │
       └──── Lenguaje ─────────┴──── Minimización ─────┴──── Aplicación: ALU ───┘
```

| Semana | Pregunta que responde |
|---|---|
| 1 | ¿Cómo se representa la información y qué piezas existen? |
| 2 | ¿Cómo se describe y se simplifica una función lógica? |
| 3 | ¿Cómo se obtiene el mínimo de forma visual y con una sola familia de compuertas? |
| 4 | ¿Cómo se construye un circuito que suma, resta y compara? |

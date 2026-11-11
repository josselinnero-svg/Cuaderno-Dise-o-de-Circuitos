
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


## Indice
<p align="center">
*<strong><a href="índice.md">ÍNDICE</a></strong>*
</p>

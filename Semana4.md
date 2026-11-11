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

## Indice
<p align="center">
*<strong><a href="índice.md">ÍNDICE</a></strong>*
</p>

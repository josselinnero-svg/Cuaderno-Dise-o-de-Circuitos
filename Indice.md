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

# Semana 1: Sistemas numéricos, códigos y compuertas

## 1.1 ¿Por qué el computador usa solo 0 y 1?

Un transistor funciona como un interruptor controlado por voltaje.

- Interruptor abierto: no pasa corriente, voltaje bajo, valor 0.
- Interruptor cerrado: pasa corriente, voltaje alto, valor 1.

**Analogía:** es como distinguir entre luz apagada y luz encendida desde lejos. Aunque haya neblina (ruido), se distingue bien. Distinguir diez niveles de brillo sería mucho más difícil.

![Interruptor abierto = 0, cerrado = 1](img/s1_interruptor_0_1.png)

## 1.2 Un "1" no es un voltaje exacto: es un rango (familia 74LS)

Cada compuerta promete una salida y exige una entrada, y entre ambas hay un margen de seguridad.

| Concepto | Valor (74LS) |
|---|---|
| Salida que promete un 1 | $\ge 2.7\text{ V}$ |
| Entrada que lee como 1 | $\ge 2.0\text{ V}$ |
| Entrada que lee como 0 | $\le 0.8\text{ V}$ |
| Salida que promete un 0 | $\le 0.5\text{ V}$ |

Entre 0.8 V y 2.0 V está la zona indefinida: el chip no garantiza nada y puede leer 0 o 1. Una causa típica es una entrada sin conectar.

```text
  5 V  ┬─────────────────────────
       │  Sale un 1:  >= 2.7 V
 2.7 V ┤- - - - - - - - - - - - -
       │  margen alto = 2.7 - 2.0 = 0.7 V
 2.0 V ┤========= Lee 1 =========
       │      ZONA INDEFINIDA
 0.8 V ┤========= Lee 0 =========
       │  margen bajo = 0.8 - 0.5 = 0.3 V
 0.5 V ┤- - - - - - - - - - - - -
       │  Sale un 0:  <= 0.5 V
  0 V  ┴─────────────────────────
```

**Margen de ruido** (el colchón de seguridad):

- Margen alto: $2.7 - 2.0 = 0.7\text{ V}$. Un 1 puede perder hasta 0.7 V en el camino y se sigue leyendo como 1.
- Margen bajo: $0.8 - 0.5 = 0.3\text{ V}$. Un 0 puede subir hasta 0.3 V por ruido y se sigue leyendo como 0.

**Ejemplo de falla:** sale un 0 de 0.5 V, un motor cercano induce 0.4 V de ruido, llegan 0.9 V (zona indefinida) y el resultado es un error.

![Niveles de voltaje y márgenes de ruido 74LS](img/s1_margen_ruido.png)

## 1.3 Sistema posicional

En un sistema posicional cada posición vale una potencia de la base. En decimal, 555 = 5·100 + 5·10 + 5·1. Los números romanos no son posicionales: la V de XV vale 5 esté donde esté, y por eso sumar con ellos es incómodo.

En binario las posiciones valen 1, 2, 4, 8, 16, ...

$$1011_2 = 1\cdot 8 + 0\cdot 4 + 1\cdot 2 + 1\cdot 1 = 11_{10}$$

- Bit = dígito binario (binary digit).
- Byte = 8 bits.
- Potencias de 2 para memorizar: 128, 64, 32, 16, 8, 4, 2, 1.

**Analogía:** las posiciones son como billetes de 1, 2, 4 y 8 dólares. Decides con un 1 o un 0 si usas cada billete.

## 1.4 Conversiones

### Decimal a binario: dividir entre 2 y leer los restos de abajo hacia arriba

| División | Cociente | Resto |
|---|---|---|
| 25 ÷ 2 | 12 | 1 |
| 12 ÷ 2 | 6 | 0 |
| 6 ÷ 2 | 3 | 0 |
| 3 ÷ 2 | 1 | 1 |
| 1 ÷ 2 | 0 | 1 |

Leyendo los restos de abajo hacia arriba: $25 = 11001_2$.

Comprobación: 16 + 8 + 1 = 25.

### Hexadecimal y octal: abreviaturas del binario

- **Hexadecimal:** grupos de 4 bits desde la derecha. A=10, B=11, C=12, D=13, E=14, F=15.
- **Octal:** grupos de 3 bits desde la derecha.
- Si faltan bits, se completa con ceros a la izquierda.

```text
157 = 1001 1101 (binario)
      ──┬─ ──┬─
        9    D      →  157 = 9D en hexadecimal

157 = 010 011 101
       2   3   5    →  157 = 235 en octal
```

### Ejercicios del reto de conversión (resueltos)

1. $45 = 101101_2 = 2D_{16}$.
2. $A7_{16} = 10\cdot 16 + 7 = 167_{10}$.
3. $11100101_2 = E5_{16} = 345_8$.

## 1.5 Suma binaria

Reglas: 0+0=0, 0+1=1, 1+1=10 (escribo 0 y llevo 1), 1+1+1=11 (escribo 1 y llevo 1).

```text
acarreo  1 1 1
         1 0 1 1   (11)
       + 0 1 1 0   ( 6)
       ---------
       1 0 0 0 1   (17)
```

## 1.6 Números negativos: complemento a dos

El circuito no tiene el signo "−". Se hace un acuerdo, igual que un cuentakilómetros que retrocede desde 000 y muestra 999.

**Analogía:** el cuentakilómetros. En 4 bits, 0000 − 1 = 1111, así que acordamos que 1111 significa −1, 1110 significa −2, y así sucesivamente.

Con 4 bits el bit izquierdo es 0 en los positivos y 1 en los negativos.

| Binario | Valor | Binario | Valor |
|---|---|---|---|
| 0000 | 0 | 1000 | −8 |
| 0001 | 1 | 1001 | −7 |
| 0010 | 2 | 1010 | −6 |
| 0011 | 3 | 1011 | −5 |
| 0100 | 4 | 1100 | −4 |
| 0101 | 5 | 1101 | −3 |
| 0110 | 6 | 1110 | −2 |
| 0111 | 7 | 1111 | −1 |

Rango con $n$ bits: de $-2^{n-1}$ a $2^{n-1}-1$. Con 4 bits va de −8 a +7, y con 8 bits de −128 a +127.

**Cómo obtener el negativo: invertir los bits y sumar 1.**

```text
 3 = 0011  →  invertir: 1100  →  +1: 1101 = -3

 5 - 3 = 5 + (-3):     0101
                     + 1101
                     ------
                     1 0010  → se descarta el bit que sobra → 0010 = 2  ✔
```

**Gran ventaja:** para restar no hace falta un circuito restador. Basta el mismo sumador más inversores.

**Desbordamiento (overflow):** con 4 bits, 7 + 1 = 0111 + 0001 = 1000, que se lee como −8 y es incorrecto. Es lo mismo que le pasa a un int de Java de 32 bits: 2147483647 + 1 da −2147483648. Se detecta con hardware en la Semana 4.

## 1.7 Códigos

### BCD (decimal codificado en binario)

Cada dígito decimal se escribe en 4 bits. Sirve para mostrar números en displays, relojes y calculadoras.

```text
  1    5    7
 0001 0101 0111      (157 en BCD)
 vs. 157 en binario natural = 10011101  (otra cosa)
```

Las combinaciones 1010 a 1111 no se usan. Esto será clave en la Semana 3, donde se tratan como condiciones indiferentes.

### Código Gray

Entre vecinos cambia un solo bit. Se construye con el método del espejo: copiar la lista al revés, anteponer 0 arriba y 1 abajo.

```text
1 bit: 0 1
2 bits: 00 01 11 10
3 bits: 000 001 011 010 110 111 101 100
```

En binario natural, pasar de 3 a 4 (011 a 100) cambia 3 bits a la vez, y un sensor puede leer valores falsos en el instante del cambio. En Gray eso no ocurre.

Se usa en perillas, encoders rotativos y en el orden 00, 01, 11, 10 del mapa de Karnaugh (Semana 3).

### ASCII

Es una tabla acordada de letras a números, con 7 bits (128 caracteres). Incluye letras, dígitos, signos y controles. La ñ y las tildes no están, para eso existe UTF-8.

| Letra | Decimal | Hex |
|---|---|---|
| A | 65 | 0x41 |
| L | 76 | 0x4C |
| O | 79 | 0x4F |
| H | 72 | 0x48 |

Respuesta a la pregunta de activación: el celular guarda la "A" como 65 = 01000001.

## 1.8 Las 7 compuertas lógicas

![NOT, AND, OR con sus tablas de verdad](img/s1_compuertas_basicas.png)

| Compuerta | Idea en palabras | Expresión |
|---|---|---|
| NOT | Invierte | $Y = A'$ |
| AND | 1 solo si todas valen 1 | $Y = A \cdot B$ |
| OR | 1 si al menos una vale 1 | $Y = A + B$ |
| NAND | NOT-AND: 0 solo si todas valen 1 | $Y = (A \cdot B)'$ |
| NOR | NOT-OR: 1 solo si ninguna vale 1 | $Y = (A + B)'$ |
| XOR | 1 si las entradas son distintas | $Y = A \oplus B$ |
| XNOR | 1 si las entradas son iguales | $Y = (A \oplus B)'$ |

Tablas de verdad de dos entradas (columna Y para cada compuerta):

| A | B | AND | OR | NAND | NOR | XOR | XNOR |
|---|---|---|---|---|---|---|---|
| 0 | 0 | 0 | 0 | 1 | 1 | 0 | 1 |
| 0 | 1 | 0 | 1 | 1 | 0 | 1 | 0 |
| 1 | 0 | 0 | 1 | 1 | 0 | 1 | 0 |
| 1 | 1 | 1 | 1 | 0 | 0 | 0 | 1 |

NOT: 0 pasa a 1 y 1 pasa a 0.

**Analogías cotidianas:**

- **AND** es una caja fuerte con dos llaves: se abre solo si giras las dos.
- **OR** es la puerta de un edificio con dos timbres: abre si pulsas cualquiera.
- **XOR** es la luz de una escalera con dos interruptores (abajo y arriba): cambia cada vez que mueves cualquiera de los dos.

El círculo en la salida significa "negado". Con solo NAND (o solo NOR) se puede construir cualquier circuito, y esto se aprovecha en la Semana 3.

![Normas ANSI/IEEE (formas) e IEC (rectángulos)](img/s1_normas_ansi_iec.png)

Hay dos normas para dibujar la misma compuerta: ANSI/IEEE (formas distintivas) e IEC (rectángulos con símbolo). En el curso se usa ANSI. Si el simulador muestra rectángulos, se cambia en sus opciones.

### Del enunciado a la compuerta (taller resuelto)

- a) La alarma suena solo si las dos puertas están abiertas: **AND**.
- b) La luz se enciende si se pulsa cualquiera de los dos botones: **OR**.
- c) El juego marca "diferentes" si los jugadores eligen distinto: **XOR**.
- d) La bomba se apaga cuando el sensor de nivel está activo: **NOT**.

Tabla de verdad de $Y = A \cdot B + C'$ (3 entradas dan $2^3 = 8$ filas):

| A | B | C | A·B | C' | Y |
|---|---|---|---|---|---|
| 0 | 0 | 0 | 0 | 1 | 1 |
| 0 | 0 | 1 | 0 | 0 | 0 |
| 0 | 1 | 0 | 0 | 1 | 1 |
| 0 | 1 | 1 | 0 | 0 | 0 |
| 1 | 0 | 0 | 0 | 1 | 1 |
| 1 | 0 | 1 | 0 | 0 | 0 |
| 1 | 1 | 0 | 1 | 1 | 1 |
| 1 | 1 | 1 | 1 | 0 | 1 |

## 1.9 Circuitos integrados: el 74LS08

Las compuertas vienen empaquetadas en circuitos integrados (CI). El 74LS08 contiene 4 compuertas AND de 2 entradas.

```text
            74LS08  (DIP-14, vista superior)
         ┌───╮───┐
    1A  1│●      │14  VCC (+5 V)
    1B  2│       │13  4B
    1Y  3│       │12  4A
    2A  4│       │11  4Y
    2B  5│       │10  3B
    2Y  6│       │ 9  3A
   GND  7│       │ 8  3Y
         └───────┘
```

- Encapsulado DIP-14: dos filas de 7 pines, encaja en el protoboard.
- Contar pines: la muesca va arriba, el pin 1 queda arriba a la izquierda y se cuenta en sentido antihorario.
- Alimentación: pin 14 = +5 V, pin 7 = tierra (GND).
- A y B son entradas, Y es la salida.

**Cómo leer el código impreso**, por ejemplo SN 74 LS 08 N:

| Parte | Significado |
|---|---|
| SN | Fabricante (Texas Instruments) |
| 74 | Rango de uso comercial (54 = militar) |
| LS | Familia/tecnología (LS = TTL, HC = CMOS) |
| 08 | Función (4 AND de 2 entradas) |
| N | Encapsulado (DIP de plástico) |

Para buscar la hoja de datos, se escribe "74LS08 datasheet" y se prefiere la web del fabricante (ti.com, onsemi.com). Si te venden un 74HC08, tiene la misma función y los mismos pines (es la versión CMOS) y sirve igual.

## Resumen de la Semana 1

- 0 y 1 son rangos de voltaje con una zona indefinida entre ellos.
- Binario = potencias de 2, hexadecimal = grupos de 4 bits, octal = grupos de 3 bits.
- Negativo = invertir + 1, y restar = sumar el negativo.
- BCD para displays, Gray para sensores, ASCII para texto.
- 7 compuertas con sus tablas de verdad.
- Hoja de datos del 74LS08: pines, alimentación (14 y 7) y función.

---

# Semana 2: Álgebra de Boole y formas canónicas

## 2.1 Un circuito y una expresión son lo mismo

![Circuito AND-OR con inversor: Y = A·B + C'](img/s2_circuito_expresion.png)

| Circuito | Expresión |
|---|---|
| Cable de entrada | Variable (A, B, C) |
| AND | Producto $\cdot$ |
| OR | Suma $+$ |
| NOT | Complemento $'$ |
| Salida | Valor de la expresión |

Para el circuito de la figura, $Y = A \cdot B + C'$. La precedencia es NOT, luego AND y al final OR.

```text
 A ──┐
     ├─ AND ──┐
 B ──┘        ├─ OR ──── Y        Y = A·B + C'
 C ── NOT ────┘
```

**Idea clave:** lo que simplificas en papel lo ahorras en hardware.

## 2.2 Del enunciado a la especificación

Los enunciados pueden ser ambiguos. Ejemplo: "La sirena suena cuando se activan dos sensores".

| Lectura | Filas con F = 1 | Función | Comentario |
|---|---|---|---|
| Exactamente dos | 011, 101, 110 | $\Sigma m(3,5,6)$ | Con los tres activos no suena |
| Al menos dos | 011, 101, 110, 111 | $\Sigma m(3,5,6,7)$ | Es la que pide el cliente |

Antes de diseñar hay que fijar con el cliente cuál es la lectura correcta.

## 2.3 Postulados de Huntington

El álgebra de Boole trabaja sobre el conjunto {0, 1} con tres operaciones: OR (+), AND (·) y NOT (').

| Postulado | Forma OR | Forma AND (dual) |
|---|---|---|
| Identidad | $X + 0 = X$ | $X \cdot 1 = X$ |
| Complemento | $X + X' = 1$ | $X \cdot X' = 0$ |
| Conmutativa | $X + Y = Y + X$ | $XY = YX$ |
| Distributiva | $X(Y+Z) = XY + XZ$ | $X + YZ = (X+Y)(X+Z)$ |

**Dualidad:** si intercambias + por · y 0 por 1 (sin complementar variables), la identidad sigue siendo válida.

## 2.4 Teoremas: tabla de consulta

| Teorema | Forma OR | Forma AND |
|---|---|---|
| Elemento nulo | $X + 1 = 1$ | $X \cdot 0 = 0$ |
| Idempotencia | $X + X = X$ | $X \cdot X = X$ |
| Involución | $(X')' = X$ | |
| Absorción | $X + XY = X$ | $X(X+Y) = X$ |
| Absorción del complemento | $X + X'Y = X + Y$ | $X(X'+Y) = XY$ |
| Adyacencia | $XY + XY' = X$ | $(X+Y)(X+Y') = X$ |
| Consenso | $XY + X'Z + YZ = XY + X'Z$ | $(X+Y)(X'+Z)(Y+Z) = (X+Y)(X'+Z)$ |

No se memorizan: se consultan y se aplican.

**La distributiva que sorprende:** $A + BC = (A+B)(A+C)$. Demostración:

$$(A+B)(A+C) = AA + AC + AB + BC = A + AC + AB + BC = A(1 + C + B) + BC = A + BC$$

Se usaron la distributiva, la idempotencia ($AA = A$), el factor común y el elemento nulo ($1 + \ldots = 1$).

## 2.5 Leyes de De Morgan

![Leyes de De Morgan con compuertas](img/s2_de_morgan.png)

$$(A \cdot B)' = A' + B' \qquad (A + B)' = A' \cdot B'$$

**Regla práctica:** rompe la barra y cambia el operador. Generaliza a $n$ variables: $(A + B + C)' = A'B'C'$.

**Analogía:** "No es cierto que (llueve Y hace frío)" equivale a "no llueve O no hace frío". Para que la frase compuesta falle, basta que falle una de las partes.

Ejercicios resueltos:

1. $(A \cdot B \cdot C)' = A' + B' + C'$
2. $(A + B'C)' = A' \cdot (B'C)' = A'(B + C')$
3. $(A'B + C)' = (A'B)' \cdot C' = (A + B')C'$

Se verifican con una fila de la tabla de verdad.

## 2.6 Minitérminos y maxitérminos

Son las 8 piezas posibles para 3 variables. Cada función elige cuáles usa.

| i | A | B | C | Minitérmino $m_i$ (vale 1) | Maxitérmino $M_i$ (vale 0) |
|---|---|---|---|---|---|
| 0 | 0 | 0 | 0 | A'B'C' | A + B + C |
| 1 | 0 | 0 | 1 | A'B'C | A + B + C' |
| 2 | 0 | 1 | 0 | A'BC' | A + B' + C |
| 3 | 0 | 1 | 1 | A'BC | A + B' + C' |
| 4 | 1 | 0 | 0 | AB'C' | A' + B + C |
| 5 | 1 | 0 | 1 | AB'C | A' + B + C' |
| 6 | 1 | 1 | 0 | ABC' | A' + B' + C |
| 7 | 1 | 1 | 1 | ABC | A' + B' + C' |

- **Minitérmino:** es un producto. Variable en 1 va sin complemento, variable en 0 va complementada.
- **Maxitérmino:** es una suma. Variable en 0 va sin complemento, variable en 1 va complementada.
- Relación: $M_i = (m_i)'$.

Ejemplo de los ejercicios de cierre: $m_4 = AB'C'$ y $M_4 = A' + B + C$.

## 2.7 Formas canónicas SOP y POS

Se usa la alarma: F = 1 si hay al menos dos sensores activos.

| i | A | B | C | F |
|---|---|---|---|---|
| 0 | 0 | 0 | 0 | 0 |
| 1 | 0 | 0 | 1 | 0 |
| 2 | 0 | 1 | 0 | 0 |
| 3 | 0 | 1 | 1 | 1 |
| 4 | 1 | 0 | 0 | 0 |
| 5 | 1 | 0 | 1 | 1 |
| 6 | 1 | 1 | 0 | 1 |
| 7 | 1 | 1 | 1 | 1 |

### SOP (suma de productos): se leen las filas donde F = 1

Por cada fila con F = 1 se toma su minitérmino y se unen con OR.

$$F = \Sigma m(3,5,6,7) = A'BC + AB'C + ABC' + ABC$$

Se lee así: "F vale 1 si la entrada es la combinación 3, o la 5, o la 6, o la 7".

### POS (producto de sumas): se leen las filas donde F = 0

Por cada fila con F = 0 se toma su maxitérmino y se unen con AND.

$$F = \Pi M(0,1,2,4) = (A+B+C)(A+B+C')(A+B'+C)(A'+B+C)$$

Se lee así: "F vale 1 si la entrada no es la 0, y no es la 1, y no es la 2, y no es la 4".

Entre ambas listas están todas las filas (0 a 7): los índices de $\Pi M$ son los que faltan en $\Sigma m$. Las formas canónicas siempre existen y son únicas.

### ¿SOP o POS? Criterio de elección

1. Pocos 1 en la tabla: SOP, porque da menos términos.
2. Pocos 0 en la tabla: POS, porque da menos términos.
3. Solo hay NAND disponibles: SOP, que se implementa en NAND-NAND (7400).
4. Solo hay NOR disponibles: POS, que se implementa en NOR-NOR (7402).

El criterio final es el costo: compuertas, circuitos integrados y niveles lógicos.

## 2.8 Simplificación algebraica

Procedimiento (no hay algoritmo que garantice el mínimo, y por eso en la Semana 3 llegan los mapas de Karnaugh):

1. Agrupar pares adyacentes: $XY + XY' = X$.
2. Duplicar términos si hace falta: $X = X + X$.
3. Absorción: $X + XY = X$.
4. Absorción del complemento: $X + X'Y = X + Y$.
5. Consenso: $XY + X'Z + YZ = XY + X'Z$.

### Alarma: de la canónica a la simplificada

Se aprovecha la idempotencia: ABC se repite tres veces, una para cada par.

$$A'BC + ABC = BC(A' + A) = BC$$
$$AB'C + ABC = AC(B' + B) = AC$$
$$ABC' + ABC = AB(C' + C) = AB$$

$$F = AB + AC + BC \qquad\text{(SOP)}$$
$$F = (A+B)(A+C)(B+C) \qquad\text{(POS)}$$

**Lectura en palabras:** F = 1 cuando al menos dos de los tres sensores valen 1 (la mayoría).

### Ejemplo de simplificación

$$F = A + A'B + AB'C$$
$$= (A + B) + AB'C \quad \text{(absorción del complemento)}$$
$$= A + B \quad \text{(absorción: } A + A(B'C) = A \text{ aplicada a } A + AB'C \text{)}$$

Se pasó de 3 términos (5 literales) a una sola OR de 2 entradas. Se comprueba con la fila A=0, B=0, C=1: original 0 + 0 + 0 = 0, simplificada 0 + 0 = 0.

### Costo en circuitos integrados

![Alarma: versión canónica vs simplificada](img/s2_alarma_costo.png)

| Versión | CI usados | Total |
|---|---|---|
| Canónica | 7404 + 2×7411 + 7432 | 4 CI |
| Simplificada | 7408 + 7432 | 2 CI |

Simplificar reduce compuertas, circuitos integrados y niveles lógicos.

### Taller por grupos (enunciados)

- Luz de pasillo: dos interruptores A y B conmutan la luz, y el maestro M (=1) la apaga siempre.
- Detector de primos: entrada ABC (0 a 7), F = 1 si el número es primo. Es decir, $F = \Sigma m(2,3,5,7)$.
- Bomba de agua: enciende si hay agua en la cisterna (B) y el tanque está bajo (A) o se pide en manual (C).
- Aviso de cinturón: suena si el motor está encendido (E) y el cinturón no está puesto (C) o la puerta está abierta (P).

Entregable: tabla, $\Sigma m$ y $\Pi M$, SOP canónica, versión simplificada, y recuento de compuertas y CI.

## Resumen de la Semana 2

- Circuito = expresión.
- La tabla de verdad manda: dos expresiones son equivalentes si y solo si tienen la misma tabla.
- La forma canónica siempre se puede obtener: $\Sigma m$ (filas con 1) y $\Pi M$ (filas con 0).
- Simplificar es elegir por costo, y el álgebra no garantiza el mínimo.

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

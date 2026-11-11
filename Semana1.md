
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

## Indice
<p align="center">
*<strong><a href="índice.md">ÍNDICE</a></strong>*
</p>

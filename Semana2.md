# Semana 2: Álgebra de Boole y formas canónicas

## 2.1 Un circuito y una expresión son lo mismo

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


## Indice
<p align="center">
*<strong><a href="índice.md">ÍNDICE</a></strong>*
</p>

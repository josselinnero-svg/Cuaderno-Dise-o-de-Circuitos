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

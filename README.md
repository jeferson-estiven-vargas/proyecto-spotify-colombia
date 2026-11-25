# Python · Estructuras de Datos (GA1-220501093-04-AA1-EV03)

**Aprendiz:** Jeferson Estiven Vargas García

## Descripción del proyecto

Practiqué las 5 estructuras de datos básicas de Python: listas, tuplas, diccionarios, conjuntos y comprehensions. Por cada tema hice ejercicios sencillos para entender cómo funciona, y al final un "reto" aplicado a la vida real.

## Temas aprendidos

- **Listas** → una caja donde puedo meter varias cosas, cambiarlas, agregar o quitar cuando quiera. Son las más flexibles.
- **Tuplas** → parecidas a las listas, pero inmutables una vez creadas. Sirven para datos que no deberían cambiar (como una coordenada) o cuando una función devuelve varios valores a la vez.
- **Diccionarios** → guardan la información con una clave en vez de una posición. Por ejemplo, `persona["nombre"]` en vez de recordar que está en la posición 0.
- **Conjuntos** → no permiten valores repetidos y son útiles para comparar grupos (comunes, diferencias, etc.).
- **Comprehensions** → una forma más corta de escribir un `for` que arma una lista, diccionario o conjunto. Al principio se ve raro, pero ahorra líneas de código.

## Retos resueltos

**Reto 1 (Listas):** sistema de gestión de inventario con listas anidadas (`[nombre, cantidad, precio]`), con funciones para actualizar precio, registrar ventas, añadir productos y mostrar el inventario final.

![Reto módulo 1](images/modulo1_listas/reto_modulo1.png)

**Reto 2 (Tuplas):** catálogo de películas como tupla de subtuplas (`titulo, director, año, puntuación`), con desempaquetado, el operador `*`, búsqueda por director y estadísticas (mínima, máxima, promedio).

![Reto módulo 2](images/modulo2_tuplas/reto_modulo2.png)

**Reto 3 (Diccionarios):** análisis de ventas por región con un diccionario anidado (`{región: {Q1, Q2, Q3, Q4}}`), calculando totales por región y trimestre, la región líder, porcentajes con dict comprehension y un reporte final ordenado.

![Reto módulo 3](images/modulo3_diccionarios/reto_modulo3.png)

**Reto 4 (Conjuntos):** catálogos de 3 tiendas (unión, intersección, exclusivos, `isdisjoint()`) y recomendaciones de películas según géneros de 3 usuarios usando `& | - ^` y `<=`.

![Reto módulo 4](images/modulo4_conjuntos/reto_modulo4.png)

**Reto 5 (Comprehensions):** analizador de ventas usando list, dict y set comprehension para totales, ranking premium, categorías únicas y un resumen final con el gran total.

![Reto módulo 5](images/modulo5_comprehensions/reto_modulo5.png)

## Reflexión

Con esta actividad entendí que no toda la información se debe guardar en una lista; ahora tengo más claro cuándo conviene una tupla, un diccionario o un conjunto según lo que necesito hacer con los datos. Lo que más trabajo me costó fue acostumbrarme a las comprehensions, pero ya le voy cogiendo el gusto porque el código queda más corto y se entiende mejor de un vistazo.

# Foro2_Colecciones_Java
Coleccion Set: identificar sus características, escenarios posibles de aplicación y ejemplo en codigo

---


## ¿Qué es un Set?

Un **Set** es una colección que **no permite elementos duplicados**. Modela el concepto matemático de *conjunto*: lo importante es **si un elemento pertenece o no**, no cuántas veces aparece ni en qué posición está.

En Java, `Set<E>` es una interfaz del paquete `java.util` que extiende a `Collection<E>`. No agrega métodos nuevos a `Collection`, pero **cambia el contrato**: `add(e)` devuelve `false` y no modifica el conjunto si `e` ya existe.


---

## Características principales

- **Sin duplicados**: la unicidad se define con `equals()` / `hashCode()` (HashSet) o con `compareTo()` / `Comparator` (TreeSet).
- **Sin acceso por índice**: no existe `get(i)`; se recorre con *for-each* o iteradores.
- **Búsqueda de pertenencia muy eficiente** (`contains`), según la implementación.
- **Orden depende de la implementación**: sin orden, orden de inserción u orden natural.
- **Admite operaciones de conjuntos**: unión, intersección, diferencia y subconjunto.
- **Nulos**: `HashSet` y `LinkedHashSet` admiten un `null`; `TreeSet` (orden natural) y `Set.of()` no.

---

##  Tipos de Set en Java

| Implementación | Orden | Estructura interna | Operaciones básicas | Hilos | Nulos | Cuándo usarlo |
|---|---|---|---|---|---|---|
| `HashSet` | Ninguno garantizado | Tabla hash (`HashMap`) | O(1) promedio | No | Un `null` | Opción por defecto, máxima velocidad |
| `LinkedHashSet` | De inserción | Tabla hash + lista doblemente enlazada | O(1) promedio | No | Un `null` | Quitar duplicados conservando el orden |
| `TreeSet` | Ordenado (natural o `Comparator`) | Árbol rojo-negro | O(log n) | No | No (orden natural) | Datos ordenados, rangos, mínimo/máximo |
| `EnumSet` | Orden de declaración del enum | Vector de bits | O(1) | No | No | Conjuntos de valores de un `enum` |
| `Set.of(...)` / `Set.copyOf()` | No especificado | Inmutable | O(1) | Sí (inmutable) | No | Constantes y datos de solo lectura |
| `CopyOnWriteArraySet` | De inserción | Arreglo copiado en cada escritura | `contains` O(n), escritura O(n) | Sí | Sí | Pocas escrituras, muchas lecturas concurrentes |
| `ConcurrentSkipListSet` | Ordenado | Skip list | O(log n) | Sí | No | Conjunto ordenado y concurrente |

Interfaces especializadas:

- **`SortedSet`**: añade `first()`, `last()`, `headSet()`, `tailSet()`, `subSet()`.
- **`NavigableSet`**: añade `floor()`, `ceiling()`, `lower()`, `higher()`, `descendingSet()`, `pollFirst()`, `pollLast()`.

> También existen `Collections.synchronizedSet(...)` (envoltorio sincronizado) y `Collections.unmodifiableSet(...)` (vista de solo lectura).

---

## Complejidad temporal

| Operación | HashSet | LinkedHashSet | TreeSet | EnumSet |
|---|---|---|---|---|
| `add` | O(1)* | O(1)* | O(log n) | O(1) |
| `remove` | O(1)* | O(1)* | O(log n) | O(1) |
| `contains` | O(1)* | O(1)* | O(log n) | O(1) |
| Iterar todo | O(n + capacidad) | O(n) | O(n) | O(n) |
| Mínimo / máximo | O(n) | O(n) | O(log n) | O(n) |

\* Promedio, con una buena función hash. Con muchas colisiones puede degradarse; desde Java 8 los *buckets* grandes se convierten en árboles, lo que limita el peor caso a O(log n).

Espacio: O(n) en todos los casos.

---

## Set vs. otras estructuras

| Característica | **Set** | **List** (`ArrayList`) | **Map** (`HashMap`) | **Array** | **Queue / Deque** |
|---|:---:|:---:|:---:|:---:|:---:|
| Permite duplicados | ❌ | ✅ | Claves ❌ / valores ✅ | ✅ | ✅ |
| Acceso por índice | ❌ | ✅ O(1) | ❌ (por clave) | ✅ O(1) | ❌ |
| Buscar elemento | **O(1) / O(log n)** | O(n) | O(1) por clave | O(n) | O(n) |
| Mantiene orden | Depende | ✅ inserción | Depende | ✅ posición | ✅ FIFO / LIFO |
| Almacena pares clave-valor | ❌ | ❌ | ✅ | ❌ | ❌ |
| Tamaño dinámico | ✅ | ✅ | ✅ | ❌ | ✅ |
| Uso típico | Unicidad y pertenencia | Secuencias | Diccionarios | Datos fijos | Procesamiento por turnos |

**Dato clave:** `HashSet` está implementado internamente con un `HashMap` (los elementos son las claves y el valor es un objeto constante). Un Set es, en esencia, un Map que solo guarda claves.

---

## Ventajas y desventajas

### Ventajas

- Garantiza **unicidad** sin código adicional.
- **Búsqueda, inserción y borrado rápidos** (O(1) en HashSet).
- API clara para **operaciones de conjuntos** (`addAll`, `retainAll`, `removeAll`, `containsAll`).
- Elimina duplicados de una colección en una sola línea.
- `TreeSet` ofrece **orden y consultas por rango** sin ordenar manualmente.
- `EnumSet` es extremadamente compacto y veloz.

### Desventajas

- **No hay acceso por índice** ni posición fija (salvo orden de inserción en `LinkedHashSet`).
- `HashSet` **no garantiza orden** de iteración.
-  Mayor **consumo de memoria** que un `ArrayList` (tabla hash, nodos, objetos auxiliares).
- Requiere **`equals()` y `hashCode()` correctos** (o un `Comparator` coherente); si no, falla de forma silenciosa.
- Mutar un elemento después de insertarlo puede **corromper** el Set.
- `TreeSet` es más lento (O(log n)) y no admite elementos no comparables.
- No almacena asociaciones: si necesitas clave-valor, usa un `Map`.

---

## ¿Cuándo usar un Set?

**Úsalo cuando:**

- Necesitas **eliminar duplicados** o garantizar elementos únicos (correos, matrículas, IDs).
- Haces **muchas verificaciones de pertenencia** (`¿está X en la colección?`).
- Aplicas **operaciones de conjuntos** (permisos comunes entre usuarios, etiquetas compartidas).
- Necesitas elementos **ordenados y únicos** (`TreeSet`) o únicos con **orden de inserción** (`LinkedHashSet`).
- Registras nodos **visitados** en recorridos de grafos (BFS/DFS).

**Evítalo cuando:**

- Necesitas acceder por posición o permitir repetidos → `List`.
- Debes asociar un valor a cada clave → `Map`.
- Procesas elementos por turnos → `Queue` / `Deque`.

---



## Errores comunes

1. **Olvidar `equals()` y `hashCode()`** en clases propias: el Set tratará como distintos a objetos "iguales".
2. **Sobrescribir solo uno** de los dos métodos: rompe el contrato y produce resultados inconsistentes.
3. **Mutar un objeto dentro de un `HashSet`** después de insertarlo: queda en el *bucket* equivocado y `contains` falla.
4. **Un `Comparator` incoherente en `TreeSet`**: si `compare()` devuelve 0, el elemento se considera duplicado aunque `equals()` diga que no.
5. **Modificar el Set mientras se itera**: lanza `ConcurrentModificationException`; usa `Iterator.remove()` o `removeIf()`.
6. **Asumir orden en `HashSet`**: el orden puede cambiar entre ejecuciones o versiones.
7. **Pasar elementos repetidos a `Set.of(...)`**: lanza `IllegalArgumentException`.

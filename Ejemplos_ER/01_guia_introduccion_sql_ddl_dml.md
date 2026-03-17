# SQL Práctico: DDL, DML y Estructura de Sentencias

> Guía directa con ejemplos reales. Sin rodeos.

---

## 1. ¿Qué es una sentencia SQL?

Una **sentencia SQL** es una instrucción que le das al motor de base de datos para que haga algo: crear una tabla, insertar un dato, consultarlo, borrarlo.

```sql
-- Esto es una sentencia SQL
SELECT nombre FROM Estudiante;
```

Tiene una estructura fija: siempre empieza con un **verbo** (`SELECT`, `INSERT`, `CREATE`...) y termina con `;`.

---

## 2. Categorías principales

| Categoría | Para qué sirve | Comandos |
|-----------|----------------|----------|
| **DDL** — Data Definition Language | Definir la estructura (tablas, columnas) | `CREATE`, `ALTER`, `DROP` |
| **DML** — Data Manipulation Language | Manejar los datos dentro de las tablas | `INSERT`, `UPDATE`, `DELETE` |
| **DQL** — Data Query Language | Consultar / leer datos | `SELECT` |

> **Regla práctica:** DDL toca la estructura, DML toca los datos.

---

---

## 3. DDL — Definir la estructura

### 3.1 CREATE TABLE

Crea una tabla nueva. Defines el nombre de cada columna y su tipo de dato.

```sql
CREATE TABLE Estudiante (
    id_estudiante INT          IDENTITY(1,1) PRIMARY KEY,
    nombre        VARCHAR(100) NOT NULL,
    apellido      VARCHAR(100) NOT NULL,
    carne         VARCHAR(20)  UNIQUE NOT NULL,
    fecha_nac     DATE
);
```

**Desglose:**

| Parte | Qué hace |
|-------|----------|
| `INT` | Número entero |
| `VARCHAR(100)` | Texto de hasta 100 caracteres |
| `DATE` | Fecha (sin hora) |
| `IDENTITY(1,1)` | El valor se genera automático: empieza en 1, sube de 1 en 1 |
| `PRIMARY KEY` | Identifica de forma única cada fila |
| `NOT NULL` | El campo es obligatorio |
| `UNIQUE` | No se puede repetir el valor en esa columna |

---

### 3.2 CREATE TABLE con llave foránea (FK)

Una llave foránea conecta dos tablas. Le dice al motor: "este valor debe existir en la otra tabla".

```sql
CREATE TABLE Inscripcion (
    id_inscripcion INT  IDENTITY(1,1) PRIMARY KEY,
    id_estudiante  INT  NOT NULL,
    id_curso       INT  NOT NULL,
    nota_final     DECIMAL(5,2),

    -- Estas dos líneas crean las llaves foráneas
    FOREIGN KEY (id_estudiante) REFERENCES Estudiante(id_estudiante),
    FOREIGN KEY (id_curso)      REFERENCES Curso(id_curso)
);
```

> Si intentás insertar un `id_estudiante` que no existe en `Estudiante`, el motor rechaza el dato. Eso es exactamente lo que querés.

---

### 3.3 Tipos de datos comunes en SQL Server

| Tipo | Uso típico | Ejemplo |
|------|-----------|---------|
| `INT` | IDs, cantidades | `42` |
| `VARCHAR(n)` | Nombres, correos, códigos | `'Kevin'` |
| `TEXT` | Descripciones largas | `'Curso de...'` |
| `DECIMAL(p,s)` | Precios, notas | `85.50` |
| `DATE` | Solo fecha | `'2025-03-09'` |
| `DATETIME` | Fecha + hora | `'2025-03-09 14:30:00'` |
| `BIT` | Booleano (0/1) | `1` |

---

### 3.4 ALTER TABLE — Modificar una tabla existente

```sql
-- Agregar una columna nueva
ALTER TABLE Estudiante
ADD email VARCHAR(150);

-- Cambiar el tipo de una columna
ALTER TABLE Estudiante
ALTER COLUMN email VARCHAR(200);

-- Eliminar una columna
ALTER TABLE Estudiante
DROP COLUMN email;
```

> Usás `ALTER` cuando la tabla ya existe y necesitás ajustarla sin borrar los datos.

---

### 3.5 DROP TABLE — Eliminar una tabla

```sql
-- Elimina la tabla completa con todos sus datos
DROP TABLE Inscripcion;
```

> **Cuidado:** `DROP` no se puede deshacer. Borra la tabla y todo su contenido permanentemente.

---

### 3.6 Constraints (restricciones)

Los constraints son reglas que ponés sobre las columnas para proteger la integridad de los datos.

```sql
CREATE TABLE Pedido (
    id_pedido  INT         IDENTITY(1,1) PRIMARY KEY,
    estado     VARCHAR(20) NOT NULL DEFAULT 'pendiente',
    id_cliente INT         NOT NULL,

    -- CHECK limita los valores válidos
    CONSTRAINT chk_estado CHECK (estado IN ('pendiente','enviado','entregado','cancelado')),

    FOREIGN KEY (id_cliente) REFERENCES Cliente(id_cliente)
);
```

| Constraint | Qué hace |
|------------|----------|
| `PRIMARY KEY` | Identifica la fila, no puede ser NULL ni repetirse |
| `FOREIGN KEY` | Referencia a otra tabla |
| `UNIQUE` | No permite valores duplicados en esa columna |
| `NOT NULL` | El campo no puede quedar vacío |
| `DEFAULT` | Valor por defecto si no se especifica uno |
| `CHECK` | Valida que el valor cumpla una condición |

---

---

## 4. DML — Manipular los datos

### 4.1 INSERT — Insertar filas

**Forma básica:** especificás qué columnas llenás y en qué orden.

```sql
INSERT INTO Estudiante (nombre, apellido, carne, fecha_nac)
VALUES ('Kevin', 'Xiloj', '202110001', '2001-05-14');
```

**Insertar varios registros a la vez:**

```sql
INSERT INTO Estudiante (nombre, apellido, carne, fecha_nac) VALUES
('Karla',  'Mejía',   '202110002', '2002-03-21'),
('Andrés', 'Pineda',  '202010003', '2000-11-08'),
('Lucía',  'Hernández','202110004', '2001-07-30');
```

> No incluís `id_estudiante` porque es `IDENTITY` — el motor lo asigna solo.

---

### 4.2 UPDATE — Actualizar datos

```sql
-- Actualizar un registro específico
UPDATE Estudiante
SET email = 'kevin@mail.com'
WHERE id_estudiante = 1;

-- Actualizar múltiples campos a la vez
UPDATE Estudiante
SET nombre = 'Kevin Josué', email = 'kevinjosue@mail.com'
WHERE carne = '202110001';
```

> **Siempre usá `WHERE`** en un `UPDATE`. Sin él, actualizás *todos* los registros de la tabla.

```sql
-- Esto actualiza TODOS los estudiantes — probablemente no es lo que querés
UPDATE Estudiante SET email = 'error@mail.com';
```

---

### 4.3 DELETE — Eliminar filas

```sql
-- Eliminar un registro específico
DELETE FROM Estudiante
WHERE id_estudiante = 3;

-- Eliminar según una condición
DELETE FROM Inscripcion
WHERE nota_final < 40;
```

> Igual que `UPDATE`: **siempre poné `WHERE`**. Sin él, borrás todos los datos de la tabla.

---

### 4.4 Diferencia entre DROP, TRUNCATE y DELETE

| Comando | Qué elimina | Recuperable |
|---------|------------|-------------|
| `DROP TABLE` | La tabla completa (estructura + datos) | No |
| `TRUNCATE TABLE` | Todos los datos, pero deja la tabla vacía | No (generalmente) |
| `DELETE FROM` | Las filas que cumplan el `WHERE` | Depende de la transacción |

```sql
-- Vaciar una tabla rápido (sin WHERE, sin LOG por fila)
TRUNCATE TABLE Inscripcion;
```

---

---

## 5. Estructura de las sentencias

### 5.1 Anatomía de un SELECT

Un `SELECT` se construye con cláusulas. Cada cláusula tiene un propósito.

```sql
SELECT   columnas_que_querés_ver       -- qué mostrar
FROM     tabla_principal               -- de dónde
JOIN     otra_tabla ON condicion       -- unir con otra tabla
WHERE    condicion_de_filtro           -- filtrar filas
GROUP BY columna_de_agrupacion         -- agrupar
HAVING   condicion_sobre_grupo         -- filtrar grupos (después de agrupar)
ORDER BY columna ASC|DESC;             -- ordenar el resultado
```

**Orden de ejecución real** (el motor no lo lee de arriba a abajo):

```
FROM → JOIN → WHERE → GROUP BY → HAVING → SELECT → ORDER BY
```

> Esto explica por qué no podés usar un alias definido en `SELECT` dentro del `WHERE` — el `WHERE` se evalúa antes del `SELECT`.

---

### 5.2 WHERE — Filtrar filas

```sql
-- Igual
WHERE estado = 'activo'

-- Diferente
WHERE estado <> 'cancelado'

-- Rango
WHERE precio BETWEEN 100 AND 500

-- Lista de valores
WHERE estado IN ('pendiente', 'enviado')

-- Texto que contiene
WHERE nombre LIKE '%García%'

-- Valor nulo
WHERE fecha_devolucion IS NULL

-- Combinaciones
WHERE estado = 'activo' AND stock > 0
WHERE categoria = 'Electrónica' OR precio < 200
```

---

### 5.3 JOIN — Unir tablas

Un `JOIN` combina filas de dos tablas basándose en una columna en común.

```sql
-- INNER JOIN: solo trae filas que tienen coincidencia en ambas tablas
SELECT e.nombre, c.nombre AS curso
FROM Inscripcion i
INNER JOIN Estudiante e ON i.id_estudiante = e.id_estudiante
INNER JOIN Curso      c ON i.id_curso      = c.id_curso;
```

```sql
-- LEFT JOIN: trae todas las filas de la tabla izquierda,
-- aunque no tengan coincidencia en la derecha (pone NULL)
SELECT c.nombre, COUNT(i.id_inscripcion) AS inscritos
FROM Curso c
LEFT JOIN Inscripcion i ON c.id_curso = i.id_curso
GROUP BY c.id_curso, c.nombre;
```

**Diagrama mental:**

```
INNER JOIN  → solo la intersección
LEFT JOIN   → todo lo de la izquierda + lo que coincida a la derecha
RIGHT JOIN  → todo lo de la derecha + lo que coincida a la izquierda
```

---

### 5.4 GROUP BY y funciones de agregación

`GROUP BY` agrupa filas con el mismo valor en una columna.  
Las funciones de agregación calculan algo sobre cada grupo.

```sql
SELECT
    c.nombre          AS curso,
    COUNT(*)          AS total_inscritos,
    AVG(nota_final)   AS promedio,
    MAX(nota_final)   AS nota_mas_alta,
    MIN(nota_final)   AS nota_mas_baja
FROM Inscripcion i
INNER JOIN Curso c ON i.id_curso = c.id_curso
GROUP BY c.id_curso, c.nombre;
```

| Función | Qué hace |
|---------|----------|
| `COUNT(*)` | Cuenta filas |
| `COUNT(columna)` | Cuenta filas donde la columna no es NULL |
| `SUM(columna)` | Suma los valores |
| `AVG(columna)` | Calcula el promedio |
| `MAX(columna)` | Valor máximo |
| `MIN(columna)` | Valor mínimo |

---

### 5.5 HAVING — Filtrar grupos

`WHERE` filtra filas individuales. `HAVING` filtra grupos (después del `GROUP BY`).

```sql
-- Cursos con más de 10 estudiantes inscritos
SELECT
    c.nombre,
    COUNT(i.id_inscripcion) AS inscritos
FROM Curso c
INNER JOIN Inscripcion i ON c.id_curso = i.id_curso
GROUP BY c.id_curso, c.nombre
HAVING COUNT(i.id_inscripcion) > 10;
```

> **Regla:** si la condición usa una función de agregación (`COUNT`, `SUM`, etc.), va en `HAVING`. Si no, va en `WHERE`.

---

### 5.6 ORDER BY — Ordenar resultados

```sql
-- Orden ascendente (A-Z, menor a mayor) — es el default
SELECT nombre, apellido FROM Estudiante ORDER BY apellido ASC;

-- Orden descendente (Z-A, mayor a menor)
SELECT nombre, nota_final FROM Inscripcion ORDER BY nota_final DESC;

-- Ordenar por múltiples columnas
SELECT nombre, apellido, nota_final
FROM Inscripcion
ORDER BY nota_final DESC, apellido ASC;
```

---

### 5.7 Ejemplo completo integrando todo

```sql
-- Docentes que tienen más de 1 curso asignado,
-- ordenados por cantidad de cursos de mayor a menor
SELECT
    d.nombre + ' ' + d.apellido AS docente,
    d.especialidad,
    COUNT(c.id_curso)           AS cursos_asignados
FROM Docente d
INNER JOIN Curso c ON d.id_docente = c.id_docente
GROUP BY d.id_docente, d.nombre, d.apellido, d.especialidad
HAVING COUNT(c.id_curso) > 1
ORDER BY cursos_asignados DESC;
```

**Trazando la ejecución:**
1. `FROM Docente` + `JOIN Curso` → combina las dos tablas
2. `GROUP BY` → agrupa por docente
3. `HAVING COUNT > 1` → descarta docentes con 1 solo curso
4. `SELECT` → elige qué columnas mostrar
5. `ORDER BY` → ordena el resultado final

---

---

## Resumen rápido

```
DDL → Estructura
  CREATE TABLE  → crear tabla
  ALTER TABLE   → modificar tabla
  DROP TABLE    → eliminar tabla

DML → Datos
  INSERT INTO   → agregar filas
  UPDATE        → modificar filas (¡siempre con WHERE!)
  DELETE FROM   → eliminar filas (¡siempre con WHERE!)

DQL → Consultas
  SELECT ... FROM ... WHERE ... GROUP BY ... HAVING ... ORDER BY
```

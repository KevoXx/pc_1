# Ejercicios de Diseño de Bases de Datos — Parte 2: SQL Server

> **Objetivo:** Implementar los modelos ER de la Parte 1 en SQL Server.  
> Se cubre **DDL** (crear y modificar estructura), **DML** (insertar, actualizar, eliminar datos) y **DQL** (consultas SELECT).

---

## Referencia rápida de categorías

| Categoría | Significado | Comandos principales |
|-----------|-------------|----------------------|
| **DDL** | Data Definition Language — define la estructura | `CREATE`, `ALTER`, `DROP` |
| **DML** | Data Manipulation Language — manipula los datos | `INSERT`, `UPDATE`, `DELETE` |
| **DQL** | Data Query Language — consulta los datos | `SELECT` |

---

## Ejercicio 1: Sistema de Biblioteca

### DDL — Crear las tablas
```sql
-- Tabla de autores
CREATE TABLE Autor (
    id_autor        INT           IDENTITY(1,1) PRIMARY KEY,
    nombre          VARCHAR(100)  NOT NULL,
    apellido        VARCHAR(100)  NOT NULL,
    nacionalidad    VARCHAR(80),
    fecha_nacimiento DATE
);

-- Tabla de libros
CREATE TABLE Libro (
    id_libro            INT           IDENTITY(1,1) PRIMARY KEY,
    titulo              VARCHAR(255)  NOT NULL,
    isbn                VARCHAR(20)   UNIQUE NOT NULL,
    anio_publicacion    INT,
    editorial           VARCHAR(150),
    num_paginas         INT
);

-- Tabla de usuarios
CREATE TABLE Usuario (
    id_usuario  INT           IDENTITY(1,1) PRIMARY KEY,
    nombre      VARCHAR(100)  NOT NULL,
    apellido    VARCHAR(100)  NOT NULL,
    telefono    VARCHAR(20),
    direccion   VARCHAR(255)
);

-- Tabla intermedia: resuelve la relación M:N entre Libro y Autor
CREATE TABLE Libro_Autor (
    id_libro    INT NOT NULL,
    id_autor    INT NOT NULL,
    PRIMARY KEY (id_libro, id_autor),
    FOREIGN KEY (id_libro)  REFERENCES Libro(id_libro),
    FOREIGN KEY (id_autor)  REFERENCES Autor(id_autor)
);

-- Tabla de préstamos: resuelve la relación M:N entre Usuario y Libro,
-- con atributos propios (fechas)
CREATE TABLE Prestamo (
    id_prestamo         INT  IDENTITY(1,1) PRIMARY KEY,
    fecha_prestamo      DATE NOT NULL,
    fecha_devolucion    DATE,
    id_usuario          INT  NOT NULL,
    id_libro            INT  NOT NULL,
    FOREIGN KEY (id_usuario) REFERENCES Usuario(id_usuario),
    FOREIGN KEY (id_libro)   REFERENCES Libro(id_libro)
);
```

### DML — Poblar la base de datos
```sql
-- Insertar autores
INSERT INTO Autor (nombre, apellido, nacionalidad, fecha_nacimiento) VALUES
('Gabriel', 'García Márquez', 'Colombiana',  '1927-03-06'),
('Jorge Luis', 'Borges',      'Argentina',   '1899-08-24'),
('Isabel',    'Allende',      'Chilena',     '1942-08-02'),
('Umberto',   'Eco',          'Italiana',    '1932-01-05');

-- Insertar libros
INSERT INTO Libro (titulo, isbn, anio_publicacion, editorial, num_paginas) VALUES
('Cien años de soledad',              '978-0-06-088328-7', 1967, 'Sudamericana',   417),
('El amor en los tiempos del cólera', '978-0-14-028525-3', 1985, 'Oveja Negra',   348),
('Ficciones',                         '978-0-8021-3030-0', 1944, 'Sur',           174),
('El nombre de la rosa',              '978-0-15-144647-6', 1980, 'Bompiani',      502),
('La casa de los espíritus',          '978-0-14-028773-8', 1982, 'Plaza & Janés', 433);

-- Insertar usuarios
INSERT INTO Usuario (nombre, apellido, telefono, direccion) VALUES
('Ana',    'López',    '5555-1111', 'Zona 1, Ciudad de Guatemala'),
('Carlos', 'Pérez',   '5555-2222', 'Zona 10, Ciudad de Guatemala'),
('María',  'González', '5555-3333', 'Mixco, Guatemala');

-- Asociar libros con autores
INSERT INTO Libro_Autor (id_libro, id_autor) VALUES
(1, 1),  -- Cien años de soledad   -> García Márquez
(2, 1),  -- El amor en los tiempos -> García Márquez
(3, 2),  -- Ficciones              -> Borges
(4, 4),  -- El nombre de la rosa   -> Eco
(5, 3);  -- La casa de los espíritus -> Allende

-- Registrar préstamos
INSERT INTO Prestamo (fecha_prestamo, fecha_devolucion, id_usuario, id_libro) VALUES
('2025-01-10', '2025-01-24', 1, 1),
('2025-01-15', NULL,         2, 3),  -- pendiente de devolución
('2025-02-01', '2025-02-15', 3, 5),
('2025-02-10', NULL,         1, 4);  -- pendiente de devolución

-- Actualizar teléfono de un usuario
UPDATE Usuario
SET telefono = '5555-9999'
WHERE id_usuario = 2;

-- Registrar devolución de un préstamo pendiente
UPDATE Prestamo
SET fecha_devolucion = '2025-03-01'
WHERE id_prestamo = 2;
```

### DQL — Consultas
```sql
-- 1. Listar todos los libros con su(s) autor(es)
SELECT
    l.titulo,
    a.nombre + ' ' + a.apellido AS autor,
    l.anio_publicacion,
    l.editorial
FROM Libro l
INNER JOIN Libro_Autor la ON l.id_libro = la.id_libro
INNER JOIN Autor a        ON la.id_autor = a.id_autor
ORDER BY l.titulo;

-- 2. Préstamos activos (sin fecha de devolución)
SELECT
    u.nombre + ' ' + u.apellido               AS usuario,
    l.titulo,
    p.fecha_prestamo,
    DATEDIFF(DAY, p.fecha_prestamo, GETDATE()) AS dias_en_prestamo
FROM Prestamo p
INNER JOIN Usuario u ON p.id_usuario = u.id_usuario
INNER JOIN Libro   l ON p.id_libro   = l.id_libro
WHERE p.fecha_devolucion IS NULL;

-- 3. Cuántos libros ha prestado cada usuario
SELECT
    u.nombre + ' ' + u.apellido AS usuario,
    COUNT(p.id_prestamo)        AS total_prestamos
FROM Usuario u
LEFT JOIN Prestamo p ON u.id_usuario = p.id_usuario
GROUP BY u.id_usuario, u.nombre, u.apellido
ORDER BY total_prestamos DESC;

-- 4. Libros que nunca han sido prestados
SELECT l.titulo, l.isbn
FROM Libro l
WHERE l.id_libro NOT IN (
    SELECT DISTINCT id_libro FROM Prestamo
);
```

---

## Ejercicio 2: Sistema de Tienda en Línea

### DDL — Crear las tablas
```sql
-- Tabla de clientes
CREATE TABLE Cliente (
    id_cliente  INT           IDENTITY(1,1) PRIMARY KEY,
    nombre      VARCHAR(100)  NOT NULL,
    apellido    VARCHAR(100)  NOT NULL,
    email       VARCHAR(150)  UNIQUE NOT NULL,
    telefono    VARCHAR(20),
    direccion   VARCHAR(255)
);

-- Tabla de productos
-- CORRECCIÓN: TEXT está obsoleto desde SQL Server 2005; usar VARCHAR(MAX)
CREATE TABLE Producto (
    id_producto INT             IDENTITY(1,1) PRIMARY KEY,
    nombre      VARCHAR(150)    NOT NULL,
    descripcion VARCHAR(MAX),
    precio      DECIMAL(10,2)   NOT NULL,
    stock       INT             NOT NULL DEFAULT 0,
    categoria   VARCHAR(80)
);

-- Tabla de pedidos
-- CORRECCIÓN: DEFAULT usa CAST(GETDATE() AS DATE) para coincidir con el tipo DATE
CREATE TABLE Pedido (
    id_pedido   INT           IDENTITY(1,1) PRIMARY KEY,
    fecha       DATE          NOT NULL DEFAULT CAST(GETDATE() AS DATE),
    estado      VARCHAR(20)   NOT NULL DEFAULT 'pendiente',
    CONSTRAINT chk_estado_pedido CHECK (estado IN ('pendiente','enviado','entregado','cancelado')),
    id_cliente  INT           NOT NULL,
    FOREIGN KEY (id_cliente) REFERENCES Cliente(id_cliente)
);

-- Tabla intermedia: un pedido contiene múltiples productos (M:N)
CREATE TABLE Detalle_Pedido (
    id_pedido       INT           NOT NULL,
    id_producto     INT           NOT NULL,
    cantidad        INT           NOT NULL,
    precio_unitario DECIMAL(10,2) NOT NULL,
    PRIMARY KEY (id_pedido, id_producto),
    FOREIGN KEY (id_pedido)   REFERENCES Pedido(id_pedido),
    FOREIGN KEY (id_producto) REFERENCES Producto(id_producto)
);

-- Tabla de pagos: relación 1:1 con Pedido
-- CORRECCIÓN: DEFAULT usa CAST(GETDATE() AS DATE) para coincidir con el tipo DATE
CREATE TABLE Pago (
    id_pago     INT           IDENTITY(1,1) PRIMARY KEY,
    metodo      VARCHAR(30)   NOT NULL,
    monto       DECIMAL(10,2) NOT NULL,
    fecha_pago  DATE          NOT NULL DEFAULT CAST(GETDATE() AS DATE),
    id_pedido   INT           UNIQUE NOT NULL,
    FOREIGN KEY (id_pedido) REFERENCES Pedido(id_pedido)
);
```

### DML — Poblar la base de datos
```sql
-- Insertar clientes
INSERT INTO Cliente (nombre, apellido, email, telefono, direccion) VALUES
('Luis',  'Morales', 'luis@mail.com',  '5544-1111', 'Zona 4, Ciudad de Guatemala'),
('Sofía', 'Ramírez', 'sofia@mail.com', '5544-2222', 'Antigua Guatemala'),
('Diego', 'Castro',  'diego@mail.com', '5544-3333', 'Quetzaltenango');

-- Insertar productos
INSERT INTO Producto (nombre, descripcion, precio, stock, categoria) VALUES
('Laptop HP 15',     'Intel i5, 8GB RAM, 256 SSD',  4500.00, 10, 'Electrónica'),
('Mouse inalámbrico','Logitech M185, 2.4GHz',         150.00, 50, 'Periféricos'),
('Teclado mecánico', 'RGB, switches blue',            850.00, 20, 'Periféricos'),
('Monitor 24"',      'Full HD, 75Hz, IPS',           2200.00, 15, 'Electrónica'),
('Mochila laptop',   'Resistente al agua, 15.6"',     320.00, 30, 'Accesorios');

-- Insertar pedidos
INSERT INTO Pedido (fecha, estado, id_cliente) VALUES
('2025-02-01', 'entregado', 1),
('2025-02-15', 'enviado',   2),
('2025-03-01', 'pendiente', 1);

-- Insertar detalles de pedido
INSERT INTO Detalle_Pedido (id_pedido, id_producto, cantidad, precio_unitario) VALUES
(1, 1, 1, 4500.00),  -- pedido 1: laptop
(1, 2, 2,  150.00),  -- pedido 1: 2 mouses
(2, 3, 1,  850.00),  -- pedido 2: teclado
(2, 4, 1, 2200.00),  -- pedido 2: monitor
(3, 5, 1,  320.00);  -- pedido 3: mochila

-- Insertar pagos
INSERT INTO Pago (metodo, monto, fecha_pago, id_pedido) VALUES
('tarjeta',       4800.00, '2025-02-01', 1),
('transferencia', 3050.00, '2025-02-15', 2);

-- Cancelar pedido 3 (contiene mochila, id_producto = 5)
UPDATE Pedido
SET estado = 'cancelado'
WHERE id_pedido = 3;

-- CORRECCIÓN: reducir stock del producto vendido en el pedido 1 (laptop, id_producto = 1)
-- El original estaba ubicado después del cancel del pedido 3, sugiriendo
-- erróneamente que correspondía a ese pedido.
UPDATE Producto
SET stock = stock - 1
WHERE id_producto = 1;
```

### DQL — Consultas
```sql
-- 1. Total facturado por pedido (suma de cantidad * precio_unitario)
SELECT
    p.id_pedido,
    c.nombre + ' ' + c.apellido           AS cliente,
    p.fecha,
    p.estado,
    SUM(dp.cantidad * dp.precio_unitario) AS total
FROM Pedido p
INNER JOIN Cliente        c  ON p.id_cliente = c.id_cliente
INNER JOIN Detalle_Pedido dp ON p.id_pedido  = dp.id_pedido
GROUP BY p.id_pedido, c.nombre, c.apellido, p.fecha, p.estado
ORDER BY p.fecha DESC;

-- 2. Productos más vendidos (por cantidad total, excluye cancelados)
SELECT
    pr.nombre,
    pr.categoria,
    SUM(dp.cantidad) AS unidades_vendidas
FROM Detalle_Pedido dp
INNER JOIN Producto pr ON dp.id_producto = pr.id_producto
INNER JOIN Pedido   p  ON dp.id_pedido   = p.id_pedido
WHERE p.estado <> 'cancelado'
GROUP BY pr.id_producto, pr.nombre, pr.categoria
ORDER BY unidades_vendidas DESC;

-- 3. Clientes con el gasto total acumulado
SELECT
    c.nombre + ' ' + c.apellido AS cliente,
    COUNT(DISTINCT p.id_pedido) AS total_pedidos,
    SUM(pa.monto)               AS gasto_total
FROM Cliente c
LEFT JOIN Pedido p  ON c.id_cliente = p.id_cliente
LEFT JOIN Pago   pa ON p.id_pedido  = pa.id_pedido
GROUP BY c.id_cliente, c.nombre, c.apellido
ORDER BY gasto_total DESC;

-- 4. Productos con stock bajo (menos de 15 unidades)
SELECT nombre, categoria, stock
FROM Producto
WHERE stock < 15
ORDER BY stock ASC;
```

---

## Ejercicio 3: Sistema de Colegio

### DDL — Crear las tablas
```sql
-- Tabla de docentes
CREATE TABLE Docente (
    id_docente   INT          IDENTITY(1,1) PRIMARY KEY,
    nombre       VARCHAR(100) NOT NULL,
    apellido     VARCHAR(100) NOT NULL,
    especialidad VARCHAR(100),
    telefono     VARCHAR(20)
);

-- Tabla de cursos: cada curso tiene un único docente asignado (1:N)
-- CORRECCIÓN: TEXT está obsoleto desde SQL Server 2005; usar VARCHAR(MAX)
CREATE TABLE Curso (
    id_curso    INT           IDENTITY(1,1) PRIMARY KEY,
    nombre      VARCHAR(150)  NOT NULL,
    codigo      VARCHAR(20)   UNIQUE NOT NULL,
    creditos    INT           NOT NULL,
    descripcion VARCHAR(MAX),
    id_docente  INT           NOT NULL,
    FOREIGN KEY (id_docente) REFERENCES Docente(id_docente)
);

-- Tabla de estudiantes
CREATE TABLE Estudiante (
    id_estudiante    INT          IDENTITY(1,1) PRIMARY KEY,
    nombre           VARCHAR(100) NOT NULL,
    apellido         VARCHAR(100) NOT NULL,
    fecha_nacimiento DATE,
    carne            VARCHAR(20)  UNIQUE NOT NULL,
    direccion        VARCHAR(255)
);

-- Tabla de inscripciones: tabla intermedia Estudiante-Curso con atributos de notas
CREATE TABLE Inscripcion (
    id_inscripcion  INT           IDENTITY(1,1) PRIMARY KEY,
    zona            DECIMAL(5,2),
    nota_final      DECIMAL(5,2),
    ciclo           VARCHAR(10)   NOT NULL,
    id_estudiante   INT           NOT NULL,
    id_curso        INT           NOT NULL,
    FOREIGN KEY (id_estudiante) REFERENCES Estudiante(id_estudiante),
    FOREIGN KEY (id_curso)      REFERENCES Curso(id_curso),
    CONSTRAINT uq_inscripcion UNIQUE (id_estudiante, id_curso, ciclo)
);
```

### DML — Poblar la base de datos
```sql
-- Insertar docentes
INSERT INTO Docente (nombre, apellido, especialidad, telefono) VALUES
('Roberto',  'Fuentes', 'Bases de Datos',   '4433-1111'),
('Patricia', 'Sánchez', 'Programación',      '4433-2222'),
('Miguel',   'Torres',  'Redes y Sistemas',  '4433-3333');

-- Insertar cursos
INSERT INTO Curso (nombre, codigo, creditos, descripcion, id_docente) VALUES
('Bases de Datos 1',       'BD-101',  4, 'Fundamentos de SQL y modelado ER',         1),
('Programación 1',         'PRG-101', 5, 'Introducción a la lógica de programación', 2),
('Redes de Computadoras',  'RDC-201', 3, 'Protocolos y arquitectura de redes',        3),
('Bases de Datos 2',       'BD-201',  4, 'SQL avanzado y optimización',               1);

-- Insertar estudiantes
INSERT INTO Estudiante (nombre, apellido, fecha_nacimiento, carne, direccion) VALUES
('Kevin',  'Xiloj',     '2001-05-14', '202110001', 'Mixco, Guatemala'),
('Karla',  'Mejía',     '2002-03-21', '202110002', 'Zona 6, Ciudad de Guatemala'),
('Andrés', 'Pineda',    '2000-11-08', '202010003', 'Villa Nueva, Guatemala'),
('Lucía',  'Hernández', '2001-07-30', '202110004', 'Antigua Guatemala');

-- Inscribir estudiantes en cursos (ciclo 2025-1)
INSERT INTO Inscripcion (zona, nota_final, ciclo, id_estudiante, id_curso) VALUES
(65.00, 72.00, '2025-1', 1, 1),
(70.00, 78.00, '2025-1', 1, 2),
(58.00, 62.00, '2025-1', 2, 1),
(80.00, 85.00, '2025-1', 2, 3),
(75.00, NULL,  '2025-1', 3, 4),  -- nota final pendiente
(90.00, 95.00, '2025-1', 4, 1),
(55.00, 60.00, '2025-1', 4, 2);

-- Registrar nota final de la inscripción pendiente
-- id_inscripcion = 5 -> Andrés Pineda, Bases de Datos 2
UPDATE Inscripcion
SET nota_final = 82.00
WHERE id_inscripcion = 5;

-- Corregir zona del mismo registro
-- id_estudiante=3 (Andrés), id_curso=4 (BD-201), ciclo='2025-1'
UPDATE Inscripcion
SET zona = 68.00
WHERE id_estudiante = 3 AND id_curso = 4 AND ciclo = '2025-1';
```

### DQL — Consultas
```sql
-- 1. Notas de todos los estudiantes con curso y docente
SELECT
    e.carne,
    e.nombre + ' ' + e.apellido AS estudiante,
    c.codigo,
    c.nombre                    AS curso,
    d.nombre + ' ' + d.apellido AS docente,
    i.zona,
    i.nota_final,
    i.ciclo
FROM Inscripcion i
INNER JOIN Estudiante e ON i.id_estudiante = e.id_estudiante
INNER JOIN Curso      c ON i.id_curso      = c.id_curso
INNER JOIN Docente    d ON c.id_docente    = d.id_docente
ORDER BY e.apellido, c.codigo;

-- 2. Promedio de nota final por curso
-- AVG ignora NULL nativamente; el filtro IS NOT NULL excluye cursos
-- donde ningún estudiante tiene nota registrada aún
SELECT
    c.codigo,
    c.nombre                    AS curso,
    d.nombre + ' ' + d.apellido AS docente,
    COUNT(i.id_inscripcion)     AS inscritos,
    AVG(i.nota_final)           AS promedio_final
FROM Curso c
INNER JOIN Inscripcion i ON c.id_curso   = i.id_curso
INNER JOIN Docente     d ON c.id_docente = d.id_docente
WHERE i.nota_final IS NOT NULL
GROUP BY c.id_curso, c.codigo, c.nombre, d.nombre, d.apellido
ORDER BY promedio_final DESC;

-- 3. Estudiantes que aprobaron (nota_final >= 61)
SELECT
    e.carne,
    e.nombre + ' ' + e.apellido AS estudiante,
    c.nombre                    AS curso,
    i.nota_final
FROM Inscripcion i
INNER JOIN Estudiante e ON i.id_estudiante = e.id_estudiante
INNER JOIN Curso      c ON i.id_curso      = c.id_curso
WHERE i.nota_final >= 61
ORDER BY i.nota_final DESC;

-- 4. Carga académica por estudiante (cantidad de cursos inscritos)
SELECT
    e.carne,
    e.nombre + ' ' + e.apellido AS estudiante,
    COUNT(i.id_inscripcion)     AS cursos_inscritos,
    SUM(c.creditos)             AS creditos_totales
FROM Estudiante e
INNER JOIN Inscripcion i ON e.id_estudiante = i.id_estudiante
INNER JOIN Curso       c ON i.id_curso      = c.id_curso
WHERE i.ciclo = '2025-1'
GROUP BY e.id_estudiante, e.carne, e.nombre, e.apellido
ORDER BY creditos_totales DESC;
```

---

## Ejercicio 4: Sistema de Hotel

### DDL — Crear las tablas
```sql
-- Tabla de huéspedes
CREATE TABLE Huesped (
    id_huesped      INT          IDENTITY(1,1) PRIMARY KEY,
    nombre          VARCHAR(100) NOT NULL,
    apellido        VARCHAR(100) NOT NULL,
    dpi_pasaporte   VARCHAR(30)  UNIQUE NOT NULL,
    nacionalidad    VARCHAR(80),
    telefono        VARCHAR(20),
    email           VARCHAR(150)
);

-- Tabla de habitaciones
CREATE TABLE Habitacion (
    id_habitacion   INT           IDENTITY(1,1) PRIMARY KEY,
    numero          VARCHAR(10)   UNIQUE NOT NULL,
    tipo            VARCHAR(20)   NOT NULL,
    precio_noche    DECIMAL(10,2) NOT NULL,
    capacidad       INT           NOT NULL,
    estado          VARCHAR(20)   NOT NULL DEFAULT 'disponible',
    CONSTRAINT chk_tipo_hab   CHECK (tipo   IN ('simple','doble','suite')),
    CONSTRAINT chk_estado_hab CHECK (estado IN ('disponible','ocupada','mantenimiento'))
);

-- Tabla de reservaciones
CREATE TABLE Reservacion (
    id_reservacion  INT          IDENTITY(1,1) PRIMARY KEY,
    fecha_checkin   DATE         NOT NULL,
    fecha_checkout  DATE         NOT NULL,
    num_personas    INT          NOT NULL,
    estado          VARCHAR(20)  NOT NULL DEFAULT 'confirmada',
    CONSTRAINT chk_estado_res CHECK (estado IN ('confirmada','activa','finalizada','cancelada')),
    id_huesped      INT          NOT NULL,
    id_habitacion   INT          NOT NULL,
    FOREIGN KEY (id_huesped)    REFERENCES Huesped(id_huesped),
    FOREIGN KEY (id_habitacion) REFERENCES Habitacion(id_habitacion)
);

-- Tabla de servicios adicionales
-- CORRECCIÓN: TEXT está obsoleto desde SQL Server 2005; usar VARCHAR(MAX)
CREATE TABLE Servicio (
    id_servicio INT           IDENTITY(1,1) PRIMARY KEY,
    nombre      VARCHAR(100)  NOT NULL,
    descripcion VARCHAR(MAX),
    precio      DECIMAL(10,2) NOT NULL
);

-- Tabla intermedia: una reservación puede incluir múltiples servicios (M:N)
CREATE TABLE Reservacion_Servicio (
    id_reservacion  INT           NOT NULL,
    id_servicio     INT           NOT NULL,
    cantidad        INT           NOT NULL DEFAULT 1,
    subtotal        DECIMAL(10,2) NOT NULL,
    PRIMARY KEY (id_reservacion, id_servicio),
    FOREIGN KEY (id_reservacion) REFERENCES Reservacion(id_reservacion),
    FOREIGN KEY (id_servicio)    REFERENCES Servicio(id_servicio)
);
```

### DML — Poblar la base de datos
```sql
-- Insertar huéspedes
INSERT INTO Huesped (nombre, apellido, dpi_pasaporte, nacionalidad, telefono, email) VALUES
('Carlos',  'Monterroso', '1234567890101', 'Guatemalteca',   '5566-1111', 'carlos@mail.com'),
('Jennifer','Smith',      'P12345678',     'Estadounidense', '5566-2222', 'jsmith@mail.com'),
('Andrea',  'Vásquez',    '9876543210101', 'Guatemalteca',   '5566-3333', 'andrea@mail.com');

-- Insertar habitaciones
INSERT INTO Habitacion (numero, tipo, precio_noche, capacidad, estado) VALUES
('101', 'simple',  350.00, 1, 'disponible'),
('102', 'doble',   550.00, 2, 'ocupada'),
('201', 'suite',  1200.00, 4, 'disponible'),
('202', 'doble',   550.00, 2, 'mantenimiento'),
('301', 'suite',  1500.00, 4, 'disponible');

-- Insertar reservaciones
INSERT INTO Reservacion (fecha_checkin, fecha_checkout, num_personas, estado, id_huesped, id_habitacion) VALUES
('2025-02-10', '2025-02-13', 2, 'finalizada', 1, 2),  -- Carlos, hab. 102
('2025-03-01', '2025-03-05', 1, 'activa',     2, 1),  -- Jennifer, hab. 101
('2025-03-10', '2025-03-15', 4, 'confirmada', 3, 3);  -- Andrea, hab. 201

-- Insertar servicios adicionales
INSERT INTO Servicio (nombre, descripcion, precio) VALUES
('Desayuno buffet', 'Desayuno incluido para una persona',   85.00),
('Lavandería',      'Servicio de lavado y planchado',      120.00),
('Spa',             'Masaje relajante 60 minutos',         350.00),
('Room service',    'Servicio a la habitación 24h',         50.00);

-- Asociar servicios a reservaciones
INSERT INTO Reservacion_Servicio (id_reservacion, id_servicio, cantidad, subtotal) VALUES
(1, 1, 3, 255.00),  -- reservación 1: 3 desayunos
(1, 4, 2, 100.00),  -- reservación 1: 2 room service
(2, 1, 4, 340.00),  -- reservación 2: 4 desayunos
(2, 3, 1, 350.00);  -- reservación 2: spa

-- Liberar habitación 102 al hacer checkout de la reservación 1 (Carlos)
UPDATE Habitacion
SET estado = 'disponible'
WHERE id_habitacion = 2;

-- CORRECCIÓN: el original finalizaba id_reservacion = 2 (Jennifer, hab. 101)
-- pero la habitación liberada arriba es la 102, que corresponde
-- a la reservación 1 (Carlos Monterroso). Se corrige el WHERE.
UPDATE Reservacion
SET estado = 'finalizada'
WHERE id_reservacion = 1;
```

### DQL — Consultas
```sql
-- 1. Reservaciones activas con detalle de habitación y huésped
SELECT
    h.nombre + ' ' + h.apellido                             AS huesped,
    h.nacionalidad,
    ha.numero                                               AS habitacion,
    ha.tipo,
    r.fecha_checkin,
    r.fecha_checkout,
    DATEDIFF(DAY, r.fecha_checkin, r.fecha_checkout)        AS noches,
    ha.precio_noche,
    DATEDIFF(DAY, r.fecha_checkin, r.fecha_checkout)
        * ha.precio_noche                                   AS costo_habitacion,
    r.estado
FROM Reservacion r
INNER JOIN Huesped    h  ON r.id_huesped    = h.id_huesped
INNER JOIN Habitacion ha ON r.id_habitacion = ha.id_habitacion
WHERE r.estado IN ('activa','confirmada')
ORDER BY r.fecha_checkin;

-- 2. Factura total por reservación (habitación + servicios)
SELECT
    r.id_reservacion,
    h.nombre + ' ' + h.apellido                                    AS huesped,
    ha.numero                                                       AS habitacion,
    DATEDIFF(DAY, r.fecha_checkin, r.fecha_checkout) * ha.precio_noche
                                                                    AS costo_habitacion,
    ISNULL(SUM(rs.subtotal), 0)                                     AS costo_servicios,
    DATEDIFF(DAY, r.fecha_checkin, r.fecha_checkout) * ha.precio_noche
        + ISNULL(SUM(rs.subtotal), 0)                               AS total_factura
FROM Reservacion r
INNER JOIN Huesped               h  ON r.id_huesped    = h.id_huesped
INNER JOIN Habitacion            ha ON r.id_habitacion = ha.id_habitacion
LEFT  JOIN Reservacion_Servicio  rs ON r.id_reservacion = rs.id_reservacion
GROUP BY r.id_reservacion, h.nombre, h.apellido, ha.numero,
         r.fecha_checkin, r.fecha_checkout, ha.precio_noche
ORDER BY r.id_reservacion;

-- 3. Habitaciones disponibles en este momento
SELECT numero, tipo, precio_noche, capacidad
FROM Habitacion
WHERE estado = 'disponible'
ORDER BY precio_noche ASC;

-- 4. Servicios más solicitados
SELECT
    s.nombre           AS servicio,
    SUM(rs.cantidad)   AS veces_solicitado,
    SUM(rs.subtotal)   AS ingresos_generados
FROM Reservacion_Servicio rs
INNER JOIN Servicio s ON rs.id_servicio = s.id_servicio
GROUP BY s.id_servicio, s.nombre
ORDER BY veces_solicitado DESC;
```

---

## Notas finales

- `IDENTITY(1,1)` es el equivalente en SQL Server al `AUTO_INCREMENT` de MySQL.
- `GETDATE()` retorna la fecha y hora actual del servidor (tipo `DATETIME`).
- `CAST(GETDATE() AS DATE)` es la forma correcta de asignar un default de fecha actual a columnas de tipo `DATE`.
- `VARCHAR(MAX)` reemplaza a `TEXT`, que está obsoleto desde SQL Server 2005 y falla en concatenaciones, `LEN()`, `UPPER()` y otros contextos.
- `DATEDIFF(DAY, fecha_inicio, fecha_fin)` calcula la diferencia en días entre dos fechas.
- `ISNULL(valor, reemplazo)` evita que los `NULL` arruinen cálculos numéricos.
- `AVG` ignora `NULL` de forma nativa; no es necesario filtrarlos para que no cuenten como cero.
- `CONSTRAINT chk_*` con `CHECK` limita los valores permitidos en una columna, útil para campos tipo "estado" o "tipo".
- Las llaves primarias compuestas (`PRIMARY KEY (col1, col2)`) en tablas intermedias garantizan que no existan duplicados en la relación.
# Bases de Datos — Modelo Entidad-Relación e Implementación SQL

Material de estudio organizado en orden progresivo: desde los conceptos básicos de SQL hasta la implementación completa de modelos relacionales en SQL Server.

---

## Contenido del repositorio

### `01_guia_introduccion_sql_ddl_dml.md`
**Guía introductoria — Leer primero**

Explica qué es SQL y sus categorías principales (DDL, DML, DQL) con ejemplos directos. Es el punto de partida antes de tocar cualquier ejercicio.

Temas cubiertos:
- Estructura de una sentencia SQL
- DDL: `CREATE`, `ALTER`, `DROP`
- DML: `INSERT`, `UPDATE`, `DELETE`
- DQL: `SELECT`

---

### `02_ejercicios_modelo_entidad_relacion.md`
**Ejercicios de diseño — Modelo Entidad-Relación**

Contiene ejercicios para practicar el diseño de bases de datos antes de escribir código SQL. El objetivo es identificar entidades, atributos y relaciones, y plasmarlos en un diagrama ER.

Ejercicios incluidos:
- Sistema de Biblioteca (nivel básico)
- Otros casos de estudio con distintos dominios

---

### `03_ejercicios_implementacion_sql_server.md`
**Implementación en SQL Server — Continuación de los ejercicios ER**

Toma los modelos diseñados en el archivo anterior y los convierte en código SQL real para SQL Server. Cubre la creación de tablas, inserción de datos y consultas.

Temas cubiertos:
- DDL: creación de tablas con claves primarias, foráneas y restricciones
- DML: inserción, actualización y eliminación de registros
- DQL: consultas `SELECT` con `JOIN`, filtros y agrupaciones

---

## Orden de estudio recomendado

```
01 → 02 → 03
```

1. Lee la guía introductoria para entender el lenguaje
2. Resuelve los ejercicios de diseño ER (en papel o con una herramienta de diagramas)
3. Implementa los modelos en SQL Server siguiendo los ejercicios de la parte 3

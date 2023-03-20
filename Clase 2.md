# Conceptos básicos
Bases de datos compuesta por el elemento básico Tabla, 
que tiene filas y columnas.
- Al conjunto de filas se le conoce como registros

## Tipos de datos
Algunas categorías o grupos:
- Numéricos exactos
  - bigint
  - int
  - smallint
  - tinyint
  - bit
  - decimal numeric, 
  - decimal(p,s)
- Numéricos aproximados
  - float
  - real
- Fecha y hora
  - datetime
  - smalldatetime
- Cadenas de caracterés
  - char (n)
  - varchar (n)
  - text

> Clic aquí para ver todos los [grupos de SQL Server](https://learn.microsoft.com/es-es/sql/t-sql/data-types/data-types-transact-sql?view=sql-server-ver16)

Desde el punto de vista de un Datawarehouse, tenemos 3 grandes grupos:
  - Números
  - Fechas
  - Textos
Es una base de datos de explotación no deberia haber 

## Querys
```SQL
-- Crear una base de datos
CREATE DATABASE name;
```

```SQL
-- Eliminar una base de datos correctamente
EXEC msdb.dbo.sp_delete_database_backuphistory @database_name = N'name'

GO
USE other_name
GO
-- Solo mantenga conexión el administrador
-- Cancele todas las operaciones con Rollback
ALTER DATABASE name SET SINGLE_USER WITH ROLLBACK IMMEDIATE 
GO
DROP DATABASE name
GO
```

```SQL
-- Crear tabla
CREATE TABLE demo (
  campo1 CHAR(10),
  campo2 VARCHAR(10)
)
```

```SQL
-- OPERACIONES CON LA TABLA
-- Adicionar columna
ALTER TABLE demo ADD campo3 VARCHAR(20) NULL
-- Eliminar columna
ALTER TABLE demo DROP COLUMN campo3
-- Actualizar columna
ALTER TABLE demo ALTER COLUMN campo1 VARCHAR(10)
-- Renombrar columna
EXEC sp_rename 'demo.campo1','NombreCliente','COLUMN'
```

> Cuando se actualiza la estructura de la columna, no se aplica el cambio al contenido de esta. Esta actividad debe realizarse posteriormente

```SQL
-- OPERACIONES CRUD
-- 1. INSERT (C)
INSERT INTO dbo.demo (campo1, campo2) VALUES ('name1','name2')
-- 2. SELECT (R)
SELECT * FROM dbo.demo
-- 3. UPDATE (U) 
UPDATE dbo.demo SET campo1 WHERE campo1 = 'value'
-- 4. DELETE (D)
DELETE dbo.demo WHERE campo1 = 'value'
```

## Backup y Restore
### Backup
- Método para sacar una copía de una base de datos.  
- Dos tipos:
  - Backup full
  - Backup incremental o diferencial
  - Backup de transactional log
- Realizar un backup consume recursos, ya que se lee todo el disco, y se recomienda en una hora de minimo uso.

```SQL
BACKUP DATABASE name
TO DISK = N'PATH\File.bak' WITH NOFORMAT, NOINIT, 
NAME = N'name operation',
SKIP, NOREWIND, NOUNLOAD, STATS = 10
GO
```

### Restore
```SQL
USE other_name
BACKUP LOG name TO DISK = N'PATH\File_log' WITH NOFORMAT, NOINIT, NAME = N'File_log', NOSKIP, NOREWIND, NOUNLOAD, NORECOVERY, STATS = 5
RESTORE DATABASE name FROM DISK = N'PATH\File.bak' WITH FILE = 2, NOUNLOAD, STATS = 5
GO
```

## BCP - Bulk copy program
> [Ver documentación](https://learn.microsoft.com/es-es/sql/tools/bcp-utility?view=sql-server-ver16)

```shell
# Previamente crear la tabla
bcp database.dbo.table in "PATH\file.txt" -c -T # Importar
bcp database.dbo.table out "PATH\file.txt" -c -T # Exportar
```
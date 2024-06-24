# Cruso Practico de SQL

## Introducción a SQL

### Breve historia de SQL

SQL (*Structured Query Language* - Lenguaje de consulta estucturada) fue creado en 1974 por IBM. Originalmente fue llamad SEQUEL, posteriormente se cambio el nombre por problemas de derechos de autor. Es un lenguaje que se baso en dos principios fundamentales:

1. Teoría de conjuntos
2. Álgebra relacional con Edgar Codd (científico informático inglés)

**Relation Company** (actualemten de nombre **Oracle**) creó el software Oracle V2 en 1979. Más adelante SQL se convertiría en un lenguaje estándar que unifica todo dentro de las bases de datos relacionales, se convirtio en una norma ANSI o ISO.

### Álgebra relacional

El álgebra relacional estudia basicamente las operaciones que se pueden realizar entre diversos conjuntos de datos.

No confundir las relaciones del álgebra relacional con las relaciones de una base de datos relacional.

- Las relaciones de una base de datos es cuando unes dos tablas.
- Las relaciones en álgebra relacional se refiere a una tabla.
  - La diferencia es conceptual: Las tablas pueden tener tuplas repetidas pero en el álgebra relacional cada relación no tiene un cuerpo, no tiene un primer ni último row.

#### Tipos de operadores

- **Operadores unarios:** Requiere una relación o tabla para funcionar
  - **Proyección:** Equivale al comando `SELECT`. Saca un número de columnas o atributos sin necesidad de hacer una unión con una segunda tabla.

    ``π<Nombre, Apellido, Email>(Tabla_Alumno)``

  - **Selección:** Equivale al comando `WHERE`. Consiste en el filtrado  de tuplas.

    ``σ<Suscripción=Expert>(Tabla_Alumno)``

- **Operadores binarios:** Requiere más de una tabla para operar.
  - **Producto cartesiano (x):** Toma todos los elementos de un tabla y los combina con los elementos de la segunda tabla.

    ``Docentes_Quinto_A x Alumnos_Quinto_A``

  - **Union (∪):** Obtiene los elementos que existen en una de las tablas o en la otra de las tablas.

    ``Alumnos_Quinto_A x Alumnos_Quinto_B``

  - **Diferencia (-):** Obtiene los elementos que existe en una de las tablas pero que no corresponde a la otra tabla.

    ``Alumnos_planExpertPlus - Alumnos_planFree``

### Qué es una proyección (`SELECT`)

Proyección equivale a `SELECT` y elegimos que queremos que se muestre en terminos de columnas.

    SELECT *;

El astrisco (*) es un comodín que indica que queremos traer una proyección completa (Todos los campos o columnas existentes en la tabla)

Se pueden crear alias para simplificar una sentencia.

    SELECT fields AS alias;

#### Funciones de agregación

Las funciones de agregación en SQL nos permiten efectuar operaciones sobre un conjunto de resultados, pero devolviendo un único valor agregado para todos ellos.
Es decir, nos permiten obtener medias, máximos, etc… sobre un conjunto de valores.

Las funciones de agregación básicas que soportan todos los gestores de datos son las siguientes:

- COUNT: devuelve el número total de filas seleccionadas por la consulta, como particularidad se puede usar COUNT()* donde contará todos los registros de la tabla incluyendo nulos.
- MIN: devuelve el valor mínimo del campo que especifiquemos.
- MAX: devuelve el valor máximo del campo que especifiquemos.
- SUM: suma los valores del campo que especifiquemos. Sólo se puede utilizar en columnas numéricas.
- AVG: devuelve el valor promedio del campo que especifiquemos. Sólo se puede utilizar en columnas numéricas.

    SELECT COUNT(id), SUM(quantity), AVG(age);
    SELECT MIN(date), MAX(quantity);

#### Función `IF`

SQL también tiene estructuras de control así como cualquier otro lenguaje de programación.

    SELECT IF(500<1000, "YES", "NO");

Sí se cumple YES si no NO

#### Función `CASE`

Cuando una sentencia tiene más de dos opciones se utiliza con la función `WHEN` por cada una y `THEN` es el retorno cuando la condición sea verdadera. También se puede utilizar `ELSE` al final cuando ya no quedan más opciones. Sino hay `ELSE` se devolverá `NULL`. La sentencia se cierra con `END`.

    SELECT OrderID, Quantity,
    CASE
        WHEN Quantity > 30 THEN "Over 30"
        WHEN Quantity = 30 THEN "Equal 30"
        ELSE "Under 30"
    END AS QuantityText

### Origen (`FROM`)

Con `SELECT` se especifica que columnas queremos obtener de una tabla determinada y con `FROM` se indica de donde se va a obtener la información que se va a proyectar con `SELECT`.

    SELECT * FROM base_de_datos.tabla;

Las sentencias SQL no son sensibles a minúsculas o mayúsculas pero se recomienda escribir las palabras claves en mayusculas y el resto en minusculas.

`JOIN` es un complemento de `FROM`. Y une dos tablas, también se acompaña de la palabra `ON` para indicar que relación tienen estas tablas. `pk` es primary key y `fk` es foreign key.

    SELECT *
    FROM tabla_diaria AS td
      JOIN tabla_mensual AS tm
      ON td.pk = tm.fk;

También se puede obtener información de una base de datos remota, es decir que el esquema de donde queremos obtener información se encuentra en otra base de datos.
Para obtener esa información se utiliza `dblink`. Esta función necesita la configuración de conexión de la base de datos remota y una consulta SQL. `dblink` es una función propia de postgresql.

    SELECT *
    FROM dblink('
      dbname=somedb
      port=5432
      host=someserver
      user=someuser
      password=somepassword',
      'SELECT gid, area, perimeter,
        state, country,
        tract, blockgroup,
        block, the_geom
      FROM massgis.cens2000blocks')
      AS blockgroups

### Productos cartesianos (`JOIN`)

LOS `JOIN` son un producto cartesiano sobretodo cuando se quiere sacar la información de todas las tablas. Y un producto cartesiano es la multiplicacaión de todos los elementos de un lado por todos los elementos de otro.

Aunque puede volverse muy complejo y caotico. Sí tienes 10 productos en un lado los multiplicas por 10 del otro lado y luego quieres ver otros 10 puedes tener sencillamente 1000 registros, y puede ser peor si no cuentas con los recursos necesarios para ver estos registros. Pero afortunadamente existen diversos tipos de `JOIN` para hacer el mapeado más sencillo y exacto a nuetras necesidades.

Ver imagenes de diagramas de ven.

### Selección (`WHERE`)

La cláusula `WHERE` se utiliza para filtrar registros. Se usa para extraer solo aquellos registros que cumplen una condición específica.
Esta clausula no solo utiliza con `SELECT`, también con `UPDATE`, `DETELE`, etc.

    SELECT * FROM tabla_diaria WHERE id = 1;

    SELECT * FROM tabla_diaria WHERE cantidad > 10;

Puede combinarse con `AND`, `OR`, `NOT`, `BETWEEN`:

- `AND` muestra un registro si todas las condiciones separadas por `AND` son `TRUE`.

    SELECT * FROM tabla_diaria WHERE cantidad > 10 AND cantidad < 100;

- `OR` muestra un registro si alguna de las condiciones separadas por `OR` es `TRUE`.

- `BETWEEN` puede ser usado para definir límites. La separación por paretesis es muy importante.

    SELECT * FROM tabla_diaria WHERE cantidad BETWEEN 10 AND 100;

Otros ejemplos:

- devuelve todos lo que son "Israel Vázquez" o "Israel López".

    SELECT *
    FROM users
    WHERE name = "Israel"
      AND (
        lastname = "Vázquez"
        OR
        lastname = "López"
      );

- devolverá a todos los que se llaman Israel Vázquez o se apellida López (sólo apellido).

    SELECT *
    FROM users
    WHERE name = "Israel"
      AND lastname = "Vázquez"
      OR lastneme = "López";

Para especificar patrones en una columna podemos usar `LIKE`. Podemos mostrar diferentes cosas que buscamos.

    SELECT *
    FROM users
    WHERE name LIKE "Is%"; // Empieza con 'Is'

    SELECT *
    FROM users
    WHERE name LIKE "Is_ael"; // El guion bajo representa un comodin

    SELECT *
    FROM users
    WHERE name NOT LIKE "Is_ael";

Usando el comando `NULL` podemos buscar registros con campos vacios.

    SELECT *
    FROM users
    WHERE name IS NULL;

    SELECT *
    FROM users
    WHERE name IS NOT NULL;

Así puedo hacer busquedas en un set de datos.

    SELECT *
    FROM users
    WHERE name IN ('Israel', 'Laura', 'Luis')

### Ordenamiento (`ORDER BY`)

`ORDER BY` se usa para ordenar de forma ascendete o descendente columnas en base a una fila determinada. Por defecto lo hace de forma `DESC`. Organiza todo alfabéticamente o numéricametne si son números.

    SELECT *
    FROM tabla_diaria
    ORDER BY fecha ASC;

    SELECT *
    FROM tabla_diaria
    ORDER BY fecha DESC;

Pero ¿qué pasa cuando se quiere ordenar por más de una columna? Se utilizan los indices. Los indices funcionan guardando el orden de un campo para hacer extracciones más rápidas. Pero si se hace una modificación se va a demorar, porque va a tener que reescribir los elementos anteriores que luego terminaran afectando al indice que se había creado. Por eso el uso de indices es recomendado con datos que son usados muy seguidos y no son modificados o muy poco.
Los indices nos pueden ahorrar tiempo, pero hay que ser cuidadosos con como usarlos.

### Agregación y limitantes (`GROUP BY` y `LIMIT`)

`GROUP BY` es una sentencia que agrupa filas que tienen el mismo valor en columnas con el sumatorio. Como decirle 'encuentra el número de clientes en cada país'.
Suele usarse frecuentemente con las funciones `COUNT`, `MAX`, `MIN`, `SUM`, `AVG` a un grupo de una o más columnas.

    SELECT *
    FROM tabla_diaria
    GROUP BY marca;

    SELECT *
    FROM tabla_diaria
    GROUP BY marca, modelo;

    SELECT COUNT(CustomerID), Country
    FROM Customers
    GROUP BY Country
    ORDER BY COUNT(CustomerID) DESC;

`SELECT TOP` es usada para especificar el número de registros que se van a retornar.
`SELECT TOP` es útil para tablas con miles de registros, pues un gran número de registros puede afectar el rendimiento.

    SELECT TOP number
    FROM table1
    WHERE condition;

    SELECT TOP 1500
    FROM tabla_diaria;

`LIMIT` se usa para limitar la cantidad de resultados que arroja el Query.
`OFFSET` se usa con `LIMIT` para indicar la cantidad de resultados que se deben omitir antes de empezara mostrar los datos de `LIMIT`.

    SELECT *
    FROM tabla_diaria
    LIMIT 1500;

    SELECT *
    FROM tabla_diaria
    OFFSET 1500
    LIMIT 1500;

## Ejercitando tu SQL

### El primero

**Reto:** Los 5 primeros.

    SELECT * FROM platzi.alumnos FETCH FIRST 5 ROWS ONLY;

    SELECT * FROM platzi.alumnos LIMIT 5;

    SELECT *
    FROM (
        SELECT ROW_NUMBER() OVER() AS row_id, *
        FROM platzi.alumnos
    ) AS alumnos_with_row_num
    WHERE row_id < 6;

### El segundo más alto

Se quiere traer la segunda colegiatura más alta.

    SELECT DISTINCT colegiatura
    FROM platzi.alumnos AS a1
    WHERE 2 = (
        SELECT COUNT (DISTINCT colegiatura)
        FROM platzi.alumnos a2
        WHERE a1.colegiatura <= a2.colegiatura
    );

    SELECT DISTINCT colegiatura, *
    FROM platzi.alumnos
    ORDER BY colegiatura DESC
    LIMIT 1 OFFSET 1; // trae solo un registro, pero se salta  el primer registro

    SELECT *
    FROM platzi.alumnos AS datos_alumnos
    INNER JOIN (
        SELECT DISTINCT colegiatura
        FROM platzi.alumnos
        ORDER BY colegiatura DESC
        LIMIT 1 OFFSET 1
    ) AS segunda_mayor_colegiatura
    ON datos_alumnos.colegiatura = segunda_mayor_colegiatura.colegiatura;

    SELECT *
    FROM platzi.alumnos AS datos_alumnos
    WHERE colegiatura = (
        SELECT DISTINCT colegiatura
        FROM platzi.alumnos
        ORDER BY colegiatura DESC
        LIMIT 1 OFFSET 1
    );

**Reto:** Traer la mitad de las consultas

    SELECT *
    FROM platzi.alumnos
    OFFSET (
        SELECT (COUNT(id)/2)
        FROM platzi.alumnos
    )

### Seleccionar de un set de opciones

Hacer `SELECT` en un grupo de opciones. Se utiliza la sentencia `IN`.
Aquí ya se tiene definido un grupo sobre el que se quiere hacer la consulta

    SELECT *
    FROM (
        SELECT ROW_NUMBER() OVER() AS row_id, *
        FROM platzi.alumnos
    ) AS alumnos_with_row_num
    WHERE row_id IN (1, 5, 10, 12, 15, 20);

Aquí se plantea una condición sobre la que hay que hacer la consulta.

    SELECT *
    FROM platzi.alumnos
    WHERE id IN (
        SELECT id
        FROM platzi.alumnos
        WHERE tutor_id = 30
    );

**Reto:** seleccionar los resultados que no estan en el set.

    SELECT *
    FROM platzi.alumnos
    WHERE id NOT IN (
        SELECT id
        FROM platzi.alumnos
        WHERE tutor_id = 30
    );

    SELECT *
    FROM platzi.alumnos
    WHERE id IN (
        SELECT id
        FROM platzi.alumnos
        WHERE tutor_id != 30
    );

### En mis tiempos

Para manipular las fechas existen diversas funciones propias para los campos tipo fecha.

    SELECT EXTRACT(YEAR FROM fecha_incorporacion) AS año
    FROM platzi.alumnos;

    SELECT DATE_PART('YEAR', fecha_incorporacion) AS año
    FROM platzi.alumnos;

    SELECT DATE_PART('YEAR', fecha_incorporacion) AS año_incorporacion,
        DATE_PART('MONTH', fecha_incorporacion) AS mes_incorporacion,
        DATE_PART('DAY', fecha_incorporacion) AS dia_incorporacion
    FROM platzi.alumnos;

**Nota:** Existe una diferencia entre usar `EXTRACT` y `DATE_PART`. El primero extrae los campos con tipo numerico, mientras que el segundo los extrae de tipo 'double precision'.

**Reto:** Extraer los campos de la hora.

    SELECT DATE_PART('YEAR', fecha_incorporacion) AS año_incorporacion,
        DATE_PART('MONTH', fecha_incorporacion) AS mes_incorporacion,
        DATE_PART('DAY', fecha_incorporacion) AS dia_incorporacion,
        DATE_PART('HOUR', fecha_incorporacion) AS hora_incorporacion,
        DATE_PART('MINUTE', fecha_incorporacion) AS minuto_incorporacion,
        DATE_PART('SECOND', fecha_incorporacion) AS segundo_incorporacion
    FROM platzi.alumnos;

    SELECT EXTRACT(YEAR FROM fecha_incorporacion) AS año,
        EXTRACT(MONTH FROM fecha_incorporacion) AS mes,
        EXTRACT(DAY FROM fecha_incorporacion) AS dia,
        EXTRACT(HOUR FROM fecha_incorporacion) AS hora,
        EXTRACT(MONTH FROM fecha_incorporacion) AS mes,
        EXTRACT(SECOND FROM fecha_incorporacion) AS segundo
    FROM platzi.alumnos;

### Seleccionar por año

    SELECT *
    FROM platzi.alumnos
    WHERE (
        EXTRACT(YEAR FROM fecha_incorporacion)
    ) = 2019;

    SELECT *
    FROM platzi.alumnos
    WHERE (
        DATE_PART('YEAR', fecha_incorporacion)
    ) = 2019;

    SELECT *
    FROM (
        SELECT *,
            DATE_PART('YEAR', fecha_incorporacion) AS año_incorporacion
        FROM platzi.alumnos
    ) AS alumnos_año
    WHERE año_incorporacion = 2020;

**Reto:** Filtrar por los alumons que se incribieron en mayo del 2018.

    SELECT *
    FROM (
        SELECT nombre, apellido, fecha_incorporacion,
            DATE_PART('YEAR', fecha_incorporacion) AS año_incorporacion,
            DATA_PART('MONTH', fecha_incorporacion) AS mes_incorporacion
        FROM platzi.alumnos
    ) AS alumnos_año
    WHERE  mes_incorporacion = 5 AND año_incorporacion = 2018;

### Duplicados

La cláusula `HAVING` es una implemetacion que el sistema gestor de bases de datos SQL crea para complementar el condicionante `WHERE`, ya que el condicionante `WHERE` no permite utilizar funciones de agregación como `SUM`, `MAX`, `MIN` o `AVG`.

    SELECT *
    FROM platzi.alumnos AS out
    WHERE (
        SELECT COUNT(*)
        FROM platzi.alumnos AS inn
        WHERE out.id = inn.id
    ) > 1;

    SELECT (platzi.alumnos.*)::text, COUNT(*)
    FROM platzi.alumnos
    GROUP BY platzi.alumnos.*
    HAVING COUNT(*) > 1;

    SELECT (
        platzi.alumnos.nombre,
        platzi.alumnos.apellido,
        platzi.alumnos.email,
        platzi.alumnos.colegiatura,
        platzi.alumnos.fecha_incorporacion,
        platzi.alumnos.carrera_id,
        platzi.alumnos.tutor_id
        )::text, COUNT(*)
    FROM platzi.alumnos
    GROUP BY platzi.alumnos.nombre,
        platzi.alumnos.apellido,
        platzi.alumnos.email,
        platzi.alumnos.colegiatura,
        platzi.alumnos.fecha_incorporacion,
        platzi.alumnos.carrera_id,
        platzi.alumnos.tutor_id
    HAVING COUNT(*) > 1;

    SELECT *
    FROM (
        SELECT id,
        ROW_NUMBER() OVER(
            PARTITION BY
                nombre,
                apellido,
                email,
                colegiatura,
                fecha_incorporacion,
                carrera_id,
                tutor_id
            ORDER BY id ASC
        ) AS row,
        *
        FROM platzi.alumnos
    ) AS duplicados
    WHERE duplicados.row > 1;

**Reto:** Eliminar el registro duplicado

    DELETE
    FROM platzi.alumnos
    WHERE id IN (
        SELECT *
        FROM (
            SELECT id,
            ROW_NUMBER() OVER(
                PARTITION BY
                    nombre,
                    apellido,
                    email,
                    colegiatura,
                    fecha_incorporacion,
                    carrera_id,
                    tutor_id
                ORDER BY id ASC
            ) AS row
            FROM platzi.alumnos
        ) AS duplicados
        WHERE duplicados.row > 1
    );

### Selectores de rango

Los tipos de rango que vienen en PostgreSQL son:

- `int4range`: Que trae un rango de enteros.
- `int8range`: Es un rango de enteros grandes.
- `numrange`: Es un rango numérico.
- `tsrange`: Es un rango del tipo timestamp pero sin la zona horaria.
- `tstzrange`: Es un rango del tipo timestamp con la zona horaria
- `daterange`: Es un rango del tipo fecha.

    SELECT *
    FROM platzi.alumnos
    WHERE tutor_id IN (1, 2, 3, 4);

    SELECT *
    FROM platzi.alumnos
    WHERE tutor_id BETWEEN 1 AND 10;

Crea un rango entre 10 y 20, y revisa sí el 3 esta dentro.

    SELECT int4range(10, 20) @> 3;

Crea dos rangos: uno entre 11.1 y 22.2. Y otro en 20.0 y 30.0. El `&&` revisa si los rangos se superponen.

    SELECT numrange(11.1, 22.2) && numrange(20.0, 30.0);

    SELECT UPPER(int8range(15, 25));

    SELECT LOWER(int8range(15, 25));

Devuelve el rango de numeros en los que coinciden los rangos.

    SELECT int4range(10, 20) * int4range(15, 25);

    SELECT ISEMPTY(numrange(1,5));

    SELECT *
    FROM platzi.alumnos
    WHERE int4range(10,20) @> tutor_id;

**Rreto:** Cuales son los valores que hay en comuno entre el tutor_id, y carrera_id.

    SELECT int4range(MIN(tutor_id), MAX(tutor_id)) * 
    int4range(MIN(carrera_id), MAX(carrera_id))
    FROM platzi.alumnos;

    SELECT numrange(
        (SELECT MIN(tutor_id) FROM platzi.alumnos),
        (SELECT MAX(tutor_id) FROM platzi.alumnos)
    ) * numrange(
        (SELECT MIN(carrera_id) FROM platzi.alumnos),
        (SELECT MAX(carrera_id) FROM platzi.alumnos)
    );

### Eres lo máximo

    SELECT carrera_id, fecha_incorporacion
    FROM platzi.alumnos
    GROUP BY carrera_id, fecha_incorporacion
    ORDER BY fecha_incorporacion DESC;

    SELECT carrera_id, MAX(fecha_incorporacion)
    FROM platzi.alumnos
    GROUP BY carrera_id
    ORDER BY carrera_id;

**Reto:** Sacar el minimo nombre alfaveticamente por id de tutor y en toda la tabla.

    SELECT MIN(nombre), tutor_id
    FROM platzi.alumnos
    GROUP BY tutor_id
    ORDER BY tutor_id;

    SELECT MIN(nombre)
    FROM platzi.alumnos;

### Egoista (selfish)

    SELECT CONCAT(a.nombre, ' ', a.apellido) AS alumno,
        CONCAT(t.nombre, ' ', t.apellido) AS tutor
    FROM platzi.alumnos AS a
    INNER JOIN platzi.alumnos AS t ON a.tutor_id = t.id;

    SELECT CONCAT(t.nombre, ' ', t.apellido) AS tutor,
        COUNT(*) AS alumnos_por_tutor
    FROM platzi.alumnos AS a
    INNER JOIN platzi.alumnos AS t ON a.tutor_id = t.id
    GROUP BY tutor
    ORDER BY alumnos_por_tutor DESC
    LIMIT 10;

**Reto:** Sacar el promedio de alumnos por tutor.

    SELECT AVG(alumnos_por_tutor) AS promedio_alumnos_por_tutor
    FROM (
        SELECT CONCAT(t.nombre, ' ', t.apellido) AS tutor,
            COUNT(*) AS alumnos_por_tutor
        FROM platzi.alumnos AS a
        INNER JOIN platzi.alumnos AS t ON a.tutor_id = t.id
        GROUP BY tutor
    ) AS alumnos_tutor;

### Resolviendo diferencias

### Todas las uniones

### Triangulando

### Generando rangos

### Regularizando expresiones

## Conceptos de SQL avanzado

### Bases de datos distribuidas

### Queries distribuídos

### Sharding

### Window functions

### Particiones y agregación

### El futuro de SQL

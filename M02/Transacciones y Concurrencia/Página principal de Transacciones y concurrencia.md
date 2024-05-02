
>[!important] Recurso externo
>[Transaction Isolation in Postgres, explained](https://www.thenile.dev/blog/transaction-isolation-postgres)
>[LOCKS a comprensive Guide](https://hevodata.com/learn/postgresql-locks/)
## Concepto 

Las **transacciones** son una forma de agrupar y aislar múltiples sentencias para procesarlas como una única operación. 

En lugar de ejecutar cada comando individualmente a medida que se envía al servidor, en una transacción los comandos se agrupan y se ejecutan en un contexto distinto al de otras peticiones.

El aislamiento es una parte importante de las transacciones. Dentro de una transacción, las sentencias ejecutadas sólo pueden afectar al entorno dentro de la propia transacción. Desde dentro de la transacción, las sentencias pueden modificar datos y los resultados son visibles inmediatamente. Desde el exterior, no se realizan cambios hasta que la transacción se confirma, momento en el que todas las acciones dentro de la transacción se hacen visibles a la vez.

Estas características ayudan a las bases de datos a lograr el **cumplimiento de ACID**, ( Atomicity, Consistency, Isolation, Durability) proporcionando:
<span style="color:#0070c0">Atomicidad</span> (las acciones en una transacción se consignan todas o se revierten todas) 
<span style="color:#0070c0">Aislamiento</span> (fuera de la transacción, nada cambia hasta la consignación, mientras que dentro, las sentencias tienen consecuencias). Todo ello ayuda a la base de datos a mantener la <span style="color:#0070c0">Coherencia</span> (garantizando que no se produzcan transformaciones parciales de los datos). Además, los cambios en las transacciones no se devuelven como realizados con éxito hasta que se consignan al almacenamiento no volátil, lo que proporciona <span style="color:#0070c0">Durabilidad</span>.

Mientras que la Atomicidad proporciona importantes garantías para una única transacción, las garantías de Aislamiento entran en juego cuando hay múltiples transacciones ejecutándose simultáneamente.

 Un usuario ejecuta una transacción que mueve dinero entre cuentas, y mientras esa transacción se está procesando, pero antes de que se confirme, otra persona ejecuta un `SELECT` de esas cuentas
 ¿Qué devolverá esta consulta? ¿Importa si se ejecutó antes o después de la primera actualización? Esto es lo que abordan los niveles de aislamiento:

El nivel de aislamiento ideal  se denomina "Serializable" porque garantiza que existe un orden serial de las transacciones con los mismos resultados.

es un gran ideal, pero no algo que pueda ser implementado sin dejar de ofrecer un rendimiento adecuado.

Dado que el ideal no era práctico, se implementan múltiples niveles de aislamiento y explicar qué puede ocurrir en cada uno de ellos. 

Existe una lista de resultados extraños que se pueden encontrar si no se tiene serializabilidad denominadas "anomalías". Después definieron los niveles de aislamiento en términos de "qué anomalías pueden ocurrir en cada nivel":

 El control de concurrencia multiversión, MVCC, se generalizó y se convirtió en la implementación más común del aislamiento de transacciones en bases de datos relacionales. Incluyendo Postgres. MVCC no bloquea filas. Lo que hace en su lugar es mantener múltiples versiones de cada fila (de ahí el nombre), 

## Niveles de aislamiento de las transacciones

Las transacciones no son una solución única. Diferentes escenarios requieren diferentes equilibrios entre rendimiento y protección.

PostgreSQL permite especificar el tipo de aislamiento de transacciones que necesita.

Los niveles de aislamiento ofrecidos por la mayoría de los sistemas de bases de datos incluyen los siguientes:

| Isolation Level  | Dirty Reads                  | Non-repeatable Reads | Phantom Reads                | Serialization Anomalies |
| ---------------- | ---------------------------- | -------------------- | ---------------------------- | ----------------------- |
| Read Uncommitted | Allowed, but not in Postgres | Possible             | Possible                     | Possible                |
| Read Committed   | Not Possible                 | Possible             | Possible                     | Possible                |
| Repeatable Reads | Not Possible                 | Not Possible         | Allowed, but not in Postgres | Possible                |
| Serializable     | Not Possible                 | Not Possible         | Not Possible                 | Not Possible            |
### Read committed ( Lectura comprometida )

`Read committed` es un nivel de aislamiento que protege específicamente contra lecturas sucias. Cuando las transacciones utilizan el nivel de consistencia de`Read committed`, los datos no comprometidos nunca pueden afectar al contexto interno de una transacción. Esto proporciona un nivel básico de consistencia asegurando que los datos no comprometidos nunca influyan en una transacción.

#### Anomalias que pueden surgir:

##### Non-repeatable reads ( Lecturas no repetibles )

Las lecturas no repetibles se producen cuando un commit fuera de la transacción altera los datos vistos dentro de la transacción. Puede reconocer este tipo de problema si, dentro de una transacción, los mismos datos se leen dos veces pero se recuperan valores diferentes en cada instancia.

![[Captura de pantalla 2024-04-30 a las 11.48.52.png]]

El principal problema con las lecturas no repetibles es un anti-patrón clásico conocido como "“write after read”".

![[Captura de pantalla 2024-04-30 a las 11.54.45.png]]
##### Phantom read ( Lectura fantasma )

Una lectura fantasma es un tipo específico de lectura no repetible que se produce cuando las filas devueltas por una consulta son diferentes la segunda vez que se ejecuta dentro de una transacción, nuevas filas que no existían antes aparecen de repente en medio de una transacción.

![[Captura de pantalla 2024-04-30 a las 12.02.14.png]]

Ambas consultas en la sesión B mostrarán el mismo saldo para la cuenta 789. Porque la actualización que se hizo en la sesión A no será visible para la sesión B. 

Sin embargo, la segunda consulta también incluirá la cuenta 12345 que fue creada por la sesión A. Esta es una nueva fila que apareció de repente en medio de una transacción. Esto significa que la sesión B producirá un informe inconsistente - tanto porque tiene dos consultas que devolvieron resultados diferentes, como porque la segunda consulta muestra los datos que la sesión A insertó, pero no los datos que actualizó.
##### Anomalías de serialización

Las anomalías de serialización ocurren cuando los resultados de múltiples transacciones confirmadas concurrentemente dan lugar a resultados diferentes que si se hubieran confirmado una tras otra. Esto puede ocurrir cada vez que una transacción permite que ocurran dos commits que modifican cada uno la misma tabla o datos sin resolver conflictos.

Las anomalías de serialización son un tipo especial de problema que los primeros tipos de transacciones no comprendían. Esto se debe a que las primeras transacciones se implementaban con bloqueo, donde una no podía continuar si otra transacción estaba leyendo o alterando la misma pieza de datos.

- lecturas no repetibles
- lecturas fantasma
- anomalías de serialización

PostgreSQL utilizará el nivel de `Read committed` por defecto si no se especifica ningún otro nivel de aislamiento.

### Repeatable read ( Lectura repetible )

 Evita las lecturas sucias como antes, pero también evita las lecturas no repetibles.

Esto significa que ningún cambio realizado fuera de la transacción afectará a los datos leídos dentro de la transacción. Una consulta ejecutada al inicio de una transacción nunca tendrá un resultado diferente al final de la transacción a menos que sea causada directamente por sentencias dentro de la transacción.

Mientras que la definición estándar del nivel de aislamiento de `Repeatable read` sólo requiere que se prevengan las lecturas sucias y no repetibles, PostgreSQL también previene las lecturas fantasma en este nivel. Esto significa que los commits fuera de la transacción no pueden alterar el número de filas que satisfacen una consulta.

El nivel de aislamiento de `Repeatable read` de PostgreSQL bloquea la mayoría de los tipos de problemas de consistencia, pero aún pueden ocurrir anomalías de serialización.

### Serializable

El nivel de aislamiento `serializable` ofrece el nivel más alto de aislamiento y consistencia. Previene todos los escenarios que el nivel de `Repeatable read` hace mientras que también elimina la posibilidad de anomalías de serialización.

El aislamiento serializable garantiza que las transacciones concurrentes son confirmadas como si fueran ejecutadas una tras otra. Si se produce un escenario en el que podría introducirse una anomalía de serialización, una de las transacciones tendrá un fallo de serialización en lugar de introducir inconsistencia en el conjunto de datos.

## Empezando una transacción

Cuando se ejecuta una sentencia, PostgreSQL la envuelve implícitamente en una transacción.

>[!important] Importante!
>Por defecto, PostgreSQL ejecuta las transacciones en modo "autocommit", es decir, cada sentencia se ejecuta en su propia transacción y se realiza implícitamente un commit al final de la sentencia (si la ejecución ha sido satisfactoria, en caso contrario se realiza un rollback).

### Ejemplo
#### Usuario que crea el **UPDATE** de manera normal

![[Captura de pantalla 2024-04-29 a las 12.01.55.png]]

#### Usuario2 que observa la transacción ( por default en autocommit )

![[Captura de pantalla 2024-04-29 a las 12.02.17.png]]

#### Transacciones de manera explicita

Para empezar una transacción de manera explicita ejecuto cualquier de estas tres sentencias

```sql
BEGIN TRANSACTION;
BEGIN WORK;
BEGIN;

BEGIN; 
INSERT INTO accounts(
	name,
	balance
) 
VALUES(
	'Alice',
	10000
);

```

![[Captura de pantalla 2024-04-29 a las 12.05.37.png]]

Usuario que observa 

![[Captura de pantalla 2024-04-29 a las 12.05.46.png]]

Como podemos observar al iniciar una transacción de manera explicita y no cerrar esa transacción con un commit, esta no es visible en otras sesiones.

Para finalizar esa transacción necesitamos un `COMMIT` explicito.

```sql
COMMIT WORK;

COMMIT TRANSACTION;

COMMIT;
```

usuario que crea la transacción

![[Captura de pantalla 2024-04-29 a las 11.59.08.png]]

Usuario en otra sesión ( ahora si puede ver ese UPDATE de los datos)

![[Captura de pantalla 2024-04-29 a las 11.59.16.png]]

Después de ejecutar la sentencia COMMIT, PostgreSQL garantiza que el cambio será duradero si se produce un fallo.


### Ejemplo 2

Como ejemplo más concreto, imaginemos que intentamos transferir 1.000 dólares de una cuenta a otra. Queremos asegurarnos de que el dinero siempre estará en una de las dos cuentas, pero nunca en las dos.

Podemos envolver las dos sentencias que juntas encapsulan esta transferencia en una transacción que tenga este aspecto:
```sql
BEGIN;

UPDATE accounts
	SET balance = balance - 1000
WHERE id = 1;

UPDATE accounts
	SET balance = balance + 1000
WHERE id = 2;
COMMIT;
```

Aquí, los $1000 no se sacarán de la cuenta con id = 1 sin poner también $1000 en la cuenta con id = 2. Aunque estas dos sentencias se ejecutan secuencialmente dentro de la transacción, se confirmarán y, por lo tanto, se ejecutarán simultáneamente en el conjunto de datos subyacente.

### Rolling back de transacciones

Dentro de una transacción, todas o ninguna de las sentencias se confirmarán en la base de datos. Abandonar las sentencias y modificaciones realizadas dentro de una transacción en lugar de aplicarlas a la base de datos se conoce como "revertir" la transacción.

Las transacciones pueden ser revertidas automática o manualmente. PostgreSQL retrocede automáticamente las transacciones si una de las sentencias que contiene produce un error. También revierte la transacción si se produce un error de serialización si el nivel de aislamiento elegido no lo permite.

Para revertir manualmente las sentencias que se han dado durante la transacción actual, puede utilizar el comando ROLLBACK. Esto cancelará todas las sentencias dentro de la transacción, en esencia retrocediendo el reloj hasta el inicio de la transacción.

Por ejemplo, suponiendo que estamos usando el mismo ejemplo de cuentas bancarias que estábamos usando antes, si descubrimos después de emitir las sentencias UPDATE que accidentalmente transferimos la cantidad equivocada o usamos las cuentas equivocadas, podríamos revertir los cambios en lugar de confirmarlos:

```sql
BEGIN;
UPDATE accounts
SET balance = balance - 1500
WHERE id = 1;
UPDATE accounts
SET balance = balance + 1500
WHERE id = 3; -- Wrong account number here! Must rollback

/* Gets us back to where we were before the transaction started */

ROLLBACK;
```

Una vez que hagamos ROLLBACK, los $1500 seguirán en la cuenta con id = 1.

### Uso de save points en el rolling back 

Por defecto, el comando ROLLBACK restablece la transacción al punto en el que se encontraba cuando se invocaron por primera vez los comandos BEGIN o START TRANSACTION. Pero, ¿y si sólo queremos revertir algunas de las sentencias de la transacción?

Aunque no puede especificar lugares arbitrarios a los que retroceder cuando emite el comando ROLLBACK, puede retroceder a cualquier "punto de guardado" que haya establecido a lo largo de la transacción. Puede marcar lugares en su transacción por adelantado con el comando SAVEPOINT y luego hacer referencia a esos lugares específicos cuando necesite revertir.

Estos puntos de guardado le permiten crear un punto intermedio de retroceso. Opcionalmente, puede revertir las sentencias realizadas entre el punto en el que se encuentra y el punto de guardado y continuar trabajando en la transacción.

Para especificar un punto de guardado, emita el comando SAVEPOINT seguido de un nombre para el punto de guardado:

`SAVEPOINT save_1;`

Para volver a ese punto de guardado, utilice el comando VOLVER A:

`ROLLBACK TO save_1;`

Continuemos con el ejemplo centrado en la cuenta que hemos estado utilizando:

```sql
BEGIN;

UPDATE accounts

SET balance = balance - 1500

WHERE id = 1;

/* Set a save point that we can return to */

SAVEPOINT save_1;

UPDATE accounts

SET balance = balance + 1500

WHERE id = 3; -- Wrong account number here! We can rollback to the save point though!

/* Gets us back to the state of the transaction at `save_1` */

ROLLBACK TO save_1;

/* Continue the transaction with the correct account number */

UPDATE accounts

SET balance = balance + 1500

WHERE id = 4;

COMMIT;

```

Aquí, somos capaces de recuperarnos de un error que cometimos sin perder todo el trabajo que hemos hecho en la transacción hasta el momento. Después de retroceder, continuamos con la transacción según lo planeado utilizando las sentencias correctas.

## Establecer el nivel de aislamiento de las transacciones

Para establecer el nivel de aislamiento que desea para una transacción, puede añadir una cláusula de NIVEL DE AISLAMIENTO a su comando START TRANSACTION o BEGIN. La sintaxis básica es la siguiente

```sql
BEGIN ISOLATION LEVEL <isolation_level>;
	statements
COMMIT;
```


El <nivel_aislamiento> puede ser cualquiera de estos (descritos en detalle anteriormente):

- `READ UNCOMMITTED` (resultará en `READ COMMITTED` ya que este nivel no está implementado en PostgreSQL)
- `READ COMMITTED`
- `REPEATABLE READ`
- `SERIALIZABLE`

El comando `SET TRANSACTION` también puede usarse para establecer el nivel de aislamiento después de iniciar una transacción. Sin embargo, sólo se puede usar `SET TRANSACTION` antes de que se ejecute cualquier consulta o comando de modificación de datos, por lo que no permite una mayor flexibilidad.

### ( Chaining ) Encadenamiento de transacciones

Si tiene varias transacciones que deben ejecutarse secuencialmente, puede encadenarlas opcionalmente utilizando el comando `COMMIT AND CHAIN`.

El comando `COMMIT AND CHAIN` completa la transacción actual confirmando las sentencias que contiene. Una vez procesado el commit, se abre inmediatamente una nueva transacción. Esto le permite agrupar otro conjunto de sentencias en una transacción.

La sentencia funciona exactamente igual que si hubiera emitido `COMMIT; BEGIN`:

```sql
BEGIN;
UPDATE accounts
SET balance = balance - 1500
WHERE id = 1;
UPDATE accounts
SET balance = balance + 1500
WHERE id = 2;
/* Commit the data and start a new transaction that will take into account the committed from the last transaction */
COMMIT AND CHAIN;
UPDATE accounts
SET balance = balance - 1000
WHERE id = 2;
UPDATE accounts
SET balance = balance + 1000
WHERE id = 3;
COMMIT;
```

Encadenar transacciones no ofrece grandes novedades funcionales, pero puede ser útil para comprometer datos en límites naturales sin dejar de centrarse en el mismo tipo de operaciones.


## Locks

### Concepto 

Postgres implementa el **Control de Concurrencia Multiversión (MVCC) :

Mecanismo utilizado por PostgreSQL para manejar la concurrencia en las transacciones,
Permite que múltiples transacciones accedan y modifiquen datos simultáneamente sin interferir entre sí, garantizando que los datos estén disponibles y sean consistentes en entornos de alta concurrencia.

Cada transacción en PostgreSQL utiliza su propia "instantánea" de la base de datos, lo que significa que las operaciones de lectura y escritura no se obstruyen entre sí.

Aunque MVCC en PostgreSQL ayuda a mitigar muchos problemas relacionados con la concurrencia al permitir que múltiples transacciones trabajen de manera concurrente sin bloquearse unas a otras, aún hay casos en los que se necesita los Locks ( bloqueos ).

Estos, son un Mecanismo utilizado para controlar el acceso concurrente a objetos de base de datos como tablas, filas.

Se utilizan para prevenir conflictos entre transacciones, asegurando que cada transacción pueda acceder y modificar la base de datos sin interferir con otras transacciones.

### Tipos de bloqueos

1. **Bloqueos a nivel de tabla:** Se aplican a toda la tabla y pueden ser explícitos o implícitos. Los bloqueos implícitos se liberan automáticamente al finalizar una transacción, mientras que los explícitos deben liberarse manualmente.
    
2. **Bloqueos a nivel de fila:** Se aplican a filas individuales dentro de una tabla. Al igual que los bloqueos a nivel de tabla, pueden ser explícitos o implícitos.
    
3. **Bloqueos a nivel de página:** Estos bloqueos se aplican a páginas específicas dentro de una tabla.
    
4. **Bloqueos consultivos:** Son bloqueos explícitos que pueden ser utilizados para coordinar entre diferentes sesiones o procesos.
    
5. **Deadlocks:** Ocurren cuando dos o más transacciones están esperando por recursos que posee el otro, lo que resulta en un estancamiento.
#### Tipos de bloqueo

**Access Exclusive (AEX) locks** (Bloqueos de Acceso Exclusivo): 
Utilizados para evitar que cualquier otra transacción acceda o modifique el objeto bloqueado. 
Utilizados cuando una transacción está realizando una operación destructiva, como eliminar una tabla o truncar una tabla.

**Exclusive (EX) locks** (Bloqueos exclusivos): 
Utilizados para evitar que otras transacciones modifiquen el objeto bloqueado, pero permiten que otras transacciones lean el objeto. 
Utilizados cuando una transacción realiza una operación de actualización que modifica la estructura del objeto, como añadir o eliminar columnas de una tabla.

**Share (SH) locks:** (Bloqueos compartidos): 
Utilizados para permitir que varias transacciones lean el mismo objeto simultáneamente, pero impiden que modifiquen el objeto. 
Utilizados cuando una transacción realiza una operación de sólo lectura, como una consulta SELECT.

Row Share (RS) y Row Exclusive (RX): 
Utilizados para controlar el acceso concurrente a filas individuales de una tabla. 
Permiten a varias transacciones leer la misma fila simultáneamente, pero les impiden modificarla. Los bloqueos exclusivos de fila permiten que una transacción modifique una fila, pero impiden que otras transacciones lean o modifiquen la fila.

Los bloqueos son adquiridos automáticamente por el sistema de base de datos cada vez que una transacción accede o modifica un objeto de la base de datos. El sistema de base de datos utiliza un gestor de bloqueos para realizar un seguimiento de qué bloqueos están en manos de qué transacciones, y utiliza un mecanismo de escalado de bloqueos para evitar tener demasiados bloqueos en memoria

Cuando una transacción intenta acceder a un objeto que ya está bloqueado por otra transacción, esperará a que se libere el bloqueo (si el modo de bloqueo es compatible), o será abortada con un error (si el modo de bloqueo es incompatible). Esto garantiza que las transacciones se ejecuten de forma coherente y serializable.

En PostgreSQL, un bloqueo ocurre cuando dos o más transacciones están esperando que la otra libere un bloqueo, causando que queden atrapadas en un estado de espera indefinido. Los bloqueos pueden ser causados por una variedad de factores, incluyendo el uso de modos de bloqueo conflictivos, el orden en que los bloqueos son adquiridos, y la presencia de dependencias circulares entre transacciones.

Este es un ejemplo de un bloqueo que puede ocurrir en PostgreSQL cuando dos transacciones intentan actualizar la misma tabla concurrentemente:


```sql
Transacción 1:
BEGIN;
UPDATE cuentas SET saldo
= saldo + 100 WHERE account_id = 1;

Transacción 2:
BEGIN;
UPDATE cuentas SET saldo
= saldo - 100 WHERE account_id = 2;
```

Transacción 1 adquiere un bloqueo exclusivo (EX) sobre la tabla accounts, y la Transacción 2 adquiere un bloqueo exclusivo (EX) sobre la misma tabla. Dado que ambas transacciones están intentando modificar la misma tabla de forma concurrente, están esperando a que la otra libere sus bloqueos, provocando un bloqueo.

Para evitar este tipo de bloqueo, es importante asegurarse de que las transacciones adquieren bloqueos sobre objetos de la base de datos en el mismo orden, y utilizar modos de bloqueo compatibles entre sí. Por ejemplo, en el ejemplo anterior, si ambas transacciones utilizan un bloqueo compartido (SH) en lugar de un bloqueo exclusivo (EX), podrían acceder a la tabla de forma concurrente sin tener que esperar la una a la otra.

```sql
test=# SELECT
    l.locktype,
    l.relation::regclass AS table_name,
    l.mode,
    l.pid,
    l.granted,
    a.usename AS username
FROM
    pg_locks l
JOIN
    pg_stat_activity a ON l.pid = a.pid
WHERE
    l.relation::regclass = 'cuenta'::regclass;
```


#### Conflictos con los Tipos de bloqueos:

![PostgreSQL Locks: Table-level lock modes' conflicts](https://res.cloudinary.com/hevo/image/upload/c_scale,w_648,h_372/f_webp,q_auto/v1710414088/hevo-learn-1/mode-of-locks-in-table-level.png?_i=AA)

En una misma tabla, dos transacciones no pueden mantener a la vez bloqueos con modos conflictivos. Por ejemplo, un bloqueo de acceso compartido continuo impide que una sesión adquiera la exclusividad de acceso.
Numerosas transacciones pueden mantener simultáneamente modos de bloqueo no conflictivos. Por ejemplo, el bloqueo de fila compartida y el bloqueo de fila exclusiva del diagrama anterior no entran en conflicto, lo que permite que muchas transacciones o sesiones mantengan ambos simultáneamente.
Algunos modos de bloqueo se contradicen entre sí. Por ejemplo, sólo una transacción puede tener un bloqueo ACCESS EXCLUSIVE a la vez.
Mientras que algunos modos de bloqueo no interfieren entre sí. Por ejemplo, diferentes transacciones pueden tener cada una un bloqueo ACCESO COMPARTIDO.



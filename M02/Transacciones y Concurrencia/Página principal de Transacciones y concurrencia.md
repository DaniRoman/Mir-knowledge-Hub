## Concepto 

Las transacciones son una forma de agrupar y aislar múltiples sentencias para procesarlas como una única operación. 

En lugar de ejecutar cada comando individualmente a medida que se envía al servidor, en una transacción los comandos se agrupan y se ejecutan en un contexto distinto al de otras peticiones.

El aislamiento es una parte importante de las transacciones. Dentro de una transacción, las sentencias ejecutadas sólo pueden afectar al entorno dentro de la propia transacción. Desde dentro de la transacción, las sentencias pueden modificar datos y los resultados son visibles inmediatamente. Desde el exterior, no se realizan cambios hasta que la transacción se confirma, momento en el que todas las acciones dentro de la transacción se hacen visibles a la vez.

Estas características ayudan a las bases de datos a lograr el **cumplimiento de ACID**, ( Atomicity, Consistency, Isolation, Durability) proporcionando:
<span style="color:#0070c0">Atomicidad</span> (las acciones en una transacción se consignan todas o se revierten todas) 
<span style="color:#0070c0">Aislamiento</span> (fuera de la transacción, nada cambia hasta la consignación, mientras que dentro, las sentencias tienen consecuencias). Todo ello ayuda a la base de datos a mantener la <span style="color:#0070c0">Coherencia</span> (garantizando que no se produzcan transformaciones parciales de los datos). Además, los cambios en las transacciones no se devuelven como realizados con éxito hasta que se consignan al almacenamiento no volátil, lo que proporciona <span style="color:#0070c0">Durabilidad</span>.

Para lograr estos objetivos, las transacciones emplean una serie de estrategias diferentes. 

PostgreSQL utiliza un sistema llamado Multiversion Concurrency Control (MVCC),
Permite a la base de datos realizar estas acciones sin bloqueos innecesarios utilizando instantáneas de datos. 

En conjunto, estos sistemas constituyen uno de los pilares fundamentales de las bases de datos relacionales modernas, ya que les permiten procesar datos complejos de forma segura y resistente a los bloqueos.
## Tipos de fallos de consistencia

Una de las razones por las se utilizan transacciones es para obtener ciertas garantías sobre la consistencia de sus datos y el entorno en el que se procesan.
La consistencia puede romperse de muchas maneras diferentes, lo que afecta a la forma en que las bases de datos intentan evitarlas.

Hay cuatro formas principales en las que la inconsistencia puede surgir dependiendo de la implementación de la transacción. 

### Dirty reads ( Lecturas sucias )

Se producen cuando las sentencias de una transacción pueden leer datos escritos por otras transacciones en curso. 
Esto significa que aunque las sentencias de una transacción no hayan sido confirmadas todavía, pueden ser leídas y por tanto influir en otras transacciones.

Considerado como una violación grave de la coherencia, ya que las transacciones no están debidamente aisladas unas de otras. Las sentencias que nunca se han consignado en la base de datos pueden afectar a la ejecución de otras transacciones, modificando su comportamiento.

Las transacciones que permiten lecturas sucias no pueden hacer ninguna afirmación razonable sobre la coherencia de los datos resultantes.

### Non-repeatable reads ( Lecturas no repetibles )

Las lecturas no repetibles se producen cuando un commit fuera de la transacción altera los datos vistos dentro de la transacción. Puede reconocer este tipo de problema si, dentro de una transacción, los mismos datos se leen dos veces pero se recuperan valores diferentes en cada instancia.

Al igual que con las lecturas sucias, las transacciones que permiten lecturas no repetibles no ofrecen un aislamiento total entre transacciones. La diferencia es que con las lecturas no repetibles, las sentencias que afectan a la transacción han sido confirmadas fuera de la transacción.

### Phantom read ( Lectura fantasma )

Una lectura fantasma es un tipo específico de lectura no repetible que se produce cuando las filas devueltas por una consulta son diferentes la segunda vez que se ejecuta dentro de una transacción.

Por ejemplo, si una consulta dentro de una transacción devuelve cuatro filas la primera vez que se ejecuta, pero cinco filas la segunda vez, se trata de una lectura fantasma. Las lecturas fantasma son causadas por commits fuera de la transacción que alteran el número de filas que satisfacen la consulta.

### Anomalías de serialización

Las anomalías de serialización ocurren cuando los resultados de múltiples transacciones confirmadas concurrentemente dan lugar a resultados diferentes que si se hubieran confirmado una tras otra. Esto puede ocurrir cada vez que una transacción permite que ocurran dos commits que modifican cada uno la misma tabla o datos sin resolver conflictos.

Las anomalías de serialización son un tipo especial de problema que los primeros tipos de transacciones no comprendían. Esto se debe a que las primeras transacciones se implementaban con bloqueo, donde una no podía continuar si otra transacción estaba leyendo o alterando la misma pieza de datos.
## Niveles de aislamiento de las transacciones

Las transacciones no son una solución única. Diferentes escenarios requieren diferentes equilibrios entre rendimiento y protección.

PostgreSQL permite especificar el tipo de aislamiento de transacciones que necesita.

Los niveles de aislamiento ofrecidos por la mayoría de los sistemas de bases de datos incluyen los siguientes:

### **Read uncommitted** (Lectura no comprometida)

Read uncommitted es el nivel de aislamiento que ofrece menos garantías sobre el mantenimiento de la consistencia y el aislamiento de los datos. Aunque las transacciones que utilizan `Read uncommitted` tienen ciertas características frecuentemente asociadas con las transacciones, como la capacidad de confirmar múltiples sentencias a la vez o de revertir sentencias si se produce un error, permiten numerosas situaciones en las que se puede romper la consistencia.

Las transacciones configuradas con el nivel de aislamiento de `Read uncommitted` permiten:

- lecturas sucias
- lecturas no repetibles
- lecturas fantasma
- anomalías de serialización

Este nivel de aislamiento en realidad no está implementado en PostgreSQL. Aunque PostgreSQL reconoce el nombre del nivel de aislamiento, internamente no está soportado y en su lugar se utilizará `read committe`.

### Read committed ( Lectura comprometida )

`Read committed` es un nivel de aislamiento que protege específicamente contra lecturas sucias. Cuando las transacciones utilizan el nivel de consistencia de`Read committed`, los datos no comprometidos nunca pueden afectar al contexto interno de una transacción. Esto proporciona un nivel básico de consistencia asegurando que los datos no comprometidos nunca influyan en una transacción.

Aunque`Read committed` ofrece mayor protección que`Read uncommitted`, no protege contra todos los tipos de inconsistencia. Estos problemas todavía pueden surgir:

- lecturas no repetibles
- lecturas fantasma
- anomalías de serialización

PostgreSQL utilizará el nivel de `Read committed` por defecto si no se especifica ningún otro nivel de aislamiento.

### Repeatable read ( Lectura repetible )

El nivel de aislamiento de `Repeatable read` se basa en la garantía proporcionada por la `Read committed`. Evita las lecturas sucias como antes, pero también evita las lecturas no repetibles.

Esto significa que ningún cambio realizado fuera de la transacción afectará a los datos leídos dentro de la transacción. Una consulta ejecutada al inicio de una transacción nunca tendrá un resultado diferente al final de la transacción a menos que sea causada directamente por sentencias dentro de la transacción.

Mientras que la definición estándar del nivel de aislamiento de `Repeatable read` sólo requiere que se prevengan las lecturas sucias y no repetibles, PostgreSQL también previene las lecturas fantasma en este nivel. Esto significa que los commits fuera de la transacción no pueden alterar el número de filas que satisfacen una consulta.

Dado que el estado de los datos vistos dentro de la transacción puede desviarse de los datos actualizados en la base de datos, las transacciones pueden fallar en el commit si los dos conjuntos de datos no pueden reconciliarse. Debido a esto, un inconveniente de este nivel de aislamiento es que puede que tenga que reintentar transacciones si hay un fallo de serialización en la confirmación.

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
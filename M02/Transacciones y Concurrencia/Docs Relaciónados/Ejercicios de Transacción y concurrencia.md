## Ejercicio 1: Identificación de tipos de inconsistencias

Dado el siguiente escenario de transacciones concurrentes en una base de datos bancaria, identifica el tipo de inconsistencia que podría surgir en cada caso:

1. Un cliente realiza una consulta para verificar el saldo de su cuenta justo después de que otra transacción haya actualizado el saldo de esa misma cuenta.

2. Dos clientes realizan transacciones de transferencia de fondos entre sus cuentas bancarias al mismo tiempo.

## Ejercicio 2: Análisis de aislamiento

**Enunciado:** Considera las siguientes situaciones de transacciones concurrentes y analiza cómo se comportarían bajo diferentes niveles de aislamiento en PostgreSQL. Identifica posibles anomalias y cómo se pueden evitar con diferentes niveles de aislamiento.

1. **Situación:** Dos clientes intentan realizar depósitos simultáneos en la misma cuenta bancaria simultáneamente.
    
    - **Problema de consistencia:** Posible sobreescritura de depósitos, ambas transacciones podrían leer el mismo valor de saldo antes de realizar la operación de depósito
 
2. **Situación:** Un cliente realiza una transferencia de fondos entre dos cuentas bancarias mientras que otro cliente consulta el saldo de una de las cuentas involucradas en la transferencia.
    
    - **Problema de consistencia:** Posible inconsistencia en los saldos durante la consulta, debido a transacciones concurrentes que están ocurriendo en ese momento

## Ejercicio 3: Escritura de transacciones

- Crea una tabla cuenta con `id SERIAL PRIMARY KEY, balance NUMERIC NOT NULL`.
- Añade dos registros uno con un balance de 500 y otro a 0.
- Escribe una transacción SQL que transfiera $500 de la cuenta con `id = 1` a la cuenta con `id = 2`. Asegúrate de que la transferencia se realice en su totalidad o no se realice en absoluto.
- 
## Ejercicio 4: Manejo de errores y rollbacks

**Enunciado:** Escribe una transacción que transfiera $500 de la cuenta con `id = 2` a la cuenta con `id = 1`. Sin embargo, si la segunda actualización (incremento del saldo en la cuenta 1) falla por alguna razón, debes revertir completamente la transacción.

### Ejercicio 5: Uso de puntos de guardado ( `SAVEPOINTS`)

- Escribe una transacción en un script que añada $1500 a la cuenta con `id = 1`
- Crea un `SAVEPOINT A`
- Resta $500 y crea `SAVEPOINT B`
- Resta otros $200 de la cuenta, crea un `SAVEPOINT C`. 
- Haz un `ROLLBACK TO B` y muestra la cuenta con un `SELECT` ( No hagas `COMMIT` ) que cantidad marca? 
- Una vez veas el total donde te llevo el `SAVEPOINT` deshaz la transacción con un `ROLLBACK` (  en una consulta nueva) 
- En el script comenta ese `ROLLBACK TO B`, y repite los pasos para ver el `SAVEPOINT A` y `C` para finalizar añade al final de la transacción un `ROLLBACK` y muestra la cuenta.

### Ejercicio 6: Cambiar el grado de aislamiento

Para realizar este ejercicio necesitaras crear otro usuario en la base de datos con Rol de SUPERUSER 

- Entra en el contenedor `pg_container` `docker exec -it pg_container bash`
- Entra en la instancia PostgreSQL y crea otro usuario `psql -U postgres -nombreBaseDatos`

```sql
CREATE ROLE nombre 
SUPERUSER
LOGIN
PASSWORD 'moreSecurePass';
```

Queremos simular un error de lectura `Non-repeatable Reads`. 

- Inicia una transacción que realize una lectura del balance de una cuenta específica.
- En otra sesión modifica el balance de la misma cuenta
- Vuelve a leer el balance en la primera transacción.
- Observa si los resultados de la segunda lectura son diferentes de la primera debido a la modificación realizada por la otra sesión.

Utiliza el nivel de aislamiento adecuado para evitar el error de lectura no repetible que se produjo.
### Ejercicio 7: Leer el tipo de Lock dependiendo de la transacción.

Parte 1
Usuario 1 crea una transaccion de Update para su balance, sin cerrar la transaccion, observa que tipo de bloqueo se genera en esa tabla, mientras con el usuario 2 haz una consulta sobre esa tabla, puedes? porque? que tipo de bloqueo genera esa transaccion de consulta?

Parte 2
cierra la transaccion del usuario 2 de consulta y mientras la transaccion del usuario 1 esta en curso trata de hacer otra transaccion igual y mira que sucede y que tipos de bloqueos suceden.

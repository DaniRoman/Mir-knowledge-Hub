Write-Ahead Logging (WAL) es un método estándar para garantizar la integridad de los datos. 

Esta caracteristica esta diseñada para proteger la integridad de los datos

 cuando postgres realiza una transacción la escribe en un registro de transacciones, y la pasa a disco, y no considera que esta transacción se ha completado hasta que los cambios en los archivos de datos (donde residen las tablas y los índices) deben escribirse sólo después de que esos cambios se hayan registrado,
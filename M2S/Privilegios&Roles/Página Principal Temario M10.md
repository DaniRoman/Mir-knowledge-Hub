#### Recursos
[Postgres con Docker y Pg_admin ](https://towardsdatascience.com/how-to-run-postgresql-and-pgadmin-using-docker-3a6a8ae918b5)
[PostGresDB BestPractices](https://docs.jivesoftware.com/9.0_on_prem_int/sys_admin/jive.help.core/admin/PostGresDBBestPractices.html)
[[Postgres Main Site]]

1. **Gestión de Roles**:

    - La gestión de roles implica la asignación de permisos y privilegios a usuarios y grupos para acceder y manipular los datos en la base de datos.
    - Los roles pueden tener privilegios específicos que controlan qué acciones pueden realizar en la base de datos, como SELECT, INSERT, UPDATE, DELETE, etc.
    - Es importante definir roles adecuados para diferentes tipos de usuarios y aplicaciones para garantizar la seguridad y la integridad de los datos.
    - [[Ejemplo de caso de uso para definir roles]]
    - [[Recurso y comandos para gestionar Docker y Postgres]]
    - [[Practica Res]]
    - [[Practica]]

1. **Soporte a Copias de Seguridad**:

    - Las copias de seguridad son una parte crucial de la administración de bases de datos para proteger los datos contra la pérdida y la corrupción.
    - Las estrategias de copia de seguridad incluyen copias completas, diferenciales e incrementales, así como la programación regular de las copias de seguridad.
    - Es importante almacenar las copias de seguridad en ubicaciones seguras y probar regularmente la capacidad de restauración para garantizar la integridad de los datos.
    - [[Practica soporte a copias de seguridad]]
    - [[Practica soporte a copias de seguridad Res]]
    
1. **Replicación**:

	[[Replicacion PostgreSQL]]
    - La replicación implica mantener copias idénticas de una base de datos en múltiples servidores para mejorar la disponibilidad y la escalabilidad.
    - La replicación puede ser síncrona o asíncrona, dependiendo de los requisitos de consistencia y rendimiento.
    - Se pueden implementar diferentes topologías de replicación, como la  maestro-esclavo, maestro-maestro y en anillo, según las necesidades del sistema.

    
1. **Recuperación de Datos**:
    
    - La recuperación de datos implica restaurar la base de datos a un estado consistente después de un fallo, error o pérdida de datos.
    - Se pueden utilizar diferentes técnicas de recuperación, como la recuperación basada en registros (log-based recovery) o la recuperación de copias de seguridad.
    - Es fundamental tener planes de recuperación de desastres bien definidos y realizar pruebas regulares para garantizar la capacidad de recuperación en caso de fallo.
    

1. **Alta Disponibilidad**:

    - La alta disponibilidad se refiere a la capacidad de un sistema para estar disponible y accesible en todo momento, incluso en caso de fallos o interrupciones.
    
    - Se pueden utilizar técnicas como la redundancia de hardware, la clustering, la virtualizacion y la distribución geográfica para mejorar la disponibilidad.
    
    - Es esencial monitorear continuamente el rendimiento y la disponibilidad del sistema, así como implementar medidas proactivas para mitigar los riesgos de tiempo de inactividad


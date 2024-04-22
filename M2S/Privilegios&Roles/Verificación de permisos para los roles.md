### Administrador del Sistema:

#### Lo que puede hacer el Administrador del Sistema:

```sql
`-- Ver todos los usuarios 
SELECT * FROM usuarios;  
-- Ver todos los proyectos 
SELECT * FROM proyectos;  
-- Ver todas las tareas SELECT * FROM tareas;  
-- Modificar usuarios UPDATE usuarios 
SET nombre = 'Nuevo Nombre' WHERE id = id_usuario;  
-- Modificar proyectos 
UPDATE proyectos SET descripcion = 'Nueva descripción' WHERE id = id_proyecto;  
-- Modificar tareas
UPDATE tareas SET estado = 'Completada' WHERE id = id_tarea;
```
`

#### Lo que no puede hacer el Administrador del Sistema:

`-- Nada en particular, ya que este rol tiene acceso completo.`

### Gerente de Proyecto:

#### Lo que puede hacer el Gerente de Proyecto:

```sql
-- Ver proyectos asignados 
SELECT * FROM proyectos WHERE responsable_id = id_del_gerente;  
-- Ver tareas de los proyectos asignados 
SELECT * FROM tareas WHERE proyecto_id IN (SELECT id FROM proyectos WHERE responsable_id = id_del_gerente);  
-- Modificar proyectos asignados 
UPDATE proyectos SET descripcion = 'Nueva descripción' WHERE id = id_proyecto AND responsable_id = id_del_gerente;  
-- Modificar tareas de los proyectos asignados 
UPDATE tareas SET estado = 'Completada' WHERE id = id_tarea AND proyecto_id IN (SELECT id FROM proyectos WHERE responsable_id = id_del_gerente);`
```



#### Lo que no puede hacer el Gerente de Proyecto:

```sql
-- Ver todos los usuarios 
SELECT * FROM usuarios;  
-- Modificar usuarios 
UPDATE usuarios SET nombre = 'Nuevo Nombre' WHERE id = id_usuario;
```

### Desarrollador:

#### Lo que puede hacer el Desarrollador:


```plsql
-- Ver proyectos asignados 
SELECT * FROM proyectos WHERE id IN (SELECT proyecto_id FROM tareas WHERE asignado_a = id_del_desarrollador);  
-- Ver tareas asignadas 
SELECT * FROM tareas WHERE asignado_a = id_del_desarrollador;  
-- Modificar estado de las tareas asignadas 
UPDATE tareas SET estado = 'En Progreso' WHERE id = id_tarea AND asignado_a = id_del_desarrollador;
```

#### Lo que no puede hacer el Desarrollador:

```sql 
-- Ver todos los usuarios 
SELECT * FROM usuarios;  
-- Ver todas las tareas 
SELECT * FROM tareas;  
-- Modificar usuarios 
UPDATE usuarios SET nombre = 'Nuevo Nombre' WHERE id = id_usuario;  
-- Modificar tareas de otros proyectos 
UPDATE tareas SET estado = 'Completada' WHERE id = id_tarea AND asignado_a != id_del_desarrollador;```

### Cliente:

#### Lo que puede hacer el Cliente:

```sql
-- Ver proyectos relacionados al cliente 
SELECT * FROM proyectos WHERE id IN (SELECT proyecto_id FROM tareas WHERE asignado_a = id_del_cliente);  
-- Ver tareas asignadas al cliente 
SELECT * FROM tareas WHERE asignado_a = id_del_cliente;
```

#### Lo que no puede hacer el Cliente:


```sql
-- Ver todos los usuarios 
SELECT * FROM usuarios;  
-- Ver todas las tareas 
SELECT * FROM tareas;  
-- Modificar usuarios 
UPDATE usuarios SET nombre = 'Nuevo Nombre' WHERE id = id_usuario;  
-- Modificar tareas asignadas a otros clientes 
UPDATE tareas SET estado = 'Completada' WHERE id = id_tarea AND asignado_a != id_del_cliente;
```
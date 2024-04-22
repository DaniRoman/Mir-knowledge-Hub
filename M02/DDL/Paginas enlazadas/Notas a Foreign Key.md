```sql

[CONSTRAINT fk_name]
   FOREIGN KEY(fk_columns) 
   REFERENCES parent_table(parent_key_columns)
   [ON DELETE delete_action]
   [ON UPDATE update_action]

CREATE TABLE customers(
   customer_id INT GENERATED ALWAYS AS IDENTITY,
   customer_name VARCHAR(255) NOT NULL,
   PRIMARY KEY(customer_id)
);

CREATE TABLE contacts(
   contact_id INT GENERATED ALWAYS AS IDENTITY,
   customer_id INT,
   contact_name VARCHAR(255) NOT NULL,
   phone VARCHAR(15),
   email VARCHAR(100),
   PRIMARY KEY(contact_id),
   
   CONSTRAINT fk_customer
      FOREIGN KEY(customer_id) 
        REFERENCES customers(customer_id)
        ON DELETE CASCADE
);

```

- Nombre de la restricción de clave externa después de la palabra clave CONSTRAINT. La cláusula CONSTRAINT es opcional. Si la omito, PostgreSQL asignará un nombre generado automáticamente.
- Segundo, especifique una o más columnas de clave foránea entre paréntesis después de las palabras clave FOREIGN KEY.
- Tercero, especifique la tabla padre y las columnas de clave padre referenciadas por las columnas de clave foránea en la cláusula REFERENCES.
- Por último, especifique las acciones de eliminación y actualización deseadas en las cláusulas ON DELETE y ON UPDATE.
- Las acciones de borrado y actualización determinan los comportamientos cuando se borra y actualiza la clave primaria de la tabla padre.Dado que la clave primaria rara vez se actualiza, la acción ON UPDATE se utiliza con poca frecuencia en la práctica. Nos centraremos en la acción ON DELETE.

- SET NULL
- SET DEFAULT
- RESTRICT
- NO ACTION
- CASCADE (Mas utilizada)

[Referencia externa a Foreign key y sus acciones de borrado](https://www.postgresqltutorial.com/postgresql-tutorial/postgresql-foreign-key/)
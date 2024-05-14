## Concepto de Autenticación

Proceso mediante el cual el servidor de base de datos establece la identidad del cliente 

Para autenticar una conexión se puede seleccionar en base a la dirección IP del cliente, la base de datos y el usuario.

>[!important] Importante!
>PostgreSQL gestiona los privilegios en términos de "roles". A partir de ahora, siempre que usemos "usuario de la base de datos" exactamente nos referimos a “rol con el privilegio de login”.

La autenticación se controla mediante el archivo de configuración `pg_hba.conf` que tiene define un conjunto de registros.. HBA significa autenticación basada en host.

```sh
#pg_hba.conf

# TYPE DATABASE USER    ADDRESS          METHOD
host    all     all     192.168.1.0/24   md5
```

Los registros no pueden ocupar más de una sola línea. Un registro está formado por varios campos separados por espacios y/o tabuladores. Los campos pueden contener espacios en blanco si el valor del campo está entre comillas.

Cada registro especifica 

- Un tipo de conexión. 
- Un intervalo de direcciones IP del cliente (si es relevante para el tipo de conexión), 
- Un nombre de base de datos. 
- Un nombre de usuario
- Método de autenticación que se utilizará para las conexiones que coincidan con estos parámetros.

El primer registro coincidente se utilizará para realizar la autenticación.
Si no se encuentra ningún registro coincidente, ¡se rechaza la conexión!

El registro puede tener uno de los siguientes siete formatos:

1. `local database user auth-method [auth-options]`
2. `host database user address auth-method [auth-options]`
3. `hostssl database user address auth-method [auth-options]`
4. `hostnossl database user address auth-method [auth-options]`
5. `host database user IP-address IP-mask auth-method [auth-options]`
6. `hostssl database user IP-address IP-mask auth-method [auth-options]`
7. `hostnossl database user IP-address IP-mask auth-method [auth-options]`

### Explicación de los campos:

- **local:** Este registro coincide con los intentos de conexión mediante sockets de dominio Unix. Sin un registro de este tipo, no se permiten conexiones de sockets de dominio Unix.
- **host:** Este registro coincide con los intentos de conexión mediante el protocolo TCP/IP. Estos registros sirven tanto para conexiones SSL como no SSL.
- **hostssl:** Este registro coincide con los intentos de conexión realizados con TCP/IP, pero solo cuando la conexión se realiza con cifrado SSL.
- **hostnossl:** Este tipo de registro tiene el comportamiento contrario a hostssl; solo coincide con los intentos de conexión realizados por TCP/IP que no usan SSL.
- **database:** Especifica con qué nombres de base de datos coincide este registro.
- **user:** Especifica con qué nombres de usuario de base de datos coincide este registro.
- **IP-address y IP-mask:** Estos dos campos pueden utilizarse como alternativa a la notación de dirección IP/longitud de la máscara.
- **auth-method:** Especifica el método de autenticación que se utilizará cuando una conexión coincida con este registro.

#### Métodos de autenticación
- `trust`
- `reject`
- `scram-sha-256`
- `md5`
- `password`

Después del campo auth-method, puede haber campos en forma de nombre=valor que especifiquen opciones para el método de autenticación.

Ejemplos de entradas en pg_hba.conf:

```sql
# Permitir que cualquier usuario en el sistema local se conecte a cualquier base de datos utilizando sockets de dominio Unix. 
local   all   all   trust  

# Permitir lo mismo pero utilizando conexiones TCP/IP locales. 
host   all   all   127.0.0.1/32   trust  

# Permitir conexiones desde una dirección IPv6 local. 
host   all   all   ::1/128    trust  

# Permitir a cualquier usuario desde cualquier host con dirección IP 192.168.93.x conectarse a la base de datos "postgres" utilizando el método 'ident'. 
host   postgres   all   192.168.93.0/24    ident  

# Rechazar todas las conexiones desde la dirección IP 192.168.54.1, pero permitir conexiones GSSAPI desde cualquier otro lugar de Internet. 
host  all   all   192.168.54.1/32   reject   host   all   all   0.0.0.0/0 gss6
```



[[Ejercicios dvdRental Mas sencillos]]


```sql 
--- _Contar el número de alquileres realizados por cada cliente en el periodo de tiempo entre 2005-06-14 y 2005-07-05 y mostrar solo aquellos clientes que han realizado entre 10 y 15.

---Cuenta el número de películas por clasificación (`rating`), calcula la duración de alquiler promedio y la suma de las tasas de alquiler total para cada clasificación, y luego filtra las clasificaciones que tienen más de 3 películas.
    
--- _Obtener el ID y el título de todas las películas lanzadas después del año 2000 y con una duración mayor que la duración promedio de todas las películas._

---elecciona los detalles (nombre, apellido, correo electrónico) de los clientes que han realizado más de 30 alquileres. 

---Seleccionamos el ID, título y la tasa de alquiler de las películas donde la tasa de alquiler es mayor que el promedio de todas las películas.

--- selecciona el `film_id` y el título de las películas que están disponibles en una tienda especifica

---selecciona todas las películas que tienen una duración mayor que la duración promedio de las películas en su respectiva clasificación (rating).

---Obtener el ID, nombre y apellido de los clientes cuyo monto promedio de pago es superior al promedio general de todos los clientes".

---selecciona el ID del cliente, su nombre y apellido, y cuenta el número de alquileres realizados por cada cliente. Solo se muestran los clientes que hayan realizado más de 10 alquileres.

---consulta calcula el total de ventas por tienda

---cuenta el número de alquileres por película en una categoría específica muestra el id y el titulo

--- obtener el número de alquileres de todas las categorías y muestra los que tengas mas de 500 alquilers

```

```sql
--- _Contar el número de alquileres realizados por cada cliente en el periodo de tiempo entre 2005-06-14 y 2005-07-05 y mostrar solo aquellos clientes que han realizado entre 10 y 15.
SELECT customer_id, COUNT(customer_id) AS num_alquileres
FROM public.rental
WHERE rental_date BETWEEN '2005-06-14' AND '2005-07-05'
GROUP BY customer_id
HAVING COUNT ('num_alquileres') BETWEEN 10 AND 15;

---Cuenta el número de películas por clasificación (`rating`), calcula la duración de alquiler promedio y la suma de las tasas de alquiler total para cada clasificación, y luego filtra las clasificaciones que tienen más de 3 películas.
SELECT rating,
    COUNT(film_id) AS num_films,
    AVG(rental_duration) AS avg_rental_duration,
    SUM(rental_rate) AS total_rental_rate
FROM 
    film
GROUP BY 
    rating
HAVING 
    COUNT(num_films) > 3;

--- _Obtener el ID y el título de todas las películas lanzadas después del año 2000 y con una duración mayor que la duración promedio de todas las películas._
SELECT film_id, title
FROM public.film
WHERE release_year >= 2000
AND length > (
    SELECT AVG(length)
    FROM film
);
---elecciona los detalles (nombre, apellido, correo electrónico) de los clientes que han realizado más de 30 alquileres. 
SELECT first_name,last_name,email
FROM customer
WHERE customer_id IN (
        SELECT customer_id
        FROM public.rental
        GROUP BY customer_id
        HAVING COUNT(*) > 30
    );
---Seleccionamos el ID, título y la tasa de alquiler de las películas donde la tasa de alquiler es mayor que el promedio de todas las películas.
SELECT film_id,title,rental_rate
FROM public.film
WHERE rental_rate > (
	SELECT AVG(rental_rate)
    FROM public.film
    );
--- selecciona el `film_id` y el título de las películas que están disponibles en una tienda especifica
SELECT film_id,title
FROM public.film
WHERE film_id IN (
        SELECT film_id
        FROM public.inventory
        WHERE store_id = 1
    )
ORDER BY title ASC;
---selecciona todas las películas que tienen una duración mayor que la duración promedio de las películas en su respectiva clasificación (rating).
SELECT film_id, title, length, rating
FROM film f
WHERE length > (
    SELECT AVG(length)
    FROM film
    WHERE rating = f.rating
);
---Obtener el ID, nombre y apellido de los clientes cuyo monto promedio de pago es superior al promedio general de todos los clientes".
SELECT c.customer_id, c.first_name, c.last_name, AVG(p.amount) AS avg_payment_amount
FROM public.customer c
JOIN payment p USING(customer_id)
GROUP BY c.customer_id, c.first_name, c.last_name
HAVING AVG(p.amount) > (
    SELECT AVG(amount)
    FROM public.payment
);
---selecciona el ID del cliente, su nombre y apellido, y cuenta el número de alquileres realizados por cada cliente. Solo se muestran los clientes que hayan realizado más de 10 alquileres.
SELECT 
    c.customer_id, 
    c.first_name, 
    c.last_name, 
    COUNT(r.rental_id) AS num_rentals
FROM public.customer c
JOIN public.rental r ON c.customer_id = r.customer_id
JOIN public.inventory i ON r.inventory_id = i.inventory_id
JOIN public.film f ON i.film_id = f.film_id
GROUP BY 
    c.customer_id, 
    c.first_name, 
    c.last_name
HAVING COUNT(r.rental_id) > 10;
---consulta calcula el total de ventas por tienda
SELECT 
    s.store_id, 
    SUM(p.amount) AS total_sales
FROM 
    public.store s
JOIN 
     public.staff sf ON s.store_id = sf.store_id
JOIN 
     public.payment p ON sf.staff_id = p.staff_id
GROUP BY 
    s.store_id;
---cuenta el número de alquileres por película en una categoría específica muestra el id y el titulo
SELECT 
    fc.category_id, 
    f.title, 
    COUNT(r.rental_id) AS num_rentals
FROM 
    public.film_category fc
JOIN 
     public.film f ON fc.film_id = f.film_id
JOIN 
     public.inventory i ON f.film_id = i.film_id
JOIN 
     public.rental r ON i.inventory_id = r.inventory_id
WHERE 
    fc.category_id = 5  -- ID de la categoría específica (por ejemplo)
GROUP BY 
    fc.category_id, 
    f.title;
--- obtener el número de alquileres de todas las categorías y muestra los que tengas mas de 500 alquilers
SELECT 
    c.name AS category_name, 
    COUNT(r.rental_id) AS num_rentals
FROM 
    public.rental r
JOIN 
    public.inventory i ON r.inventory_id = i.inventory_id
JOIN 
    public.film_category fc ON i.film_id = fc.film_id
JOIN 
    public.category c ON fc.category_id = c.category_id
GROUP BY 
    c.name
	HAVING COUNT('num_rentals') > 500;
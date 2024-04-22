```sql
---Selecciona todas las películas que tienen una duración de al menos 120 minutos y que tengan una clasificación de 'PG-13' o 'R'.
    
SELECT * FROM film WHERE length >= 120 AND (rating = 'PG-13' OR rating = 'R');
    
--Encuentra todos los clientes que se registraron entre el 1 de enero de 2005 y el 30 de junio de 2005 y cuyos correos electrónicos son de dominio '---.com'.
    
    SELECT * FROM customer WHERE create_date BETWEEN '2005-01-01' AND '2005-06-30' AND email LIKE '%---.com';
   
---Muestra todas las películas lanzadas después del año 2000 ordenadas por duración de manera descendente.
     
SELECT * FROM film WHERE release_year > 2000 ORDER BY length DESC;
    
--Calcula el total de películas en la base de datos.
    
SELECT COUNT(*) AS total_films FROM film;
    
---Encuentra el número de películas por categoría, pero solo muestra las categorías que tienen más de 3 películas.
    
  SELECT category_id, COUNT(*) AS num_movies FROM film_category GROUP BY category_id HAVING COUNT(*) > 3;
    
-- Muestra todos los pagos realizados después del año 2005 y que sean mayores a $5.
    
    SELECT * FROM payment WHERE amount > 5 AND payment_date >= '2005-01-01';
    
  ---encuentra todas las direcciones con códigos postales entre '10000' y '20000' y cuyos números de teléfono comiencen con '1'
    
    SELECT * FROM address WHERE postal_code BETWEEN '10000' AND '20000' AND phone LIKE '1%';`
    
 --- Muestra todos los actores ordenados por apellido de manera ascendente.
    
    SELECT * FROM actor ORDER BY last_name ASC;
    
--Encuentra el costo de reemplazo más alto entre todas las películas.
    
    SELECT MAX(replacement_cost) AS max_replacement_cost FROM film;`

--Calcula la duración promedio de las películas para cada clasificación y muestra solo las clasificaciones cuya duración promedio sea superior a 120 minutos.

SELECT rating, AVG(length) AS avg_length FROM film GROUP BY rating HAVING AVG(length) > 120;
```

```sql
--Encuentra todas las películas que tienen una duración superior al promedio de duración de todas las películas. 
SELECT * FROM film WHERE length > (SELECT AVG(length) FROM film);` 

--Enunciado:** Muestra el total de alquileres realizados por cada cliente.
SELECT c.customer_id, c.first_name, c.last_name,         (SELECT COUNT(*) FROM rental r WHERE r.customer_id = c.customer_id) AS num_rentals FROM customer c;`
    
---Obtén el nombre de cada actor junto con el número de películas en las que han actuado.
SELECT a.actor_id, a.first_name, a.last_name,         (SELECT COUNT(*) FROM film_actor WHERE actor_id = a.actor_id) AS num_movies FROM actor a;`

---Encuentra todas las películas que están en la categoría 'Horror' o 'Comedy'.
SELECT * FROM film WHERE film_id IN (SELECT film_id FROM film_category WHERE category_id IN (SELECT category_id FROM category WHERE name IN ('Horror', 'Comedy')))

---Obtén todos los clientes junto con una bandera que indique si han realizado al menos un alquiler.
SELECT customer_id, first_name, last_name,         (EXISTS (SELECT 1 FROM rental WHERE rental.customer_id = customer.customer_id)) AS has_rental FROM customer;`

--Muestra todas las direcciones junto con el número de clientes que tienen esa dirección.
SELECT a.address_id, a.address, a.district, a.city_id, a.postal_code, a.phone,        (SELECT COUNT(*) FROM customer WHERE customer.address_id = a.address_id) AS num_customers FROM address a;`
    
---Encuentra todas las películas que no han sido alquiladas.
SELECT * FROM film WHERE NOT EXISTS (SELECT 1 FROM rental WHERE rental.film_id = film.film_id);`
    
---Obtiene el número de alquileres por película junto con el título de la película.
SELECT film_id, title,         (SELECT COUNT(*) FROM rental WHERE rental.film_id = film.film_id) AS num_rentals FROM film;`
    
---Muestra todas las películas junto con el número de alquileres que han tenido
SELECT f.film_id, f.title,         (SELECT COUNT(*) FROM rental WHERE rental.film_id = f.film_id) AS num_rentals FROM film f;`
    
---Encuentra todas las categorías que no tienen películas con una duración superior a 120 minutos.
SELECT * FROM category WHERE category_id NOT IN (SELECT fc.category_id FROM film_category fc WHERE fc.film_id IN (SELECT film_id FROM film WHERE length > 120));`
```
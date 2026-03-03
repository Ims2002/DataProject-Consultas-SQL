# Consultas SQL - Parte 1

## 1. Crea el esquema de la BBDD.
![Diagrama de la BBDD](DDBB_diagram.png)

## 2. Muestra los nombres de todas las películas con una clasificación por edades de 'R' SQL
```
SELECT title, rating 
FROM film
WHERE rating IN ('R');
```
## 3 Encuenta los nombres de los catores que tengan un "actor_id" entre 30 y 40
```
select actor_id , first_name   
from actor
where actor_id  between 30 and 40;
```
## 4. Obtén las películas cuyo idioma coincide con el idioma original
En este caso el campo original_language_id es todo NULL y por eso no devuelve registros.
```
select *
from film
where language_id = original_language_id 
```
## 5. Ordena las películas por duración de forma ascendente.
```
select *
from film
order by length asc
```
## 6. Encuentra el nombre y apellido de los actores que tengan ‘Allen’ en su
apellido.
```
SELECT CONCAT(first_name, ' ', last_name) AS "Nombre Completo"
FROM actor
WHERE LOWER(last_name) LIKE '%allen%';
```
## 7. Encuentra la cantidad total de películas en cada clasificación de la tabla
“film” y muestra la clasificación junto con el recuento.
```
select rating, count(film_id) 
from film
group by rating
```
## 8. Encuentra el título de todas las películas que son ‘PG-13’ o tienen una
duración mayor a 3 horas en la tabla film.
```
select title 
from film
where rating = 'PG-13' or length > 180
```
## 9. Encuentra la variabilidad de lo que costaría reemplazar las películas.
```
SELECT STDDEV(replacement_cost) AS "Desviacion_Estandar"
FROM film;
```
## 10. Encuentra la mayor y menor duración de una película de nuestra BBDD.
```
select MAX(length) as "Mayor Duración",
	MIN(length) as "Menor Duración"
from film 
```
## 11. Encuentra lo que costó el antepenúltimo alquiler ordenado por día.
```
SELECT amount
FROM payment
ORDER BY payment_date DESC
LIMIT 1 OFFSET 2;
```
## 12. Encuentra el título de las películas en la tabla “film” que no sean ni ‘NC17’ ni ‘G’ en cuanto a su clasificación.
```
select title 
from film
where rating not in  ('NC-17', 'G')
```
## 13. Encuentra el promedio de duración de las películas para cada clasificación de la tabla film y muestra la clasificación junto con elpromedio de duración.
```
select rating as Rating, round(AVG(length),2) as AVG_Length 
from  film
group by rating 
```
## 14. Encuentra el título de todas las películas que tengan una duración mayor a 180 minutos.
```
select title
from film
where length > 180
```
## 15. ¿Cuánto dinero ha generado en total la empresa?
```
select SUM(amount)
from payment
```
## 16. Muestra los 10 clientes con mayor valor de id.
```
select *
from customer
order by customer_id desc
limit 10
```
## 17. Encuentra el nombre y apellido de los actores que aparecen en la película con título ‘Egg Igby’.
```
select *
from film as f
join film_actor as fa on f.film_id = fa.film_id  
join actor as a on fa.actor_id = a.actor_id 
where LOWER(f.title) like 'egg igby' 
```
## 18. Selecciona todos los nombres de las películas únicos.
```
select distinct title
from film
```
## 19. Encuentra el título de las películas que son comedias y tienen una duración mayor a 180 minutos en la tabla “film”.
```
select f.title
from film f
join film_category fc on f.film_id = fc.film_id 
join category c on fc.category_id = c.category_id 
where c."name" = 'Comedy' and f.length > 180
```
## 20. Encuentra las categorías de películas que tienen un promedio de duración superior a 110 minutos y muestra el nombre de la categoría
junto con el promedio de duración.
```
select c."name", round(AVG(f.length),2) as total_min_categoria
from film f
join film_category fc on f.film_id = fc.film_id 
join category c on fc.category_id = c.category_id 
group by c."name" 
having AVG(f.length) > 110
```
## 21. ¿Cuál es la media de duración del alquiler de las películas?
```
SELECT AVG(DATEDIFF(return_date, rental_date)) AS media_duracion_real
FROM rental
WHERE return_date IS NOT NULL;
```
## 22. Crea una columna con el nombre y apellidos de todos los actores y actrices.
```
create view act_info as 
	select concat(first_name, ' ', last_name) as full_name
	from actor a 
	
select * from act_info
```
## 23. Números de alquiler por día, ordenados por cantidad de alquiler de forma descendente.
```
SELECT AVG(DATEDIFF(return_date, rental_date)) AS media_duracion_real
FROM rental
WHERE return_date IS NOT NULL;
```
## 24. Encuentra las películas con una duración superior al promedio.
```
select * 
from film
where (select AVG(length) from film) < length
```
## 25. Averigua el número de alquileres registrados por mes.
```
SELECT 
DATE_FORMAT(rental_date, '%m-%Y') AS mes,
COUNT(*) AS alquileres_mes
FROM rental
GROUP BY DATE_FORMAT(rental_date, '%m-%Y')
ORDER BY MIN(rental_date);
```
## 26. Encuentra el promedio, la desviación estándar y varianza del total pagado.
```
select AVG(amount) as promedio, 
stddev(amount) as desviacion_est, 
variance(amount) as varianza
from payment
```
## 27. ¿Qué películas se alquilan por encima del precio medio?
```
create view price_avg as 
	select AVG(amount) as avg_price
	from payment p 

select * 
from film f
join inventory i on f.film_id = i.film_id 
join rental r on i.inventory_id = r.inventory_id 
join payment p on r.rental_id = p.rental_id 
where (select avg_price from price_avg) < p.amount 
```
## 28. Muestra el id de los actores que hayan participado en más de 40 películas.
```
select a.actor_id
from actor a
join film_actor fa on a.actor_id = fa.actor_id  
join film f on fa.film_id = f.film_id 
group by a.actor_id 
having count(f.film_id) > 40
```
## 29. Obtener todas las películas y, si están disponibles en el inventario, mostrar la cantidad disponible.
```
SELECT 
f.film_id,
f.title,
COUNT(i.inventory_id) AS cantidad_disponible
FROM film f
LEFT JOIN inventory i 
ON f.film_id = i.film_id
GROUP BY f.film_id, f.title;
```
## 30. Obtener los actores y el número de películas en las que ha actuado.
```
SELECT 
a.actor_id,
CONCAT(a.first_name, ' ', a.last_name) AS nombre_completo,
COUNT(fa.film_id) AS cantidad_peliculas
FROM actor a
LEFT JOIN film_actor fa 
ON a.actor_id = fa.actor_id
GROUP BY a.actor_id, a.first_name, a.last_name
ORDER BY cantidad_peliculas DESC;
```
## 31. Obtener todas las películas y mostrar los actores que han actuado en ellas, incluso si algunas películas no tienen actores asociados.
```
SELECT 
f.title,
CONCAT(a.first_name, ' ', a.last_name) AS actor
FROM film f
LEFT JOIN film_actor fa 
ON f.film_id = fa.film_id
LEFT JOIN actor a 
ON fa.actor_id = a.actor_id
ORDER BY f.title;
```
## 32. Obtener todos los actores y mostrar las películas en las que han actuado, incluso si algunos actores no han actuado en ninguna película.
```
SELECT 
CONCAT(a.first_name, ' ', a.last_name) AS actor,
f.title
FROM actor a
LEFT JOIN film_actor fa 
ON a.actor_id = fa.actor_id
LEFT JOIN film f 
ON fa.film_id = f.film_id
ORDER BY actor;
```
## 33. Obtener todas las películas que tenemos y todos los registros de alquiler.
```
select * 
from film f
full join inventory i on f.film_id = i.film_id 
full join rental r on i.inventory_id = r.inventory_id 
```
## 34. Encuentra los 5 clientes que más dinero se hayan gastado con nosotros.
```
select concat(c.first_name, ' ', c.last_name) as nombre, SUM(p.amount) as cantidad 
from customer c 
join payment p on c.customer_id = p.customer_id 
group by nombre 
order by SUM(p.amount) desc
limit 5
```
## 35. Selecciona todos los actores cuyo primer nombre es 'Johnny'.
```
select * 
from actor a 
where a.first_name like 'JOHNNY'
```
## 36. Renombra la columna “first_name” como Nombre y “last_name” como Apellido.
```
alter table customer 
rename column first_name to nombre

alter table customer 
rename column last_name to apellido
```
## 37. Encuentra el ID del actor más bajo y más alto en la tabla actor.
```
select MAX(actor_id), MIN(actor_id) from actor 
```
## 38. Cuenta cuántos actores hay en la tabla “actor”.
```
select count(actor_id) from actor 
```
## 39. Selecciona todos los actores y ordénalos por apellido en orden ascendente.
```
select * from actor 
order by actor.last_name asc
```
## 40. Selecciona las primeras 5 películas de la tabla “film”.
```
select * from film
limit 5
```
## 41. Agrupa los actores por su nombre y cuenta cuántos actores tienen el mismo nombre. ¿Cuál es el nombre más repetido?
```
select first_name, count(first_name) as cantidad 
from actor
group by first_name 
order by cantidad desc
```
KENNETH	4
PENELOPE 4

## 42. Encuentra todos los alquileres y los nombres de los clientes que los realizaron.
```
select r.rental_id, c.nombre
from rental r 
join customer c on r.customer_id = c.customer_id 
```
## 43. Muestra todos los clientes y sus alquileres si existen, incluyendo aquellos que no tienen alquileres.
```
SELECT 
c.customer_id,
c.first_name,
c.last_name,
r.rental_id,
r.rental_date
FROM customer c
LEFT JOIN rental r 
ON c.customer_id = r.customer_id
ORDER BY c.customer_id;
```
## 44. Realiza un CROSS JOIN entre las tablas film y category. ¿Aporta valor esta consulta? ¿Por qué? Deja después de la consulta la contestación.
```
select f.title, c."name"  
from film f
cross join category c
```
No aporta valor en este caso ya que únicamente junta cada película con todas las categorías que hay en la tabla category, así con todas las películas. Lo que aportaría valor es mostrar las categorías a las que pertenece una o todas película.

## 45. Encuentra los actores que han participado en películas de la categoría 'Action'.
```
SELECT 
CONCAT(a.first_name, ' ', a.last_name) AS actor,
c.name AS categoria
FROM actor a
JOIN film_actor fa 
ON a.actor_id = fa.actor_id
JOIN film_category fc 
ON fa.film_id = fc.film_id
JOIN category c 
ON fc.category_id = c.category_id
WHERE c.name = 'Action'
ORDER BY actor;
```
## 46. Encuentra todos los actores que no han participado en películas.
```
SELECT 
CONCAT(a.first_name, ' ', a.last_name) AS actor
FROM actor a
LEFT JOIN film_actor fa 
ON a.actor_id = fa.actor_id
WHERE fa.actor_id IS NULL;
```

## 47. Selecciona el nombre de los actores y la cantidad de películas en las que han participado.
```
select ai.full_name, count(fa.film_id) 
from act_info ai 
left join film_actor fa on ai.actor_id = fa.actor_id 
group by ai.actor_id, ai.full_name 
```
## 48. Crea una vista llamada “actor_num_peliculas” que muestre los nombres de los actores y el número de películas en las que han participado.
```
create view actor_num_peliculas as
	select ai.full_name, count(fa.film_id) 
	from act_info ai 
	left join film_actor fa on ai.actor_id = fa.actor_id 
	group by ai.actor_id, ai.full_name 
```
## 49. Calcula el número total de alquileres realizados por cada cliente.
```
SELECT 
c.customer_id,
c.email,
COUNT(r.rental_id) AS total_alquileres
FROM customer c
LEFT JOIN rental r 
ON c.customer_id = r.customer_id
GROUP BY c.customer_id, c.email
ORDER BY total_alquileres DESC;
```
## 50. Calcula la duración total de las películas en la categoría 'Action'.
```
select sum(f.length)
from film f
join film_category fc on f.film_id = fc.film_id 
join category c on fc.category_id = c.category_id 
where c."name" = 'Action'
```
## 51. Crea una tabla temporal llamada “cliente_rentas_temporal” para almacenar el total de alquileres por cliente.
```
CREATE TEMP TABLE cliente_rentas_temporal AS
SELECT 
    c.customer_id,
    c.email,
    COUNT(r.rental_id) AS total_alquileres
FROM customer c
LEFT JOIN rental r 
ON c.customer_id = r.customer_id
GROUP BY c.customer_id, c.email;
```
## 52. Crea una tabla temporal llamada “peliculas_alquiladas” que almacene las películas que han sido alquiladas al menos 10 veces.
```
CREATE TEMP TABLE peliculas_alquiladas AS
SELECT 
    f.film_id,
    f.title,
    COUNT(r.rental_id) AS total_alquileres
FROM film f
JOIN inventory i 
ON f.film_id = i.film_id
JOIN rental r 
ON i.inventory_id = r.inventory_id
GROUP BY f.film_id, f.title
HAVING COUNT(r.rental_id) >= 10;
```
## 53. Encuentra el título de las películas que han sido alquiladas por el cliente con el nombre ‘Tammy Sanders’ y que aún no se han devuelto. Ordena los resultados alfabéticamente por título de película.
```
SELECT 
    f.title
FROM film f
JOIN inventory i 
ON f.film_id = i.film_id
JOIN rental r 
ON i.inventory_id = r.inventory_id
JOIN customer c 
ON r.customer_id = c.customer_id
WHERE 
    LOWER(CONCAT(c.first_name, ' ', c.last_name)) = 'tammy sanders'
    AND r.return_date IS NULL
ORDER BY f.title ASC;
```
## 54. Encuentra los nombres de los actores que han actuado en al menos una película que pertenece a la categoría ‘Sci-Fi’. Ordena los resultados alfabéticamente por apellido.
```
create temp table sci_films as 
	select f.film_id,
    f.title,
    c.category_id,
    c."name"
	from film f 
	join film_category fc on f.film_id = fc.film_id
	join category c on fc.category_id = c.category_id
	where c."name" = 'Sci-Fi'
```
```
select a.first_name, a.last_name, sf.title, sf.name
from actor a
join film_actor fa on a.actor_id = fa.actor_id 
join sci_films sf on fa.film_id = sf.film_id
order by a.last_name asc
```
## 55. Encuentra el nombre y apellido de los actores que han actuado en películas que se alquilaron después de que la película ‘Spartacus Cheaper’ se alquilara por primera vez. Ordena los resultados alfabéticamente por apellido.
```
SELECT DISTINCT
    a.first_name,
    a.last_name
FROM actor a
JOIN film_actor fa 
ON a.actor_id = fa.actor_id
JOIN inventory i 
ON fa.film_id = i.film_id
JOIN rental r 
ON i.inventory_id = r.inventory_id
WHERE r.rental_date > (
    SELECT MIN(r2.rental_date)
    FROM rental r2
    JOIN inventory i2 
    ON r2.inventory_id = i2.inventory_id
    JOIN film f2 
    ON i2.film_id = f2.film_id
    WHERE f2.title = 'Spartacus Cheaper'
)
ORDER BY a.last_name;
```

## 56. Encuentra el nombre y apellido de los actores que no han actuado en ninguna película de la categoría ‘Music’.
```
SELECT 
    a.first_name,
    a.last_name
FROM actor a
WHERE NOT EXISTS (
    SELECT 1
    FROM film_actor fa
    JOIN film_category fc 
        ON fa.film_id = fc.film_id
    JOIN category c 
        ON fc.category_id = c.category_id
    WHERE fa.actor_id = a.actor_id
      AND c.name = 'Music'
)
ORDER BY a.last_name;
```
## 57. Encuentra el título de todas las películas que fueron alquiladas por más de 8 días.
```
SELECT DISTINCT
    f.title
FROM film f
JOIN inventory i 
ON f.film_id = i.film_id
JOIN rental r 
ON i.inventory_id = r.inventory_id
WHERE 
    r.return_date IS NOT NULL
    AND (r.return_date - r.rental_date) > INTERVAL '8 days';
```
## 58. Encuentra el título de todas las películas que son de la misma categoría que ‘Animation’.
```
select f.title
from film f
join film_category fc on f.film_id = fc.film_id 
join category c on fc.category_id = c.category_id 
where c."name" = 'Animation'
```
## 59. Encuentra los nombres de las películas que tienen la misma duración que la película con el título ‘Dancing Fever’. Ordena los resultados alfabéticamente por título de película.
```
select f.title, f.length 
from film f
where (
	select f.length from film f
	where lower(f.title) = 'dancing fever'
) = f.length 
order by f.title asc
```
## 60. Encuentra los nombres de los clientes que han alquilado al menos 7 películas distintas. Ordena los resultados alfabéticamente por apellido.
```
SELECT 
    c.first_name,
    c.last_name,
    COUNT(DISTINCT i.film_id) AS peliculas_distintas
FROM customer c
JOIN rental r 
    ON c.customer_id = r.customer_id
JOIN inventory i 
    ON r.inventory_id = i.inventory_id
GROUP BY c.customer_id, c.first_name, c.last_name
HAVING COUNT(DISTINCT i.film_id) >= 7
ORDER BY c.last_name ASC;
```
## 61. Encuentra la cantidad total de películas alquiladas por categoría y muestra el nombre de la categoría junto con el recuento de alquileres.
```
CREATE TEMP TABLE film_rents AS 
SELECT 
    f.film_id, 
    COUNT(r.rental_id) AS total_rentas
FROM film f 
JOIN inventory i 
    ON f.film_id = i.film_id 
JOIN rental r 
    ON i.inventory_id = r.inventory_id 
GROUP BY f.film_id;
```
```
SELECT 
    c.name AS categoria, 
    SUM(fr.total_rentas) AS total_alquileres
FROM film_rents fr
JOIN film_category fc 
    ON fr.film_id = fc.film_id 
JOIN category c 
    ON fc.category_id = c.category_id 
GROUP BY c.name
ORDER BY total_alquileres DESC;
```
## 62. Encuentra el número de películas por categoría estrenadas en 2006.
```
select c."name" ,count(f.film_id) 
from film f
join film_category fc on f.film_id = fc.film_id 
join category c on fc.category_id = c.category_id 
where f.release_year = 2006
group by c."name" 
```
## 63. Obtén todas las combinaciones posibles de trabajadores con las tiendas que tenemos.
```
select * from staff s 
cross join store
```
## 64. Encuentra la cantidad total de películas alquiladas por cada cliente y muestra el ID del cliente, su nombre y apellido junto con la cantidad de películas alquiladas.
```
select c.customer_id, c.nombre, c.apellido, count(r.rental_id)
from customer c 
join rental r on c.customer_id = r.customer_id 
group by c.customer_id, c.nombre, c.apellido  
```
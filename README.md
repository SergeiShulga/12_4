#### Домашнее задание к занятию «SQL. Часть 2» - 'Сергей Шульга'

#### Задание 1
Одним запросом получите информацию о магазине, в котором обслуживается более 300 покупателей, и выведите в результат следующую информацию:

фамилия и имя сотрудника из этого магазина;
город нахождения магазина;
количество пользователей, закреплённых в этом магазине.

```
SELECT s.store_id, count(c.customer_id) AS "number of buyers", ci.city, concat(st.last_name, ' ', st.first_name) AS "salesman"
FROM store s
JOIN customer c ON c.store_id = s.store_id
JOIN address a ON a.address_id = s.address_id
JOIN city ci ON ci.city_id = a.city_id
JOIN staff st ON st.store_id = s.store_id
GROUP BY s.store_id, ci.city_id, st.staff_id
HAVING count(c.customer_id) > 300;
```
#### Задание 2
Получите количество фильмов, продолжительность которых больше средней продолжительности всех фильмов.

```
SELECT COUNT(film_id) AS 'number of films'
FROM (SELECT *, AVG(LENGTH) OVER () AS TIME FROM film) t
WHERE TIME < LENGTH;
```
#### Задание 3
Получите информацию, за какой месяц была получена наибольшая сумма платежей, и добавьте информацию по количеству аренд за этот месяц.

```
SELECT YEAR(p.payment_date) AS year, MONTH(p.payment_date) AS month, SUM(p.amount) AS 'total amount', count(p.rental_id) AS 'rentals by month'
FROM payment p
GROUP BY YEAR(p.payment_date), MONTH(p.payment_date)
ORDER BY SUM(p.amount) DESC
LIMIT 1;
```

Дополнительные задания (со звёздочкой*)
Эти задания дополнительные, то есть не обязательные к выполнению, и никак не повлияют на получение вами зачёта по этому домашнему заданию. Вы можете их выполнить, если хотите глубже шире разобраться в материале.

#### Задание 4*
Посчитайте количество продаж, выполненных каждым продавцом. Добавьте вычисляемую колонку «Премия». Если количество продаж превышает 8000, то значение в колонке будет «Да», иначе должно быть значение «Нет».

```
SELECT CONCAT(s.first_name, ' ', s.last_name) AS salesman,
COUNT(p.amount) AS sales,
CASE
	WHEN COUNT(p.amount) > 8000 THEN 'YES'
	WHEN COUNT(p.amount) < 8000 THEN 'NO'
END AS bonus,
SUM(p.amount) AS revenue
FROM payment p
JOIN staff s ON s.staff_id = p.staff_id
WHERE p.amount > 0
GROUP BY salesman
ORDER BY sales DESC;
```

#### Задание 5*
Найдите фильмы, которые ни разу не брали в аренду.

```
SELECT f.title, f.release_year, p.amount, p.payment_date, r.return_date
FROM payment p
JOIN rental r ON r.rental_id = p.rental_id
JOIN inventory i ON i.inventory_id = r.inventory_id
JOIN film f ON f.film_id = i.film_id
WHERE p.amount = 0
ORDER BY f.title;
```

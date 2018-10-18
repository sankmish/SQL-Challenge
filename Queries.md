USE sakila;


-- instruction 1a.
select first_name, last_name from actor;

-- instruction 1b.
SELECT concat(first_name, " ", last_name) AS 'Actor Name'
FROM actor;

-- instruction 2a.
select actor_id, first_name, last_name from actor
WHERE first_name = "Joe";

-- instruction 2b.
SELECT * from actor
where last_name LIKE "%GEN%";

-- instruction 2c.
SELECT last_name, first_name from actor
WHERE last_name LIKE "%LI%"
order by last_name, first_name;

-- instruction 2d
select country_id, country from country
where country IN ("Afghanistan", "Bangladesh", "China");

-- instruction 3a
ALTER TABLE actor
ADD description BLOB(100);

-- instruction 3b
alter table actor
drop description;

-- instruction 4a
select last_name, COUNT(actor_id) as "total number of actors with this last name" from actor
group by last_name;

-- instruction 4b
select last_name, COUNT(actor_id) as "actor_count" from actor
group by last_name
having actor_count >1;

-- instruction 4c
select * from actor
where last_name = "WILLIAMS" and first_name = "GROUCHO";

update actor
set first_name = "HARPO"
where last_name = "WILLIAMS" and first_name = "GROUCHO";

-- instruction 4d
-- note that in the previous instruction, I checked to make sure that there would be only one HARPO WILLIAMS if I changed the name
update actor
set first_name = "GROUCHO"
where last_name = "WILLIAMS" and first_name = "HARPO";

-- instruction 5a
show create table address;
-- at this point I can copy/paste the result and use the "create schema" command

-- instruction 6a
select first_name, last_name, address.address
from staff
inner join address on staff.address_id = address.address_id;

-- instruction 6b
select staff.staff_id, staff.first_name, staff.last_name, SUM(payment.amount) as "total amount rung"
from staff
left join payment on payment.staff_id = staff.staff_id
group by staff.staff_id;

-- instruction 6c
select title, COUNT(film_actor.film_id) as "number of actors"
from film
inner join film_actor on film_actor.film_id = film.film_id
group by title;

-- instruction 6d
select COUNT(film_id) as "number of copies" from inventory
where film_id in
(select film_id from film 
where title = "Hunchback Impossible")
group by inventory.film_id;

-- instruction 6e
select first_name, last_name, SUM(payment.amount) as "Total Amount Paid"
from customer
inner join payment on payment.customer_id = customer.customer_id
group by customer.customer_id
order by customer.last_name;

-- instruction 7a
select title from film
where language_id in
(select language_id from language
where language_id = "1")
AND (title LIKE "K%" or title LIKE "Q%");

-- instruction 7b
select first_name, last_name from actor
where actor_id in
(select actor_id from film_actor
where film_id in
(select film_id from film
where title = "Alone Trip")
);

-- instruction 7c
-- in reverse order: country = "Canada" (country_id), select city_id city in country_id, select address, address_id in city_id, select first_name, last_name customer in customer_id
select cust.first_name, cust.last_name, a.address, a.district, ci.city, a.postal_code from customer as cust
inner join address as a on a.address_id = cust.address_id
inner join city as ci on ci.city_id = a.city_id
inner join country as c on c.country_id = ci.country_id
where c.country = "Canada";

-- instruction 7d
-- in reverse order: category = "Family", category_id join to film_category, film_id join to film (show title)
select f.title from film as f
inner join film_category as fc on fc.film_id = f.film_id
inner join category as c on c.category_id = fc.category_id
where c.name = "Family";

-- instruction 7e
-- in reverse order: count on rental_id in rental table, join to inventory_id (inventory), join to film_id (inventory to film)
select f.title, COUNT(r.rental_id) as "Number of rentals"
from film as f
inner join inventory as i on i.film_id = f.film_id
inner join rental as r on r.inventory_id = i.inventory_id
group by f.title
order by 2 desc;

-- instruction 7f
select staff.store_id, SUM(payment.amount) as "Total Business by Store"
from staff
inner join payment on payment.staff_id = staff.staff_id
group by staff.staff_id;

-- instruction 7g
-- join store to address, then to city and country. get city from city and country from country
select store.store_id, ci.city, c.country from store
inner join address as a on a.address_id = store.address_id
inner join city as ci on ci.city_id = a.city_id
inner join country as c on c.country_id = ci.country_id;

-- instruction 7h
select c.name, SUM(p.amount) as "Gross Revenues" from category as c
inner join film_category as fc on fc.category_id = c.category_id
inner join film as f on fc.film_id = f.film_id
inner join inventory as i on i.film_id = f.film_id
inner join rental as r on r.inventory_id = i.inventory_id
inner join payment as p on p.rental_id = r.rental_id
group by c.name
order by 2 desc
limit 5;

-- instruction 8a
create view top_five_genres as
select c.name, SUM(p.amount) as "Gross Revenues" from category as c
inner join film_category as fc on fc.category_id = c.category_id
inner join film as f on fc.film_id = f.film_id
inner join inventory as i on i.film_id = f.film_id
inner join rental as r on r.inventory_id = i.inventory_id
inner join payment as p on p.rental_id = r.rental_id
group by c.name
order by 2 desc
limit 5;

-- instruction 8b
select * from top_five_genres;

-- instruction 8c
drop view top_five_genres;
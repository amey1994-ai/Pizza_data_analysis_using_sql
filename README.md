🍕 Pizza Hut Sales & Revenue Analysis using SQL

create database pizzahut;


USE pizzahut;


SELECT 
    *
FROM
    pizzahut.pizzas;

SELECT 
    *
FROM
    pizzahut.pizza_types;

CREATE TABLE orders (
    order_id INT NOT NULL,
    order_date DATE NOT NULL,
    order_time TIME NOT NULL,
    PRIMARY KEY (order_id)
);

CREATE TABLE orders (
    order_id INT NOT NULL,
    order_date DATE NOT NULL,
    order_time TIME NOT NULL,
    PRIMARY KEY (order_id)
);


SELECT 
    *
FROM
    pizzahut.orders;

CREATE TABLE orders_details (
    order_details_id INT NOT NULL,
    order_id INT NOT NULL,
    pizza_id TEXT NOT NULL,
    quantity INT NOT NULL,
    PRIMARY KEY (order_details_id)
);
SELECT 
    orders_details
FROM
    pizzahut;

SELECT 
    *
FROM
    pizzahut.orders_details;
    


-- Revenue per pizza type:



SELECT 
    pizza_types.category,
    SUM(pizzas.price * orders_details.quantity)
FROM
    pizza_types
        JOIN
    pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
        JOIN
    orders_details ON pizzas.pizza_id = orders_details.pizza_id
GROUP BY pizza_types.category
ORDER BY SUM(pizzas.price * orders_details.quantity) DESC;


-- Revenue per pizza type:

SELECT 
    pizza_types.name,
    SUM(orders_details.quantity * pizzas.price) AS revenue
FROM
    pizza_types
        JOIN
    pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
        JOIN
    orders_details ON pizzas.pizza_id = orders_details.pizza_id
GROUP BY pizza_types.name
ORDER BY revenue DESC;

-- Top 3 most popular pizza categories:

SELECT 
    pizza_types.category,
    SUM(orders_details.quantity) AS total_ordered
FROM
    pizza_types
        JOIN
    pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
        JOIN
    orders_details ON pizzas.pizza_id = orders_details.pizza_id
GROUP BY pizza_types.category
ORDER BY total_ordered DESC
LIMIT 3;

-- Least popular pizza type:

SELECT 
    pizza_types.name,
    SUM(orders_details.quantity) AS total_ordered
FROM
    pizza_types
        JOIN
    pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
        JOIN
    orders_details ON pizzas.pizza_id = orders_details.pizza_id
GROUP BY pizza_types.name
ORDER BY total_ordered ASC
LIMIT 1;


-- . Find the details of orders where the quantity ordered is greater than 5.
SELECT 
    orders.order_id, pizzas.name, orders_details.quantity
FROM
    orders
        JOIN
    orders_details ON orders.order_id = orders_details.order_id
        JOIN
    pizzas ON orders_details.pizza_id = pizzas.pizza_id
WHERE
    orders_details.quantity > 5;








-- Retrieve all orders with the names of the pizzas ordered.

SELECT 
    pizza_types.name,
    orders.order_time,
    orders_details.pizza_id,
    SUM(orders_details.quantity) AS total_order
FROM
    orders
        INNER JOIN
    orders_details ON orders_details.order_id = orders.order_id
        INNER JOIN
    pizzas ON pizzas.pizza_id = orders_details.pizza_id
        INNER JOIN
    pizza_types ON pizzas.pizza_type_id = pizza_types.pizza_type_id
WHERE
    HOUR(orders.order_time) = 12
GROUP BY orders_details.pizza_id , pizza_types.name , orders.order_time
ORDER BY total_order DESC
LIMIT 1;


 -- Display all pizzas along with their categories.


SELECT DISTINCT
    category, pizza_types.name, pizzas.price
FROM
    pizza_types
        INNER JOIN
    pizzas ON pizzas.pizza_type_id = pizza_types.pizza_type_id
ORDER BY pizzas.price DESC;

SELECT 
    pizza_types.category, pizzas.name
FROM
    pizza_types
        INNER JOIN
    pizzas ON pizzas.pizza_type_id = pizza_types.pizza_type_id
;

--  List all pizzas and their prices along with the pizza type names.

SELECT 
    pizza_types.name, pizza_types.category, pizzas.price
FROM
    pizzas
        INNER JOIN
    pizza_types ON pizza_types.pizza_type_id = pizzas.pizza_type_id;



--  Show the total quantity ordered for each pizza.

SELECT 
    pizza_types.name AS pizza_name,
    orders_details.quantity AS qty
FROM
    orders_details
        INNER JOIN
    pizzas ON pizzas.pizza_id = orders_details.pizza_id
        INNER JOIN
    pizza_types ON pizza_types.pizza_type_id = pizzas.pizza_type_id
GROUP BY pizza_name , qty
ORDER BY qty DESC;

--  Retrieve all orders along with the order time and pizza names.

SELECT 
    pizza_types.name, orders_details.order_id, orders.order_time
FROM
    orders
        INNER JOIN
    orders_details ON orders.order_id = orders_details.order_id
        INNER JOIN
    pizzas ON pizzas.pizza_id = orders_details.pizza_id
        INNER JOIN
    pizza_types ON pizza_types.pizza_type_id = pizzas.pizza_type_id
;

--  Find the details of orders where the quantity ordered is greater than 5.

SELECT 
    pizza_types.name,
    orders_details.order_details_id,
    orders_details.order_id,
    orders_details.pizza_id,
    orders_details.quantity AS Quantity
FROM
    orders_details
        INNER JOIN
    pizzas ON pizzas.pizza_id = orders_details.pizza_id
        INNER JOIN
    pizza_types ON pizza_types.pizza_type_id = pizzas.pizza_type_id
WHERE
    Quantity > 3;
    
 -- Display the total revenue generated for each pizza using 

SELECT 
    pizza_types.name,
    SUM(orders_details.quantity * pizzas.price) AS total_revenue
FROM
    orders_details
        INNER JOIN
    pizzas ON pizzas.pizza_id = orders_details.pizza_id
        INNER JOIN
    pizza_types ON pizza_types.pizza_type_id = pizzas.pizza_type_id
GROUP BY pizza_types.name;

-- List all orders with the size of the pizzas included in the order.

SELECT 
    orders.order_id, pizzas.size
FROM
    orders
        JOIN
    orders_details ON orders.order_id = orders_details.order_id
        JOIN
    pizzas ON orders_details.pizza_id = pizzas.pizza_id;



-- Show all orders placed along with the category of pizzas.
SELECT 
    orders.order_id, pizza_types.category
FROM
    orders
        JOIN
    orders_details ON orders.order_id = orders_details.order_id
        JOIN
    pizzas ON orders_details.pizza_id = pizzas.pizza_id
        JOIN
    pizza_types ON pizzas.pizza_type_id = pizza_types.pizza_type_id;


--  Retrieve the pizza type with the highest total revenue

SELECT 
    orders.order_id,
    SUM(orders_details.quantity * pizzas.price) AS total_order_revenue
FROM
    orders
        JOIN
    orders_details ON orders.order_id = orders_details.order_id
        JOIN
    pizzas ON orders_details.pizza_id = pizzas.pizza_id
GROUP BY orders.order_id
HAVING SUM(orders_details.quantity * pizzas.price) > (SELECT 
        AVG(total_revenue)
    FROM
        (SELECT 
            SUM(orders_details.quantity * pizzas.price) AS total_revenue
        FROM
            orders
        JOIN orders_details ON orders.order_id = orders_details.order_id
        JOIN pizzas ON orders_details.pizza_id = pizzas.pizza_id
        GROUP BY orders.order_id) AS avg_revenue);
        
        

--  Find the most popular pizza category by revenue

SELECT 
    total_revenue.name, total_revenue.revenue
FROM
    (SELECT 
        pizza_types.name AS name,
            SUM(orders_details.quantity * pizzas.price) AS revenue
    FROM
        orders_details
    INNER JOIN pizzas ON pizzas.pizza_id = orders_details.pizza_id
    INNER JOIN pizza_types ON pizza_types.pizza_type_id = pizzas.pizza_type_id
    GROUP BY name) AS total_revenue
WHERE
    total_revenue.revenue = (SELECT 
            MAX(revenue)
        FROM
            (SELECT 
                pizza_types.name AS name,
                    SUM(orders_details.quantity * pizzas.price) AS revenue
            FROM
                orders_details
            INNER JOIN pizzas ON pizzas.pizza_id = orders_details.pizza_id
            INNER JOIN pizza_types ON pizza_types.pizza_type_id = pizzas.pizza_type_id
            GROUP BY name) AS subquery)
;
-- Find the order with the highest total revenue  


SELECT 
    total_order.order_number, total_order.revenue
FROM
    (SELECT 
        orders_details.order_id AS order_number,
            SUM(orders_details.quantity * pizzas.price) AS revenue
    FROM
        orders_details
    INNER JOIN pizzas ON pizzas.pizza_id = orders_details.pizza_id
    INNER JOIN pizza_types ON pizza_types.pizza_type_id = pizzas.pizza_type_id
    GROUP BY order_number) AS total_order
WHERE
    total_order.revenue = (SELECT 
            MAX(revenue)
        FROM
            (SELECT 
                orders_details.order_id AS order_number,
                    SUM(orders_details.quantity * pizzas.price) AS revenue
            FROM
                orders_details
            INNER JOIN pizzas ON pizzas.pizza_id = orders_details.pizza_id
            INNER JOIN pizza_types ON pizza_types.pizza_type_id = pizzas.pizza_type_id
            GROUP BY order_number) AS subquery)
;


-- Find pizza sizes with below-average revenue


SELECT 
    total_revenue.pizza_size, total_revenue.revenue
FROM
    (SELECT 
        pizzas.size AS pizza_size,
            SUM(orders_details.quantity * pizzas.price) AS revenue
    FROM
        orders_details
    INNER JOIN pizzas ON orders_details.pizza_id = pizzas.pizza_id
    GROUP BY pizza_size) AS total_revenue
WHERE
    total_revenue.revenue < (SELECT 
            AVG(revenue)
        FROM
            (SELECT 
                pizzas.size AS pizza_size,
                    SUM(orders_details.quantity * pizzas.price) AS revenue
            FROM
                orders_details
            INNER JOIN pizzas ON orders_details.pizza_id = pizzas.pizza_id
            GROUP BY pizza_size) AS subquery);


-- Store Procedures


-- Revenue per pizza type id (Stored procudures)

call Pizza_title_vise_revenue('bbq_ckn');

-- Revenue per containing ingredient (Stored procudures)


call Ingredient_vise_revenue('onion');
call Ingredient_vise_revenue('chicken');


-- Which day generated the highest total revenue?

SELECT 
    date AS highest_total_revenue
FROM
    (SELECT 
        orders.order_date AS date,
            SUM(pizzas.price * orders_details.quantity) AS orderxpizza
    FROM
        orders
    JOIN orders_details ON orders.order_id = orders_details.order_id
    JOIN pizzas ON orders_details.pizza_id = pizzas.pizza_id
    JOIN pizza_types ON pizza_types.pizza_type_id = pizzas.pizza_type_id
    GROUP BY date
    ORDER BY orderxpizza DESC
    LIMIT 1) AS subquery;




























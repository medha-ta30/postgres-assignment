ERROR:  null value in column "user_id" of relation "orders" violates not-null constraint
DETAIL:  Failing row contains (1, null, 2026-07-03 17:00:41.811477).
comm_db=# SELECT * FROM orders;
 order_id | user_id |         order_date         
----------+---------+----------------------------
        4 |       1 | 2026-07-03 16:54:41.907424
        5 |       2 | 2026-07-03 16:54:41.907424
        6 |       1 | 2026-07-03 16:54:41.907424
(3 rows)

comm_db=# INSERT INTO order_items (order_id, product_id, quantity)VALUES
(4,1,1),
(4,2,2),
(5,3,1),
(6,2,1),
(6,3,2);
INSERT 0 5
comm_db=# SELECT
comm_db-# orders.order_id,
comm_db-# users.name,
comm_db-# orders.order_date
comm_db-# FROM orders
comm_db-# JOIN users
comm_db-# ON orders.user_id = users.user_id;
 order_id | name  |         order_date         
----------+-------+----------------------------
        4 | medha | 2026-07-03 16:54:41.907424
        5 | omar  | 2026-07-03 16:54:41.907424
        6 | medha | 2026-07-03 16:54:41.907424
(3 rows)

comm_db=# SELECT
comm_db-# orders.order_id,
comm_db-# products.product_name,
comm_db-# order_items.quantity
comm_db-# FROM order_items JOIN orders
comm_db-# ON order_items.order_id = orders.order_id
comm_db-# JOIN products
comm_db-# ON order_items.product_id = products.product_id;
 order_id | product_name | quantity 
----------+--------------+----------
        4 | shoes        |        1
        4 | laptop       |        2
        5 | bulb         |        1
        6 | laptop       |        1
        6 | bulb         |        2
(5 rows)

comm_db=# SELECT users.name, COUNT(orders.order_id) AS total_orders
comm_db-# FROM users
comm_db-# LEFT JOIN orders
comm_db-# ON users.users_id = orders.user_id
comm_db-# GROUP BY users.user_id, users.name;
ERROR:  column users.users_id does not exist
LINE 4: ON users.users_id = orders.user_id
           ^
HINT:  Perhaps you meant to reference the column "users.user_id".
comm_db=# SELECT
    users.name,
    COUNT(orders.order_id) AS total_orders
FROM users
LEFT JOIN orders
ON users.user_id = orders.user_id
GROUP BY users.user_id, users.name;
 name  | total_orders 
-------+--------------
 omar  |            1
 medha |            2
(2 rows)

comm_db=# SELECT * FROM users;
 user_id | name  |       email       | password 
---------+-------+-------------------+----------
       1 | medha | medha@example.com | pass123
       2 | omar  | omar@example.com  | pass456
(2 rows)

comm_db=# SELECT * FROM products;
 product_id | product_name |  price   | stock 
------------+--------------+----------+-------
          1 | shoes        | 10000.00 |    25
          2 | laptop       | 70000.00 |    50
          3 | bulb         |   500.00 |   100
(3 rows)

comm_db=# SELECT * FROM orders;
 order_id | user_id |         order_date         
----------+---------+----------------------------
        4 |       1 | 2026-07-03 16:54:41.907424
        5 |       2 | 2026-07-03 16:54:41.907424
        6 |       1 | 2026-07-03 16:54:41.907424
(3 rows)

comm_db=# SELECT * FROM order_items;
 order_item_id | order_id | product_id | quantity 
---------------+----------+------------+----------
            18 |        4 |          1 |        1
            19 |        4 |          2 |        2
            20 |        5 |          3 |        1
            21 |        6 |          2 |        1
            22 |        6 |          3 |        2
(5 rows)

comm_db=# 

medhabhardwaj@TA-MM-013 ~ % psql postgres
psql (15.18 (Homebrew))
Type "help" for help.

postgres=# CREATE DATABASE comm-db;
ERROR:  syntax error at or near "-"
LINE 1: CREATE DATABASE comm-db;
                            ^
postgres=# CREATE DATABASE comm_db;
CREATE DATABASE
postgres=# \c comm_db
You are now connected to database "comm_db" as user "medhabhardwaj".
comm_db=# CREATE TABLE users (
comm_db(# user_id SERIAL PRIMARY KEY,
comm_db(# name VARCHAR(100) NOT NULL,
comm_db(# email VARCHAR(100) UNIQUE NOT NULL,
comm_db(# password VARCHAR(255) NOT NULL);
CREATE TABLE
comm_db=# CREATE TABLE products (
comm_db(# product_id SERIAL PRIMARY KEY,
comm_db(# product_name VARCHAR(100) NOT NULL,
comm_db(# price DECIMAL(10,2) NOT NULL, 
comm_db(# stock INT NOT NULL);
CREATE TABLE
comm_db=# CREATE TABLE orders (
comm_db(# order_id SERIAL PRIMARY KEY,
comm_db(# user_id INT NOT NULL,
comm_db(# order_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
comm_db(# FOREIGN KEY (user_id) REFERENCES users(user_id));
CREATE TABLE
comm_db=# CREATE TABLE order_items (
comm_db(# order_item_id SERIAL PRIMARY KEY,
comm_db(# order_id INT NOT NULL,
comm_db(# product_id INT NOT NULL,
comm_db(# quantity INT NOT NULL,
comm_db(# FOREIGN KEY (order_id) REFERENCES orders(order_id),
comm_db(# FOREIGN KEY (product_id) REFERENCES products(product_id));
CREATE TABLE
comm_db=# \DT
invalid command \DT
Try \? for help.
comm_db=# \dt
              List of relations
 Schema |    Name     | Type  |     Owner     
--------+-------------+-------+---------------
 public | order_items | table | medhabhardwaj
 public | orders      | table | medhabhardwaj
 public | products    | table | medhabhardwaj
 public | users       | table | medhabhardwaj
(4 rows)

comm_db=# INSERT INTO users (name, email, password) 
comm_db-# VALUES
comm_db-# ('medha', 'medha@example.com', 'pass123'),
comm_db-# ('omar', 'omar@example.com', ,pass456,),
comm_db-# ('aditya', aditya@example.com, 'pass789');
ERROR:  syntax error at or near ","
LINE 4: ('omar', 'omar@example.com', ,pass456,),
                                     ^
comm_db=# INSERT INTO users (name, email, password) 
VALUES
('medha', 'medha@example.com', 'pass123'),
('omar', 'omar@example.com', 'pass456'),
('aditya', aditya@example.com, 'pass789');
ERROR:  column "aditya" does not exist
LINE 5: ('aditya', aditya@example.com, 'pass789');
                   ^
comm_db=# INSERT INTO users (name, email, password) 
VALUES
('medha', 'medha@example.com', 'pass123'),
('omar', 'omar@example.com', 'pass456');
INSERT 0 2
comm_db=# INSERT INTO products (product_name, price, stock)
comm_db-# VALUES
comm_db-# ('shoes', 10000, 25),
comm_db-# ('laptop', 70000, 50),
comm_db-# ('bulb', 500, 100);
INSERT 0 3
comm_db=# INSERT INTO orders (user_id)
comm_db-# VALUES
comm_db-# (1),
comm_db-# (2),
comm_db-# (3);
ERROR:  insert or update on table "orders" violates foreign key constraint "orders_user_id_fkey"
DETAIL:  Key (user_id)=(3) is not present in table "users".
comm_db=# INSERT INTO orders (user_id)
VALUES
(1),
(2),
(1);
INSERT 0 3
comm_db=# INSERT INTO order_items (order_id, product_id, quantity)
comm_db-# VALUES
comm_db-# (1,1,1),
comm_db-# (1,2,1),
comm_db-# (2,2,1),
comm_db-# (3,2,1),
comm_db-# (2,3,1),
comm_db-# (3,2,2);
ERROR:  insert or update on table "order_items" violates foreign key constraint "order_items_order_id_fkey"
DETAIL:  Key (order_id)=(1) is not present in table "orders".
comm_db=# INSERT INTO order_items (order_id, product_id, quantity)
VALUES
(1,1,1),
(1,2,1),
(2,2,1),
(1,2,1),
(2,3,1),
(2,2,2);
ERROR:  insert or update on table "order_items" violates foreign key constraint "order_items_order_id_fkey"
DETAIL:  Key (order_id)=(1) is not present in table "orders".
comm_db=# INSERT INTO order_items (order_id, product_id, quantity)
VALUES
(1,1,1),
(1,2,2),
(2,3,1),
(3,2,1),
(3,3,2);
ERROR:  insert or update on table "order_items" violates foreign key constraint "order_items_order_id_fkey"
DETAIL:  Key (order_id)=(1) is not present in table "orders".
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

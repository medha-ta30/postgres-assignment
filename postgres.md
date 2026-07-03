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
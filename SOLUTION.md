#SOLUTION#

##Part 1: Setting Up the Database Environment##
user@user-ThinkPad-L15-Gen-1:~$ sudo systemctl status postgresql
[sudo] password for user:
● postgresql.service - PostgreSQL RDBMS
Loaded: loaded (/lib/systemd/system/postgresql.service; enabled; vendor preset: enabled)
Active: active (exited) since Thu 2024-02-22 09:51:24 CET; 4h 55min ago
Main PID: 123408 (code=exited, status=0/SUCCESS)
Tasks: 0 (limit: 18681)
Memory: 0B
CGroup: /system.slice/postgresql.service

Feb 22 09:51:24 user-ThinkPad-L15-Gen-1 systemd[1]: Starting PostgreSQL RDBMS...
Feb 22 09:51:24 user-ThinkPad-L15-Gen-1 systemd[1]: Finished PostgreSQL RDBMS.
user@user-ThinkPad-L15-Gen-1:~$ sudo -i -u postgres
postgres@user-ThinkPad-L15-Gen-1:~$ psql
psql (16.2 (Ubuntu 16.2-1.pgdg20.04+1))
Type "help" for help.
![Setting Up the Database Environment](/home/user/Desktop/JavaClasses/feb22_PostgreSQLexample/image/img1.png)

postgres=# CREATE DATABASE DB_Navigator;
CREATE DATABASE
postgres=# /c DB_Navigator
postgres-# CREATE SCHEMA RoutesSchema;
ERROR:  syntax error at or near "/"
LINE 1: /c DB_Navigator
^
postgres=# CREATE SCHEMA RoutesSchema;
CREATE SCHEMA
postgres=# CREATE SCHEMA BookingSchema;
CREATE SCHEMA
postgres=# /dt
postgres-# ^C
postgres=# /dn
postgres-# \dt
Did not find any relations.
postgres-# \dn
List of schemas
Name      |       Owner       
---------------+-------------------
bookingschema | postgres
public        | pg_database_owner
routesschema  | postgres
(3 rows)

postgres-# SELECT current_schema();
ERROR:  syntax error at or near "/"
LINE 1: /dn
^
postgres=# SELECT current_schema();
current_schema
----------------
public
(1 row)

postgres=# SET Public TO RoutesSchema;
ERROR:  unrecognized configuration parameter "public"
postgres=# SET search_path TO RoutesSchema;
SET
postgres=# SELECT current_schema();
current_schema
----------------
routesschema
(1 row)

postgres=# CREATE TABLE routesschema.trains(TRAIN_ID INT PRIMARY KEY NOT NULL, TRAIN_NAME VARCHAR(255) NOT NULL, TRAIN_TYPE VARCHAR(255) NOT NULL);
CREATE TABLE
postgres=# CREATE TABLE routesschema.routes(ROUTE_ID INT PRIMARY KEY NOT NULL, DEPARTURE_STATION VARCHAR(255), ARRIVAL_STATION VARCHAR(255), DEPARTURE_TIME TIMESTAMP, ARRIVAL_TIME TIMESTAMP);
CREATE TABLE
postgres=# SELECT current_schema();
current_schema
----------------
routesschema
(1 row)

postgres=# SET search_path TO bookingschema;
SET
postgres=# SELECT current_schema();
current_schema
----------------
bookingschema
(1 row)

##Part 2: Creating Tables and (NOT) Relationships##
![Creating Tables and Relationships](/home/user/Desktop/JavaClasses/feb22_PostgreSQLexample/image/img2.png)
postgres=# CREATE TABLE bookingschema.customers(CUSTOMER_ID INT PRIMARY KEY NOT NULL, CUSTOMER_NAME VARCHAR(255), CUSTOMER_EMAIL VARCHAR(255), PHONE_NUMBER VARCHAR(30);
postgres(# ^C
postgres=# CREATE TABLE bookingschema.customers(CUSTOMER_ID INT PRIMARY KEY NOT NULL, CUSTOMER_NAME VARCHAR(255), CUSTOMER_EMAIL VARCHAR(255), PHONE_NUMBER VARCHAR(30));
CREATE TABLE
postgres=# CREATE TABLE bookingschema.tickets(TICKET_ID INT PRIMARY KEY NOT NULL, BOOKING_DATE TIMESTAMP, PRICE DECIMAL(10, 2));
CREATE TABLE
postgres=# CREATE TABLE bookingschema.deals(DEAL_ID INT KEY NOT NULL, DESCRIPTION VARCHAR(255), DISCOUNT_PERCENTAGE REAL, VALIDITY_PERIOD TIMESTAMP);
ERROR:  syntax error at or near "KEY"
LINE 1: CREATE TABLE bookingschema.deals(DEAL_ID INT KEY NOT NULL, D...
^
postgres=# CREATE TABLE bookingschema.deals(DEAL_ID INT PRIMARY KEY NOT NULL, DESCRIPTION VARCHAR(255), DISCOUNT_PERCENTAGE REAL, VALIDITY_PERIOD TIMESTAMP);
CREATE TABLE
postgres=# SELECT current_schema();
current_schema
----------------
bookingschema
(1 row)
##Part 3: Inserting Sample Data##
![Inserting Sample Data](/home/user/Desktop/JavaClasses/feb22_PostgreSQLexample/image/img3.png)
postgres=# INSERT INTO bookingschema.customers VALUES (1, '(1, 'John Doe', 'john.doe@example.com', '+1234567890'),
postgres'#   (2, 'Jane Smith', 'jane.smith@example.com', '+9876543210'),
postgres'#   (3, 'Alice Johnson', 'alice.johnson@example.com', '+1122334455')^Z
[1]+  Stopped                 psql
postgres@user-ThinkPad-L15-Gen-1:~$ psql
psql (16.2 (Ubuntu 16.2-1.pgdg20.04+1))
Type "help" for help.

postgres=# SELECT current_schema();
current_schema
----------------
public
(1 row)

postgres=# SET search_path to bookingschema;
SET
postgres=# SELECT current_schema();
current_schema
----------------
bookingschema
(1 row)

postgres=# INSERT INTO bookingschema.customers VALUES (1, 'John Doe', 'john.doe@example.com', '+1234567890'),
postgres-#   (2, 'Jane Smith', 'jane.smith@example.com', '+9876543210'),
postgres-#   (3, 'Alice Johnson', 'alice.johnson@example.com', '+1122334455');
INSERT 0 3
postgres=# INSERT INTO bookingschema.customers VALUES (4, 'Sebastian Becker', 'sebastian.becker@example.com', '+1122334455'),
postgres-#   (5, 'Anna Weber', 'anna.weber@example.com', '+9876543210');
INSERT 0 2
postgres=# INERT INTO bo

postgres=# INSERT INTO bookingschema.tickets VALUES (1, '2024-02-23 10:30:00', 40.25),
postgres-#   (2, '2024-02-23 13:15:00', 55.00),
postgres-#   (3, '2024-02-23 16:00:00', 48.75),
postgres-#   (4, '2024-02-23 09:45:00', 37.50),
postgres-#   (5, '2024-02-23 12:30:00', 42.80);
INSERT 0 5
postgres=# INSERT INTO bookingschema.deals VALUES (1, 'Weekday Special', 12.0, '2024-03-10 00:00:00'),
postgres-#   (2, 'Senior Citizen Discount', 8.5, '2024-03-15 00:00:00'),
postgres-#   (3, 'Group Discount', 15.0, '2024-02-28 23:59:59'),
postgres-#   (4, 'Student Offer', 20.0, '2024-03-20 00:00:00'),
postgres-#   (5, 'Spring Sale', 18.75, '2024-04-01 00:00:00');
INSERT 0 5
postgres=# SET search_path TO routesschema;
SET
postgres=# INSERT INTO routesschema.trains VALUES (1, 'EC 789', 'EuroCity'),
postgres-#   (2, 'RE 101', 'Regional Express'),
postgres-#   (3, 'ICE 234', 'High-Speed'),
postgres-#   (4, 'IC 567', 'InterCity'),
postgres-#   (5, 'RB 890', 'Regional');
INSERT 0 5
postgres=# INSERT INTO routesschema.routes VALUES (1, 'Cologne', 'Berlin', '2024-02-23 09:30:00', '2024-02-23 13:45:00'),
postgres-#   (2, 'Munich', 'Hamburg', '2024-02-23 12:15:00', '2024-02-23 16:30:00'),
postgres-#   (3, 'Dresden', 'Frankfurt', '2024-02-23 15:45:00', '2024-02-23 19:00:00'),
postgres-#   (4, 'Berlin', 'Hamburg', '2024-02-23 08:45:00', '2024-02-23 12:00:00'),
postgres-#   (5, 'Munich', 'Frankfurt', '2024-02-23 11:30:00', '2024-02-23 15:45:00');
INSERT 0 5

##Part 4: SQL Operations##
### 1- Query All Available Routes:###
![Query All Available Routes](/home/user/Desktop/JavaClasses/feb22_PostgreSQLexample/image/img4.png)
postgres=# SELECT * FROM ROUTES;
route_id | departure_station | arrival_station |   departure_time    |    arrival_time     
----------+-------------------+-----------------+---------------------+---------------------
1 | Cologne           | Berlin          | 2024-02-23 09:30:00 | 2024-02-23 13:45:00
2 | Munich            | Hamburg         | 2024-02-23 12:15:00 | 2024-02-23 16:30:00
3 | Dresden           | Frankfurt       | 2024-02-23 15:45:00 | 2024-02-23 19:00:00
4 | Berlin            | Hamburg         | 2024-02-23 08:45:00 | 2024-02-23 12:00:00
5 | Munich            | Frankfurt       | 2024-02-23 11:30:00 | 2024-02-23 15:45:00
(5 rows)

postgres=# SELECT * FROM routesschema.routes;
route_id | departure_station | arrival_station |   departure_time    |    arrival_time     
----------+-------------------+-----------------+---------------------+---------------------
1 | Cologne           | Berlin          | 2024-02-23 09:30:00 | 2024-02-23 13:45:00
2 | Munich            | Hamburg         | 2024-02-23 12:15:00 | 2024-02-23 16:30:00
3 | Dresden           | Frankfurt       | 2024-02-23 15:45:00 | 2024-02-23 19:00:00
4 | Berlin            | Hamburg         | 2024-02-23 08:45:00 | 2024-02-23 12:00:00
5 | Munich            | Frankfurt       | 2024-02-23 11:30:00 | 2024-02-23 15:45:00
(5 rows)

### 2- Find Tickets for a Specific Route:### (Specific route id = 5)
![Find Tickets for a Specific Route](/home/user/Desktop/JavaClasses/feb22_PostgreSQLexample/image/img5.png)
postgres=# SELECT * FROM routesschema.routes WHERE route_id = 5;
route_id | departure_station | arrival_station |   departure_time    |    arrival_time     
----------+-------------------+-----------------+---------------------+---------------------
5 | Munich            | Frankfurt       | 2024-02-23 11:30:00 | 2024-02-23 15:45:00
(1 row)


postgres=# SELECT * FROM bookingschema.tickets WHERE route_id = 5;
ERROR:  column "route_id" does not exist
LINE 1: SELECT * FROM bookingschema.tickets WHERE route_id = 5;
^

### 3- Apply a Deal to Tickets:###
postgres=# UPDATE bookingschema.tickets SET DEAL_ID = 1 WHERE BOOKING_DATE = '2024-02-01' AND BOOKING_DATE = '2024-02-29';
ERROR:  column "deal_id" of relation "tickets" does not exist
LINE 1: UPDATE bookingschema.tickets SET DEAL_ID = 1 WHERE BOOKING_D...
^
postgres=# UPDATE bookingschema.tickets SET TICKET_ID = 1 WHERE BOOKING__DATE = '2024-02-01' AND BOOKING_DATE = '2024-02-29';
ERROR:  column "booking__date" does not exist
LINE 1: ...ATE bookingschema.tickets SET TICKET_ID = 1 WHERE BOOKING__D...
^
HINT:  Perhaps you meant to reference the column "tickets.booking_date".
postgres=# UPDATE bookingschema.tickets SET TICKET_ID = 1 WHERE BOOKING_DATE = '2024-02-01' AND BOOKING_DATE = '2024-02-29';
UPDATE 0

### 4- List All Deals Valid in the Next Month:###
postgres=# SEARCH * FROM bookingschema.deals WHERE VALIDITY_PERIOD > '2024-03-01' AND VALIDITY_PERIOD < '2024-04-01'
postgres-# SEARCH * FROM bookingschema.deals WHERE VALIDITY_PERIOD > '2024-03-01' AND VALIDITY_PERIOD < '2024-04-01';
ERROR:  syntax error at or near "SEARCH"
LINE 1: SEARCH * FROM bookingschema.deals WHERE VALIDITY_PERIOD > '2...
^
postgres=# SELECT * FROM bookingschema.deals WHERE VALIDITY_PERIOD > '2024-03-01' AND VALIDITY_PERIOD < '2024-04-01';
deal_id |       description       | discount_percentage |   validity_period   
---------+-------------------------+---------------------+---------------------
1 | Weekday Special         |                  12 | 2024-03-10 00:00:00
2 | Senior Citizen Discount |                 8.5 | 2024-03-15 00:00:00
4 | Student Offer           |                  20 | 2024-03-20 00:00:00
(3 rows)

### 5- Delete Expired Deals:###
postgres=# DELETE FROM bookingschema.deals WHERE VALIDITY_PERIOD < '2024-02-24';
DELETE 0
postgres=#
![3-4-5 Apply a Deal to Tickets/List All Deals Valid in the Next Month/Delete Expired Deals](/home/user/Desktop/JavaClasses/feb22_PostgreSQLexample/image/img6.png)

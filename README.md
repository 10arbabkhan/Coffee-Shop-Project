# Coffee-Shop-Project
## Overview
This project implements a relational database to manage coffee shop operations, focusing on employee management, shop locations, and supplier relationships. The database is designed to support efficient tracking of resources and operations.

## Problem Statement
Coffee shop franchises often face challenges in managing:
1. Employee records across multiple branches.
2. Supplier relationships and the types of coffee they provide.
3. Accurate tracking of shop locations and their hierarchical links to cities and countries.
This project addresses these challenges by creating a relational schema that integrates these aspects, ensuring data consistency and ease of retrieval.

## Schemas
```sql
-- Create employesss table
CREATE TABLE employees (
    employees_id INT PRIMARY KEY,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    email VARCHAR(50),
    hire_date DATE,
    gender VARCHAR(1),
    salary INT,
    coffeeshop_id INT
);

CREATE TABLE shops (
    coffeeshop_id INT PRIMARY KEY,
    coffeeshop_name VARCHAR(50),
    city_id INT
);

ALTER TABLE employees
ADD FOREIGN KEY (coffeeshop_id)
REFERENCES shops(coffeeshop_id)
ON DELETE SET NULL;

CREATE TABLE locations (
    city_id INT PRIMARY KEY,
    city VARCHAR(50),
    country VARCHAR(50)
);

ALTER TABLE shops
ADD FOREIGN KEY (city_id)
REFERENCES locations(city_id)
ON DELETE SET NULL;

CREATE TABLE suppliers (
    coffeeshop_id INT,
    supplier_name VARCHAR(40),
    coffee_type VARCHAR(20),
    PRIMARY KEY (coffeeshop_id , supplier_name),
    FOREIGN KEY (coffeeshop_id)
        REFERENCES shops (coffeeshop_id)
        ON DELETE CASCADE
);
```

## Business Problems and Solutions (Queries Solved)
-- 1. Find all shops in a specific city.
```sql
SELECT 
    *
FROM
    shops
        JOIN
    locations ON shops.city_id = locations.city_id
WHERE
    locations.city = 'New York';
```
-- 2. Get all employees hired after 2017.
```sql
SELECT 
    *
FROM
    employees
WHERE
    hire_date > '2017-12-31';
```
-- 3. Count the number of employees.
```sql
SELECT 
    COUNT(*) total_employees
FROM
    employees;
```
-- 4. List the unique coffee types supplied.
```sql
SELECT DISTINCT
    (coffee_type)
FROM
    suppliers;
```
-- 5. Find employees with salaries above 50,000.
```sql
SELECT 
    *
FROM
    employees
WHERE
    salary > '50000';
```
-- 6.List all shops and their respective cities.
```sql
SELECT 
    *
FROM
    shops
        JOIN
    locations ON shops.city_id = locations.city_id
;
```
-- 7. Get the total salary paid by each coffee shop.
```sql
SELECT DISTINCT
    (coffeeshop_id), SUM(salary) total_salary_paid
FROM
    employees
GROUP BY coffeeshop_id;
```
-- 8. Find the highest-paid employee in each coffee shop.
```sql
SELECT DISTINCT
    (coffeeshop_id), MAX(salary)
FROM
    employees
GROUP BY coffeeshop_id;
```
-- 9. List employees grouped by gender and count in each group.
```sql
SELECT DISTINCT
    (gender), COUNT(*) Court_
FROM
    employees
GROUP BY gender;
```
-- 10. List suppliers supplying to a specific shop.
```sql
SELECT 
    *
FROM
    suppliers
WHERE
    coffeeshop_id = '1';
```
-- 11. List employees whose first name starts with 'A'.
```sql
SELECT 
    *
FROM
    employees
WHERE
    first_name LIKE 'A%';
```
-- 12. Get the average salary by gender.
```sql
SELECT DISTINCT
    (gender), AVG(salary)
FROM
    employees
GROUP BY gender;
```
-- 13. Find the top 3 highest-paid employees
```sql
SELECT 
    *
FROM
    employees
ORDER BY salary DESC
LIMIT 3;
```
-- 14. List suppliers serving shops in a specific country.
```sql
SELECT 
    suppliers.supplier_name,
    suppliers.coffee_type,
    locations.country
FROM
    suppliers
        JOIN
    shops ON suppliers.coffeeshop_id = shops.coffeeshop_id
        JOIN
    locations ON shops.coffeeshop_id = locations.city_id
WHERE
    locations.country = 'United States';
```
-- 15. Find employees with a salary above the average salary in their coffee shop.
```sql
SELECT 
    *
FROM
    employees
WHERE
    salary > (SELECT 
            AVG(salary)
        FROM
            employees)
ORDER BY salary ASC;
```
-- 16. Count the total number of employees in each country.
```sql
SELECT 
    locations.country,
    COUNT(employees.employees_id) Total_Employees
FROM
    employees
        JOIN
    shops ON employees.coffeeshop_id = shops.coffeeshop_id
        JOIN
    locations ON shops.city_id = locations.city_id
GROUP BY locations.country;
```
-- 17. List the top supplier by the number of coffee types they supply.
```sql
SELECT 
    supplier_name, COUNT(DISTINCT coffee_type) Coffee_count
FROM
    suppliers
GROUP BY supplier_name
ORDER BY coffee_count
LIMIT 1;
```
-- 18. Find the employee with the longest tenure.
```sql
SELECT 
    *, DATEDIFF(CURDATE(), hire_date) AS tenure_days
FROM
    employees
ORDER BY tenure_days DESC
LIMIT 1;
```
-- 19. Get the total salary and number of employees in each coffee shop, grouped by city.
```sql
SELECT 
    locations.city,
    shops.coffeeshop_name,
    COUNT(employees.employees_id) total_employees,
    SUM(employees.salary) total_salary
FROM
    employees
        JOIN
    shops ON employees.coffeeshop_id = shops.coffeeshop_id
        JOIN
    locations ON shops.city_id = locations.city_id
GROUP BY locations.city , shops.coffeeshop_name;
```
-- 20. List all employees and their respective shop locations.
```sql
SELECT 
    employees.first_name,
    employees.last_name,
    employees.salary,
    locations.city,
    locations.country
FROM
    employees
        JOIN
    shops ON employees.coffeeshop_id = shops.coffeeshop_id
        JOIN
    locations ON shops.city_id = locations.city_id;
```
## Findings
A relational database structure is ideal for managing hierarchical relationships (e.g., shops tied to cities and countries).
Constraints such as foreign keys and cascading deletes help maintain data integrity.
Sample data provided insights into how employee, supplier, and shop information is interconnected.

## Conclusions
The Coffee Shop Database provides a scalable and efficient solution for franchise management. By leveraging SQL relationships and constraints, the database ensures that critical operations such as employee tracking, supplier coordination, and shop location management are streamlined.

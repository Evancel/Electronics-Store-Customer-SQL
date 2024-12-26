# Electronics-Store-Customer-SQL
SQL Project

##1st stage:
SELECT pc_code, model, speed, ram
FROM PC
WHERE ram >= 16
ORDER BY ram,
speed DESC;

##2st stage:
Identify the average price of printers having the Inkjet type 
and C color from the Printer table. 
Round the result up to 2 decimal places.
SELECT ROUND(AVG(price),2) AS average_price
FROM Printer
WHERE type = 'Inkjet'
AND color = 'C';

##3rd stage:
Identify the total price of all laptop models produced by each maker. 
Find the maker in the Product table and SUM of the prices in Laptop as total_price. 
Ensure the results are sorted by total_price in ascending order. 
Use GROUP_BY and SUM functions to solve this.
SELECT p.maker, lp.total_price
    FROM 
    (SELECT model, SUM(price) total_price 
    FROM Laptop
    GROUP BY model) as lp
    LEFT JOIN Product p
    ON p.model = lp.model
ORDER BY lp.total_price;

##4rd stage:
Identify manufacturers producing PCs and laptops, then count their production quantities. 
Find the maker in the Product table, COUNT the number of PC-type models it produces as pc_count,
 and COUNT the number of laptop-type models it produces as laptop_count. Use GROUP_BY and SUM functions
 to solve this task. The order of the columns matters.
 
SELECT maker, 
    SUM(CASE WHEN type = 'PC' THEN 1 ELSE 0 END) AS pc_count, 
    SUM(CASE WHEN type = 'Laptop' THEN 1 ELSE 0 END) AS laptop_count
FROM Product
GROUP BY maker
HAVING pc_count > 0
AND laptop_count > 0;

##5th stage:
Identify the laptops priced higher than any PC, including model, maker (Product table), and price. 
Also, compute the differences: laptop vs. most expensive PC as price_difference_max_pc and laptop 
vs. average laptop price as price_difference_avg_laptop. The order of the columns matters.

SELECT l.model, 
    p.maker, 
    l.price, 
    l.price - (SELECT MAX(price) max_pc_price FROM PC) AS price_difference_max_pc, 
    l.price - (SELECT AVG(price) avg_laptop_price FROM Laptop) AS price_difference_avg_laptop 
FROM Laptop l, Product p
WHERE l.model = p.model
HAVING price_difference_max_pc > 0;

##6rd stage:
Identify the second most expensive PCs based on the RAM capacity. 
Find the pc_code, model, speed, ram, hd, cd, and price from the PC table. 
Use PARTITION BY to solve this challenge. The order of the columns matters.

WITH sort_price_desc_by_ram AS (SELECT pc_code, ROW_NUMBER() OVER (PARTITION BY ram ORDER BY price DESC) AS rw FROM PC)
SELECT pc.pc_code, pc.model, pc.speed, pc.ram, pc.hd, pc.cd, pc.price
FROM PC pc, sort_price_desc_by_ram s
WHERE pc.pc_code = s.pc_code
AND s.rw = 2;







USE [Northwind]

--1 列出各產品的供應商名稱
SELECT
p.ProductID,
p.ProductName,
s.SupplierID,
s.CompanyName
FROM Products AS p
INNER JOIN Suppliers AS s
ON p.SupplierID=s.SupplierID
--2 列出各產品的種類名稱
SELECT
p.ProductID,
p.ProductName,
c.CategoryID,
c.CategoryName
FROM Products AS p
INNER JOIN Categories AS c
ON p.CategoryID=c.CategoryID
--3 列出各訂單的顧客名字
SELECT
o.OrderID,
c.CustomerID,
c.CompanyName
FROM Orders AS o
INNER JOIN Customers AS c
ON o.CustomerID=c.CustomerID
--4 列出各訂單的所負責的物流商名字以及員工名字
SELECT
o.OrderID,
o.ShipVia,
sh.CompanyName,
o.ShipName
FROM Orders AS o
INNER JOIN Shippers AS sh
ON o.ShipVia=sh.ShipperID

--5 列出1998年的訂單
SELECT * FROM Orders
WHERE YEAR(OrderDate) = '1998' 
--6 各產品，UnitsInStock < UnitsOnOrder 顯示'供不應求'，否則顯示'正常'
SELECT 
ProductID,
ProductName,
UnitsInStock,
UnitsOnOrder,
IIF(UnitsInStock<UnitsOnOrder,'供不應求','正常') AS '供應狀態'
FROM Products
--7 取得訂單日期最新的9筆訂單
SELECT TOP 9
*
FROM Orders
ORDER BY OrderDate DESC
--8 產品單價最便宜的第4~8名
SELECT
*
FROM(
	SELECT
	ProductID,
	ProductName,
	UnitPrice,
	RANK() OVER(ORDER BY  UnitPrice) AS 'RANKS'
	FROM Products AS p
) AS temp
WHERE RANKS BETWEEN 4 AND 8
--9 列出每種類別中最高單價的商品
SELECT
*
FROM(
	SELECT
	c.CategoryID,
	c.CategoryName,
	p.ProductID,
	p.ProductName,
	p.UnitPrice,
	RANK() OVER (PARTITION BY c.CategoryID ORDER BY p.UnitPrice DESC)  AS priceRank
	FROM Products AS p
	INNER JOIN Categories AS c
	ON p.CategoryID=c.CategoryID
) AS temp
WHERE priceRank=1
--10 列出每個訂單的總金額
SELECT
OrderID,
SUM(UnitPrice*Quantity*(1-Discount)) AS Total
FROM [Order Details]
GROUP BY OrderID
--11 列出每個物流商送過的訂單筆數
SELECT
sh.ShipperID,
sh.CompanyName,
COUNT(*) AS OrdersCount
FROM Orders AS o
INNER JOIN Shippers AS sh
ON o.ShipVia=sh.ShipperID
GROUP BY sh.ShipperID,sh.CompanyName
--12 列出被下訂次數小於9次的產品
SELECT 
* 
FROM(
	SELECT
	p.ProductID,
	p.ProductName,
	COUNT(*) AS productOrderCount
	FROM [Order Details] AS od
	INNER JOIN Products AS p
	ON od.ProductID=p.ProductID
	GROUP BY p.ProductID,p.ProductName
	) AS temp
WHERE  productOrderCount < 9
-- (13、14、15請一起寫)
--13 新增物流商(Shippers)：
-- 公司名稱: 青杉人才，電話: (02)66057606
-- 公司名稱: 青群科技，電話: (02)14055374
INSERT INTO Northwind.dbo.Shippers
		(CompanyName,Phone)
VALUES('青杉人才','(02)66057606'),('青群科技','(02)14055374')

SELECT * FROM Shippers
--14 方才新增的兩筆資料，電話都改成(02)66057606，公司名稱結尾加'有限公司'
UPDATE Northwind.dbo.Shippers
SET 
CompanyName=CONCAT(CompanyName,'有限公司'),
PHONE ='(02)66057606'
WHERE CompanyName IN ('青杉人才','青群科技')
SELECT * FROM Shippers
--15 刪除剛才兩筆資料
DELETE  FROM Shippers
WHERE CompanyName LIKE '青杉人才%' OR CompanyName LIKE '青群科技%'


SELECT * FROM Shippers



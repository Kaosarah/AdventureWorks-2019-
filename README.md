# PROJECT NAME: AdventureWorks(2019)
AdventureWorks(2019) is a frictious bicycle manufacturing database designed by Microsoft. The Organisation is based in Bothell, Washington and sells to other part of the world. The database contain information about Sales, Purchasing, Production, Contact management and Human resources.


# PROJECT OBJECTIVE: 
The organisation is seeking knowledge about the best product category to invest on, the growth in business revenue over the years, regions to target and avoid,best customers in each region and Organisation's employee's analysis.

# Data Sourcing: 
The data used for this analysis is accessible through this link https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorks2019.bak.
The database contain 70 tables.

# Data Transformation
The data were explored and cleaned  using Microsoft SQL. 
#### Sales table (Fact table)
        WITH SALES AS (SELECT PP.BusinessEntityID, COALESCE (PC.CreditCardID, '10000') AS creditcard, SH.CustomerID,SH.OrderDate,SH.ShipDate,SH.SalesOrderID,
              	  SH.SubTotal, coalesce (SH.TerritoryID, 0) AS   TerritoryID,SH.SalesPersonID,SD.LineTotal,SD.UnitPrice,SD.ProductID,SD.OrderQty,SD.UnitPriceDiscount
		  FROM Person.Person PP LEFT JOIN Sales.PersonCreditCard PC ON 
		  PP.BusinessEntityID  = PC.BusinessEntityID 
		  LEFT JOIN ( SELECT coalesce (CreditCardID, '10000') AS creditcard, CustomerID,OrderDate,ShipDate,SalesOrderID,
			SubTotal,SalesPersonID,TerritoryID  from Sales.SalesOrderHeader ) AS SH ON PC.CreditCardID = SH.creditcard 
			LEFT JOIN Sales.SalesOrderDetail SD ON SH.SalesOrderID = SD.SalesOrderID)
			SELECT * FROM SALES WHERE SalesOrderID IS NOT NULL
#### Purchase(second fact table)
                 WITH purchase AS( SELECT POD.TotalDue,POD.OrderDate,POD.ShipDate, DATEDIFF( day, POD.OrderDate, POD.ShipDate) AS Delivery_day,
			POH.LineTotal, POH.ProductID, POH.PurchaseOrderID, POH.ReceivedQty,POD.SubTotal,POD.VendorID ,POH.RejectedQty,POH.StockedQty, POH.UnitPrice
			FROM Purchasing.PurchaseOrderDetail AS POH
			LEFT JOIN Purchasing.PurchaseOrderHeader AS POD  ON POH.PurchaseOrderID= POD.PurchaseOrderID)
			SELECT * FROM purchase;
#### Employees table

	WITH employee AS(  SELECT p.BusinessEntityID,d.DepartmentID, p.FirstName,
		p.LastName, e.JobTitle,e.SickLeaveHours,e.[VacationHours] FROM [HumanResources].[Department] d
		left join [HumanResources].[EmployeeDepartmentHistory] dh ON d.DepartmentID = dh.DepartmentID
		left join [HumanResources].[Employee] e ON dh.BusinessEntityID = e.BusinessEntityID
		left join Person.Person p ON e.BusinessEntityID =p.BusinessEntityID)
		SELECT * FROM employee ;
#### Customer table

      WITH locate AS (SELECT B.BusinessEntityID, S.Name AS state_name, C.Name AS Country_name FROM Person.BusinessEntityAddress AS B
		LEFT JOIN Person.Address AS PA ON B.AddressID = PA.AddressID
		LEFT JOIN Person.StateProvince AS S ON PA.StateProvinceID= S.StateProvinceID
		LEFT JOIN Person.CountryRegion AS C ON S.CountryRegionCode= C.CountryRegionCode)
		SELECT  P.BusinessEntityID, CONCAT(P.FirstName, ' ',P.LastName)  AS FullName,locate.state_name, locate.Country_name FROM Person.Person AS P
		LEFT JOIN locate ON P.BusinessEntityID= locate.BusinessEntityID;
#### Product, product subcategory and product category

           --CATEGORY
		SELECT* FROM Production.ProductCategory
           -- SUBCATEGORY
		SELECT* FROM Production.Productsubcategory
	   --PRODUCT
		SELECT * FROM Production.Product;
		
#### Sales Territory

			SELECT TerritoryID, [Name],[CountryRegionCode], [Group] FROM Sales.SalesTerritory
			
A date table was generated using Powerquery

![Screenshot (50)](https://user-images.githubusercontent.com/109418747/184531083-5476c83d-fec0-4bb9-a1fd-f42f594ad214.png)

These are the tables **(8)** needed to answer business questions.

# Analysis and Insight

The total sales generated from 31st of May 2011 to 30th of June 2014 was $109,850,000, total cost of production was $ 63,790,000 and the gross profit generated was $46,050,000.
 
 ![image](https://user-images.githubusercontent.com/109418747/184532543-c1fa36c3-9ae9-42ae-8baf-dc928bb294f5.png)
 
 There was a huge increase in Year-over-year growth in 2012 by 165.19%
 
 
 ![image](https://user-images.githubusercontent.com/109418747/184533221-403e4e92-123d-4023-86b9-080ddb0ad790.png)
 
 
 and a drastic fall in 2014 by -44.04%
 ![image](https://user-images.githubusercontent.com/109418747/184533107-92b924ff-825d-428d-b0f3-3893bc5f1815.png)


#### SALES TREND BY DATE

There were massive sales at the end of each month throughout the years except for **May and June of the year **2014**. Drastic fall in sales started from **28th of May,2014 till  30th of June 2014**.

![image](https://user-images.githubusercontent.com/109418747/184536887-541e880b-24d8-454e-936b-4d4bfd5e0a8c.png)


![image](https://user-images.githubusercontent.com/109418747/184536925-eed06f58-41ba-4377-a4b0-4b2a236607af.png)


![image](https://user-images.githubusercontent.com/109418747/184536953-53ccc1f1-4ad3-404d-aaed-6927521b26a6.png)


**SALES BY REGION**
Throughout the years, Southwest of United state of America generated the highest revenue($24,184,609) making 22% of the revenue, Canada($16,355,790.45)14.9%, Northwest of USA($16,084,942)14.64/% and the least revenue was generated in Germany($4,915,407.60) making 4.47% of total sales generated.


![image](https://user-images.githubusercontent.com/109418747/184537765-99cf3811-bd73-4aeb-9b4f-8e70fe015bdf.png)

![image](https://user-images.githubusercontent.com/109418747/184537789-314fa7df-570c-4f2a-be6a-e2b15305f56f.png)


**REVENUE BY CUSTOMERS**

 Top 10 customers throughout the regions, the best customer between 2011-2014 is **Roger Harui($877,303.48)**
 
 ![image](https://user-images.githubusercontent.com/109418747/184540249-d76602e6-bcb7-485f-92e0-302b457e5b5d.png)
 
 
 **SALES BY PRODUCT**
 
 Bikes was the highest selling product category with revenue of $94,651,173 and 90268 units ordered, Components generated $ 11,802,593 with 49044 units ordered,  Clothings generated $2,120,543 with 73670 and Accessories generated $1,272,072. Best selling product was **Mountain-200 Black, 38(Bike)** and least selling product was **LL Road Seat/Saddle**
 
 
 ![image](https://user-images.githubusercontent.com/109418747/184541446-6f5a301e-590f-43dc-b38c-129fbfdfeff3.png)
 
 ![image](https://user-images.githubusercontent.com/109418747/184541824-edeb8fe1-ac11-4504-bb58-41907acd8346.png)
 
 
**Employee's dashboard**

The organisation have 290 employees, the Production group have the highest number of employees(180) and took the longest duration of Vacation and sick leave and the Executive group have 2 employees.


![image](https://user-images.githubusercontent.com/109418747/184542273-13547359-0abc-409b-a1a8-280c7d420be0.png)

# Findings 
The company lost $20,640,000 (-44.04% YOY Sale growthrate) in 2014.

Bikes generate 86.17% of the company's revenue, Component(10.74%), Clothing(1.93%) and Accessories generated the least revenue by 1.16%.

Best selling product is Mountain-200 Black, 38(Bike)

Roger Harui is the best consumer throughout the years with purchase of $877,303.48.


# Recommendation
The company should continue with all action put in place in country with high revenue and enhance actions put in place.

The yearly or monthly promo of the company should be done by mid of the month as there is already huge sales at the end and beginning of the month.

Awareness about the product in most european countries like France, Germany and United Kingdom should be the country priority, for instance the sales in Germany increased from 2012 to 2014 by 64.58%, measures used in this country can be used across countries with least sales.

Marketing should be improved for other product categories aside bikes.

# For interaction with dashboard, access link below.




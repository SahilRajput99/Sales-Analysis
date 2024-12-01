# Sales and Order Analysis


### Objective:

The goal of this project is to analyze sales, orders, quantity sold, and other related metrics using SQL queries. This analysis is done across different time frames (monthly, yearly, etc.) and based on various factors like product sales, shipping methods, and order types.

### Database and Tables:
The analysis is done on the RB_Project database and uses the following tables:

1. ### Fact_Sales
Description: Contains data related to sales transactions, including order totals, quantity sold, shipping method, etc.

#### Key Columns:

- Total: Sales Total for the transaction.
- Lineitem_quantity: Quantity of the product sold in the transaction.
- New_Created_at: Timestamp of when the sale was made.
- Billing_City: The billing city of the customer.
- Vendor: The vendor associated with the sale.
- Region: The region where the sale occurred.
- Country: The country where the customer resides.
- Payment_Type: Type of payment used in the transaction (e.g., Credit Card, PayPal, etc.).
- Product_Name: Name of the product sold.
- Product_Category: Category of the product (e.g., Electronics, Clothing).
- Shipping_Method: The shipping method used for the order (e.g., Standard, Express).
- Discount: Any discount applied to the sale.
- Tax: The tax applied on the transaction.
- Currency: The currency used for the sale.


2. ### ConsolidatedOrders
Description: Contains information related to customer orders, such as order ID and creation timestamp.

#### Key Columns:

- OrderID: Unique Identifier for each order.
- CreatedAt: Timestamp for when the order was created.
- CustomerID: Unique identifier for the customer placing the order.
- Order_Status: The status of the order (e.g., Completed, Pending).
- Order_Total: Total value of the order.
- Shipping_Address: The address where the order is being shipped.
- Shipping_Country: The country for shipping.

3. ### Shipping_Info
Description: Contains data about the shipping methods used in the sales transactions.

### SQL Queries:
 #### Total Sales Analysis

#### Monthly sales

```sql
SELECT SUM(Total) AS TotalSales
FROM RB_Project.dbo.Fact_Sales
GROUP BY MONTH(RB_Project.dbo.Fact_Sales.New_Created_at);
```

![Monthly_Sales](https://github.com/user-attachments/assets/aba4c462-0e46-4745-be39-ec3a7739d53f)

 #### Sales By Year
```sql
SELECT SUM(total) AS totalsales
FROM RB_Project.dbo.Fact_Sales
GROUP BY YEAR(RB_Project.dbo.Fact_Sales.New_Created_at);
```

 ![sales year](https://github.com/user-attachments/assets/cac23978-2549-40ef-9342-4577778dbe04)

 #### Calculate the total sales for each respective month
```sql
SELECT SUM(total) AS totalsales, 
       MONTH(RB_Project.dbo.Fact_Sales.New_Created_at) AS Monthlysales, 
       YEAR(RB_Project.dbo.Fact_Sales.New_Created_at) AS Yearlysales
FROM RB_Project.dbo.Fact_Sales
GROUP BY 
    MONTH(RB_Project.dbo.Fact_Sales.New_Created_at),
    YEAR(RB_Project.dbo.Fact_Sales.New_Created_at)
ORDER BY
    Yearlysales,
    Monthlysales ASC;
```

 ![Totalsales by month and year](https://github.com/user-attachments/assets/a50cfa59-dbcb-4282-8e10-0582e0ddbb56)

 #### Calculate the month on month increase or decrease in sales & Difference in sales in % between the selected month and previous month
```sql
SELECT 
    YEAR(New_Created_at) AS SalesYear,
    MONTH(New_Created_at) AS SalesMonth,
    ROUND(SUM(Total), 0) AS TotalSales,
    ((SUM(Total) - LAG(SUM(Total), 1) OVER (ORDER BY YEAR(New_Created_at), MONTH(New_Created_at))) 
        / LAG(SUM(Total), 1) OVER (ORDER BY YEAR(New_Created_at), MONTH(New_Created_at))) * 100 
        AS MOM_Increase_Percentage
FROM 
    RB_Project.dbo.Fact_Sales
GROUP BY 
    YEAR(New_Created_at), MONTH(New_Created_at)
ORDER BY 
    YEAR(New_Created_at), MONTH(New_Created_at);
```

![MOM % sales](https://github.com/user-attachments/assets/5c13a89b-8da9-4b0a-9e84-47ec1a59080b)

 ### Total Order Analysis

#### Calculate the total number of orders for each respective month
```sql
SELECT COUNT(RB_Project.dbo.ConsolidatedOrders.OrderID) AS Total_Orders, MONTH(RB_Project.dbo.ConsolidatedOrders.CreatedAt) as Monthlyorders, YEAR(RB_Project.dbo.ConsolidatedOrders.CreatedAt) as yearlyorders
from RB_Project.dbo.ConsolidatedOrders
Group By
		MONTH(RB_Project.dbo.ConsolidatedOrders.CreatedAt),
		YEAR(RB_Project.dbo.ConsolidatedOrders.CreatedAt)
Order By 
		yearlyorders,
		Monthlyorders;
```
![No  of Orders by Month](https://github.com/user-attachments/assets/2c62c080-ed7f-4a0a-9b63-e2ec7c6f2550)

#### Determine the month on month increase or decrease in number of orders and calculate the difference in the number of orders between the selected month and the previous month
```sql
SELECT
    MONTH(RB_Project.dbo.ConsolidatedOrders.CreatedAt) AS MonthlyOrders, 
    YEAR(RB_Project.dbo.ConsolidatedOrders.CreatedAt) AS YearlyOrders,
    Round(COUNT(RB_Project.dbo.ConsolidatedOrders.OrderID),0) AS Total_Orders,
    (
        (COUNT(OrderID) - LAG(COUNT(OrderID), 1) OVER (
            ORDER BY YEAR(RB_Project.dbo.ConsolidatedOrders.CreatedAt), 
                     MONTH(RB_Project.dbo.ConsolidatedOrders.CreatedAt)
        )) 
        / LAG(COUNT(OrderID), 1) OVER (
            ORDER BY YEAR(RB_Project.dbo.ConsolidatedOrders.CreatedAt), 
                     MONTH(RB_Project.dbo.ConsolidatedOrders.CreatedAt)
        )
    ) * 100 AS MOM_Percentage
FROM 
    RB_Project.dbo.ConsolidatedOrders
GROUP BY 
    YEAR(RB_Project.dbo.ConsolidatedOrders.CreatedAt), 
    MONTH(RB_Project.dbo.ConsolidatedOrders.CreatedAt)
ORDER BY 
    YEAR(RB_Project.dbo.ConsolidatedOrders.CreatedAt), 
    MONTH(RB_Project.dbo.ConsolidatedOrders.CreatedAt);
```

![MOM % Orders](https://github.com/user-attachments/assets/84031e3b-4b25-4913-afd3-4b53729b74c9)

### Quantity Sold Analysis

#### Calculate the total quantity sold for each respective month
```sql
SELECT SUM(Lineitem_quantity) as totalQuantitySold, Month(RB_Project.dbo.Fact_Sales.New_Created_at) as Monthlyquantitysold, Year(RB_Project.dbo.Fact_Sales.New_Created_at) as Yearlyquantitysold
 from RB_Project.dbo.Fact_Sales
 Group By 
 Month(RB_Project.dbo.Fact_Sales.New_Created_at),
 Year(RB_Project.dbo.Fact_Sales.New_Created_at)
 Order By
 Yearlyquantitysold,
 Monthlyquantitysold asc;
```

![Total Quantity sold](https://github.com/user-attachments/assets/c161e6e9-5f6f-441d-9f77-edd8079e04f8)
 
#### Determine the month on month increase or decrease in number of orders and calculate the difference in the number of orders between the selected month and the previous month

```sql
SELECT
    MONTH(RB_Project.dbo.Fact_Sales.New_Created_at) AS MonthlyQuantitySold, 
    YEAR(RB_Project.dbo.Fact_Sales.New_Created_at) AS YearlyQuantitySold,
    SUM(RB_Project.dbo.Fact_Sales.Lineitem_quantity) AS Total_Quantity_Sold,
    CASE 
        WHEN LAG(SUM(RB_Project.dbo.Fact_Sales.Lineitem_quantity)) 
             OVER (ORDER BY YEAR(RB_Project.dbo.Fact_Sales.New_Created_at), 
                               MONTH(RB_Project.dbo.Fact_Sales.New_Created_at)) IS NULL 
        THEN NULL 
        ELSE 
            (SUM(RB_Project.dbo.Fact_Sales.Lineitem_quantity) 
            - LAG(SUM(RB_Project.dbo.Fact_Sales.Lineitem_quantity)) 
              OVER (ORDER BY YEAR(RB_Project.dbo.Fact_Sales.New_Created_at), 
                                MONTH(RB_Project.dbo.Fact_Sales.New_Created_at))
            ) 
            / LAG(SUM(RB_Project.dbo.Fact_Sales.Lineitem_quantity)) 
              OVER (ORDER BY YEAR(RB_Project.dbo.Fact_Sales.New_Created_at), 
                                MONTH(RB_Project.dbo.Fact_Sales.New_Created_at)) * 100
    END AS MOM_Percentage
FROM 
    RB_Project.dbo.Fact_Sales
GROUP BY 
    YEAR(RB_Project.dbo.Fact_Sales.New_Created_at), 
    MONTH(RB_Project.dbo.Fact_Sales.New_Created_at)
ORDER BY 
    YEAR(RB_Project.dbo.Fact_Sales.New_Created_at), 
    MONTH(RB_Project.dbo.Fact_Sales.New_Created_at);
```

![MOM quanities sold](https://github.com/user-attachments/assets/0c9a54cc-ea6c-4a66-8663-f115a84973aa)

### Detailed Metrics i,e.,(Sales, Qty Sold, Orders) of any specific date
```sql
SELECT SUM(Total) as Totalsales,
	SUM(Lineitem_quantity) as Total_Qty_Sold,
		count(distinct Name) as Totalorders
	From RB_Project.dbo.Fact_Sales
Where
Cast(New_Created_at as Date) = '2023-08-18';
```

![Detailed metrics by specific date](https://github.com/user-attachments/assets/f36fb1a8-fffe-47ac-bfb4-4cde8f398a94)

#### Sales  by Weekdays and Weekends
```sql
SELECT
	CASE WHEN DATEPART(WEEKDAY, RB_Project.dbo.Fact_Sales.New_Created_at) IN (1,7) THEN 'Weekends'
	Else 'Weekdays'
	End AS DAY_TIME,
	Concat(Round(sum(total)/1000,1),'K') as Totalsales
from RB_Project.dbo.Fact_Sales
	WHERE MONTH(RB_Project.dbo.Fact_Sales.New_Created_at) = 2 AND Year(RB_Project.dbo.Fact_Sales.New_Created_at) = '2023'
	Group BY
	CASE WHEN DATEPART(WEEKDAY, RB_Project.dbo.Fact_Sales.New_Created_at) IN (1,7) THEN 'Weekends'
	Else 'Weekdays'
	End;
```

![Sales by weekday-weekends](https://github.com/user-attachments/assets/a040bad3-e3d7-4362-820b-defb34f64696)

#### Sales by Location
```sql
SELECT Billing_City, CONCAT(ROUND(sum(total)/1000,1), 'K')  as totalsales
from RB_Project.dbo.Fact_Sales
Where MONTH(RB_Project.dbo.Fact_Sales.New_Created_at) = 2 AND Year(RB_Project.dbo.Fact_Sales.New_Created_at) = '2024'
Group BY Billing_City
Order by totalsales desc;
```

![sales by location](https://github.com/user-attachments/assets/d0291842-98d7-4737-906a-797202e4d1fe)

#### Daily sales Analysis with Average Line

```sql
WITH DailySales AS (
    SELECT 
        DAY(New_Created_at) AS day_of_the_month,
        SUM(Total) AS TotalSales
    FROM 
        RB_Project.dbo.Fact_Sales
    WHERE 
        MONTH(New_Created_at) = 1
        AND YEAR(New_Created_at) = 2024
    GROUP BY 
        DAY(New_Created_at)
),
AvgSales AS (
    SELECT 
        AVG(TotalSales) AS AvgSales
    FROM 
        DailySales
)
SELECT 
    DS.day_of_the_month,
    CASE 
        WHEN DS.TotalSales > ASales.AvgSales THEN 'Above Average'
        WHEN DS.TotalSales < ASales.AvgSales THEN 'Below Average'
        ELSE 'Average'
    END AS sales_status,
    DS.TotalSales
FROM 
    DailySales DS
CROSS JOIN 
    AvgSales ASales
ORDER BY 
    DS.day_of_the_month;
```

![Totalsales status](https://github.com/user-attachments/assets/797263b2-2e9e-4fd4-a35d-b9cc1cf2e0dd)

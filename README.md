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
1. Total Sales Analysis

--Monthly sales
Select sum(Total) as Totalsales
FROM RB_Project.dbo.Fact_Sales
Group By Month(RB_Project.dbo.Fact_Sales.New_Created_at)

 --Sales By Year
 Select sum(total) as totalsales
 from RB_Project.dbo.Fact_Sales
 Group By YEAR(RB_Project.dbo.Fact_Sales.New_Created_at)

 --Calculate the toal sales for each respective month
  Select sum(total) as totalsales, Month(RB_Project.dbo.Fact_Sales.New_Created_at) as Monthlysales, Year(RB_Project.dbo.Fact_Sales.New_Created_at) as Yearlysales
 from RB_Project.dbo.Fact_Sales
 Group By 
 Month(RB_Project.dbo.Fact_Sales.New_Created_at),
 Year(RB_Project.dbo.Fact_Sales.New_Created_at)
 Order By
 Yearlysales,
 Monthlysales asc;

 -- Calculate the month on month increase or decrease in sales & Difference in sales in % between the selected month and previous month
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




# Shopify-Data-Science-Intern-Challenge


## Question 1: Given some sample data, write a program to answer the following:

### On Shopify, we have exactly 100 sneaker shops, and each of these shops sells only one model of shoe. We want to do some analysis of the average order value (AOV). When we look at orders data over a 30 day window, we naively calculate an AOV of $3145.13. Given that we know these shops are selling sneakers, a relatively affordable item, something seems wrong with our analysis. 

<ol type="a">
  <li>Think about what could be going wrong with our calculation. Think about a better way to evaluate this data.</li>
  <li>What metric would you report for this dataset?</li>
  <li>What is its value?</li>
</ol> 

Question #1 Solution:

<ol type="a">
    <li>After observing the data, it is clear that the naive calculation of the AOV is caused by the the few outliers. The vast majority of the order amounts are under $1000, however, there are some outliers with values up to $704000. A better way to evaluate this data would be to use a metric that is not sensitive to outliers, such as the median or the mode.
    </li><br>
    <li>For this dataset I would use the median since it is much less affected by outliers. The median is the middle value from all the order values. Using this metric prevents huge outliers in the order amounts, such as $700400, from having such a large influence in the metric's value.
    </li><br>
    <li>The median value for the order amount in this dataset is $284.
    </li><br>
</ol>
<br>


## Question 2:  For this question you’ll need to use SQL. Please use queries to answer the following questions. Paste your queries along with your final numerical answers below.

<ol type="a">
  <li>How many orders were shipped by Speedy Express in total?</li><br/>
To find the number of orders shipped by Speedy Express, I started by finding the ShipperID of 'Speedy Express' from the Shippers table. Once I had the right ShipperID, I simply had to find the number of occurrences in the Orders table where the ShipperID was the same as the one found for Speedy Express in the Shippers table.
	
```sql
SELECT COUNT(*) AS NumOrders
FROM Orders
WHERE ShipperID IN (
	SELECT ShipperID 
	FROM Shippers
	WHERE ShipperName = "Speedy Express")
```
Result: 54
	
	
  <li>What is the last name of the employee with the most orders?</li><br>
For this query, I started by finding the EmployeeID that appeared most frequently in the Orders table. Once I had that, I used an inner join on that result with the Employees table to retrieve the Employee that matched the EmployeeID in both these tables. Selecting the LastName from this inner join gives us the LastName of the employee with the most orders.
	
```sql
SELECT LastName 
FROM Employees
INNER JOIN (
	SELECT TOP 1 EmployeeID, COUNT(EmployeeID) 
	FROM Orders
	GROUP BY EmployeeID
	ORDER BY COUNT(EmployeeID) DESC) x
ON Employees.EmployeeID = x.EmployeeID
```
Result: Peacock
	
 <li>What product was ordered the most by customers in Germany?</li><br/>
This was the most lengthy of the 3 queries to solve since it required values across 4 different tables. I solved this by essentialy adding filters by using conditions in the WHERE clause. I started by finding the Customers in which their Country was 'Germany', then I had to make sure the CustomerID's from the Orders table were the same as those from the Customers table. Up to this point, I'd have the orders from customers in Germany. Then, I made sure the OrderID's from the Orders table wee the same as those in the OrderDetails page. The next step was similar to the previous two except it being the ProductID's between the OrderDetails table and the Products table. All that was left was to order the remaining products by the sum of their quanities ordered and select product name of the top result. 
	
```sql
SELECT TOP 1 p.ProductName
FROM Customers AS c, Orders AS o, OrderDetails AS d, Products AS p
WHERE c.Country = "Germany" AND c.CustomerID = o.CustomerID AND o.OrderID = d.OrderID AND d.ProductID = p.ProductID 
GROUP BY p.ProductName
ORDER BY SUM(d.Quantity) DESC
```	
Result: Boston Crab Meat

<ol/>

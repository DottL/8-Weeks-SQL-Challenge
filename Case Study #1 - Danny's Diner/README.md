# Case Study #1 - Danny's Diner

<div align="center">
  <img src="https://github.com/user-attachments/assets/46eddd5e-45f8-4e2d-9308-08139c1f9a13" alt="1" width="500"/>
</div>

**Table of Content**
- [Problem Context](#problem-context)
- [Entity Relationship Diagram](#entity-relationship-diagram)
- [Core Questions](#core-questions)
- [Solutions](#solutions)



## Problem Context 
Danny’s Diner is a small restaurant that opened recently. Danny, the owner, is looking for insights into his customers' patterns and preferences.

----
## Entity Relationship Diagram
<div align="center">
  <img src="https://github.com/user-attachments/assets/af4821ca-8cf8-49ab-896b-95a38842b44f"/>
</div>

-------

### Core Questions
1. [Total Expenditure per Customer](#1-total-expenditure-per-customer)  
2. [Customer Visit Frequency](#2-customer-visit-frequency)  
3. [First Menu Item Purchased](#3-first-menu-item-purchased)  
4. [Most Purchased Menu Item](#4-most-purchased-menu-item)  
5. [Most Popular Item per Customer](#5-most-popular-item-per-customer)  
6. [First Purchase After Membership](#6-first-purchase-after-membership)  
7. [Last Purchase Before Membership](#7-last-purchase-before-membership)  
8. [Total Items and Expenditure Before Membership](#8-total-items-and-expenditure-before-membership)  
9. [Hypothetical Point Program](#9-hypothetical-point-program)  
10. [Expanded Point Program](#10-expanded-point-program)  
11. [Bonus Questions](#11-bonus-questions)  

-------

## Solutions

### 1. Total Expenditure per Customer 
**What is the total amount each customer spent at the restaurant?** 

<table>
  <tr>    
    <td>
      <table>
        <tr>
          <th>customer_id</th>
          <th>total_amount</th>
        </tr>
        <tr>
          <td>A</td>
          <td>76</td>
        </tr>
        <tr>
          <td>B</td>
          <td>74</td>
        </tr>
        <tr>
          <td>C</td>
          <td>36</td>
        </tr>
      </table>
    </td>
    <td><ul>
      <li>Customer A spent $76</li>
      <li>Customer B spent $74</li>
      <li>Customer C spent $36</li>
    </ul></td>
  </tr>
</table>

**Code:**
```sql
SELECT sales.customer_id, 
  SUM(menu.price) as total_amount
FROM sales LEFT JOIN menu 
ON sales.product_id = menu.product_id
GROUP BY sales.customer_id
ORDER BY sales.customer_id
```

**Approach:**
* Joins menu to get product price
* Group by customer ID to sum up their products’ prices

--------
### 2. Customer Visit Frequency
**How many days has each customer visited the restaurant?** 
<table>
  <tr>    
    <td>
      <table>
        <tr>
          <th>customer_id</th>
          <th>days_visited</th>
        </tr>
        <tr>
          <td>A</td>
          <td>4</td>
        </tr>
        <tr>
          <td>B</td>
          <td>6</td>
        </tr>
        <tr>
          <td>C</td>
          <td>2</td>
        </tr>
      </table>
    </td>
    <td><ul>
      <li>Customer A visited 4 days</li>
      <li>Customer B visited 6 days</li>
      <li>Customer C visited 2 days</li>
    </ul></td>
  </tr>
</table>

**Code:**
```sql
SELECT customer_id, 
  COUNT(DISTINCT order_date) as days_visited
FROM sales
GROUP BY customer_id
ORDER BY customer_id
```

**Approach:**
* Grouped each customer_id to count their respective order date
* Count the number of distinct days each customer visited

--------
### 3. First Menu Item Purchased
**What was the first item from the menu purchased by each customer?** 
<table>
  <tr>    
    <td>
      <table>
        <tr>
          <th>customer_id</th>
          <th>first_order_date</th>
          <th>first_order</th>
        </tr>
        <tr>
          <td>A</td>
          <td>2021-01-01</td>
          <td>curry, sushi</td>
        </tr>
        <tr>
          <td>B</td>
          <td>2021-01-01</td>
          <td>curry</td>
        </tr>
        <tr>
          <td>C</td>
          <td>2021-01-01</td>
          <td>ramen</td>
        </tr>
      </table>
    </td>
    <td><ul>
      <li>Customer A’s first order was curry and sushi</li>
      <li>Customer B’s first order was curry</li>
      <li>Customer C’s first order was ramen</li>
    </ul></td>
  </tr>
</table>

**Code:**
```sql
SELECT sales.customer_id, 
  sales.order_date as first_order_date,
  STRING_AGG(DISTINCT menu.product_name, ', ' ORDER BY menu.product_name) AS first_order
FROM sales JOIN menu 
ON sales.product_id = menu.product_id
WHERE (sales.customer_id, sales.order_date) IN (
  SELECT sales.customer_id, MIN(order_date)
  FROM sales LEFT JOIN members
  ON sales.customer_id = members.customer_id
  GROUP BY sales.customer_id)
GROUP BY sales.customer_id, sales.order_date;
```

**Approach:**
* Select records where each customer’s order date is their earliest one.
* Grouped orders in case they had several items on that date
* Combined items into a single cell for readability

--------
### 4. Most Purchased Menu Item
**What is the most purchased item on the menu and how many times was it purchased by all customers?** 
<table>
  <tr>    
    <td>
      <table>
        <tr>
          <th>product_name</th>
          <th>purchased_count</th>          
        </tr>
        <tr>
          <td>ramen</td>          
          <td>8</td>
        </tr>
        <tr>                  
          <td>curry</td>
          <td>4</td>
        </tr>
        <tr>
          <td>sushi</td>
          <td>3</td>
        </tr>
      </table>
    </td>
    <td><ul>
      <li>Ramen is the most purchased item on the menu, it has a total of 8 purchased, double that of the next highest food item, curry, with 4 purchases.</li>
    </ul></td>
  </tr>
</table>

**Code:**
```sql
SELECT menu.product_name, 
  COUNT(*) as purchased_count
FROM sales LEFT JOIN menu
ON sales.product_id = menu.product_id
GROUP BY sales.product_id, menu.product_name
ORDER BY purchased_count DESC, menu.product_name
```

**Approach:**
* Joined menu with sales to get information on food products
* Grouped by sold food products to count their instances 


--------
### 5. Most Popular Item per Customer
**Which item was the most popular for each customer?** 
<table>
  <tr>    
    <td>
      <table>
        <tr>
          <th>customer_id</th>
          <th>product_name</th>
          <th>purchase_count</th>
        </tr>
        <tr>
          <td>A</td>
          <td>ramen</td>
          <td>3</td>
        </tr>
        <tr>
          <td>B</td>
          <td>curry, ramen, sushi</td>
          <td>2</td>
        </tr>
        <tr>
          <td>C</td>          
          <td>ramen</td>
          <td>3</td>
        </tr>
      </table>
    </td>
    <td><ul>
      <li>Ramen is a top favorite among all customers.</li>
      <li>Customer B purchased all 3 food items, curry, ramen, and sushi equally in terms of purchased counts.</li>
    </ul></td>
  </tr>
</table>

**Code:**
```sql
WITH ranked_prod AS (
  SELECT customer_id, product_id,
  COUNT(*) AS purchase_count,
  DENSE_RANK() OVER(PARTITION BY customer_id ORDER BY COUNT(*) DESC) as ranking
  FROM sales
  GROUP BY customer_id, product_id)
SELECT ranked_prod.customer_id, 
  STRING_AGG(DISTINCT menu.product_name, ', ' ORDER BY menu.product_name) as favorite_item,
  MAX(purchase_count) as purchase_count
FROM ranked_prod JOIN menu
ON ranked_prod.product_id = menu.product_id
WHERE ranking = 1
GROUP BY customer_id
```

**Approach:**
* Created a cte to rank each customer’s most-bought item
* Joined the cte with the menu table to get food product name
* Selects records where the frequency ranking is top 1
* Combines the same ranking into one cell for readability


--------
### 6. First Purchase After Membership
**Which item was purchased first by the customer after they became a member?** 
<table>
  <tr>    
    <td>
      <table>
        <tr>
          <th>customer_id</th>
          <th>order_date</th>
          <th>products</th>
        </tr>
        <tr>
          <td>A</td>
          <td>2021-01-07</td>
          <td>curry</td>
        </tr>
        <tr>
          <td>B</td>
          <td>2021-01-11</td>          
          <td>sushi</td>
        </tr>
      </table>
    </td>
    <td><ul>
      <li>Customer A’s first product after becoming a member was curry</li>
      <li>Customer B’s first product after becoming a member was sushi</li>
      <li>Customer C has yet to be a member</li>
    </ul></td>
  </tr>
</table>

**Code:**
```sql
SELECT customer_id, 
  order_date, 
  STRING_AGG(DISTINCT menu.product_name, ', ' ORDER BY menu.product_name) as products  
FROM sales LEFT JOIN menu
ON sales.product_id = menu.product_id
WHERE (customer_id, order_date) IN (
  SELECT sales.customer_id, MIN(order_date)
  FROM sales LEFT JOIN members
  ON sales.customer_id = members.customer_id
  WHERE sales.order_date >= members.join_date
  GROUP BY sales.customer_id)
 GROUP BY customer_id, order_date
 ORDER BY customer_id
```

**Approach:**
* Select only records after each member’s join date
* Choose the earliest order from the filtered data to get food products from
* Combines multiple orders into one cell for readability


--------
### 7. Last Purchase Before Membership
**Which item was purchased just before the customer became a member?** 
<table>
  <tr>    
    <td>
      <table>
        <tr>
          <th>customer_id</th>
          <th>max_nonmember_date</th>
          <th>products</th>
        </tr>
        <tr>
          <td>A</td>
          <td>2021-01-01</td>
          <td>curry, sushi</td>
        </tr>
        <tr>
          <td>B</td>
          <td>2021-01-04</td>          
          <td>sushi</td>
        </tr>
      </table>
    </td>
    <td><ul>
      <li>Customer A’s last order before membership was curry</li>
      <li>Customer B’s last order before membership was sushi</li>
      <li>Customer C is currently not a member</li>
    </ul></td>
  </tr>
</table>

**Code:**
```sql
SELECT customer_id, 
	order_date as max_nonmember_date, 
	STRING_AGG(DISTINCT menu.product_name, ', ' ORDER BY menu.product_name) as products  
FROM sales LEFT JOIN menu
ON sales.product_id = menu.product_id
WHERE (customer_id, order_date) IN (
  SELECT sales.customer_id, MAX(order_date)
  FROM sales LEFT JOIN members
  ON sales.customer_id = members.customer_id
  WHERE sales.order_date < members.join_date
  GROUP BY sales.customer_id)
 GROUP BY customer_id, order_date
 ORDER BY customer_id
```

**Approach:**
* Select the latest record from before the members’ join date
* Combines multiple orders into one cell for readability

--------
### 8. Total Items and Expenditure Before Membership
**What is the total items and amount spent for each member before they became a member?** 
<table>
  <tr>    
    <td>
      <table>
        <tr>
          <th>customer_id</th>
          <th>items</th>
          <th>amount</th>
        </tr>
        <tr>
          <td>A</td>          
          <td>2</td>
          <td>25</td>
        </tr>
        <tr>                  
          <td>B</td>          
          <td>3</td>
          <td>40</td>
        </tr>        
      </table>
    </td>
    <td><ul>
      <li>Customer A bought 2 items and spent $25 before becoming a member</li>
      <li>Customer B bought 3 items and spent $40</li>
      <li>Customer C is currently not a member</li>
    </ul></td>
  </tr>
</table>

**Code:**
```sql
SELECT sales.customer_id, 
	COUNT(*) as items,
	SUM(menu.price) as amount
FROM sales LEFT JOIN members
ON sales.customer_id = members.customer_id
JOIN menu ON sales.product_id = menu.product_id
WHERE sales.order_date < members.join_date
GROUP BY sales.customer_id
ORDER BY sales.customer_id
```

**Approach:**
* Filter to records from before members’ join date
* Group by their purchases from the sales table via customer-id
* Counted the number of items purchased and summed the amount each member spent







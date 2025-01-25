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









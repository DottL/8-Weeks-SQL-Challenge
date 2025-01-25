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
- [Total Expenditure per Customer](#what-is-the-total-amount-each-customer-spent-at-the-restaurant)
- [Customer Visit Frequency](#how-many-days-has-each-customer-visited-the-restaurant)
- [First Menu Item Purchased](#what-was-the-first-item-from-the-menu-purchased-by-each-customer)
- [Most Purchased Menu Item](#what-is-the-most-purchased-item-on-the-menu-and-how-many-times-was-it-purchased-by-all-customers)
- [Most Popular Item per Customer](#which-item-was-the-most-popular-for-each-customer)
- [First Purchase After Membership](#which-item-was-purchased-first-by-the-customer-after-they-became-a-member)
- [Last Purchase Before Membership](#which-item-was-purchased-just-before-the-customer-became-a-member)
- [Total Items and Expenditure Before Membership](#what-is-the-total-items-and-amount-spent-for-each-member-before-they-became-a-member)
- [Hypothetical Point Program](#if-each-1-spent-equates-to-10-points-and-sushi-has-a-2x-points-multiplier-how-many-points-would-each-customer-have)
- [Expanded Point Program](#in-the-first-week-after-a-customer-joins-the-program-including-their-join-date-they-earn-2x-points-on-all-items-not-just-sushi-how-many-points-do-customer-a-and-b-have-at-the-end-of-january)



-------

## Solutions

### What is the total amount each customer spent at the restaurant?
| customer_id | total_amount |
| ----------- | ------------ |
| A           | 76           |
| B           | 74           |
| C           | 36           |

* Customer A spent $76
* Customer B spent $74
* Customer C spent $36

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
* Group by customer id to sum up their products’ prices

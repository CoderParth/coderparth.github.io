---
layout: post
title:  "N+1 Problem in Simple Words"
date:   2025-12-29
---

In simple words, the **N+1 query problem** is a situation where too many queries are being performed, and which could have been solved by making a **single query**. 

A simple example: 
- In the first step, you execute a query to get a list of **users**.  
- In the second step, you make individual queries to get a list of **orders** for each single user.  

Situations like these are exactly where **N+1 query problems** occur. You may think that making many small queries would be faster than making a single large query. **However, the opposite is the truth:** making a large single query is usually faster than many small queries. 

An oversimplified code for the above example in **Go (with PostgreSQL)** can be found below:

**Bad Example:**
```go
// Bad way to make queries; results in N+1 query problem.
func getOrdersForEachUser() ([]CustomerOrder, error) {
	var customerOrders []CustomerOrder
	// Get all the users, and their info.
	userRows, err := db.Query("SELECT * FROM users;")
	if err != nil {
		return nil, err
		// NOTE: Handle errors gracefully in your code. This function is only an oversimplified example.
	}
	defer userRows.Close()
	// For each row get the user id, and query for all the orders of that user.
	for userRows.Next() {
		var u User
		if err := userRows.Scan(&u.User_id, &u.Name, &u.Email); err != nil {
			return nil, err
			// NOTE: Handle errors gracefully in your code. This function is only an oversimplified example.
		}
		// Get all the orders of that user.
		orderRows, err := db.Query("SELECT * FROM orders WHERE customer_id = $1;", u.User_id)
		if err != nil {
			return nil, err
			// NOTE: Handle errors gracefully in your code. This function is only an oversimplified example.
		}
		c := CustomerOrder{
			User:   u,
			Orders: []Order{},
		}
		defer orderRows.Close()
		for orderRows.Next() {
			var o Order
			if err := orderRows.Scan(&o.Order_id, &o.Customer_id, &o.Order_date); err != nil {
				return nil, err
				// NOTE: Handle errors gracefully in your code. This function is only an oversimplified example.
			}
			c.Orders = append(c.Orders, o)
		}
		customerOrders = append(customerOrders, c)
	}
	return customerOrders, nil
}
```
The above example shows **a bad approach**, leading to N+1 problem. 

## The Better Approach
A better approach would be to use a single query. In this case, a `LEFT JOIN` operation could easily solve this with a single sql query. 

There are multiple ways to do this too, this is just one of the ways: 

```sql
SELECT u.user_id, u.name, u.email, o.order_id, o.order_date 
FROM users u 
LEFT JOIN orders o 
ON u.user_id = o.customer_id;
```

One of the ways to do this with an oversimplified example in **Go (with PostgreSQL)** can be found below:

```go
// One of the good ways to do this: using a single SQL query.
func getOrdersForEachUser() ([]CustomerOrder, error) {
	customerOrdersMap := make(map[int]*CustomerOrder)
	// Make a single query.
	rows, err := db.Query(`
	SELECT u.user_id, u.name, u.email, o.order_id, o.order_date 
	FROM users u 
	LEFT JOIN orders o 
	ON u.user_id = o.customer_id;`)

	if err != nil {
		return nil, err
		// NOTE: Handle errors gracefully in your code. This function is only an oversimplified example.
	}
	defer rows.Close()

	for rows.Next() {
		var u User
		var o Order
		if err := rows.Scan(&u.User_id, &u.Name, &u.Email, &o.Order_id, &o.Order_date); err != nil {
			return nil, err
			// NOTE: Handle errors gracefully in your code. This function is only an oversimplified example.
		}
		// Check if this is the first time adding this user to the customerOrdersMap.
		if _, exists := customerOrdersMap[u.User_id]; !exists {
			customerOrdersMap[u.User_id] = &CustomerOrder{
				User:   u,
				Orders: []Order{},
			}
		}
		customerOrdersMap[u.User_id].Orders = append(customerOrdersMap[u.User_id].Orders, o)
	}

	customerOrders := []CustomerOrder{}
	for _, co := range customerOrdersMap {
		customerOrders = append(customerOrders, *co)
	}
	return customerOrders, nil
}
```

**Conclusion**

Writing your code such that you have a single query that returns all the data you need, instead of executing multiple small queries, can make your reads significantly faster and more efficient. 

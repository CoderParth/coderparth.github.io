---
layout: post
title:  "Different Types of Tests and their Implementation Example in Go"
date: 2026-01-02
---

**NOTE:** The aim of this post is to explain common types of tests in the simplest and easiest words, without diving too deep. 

## Unit test
A unit test is a way of testing where the smallest unit of code is tested to see if it works correctly. This smallest part is usually a function, a method, or a code block. 

Below is an oversimplified example of a unit test in Go. 

Here, we have a function `add`, and two corresponding unit tests: (1) `TestAddPositive`, and (2) `TestAddNegative`.

```go
package main

import "testing"

func add(x, y int) int {
	return x + y
}

func TestAddPositive(t *testing.T) {
	sum := add(2, 3)
	if sum != 5 {
		t.Errorf("\nExpected: %v \nReceived: %v \n", 5, sum)
	}
}

func TestAddNegative(t *testing.T) {
	sum := add(-2, -3)
	if sum != -5 {
		t.Errorf("\nExpected: %v \nReceived: %v \n", 5, sum)
	}
}
```

Unit tests are a great way to quickly pinpoint to the exact part of the code, when an error occurs, especially during testing. It also helps to quickly catch regression-based bugs, which are bugs that are produced from recent code changes. 


## Integration test
Integration testing is a way of testing where different components are integrated and tested to see if it works correctly. 

Example: Let us say you have 2 functions:  
(1) `CreateOrder` function that creates an order.  
(2) `SaveOrder` function that saves the order to your database. 

If you integrate them, and then test them together, that is an integration test. Below is an oversimplified example to demonstrate this:

```go
type Order struct {
	Name  string
	Price float64
}

// A slice of Orders to simulate a database.
var orders []Order

// createOrder creates an instance of an Order.
func createOrder() Order {
	return Order{
		Name:  "Headphone",
		Price: 50.00,
	}
}

// saveOrder appends it to the 'orders' slice.
// It simulates a database for this example.
func saveOrder(o Order) {
	orders = append(orders, o)
}

func TestCreateAndSaveOrder(t *testing.T) {
	order := createOrder()
	saveOrder(order)

	if len(orders) < 0 {
		t.Errorf("Order did not get saved correctly.")
	}

	if orders[0] != order {
		t.Errorf("\nExpected: %v \nReceived: %v \n", order, orders[0])
	}
}
```

## End-to-End Test

The end-to-end test is best understood with an example. Imagine, you have a Login webpage for your website, and you want to test it. Here, in this case, the end-to-end test will be written in a way such that login details are automatically filled, and the login button is automatically clicked. In other words, the interaction with that webpage is done just like a real user. Furthermore, the end-to-end test will ensure that interaction with the error messages, responses, url redirects, database updates, etc are all done correctly, and automatically. 

To achieve this kind of testing, tools like **“Selenium”** are commonly used. **“Selenium”** can drive a web browser natively, just like a user would.  

Below is an official link from Selenium to get you started:  
[https://www.selenium.dev/documentation/webdriver/getting_started/](https://www.selenium.dev/documentation/webdriver/getting_started/)


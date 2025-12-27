---
layout: post
title:  "What is a REST API, and how to design it correctly?"
date:   2025-12-27 
---

**NOTE**: The goal of this blog post is to explain REST API in easy to understand words, like a student’s notes, without diving too deep.

**What is REST?**  
REST stands for Representational State Transfer; it is a set of rules for designing web APIs where data is treated as resources, and HTTP Methods like GET, PUT, POST, and DELETE are used to access them.

**What is an API?**  
API stands for Application Programming Interface; it is a way through which different systems can communicate with each other.  API lets you share your resources while maintaining security and control.

For RESTful APIs, `JSON` is usually used as a file format to transfer the information from client to server, and vice versa. This allows RESTful APIs to provide platform independence.

In REST APIs, HTTP methods are used to perform the fundamental data management operations: CRUD (Create, Read, Update, and Delete). Corresponding HTTP methods that are used to perform those operations are mentioned below:

| Operation | HTTP Method |
|---------|-------------|
| Create  | POST request |
| Read    | GET request |
| Update  | PUT request |
| Delete  | Delete request |

**Designing RESTful APIs correctly:**

REST APIs are designed around resources (data), and each resource is represented by a `URI (Uniform Resource Identifier)` that uniquely identifies that resource.

Example of a particular user:
```
https://api.bypress.com/users/1
```

**Follow the guidelines mentioned below to correctly design APIs:**

- ***Name URIS on `nouns` (the resource) and not on verbs (the operations on the resource).***

	The HTTP methods such as GET, POST, PUT, DELETE, etc imply the verbal action. That is why, adding operation names to URIs are unnecessary.
	
	Correct and good examples:
	```
	https://api.bypress.com/accounts  // Good
	https://api.bypress.com/reactions // Good
	https://api.bypress.com/reminders // Good
	```
	
	Avoid representing operations, and avoid using verbs in URIs. Bad examples that you should avoid:
	```
	https://api.bypress.com/get-accounts    // Bad
	https://api.bypress.com/post-reaction   // Bad
	https://api.bypress.com/remove-reminder // Bad
	```

- ***Plural nouns should be used to name collection URIs.***

	Examples:
	```
	/accounts // Good
	/users    // Good
	/orders   // Good
	```

- ***Avoid URIs that include several levels of relationships.***

	Example:
	```
	/accounts/1/posts/22/comments // Avoid this: this is bad.
	```
	
	The example mentioned above is hard to maintain. Instead, follow these steps:

	1. Get all the posts for account 1.
	```
	/accounts/1/posts
	```
	
	2. Then, get all the comments for that post.
	```
	/posts/22/comments
	```
	

- ***Avoid creating a large number of APIs that respond with smaller resources.*** 

	In other words, have a smaller number of APIs. This small set of APIs should combine related information, create a bigger resource, and then return them via a single request. But be careful not to take it to its extreme, and keep it balanced, as unnecessary fetching will increase latency and bandwidth costs.
	
	Bad Example:
	```
	/users/1
	/users/1/address
	/users/1/contact
	```
	
	Instead of creating 3 different APIs to get basic user information, you should return all these details through a single API:
	```
	/users/1
	```

- ***Implement data pagination and filtering:***

	**Data pagination:**
	- In RESTful APIs, pagination is a technique of dividing large datasets into smaller chunks.
	- To achieve this, query parameters like `limit` (to specify the number of items to return), and `offset` (to specify the starting index) are generally used.
	
		Example:
		```
		GET /posts?limit=10&offset=5
		```
		
		Note: Implement `max-limit` to prevent server overload.
	
	**Filtering:**
	- Filtering allows clients to request only the data that they are interested in.
	
		Example:
		```
		GET /posts?status=hidden&edited=true
		```
	
	These techniques improve performance, and allow clients to retrieve only the subset of data that they need.

- ***Implement API versioning:***
	- API versioning allows you to update/change your API over time without breaking existing clients.
	- One of the most common ways to achieve this is through URI versioning: adding `v1, v2, v3, etc` to your URI.

		Examples:
		```
		https://api.bypress.com/v1/accounts
		https://api.bypress.com/v2/accounts // Respond with different resources.
		https://api.bypress.com/v3/accounts // Respond with different resources.
		```


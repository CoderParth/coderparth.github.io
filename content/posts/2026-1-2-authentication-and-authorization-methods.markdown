---
layout: post
title:  "Authentication and Authorization Methods"
date: 2026-01-02
---


# Basic HTTP Authentication Method

In this method:  

1. The client sends the word **“Basic”** followed by the Base64-encoded `<username>:<password>` in the standard **Authorization** header.  
2. The server then matches that username and password against the credentials stored on the server/db.  
3. If the credentials match, access to the resource is allowed, else, access is denied.

In **Basic HTTP Authentication**, each request follows the same methods mentioned above - each request contains ``Basic <username>:<password>`` in the authorization header.  

Since this request is sent in Base64-encoded plain text, it is highly vulnerable to security attacks. Although, using **HTTPS** makes it at least usable, it is still considered a very weak authentication method, as **HTTPS** keeps the data encrypted only in transit, not in other places where it is decrypted, such as web browsers, servers, reverse proxies, etc.  

# Bearer-Token-Based Authentication Method

This method is also a part of the **Basic HTTP Authentication** scheme. In this method:  

1. The client sends a login request, with a username and a password (in the request body).  
2. Server generates a string (a hexadecimal character string, or a **JWT token** - which has been written about in the next section).  
3. The client then uses this new string (called **Token**) every time to make authenticated requests - unlike **Basic HTTP**, where username and password were sent in the **Authorization Header** in each request.  

The generated token is sent in this format in the authorization header:  
```
Authorization: Bearer <token>
```


# JWT

**JWT (JSON Web Token)** is a cryptographically signed JSON object that contains information about the user. **JWT** is commonly used as a bearer token in **Bearer Token Based authentication method**. Using **JWT**, the server can verify the integrity of the request, and get the information about the user.  

**NOTE:** `JWT is not an encryption, it is a digitally signed token. ` 

Because **JWTs** are signed, we can be sure that the senders are who they claim to be, and verify that the content has not been changed.  

## Structure of JWT

**JWT** consists of 3 parts, separated by dots:  

```
Header.Payload.Signature
```

### Header

The header consists of two parts: the type of token (which is **JWT**), and the signing algorithm that is being used, such as **HMAC SHA256** or **RSA**.  

**Example:**  
```
{
    “alg”: “HS256”,
    “typ”: “JWT”
}
```

### Payload

Payload is where the main data lives. It generally contains information about a user, and the expiration time for that token. This information/data is also called as **claims**.  

**Example:**  
```
{
  "sub": "1234567890", 
  "name": "John Doe",
  "admin": true,
  "iat": 1751494086,
  "exp": 1751497686
}
```

In the above example:  
- **“sub”** stands for subject, and it is commonly set to User ID or some unique data of a user.  
- **“iat”** is the time the token was issued at.  
- **“exp”** is the expiration time of that token.  

### Signature

The signature part is created by taking the encoded header, encoded payload, a secret, the algorithm specified in the header, and then signing it.  

Here, a **secret** is like a password that only the server knows.  

**Example:**  
If you used **HMAC SHA256** algorithm, the creation of the signature will look like this:  

```
HMACSHA256(
    base64UrlEncode(header) + “.” + base64UrlEncode(payload), secret)
```
# JWT Example Output

This will generate three Base64-URL strings separated by dots. It will look something like this for the example set above:  

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWUsImlhdCI6MTc1MTQ5NDA4NiwiZXhwIjoxNzUxNDk3Njg2fQ.XLD0Tg9wzbv3Y3XgjL3fHpsXlIqCWeoNsiYPvyzVnFk
```

Below is an oversimplified example of generating and validating **JWT** in **Go**, using `golang-jwt` package.  

## Generating a JWT in Go


```go
func generateJWT() (string, error) {
	// Create a new token with the specified signing method and claims.
	token := jwt.NewWithClaims(jwt.SigningMethodHS256, // This is the Header (the signing method, and the token type)
		jwt.MapClaims{ // This is the Payload Section.
			"sub":   "1234567890", // These are the claims about an entity - the user.
			"name":  "John Doe",
			"admin": true,
			"iat":   time.Now().Unix(), // These are the metadata of the token (issue date and expiration time).
			"exp":   time.Now().Add(5 * time.Minute).Unix(),
		})

	// Convert the token into a string format.
	tokenString, err := token.SignedString([]byte(JWT_SECRET)) // JWT_SECRET is loaded as an environment variable.
	if err != nil {
		return "", err
	}
	return tokenString, nil
}
```

## Parsing and Validating a JWT in Go

An oversimplified example of a function for parsing and validating a JWT in Go, using `golang-jwt`:  

```go
func parseAndValidateJWT() (jwt.MapClaims, error) {
	// Token string generated from the previous generateJWT() function.
	tokenString := "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhZG1pbiI6dHJ1ZSwiZXhwIjoxNzY3MjY0MjkwLCJpYXQiOjE3NjcyNjM5OTAsIm5hbWUiOiJKb2huIERvZSIsInN1YiI6IjEyMzQ1Njc4OTAifQ.qR3WwHotE4u3PrZLCRCkMVsvIOn9rcPW5sLPHdGVbRU"

	// Parse, validate, and verify the signature
	// jwt.Parse takes a token string, and a keyFunc as arguments.
	// The keyFunc should return the cryptographic key, which was used for signing.
	token, err := jwt.Parse(tokenString, func(token *jwt.Token) (any, error) {
		return []byte(JWT_SECRET), nil // JWT_SECRET is loaded as an environment variable.
	}, jwt.WithValidMethods([]string{jwt.SigningMethodHS256.Alg()})) // Validate if 'alg' claimed in the token matches the expected algorithm.

	if err != nil {
		return nil, err
	}

	claims, ok := token.Claims.(jwt.MapClaims)
	if ok {
		return claims, nil
	}
	return nil, fmt.Errorf("Error extracting claims")
}
```
# OAuth - An Authorization Method

OAuth can be understood with this simple problem scenario: Imagine you want to let a photo editor app get access to your facebook’s profile picture. However, you certainly do not want to provide your login credentials to that photo editor app. **OAuth** comes in to solve problems like these: providing authorization to resources without handing in user credentials.  

## Getting started with OAuth

All **OAuth providers** such as Github, Google, and Facebook issue a **client ID** and **client secret** to use their OAuth service. As a developer, you need those to get started. Follow the documentation provided on their website to get them.  

For example, to get started with using Google’s OAuth service, you can follow this documentation:  
[Google OAuth 2.0 Documentation](https://developers.google.com/identity/protocols/oauth2)  

## Understanding Implementation with an Example

For an example, let us suppose we have 3 routes: `/login`, `/callback`, `/profile`.

1. **/login:** Here, using our **client ID** and **secret**, we can generate a unique OAuth URL, and redirect the user to that URL - which will take them to the OAuth Provider’s website. While creating the redirect OAuth URL, two important variables `state` and `verifier` are created, which are later used in the `callback` to stay protected from CSRF attacks. 

2. **/callback:** After successful authentication and authorization, the user can be redirected to this route. Here, we can process the redirected callback, and create an account for that user.  

3. **/profile:** This is our protected resource. After a successful account creation in `/callback`, the user can be redirected here.  

In the example, we have set, after a successful login with the OAuth provider, the user is redirected to `/callback` URL. The redirect URL contains a ``code`` parameter, which can be used to get an **Access Token** from the OAuth provider, which then canb be used to get the user information from the OAuth provider.  

**NOTE:** In OAuth protocol, there are 2 access types. When the access type is set to ``offline``, a **Refresh Token** is provided in addition to the **Access Token**. This **Refresh Token** can be used to generate a new **Access Token** without re-authenticating the user.  

**NOTE:** To reduce chances of CSRF attack - and to make sure that the browser that started the login is the one finishing it - you can pass a ``state`` variable while generating a unique OAuth URL for that user. Then, after a successful login, when the user is returned to `/callback` (you can have a different URL to handle callback, this is what we have set in our example), you can check if the sent state matches the ``state`` that you created. ``state`` is generally a random string.  


# OAuth in Go Example

The following example is implemented in **Go**, with the official OAuth2 implementation package ``golang.org/x/oauth2``, and the OAuth2 provider package from Google ``golang.org/x/oauth2/google``.  

Code comments have been added to explain each section/part, rather than using separate paragraphs for explanation.  

**NOTE:** You will need to add ``http://localhost:8080/auth/callback`` to your **Authorized redirect URIs** section for your project in Google console.  

Below is an oversimplified way to get the user’s details through **OAuth**, and **Google OAuth API**. It is a `single function` that creates an OAuth URL, and prints it to the terminal. You can then copy the URL and paste it to your browser, which will take you to Google's consent and permission page. After successful consent, you will be forwarded to the `/callback` page, which in this case, will fail, as we have not set up a server yet. From the `/callback` URL that failed in your browser, copy the value of the ``code`` query, and paste it to the terminal. This code will be used to request a token from the OAuth provider (in this case Google). Once the token is received, a request is made to a Google API endpoint to retrieve the public details of the user.  

**NOTE:** The function is meant to show the process in which **OAuth** works, and does not include any actual routes or server implementation.  


```go
func initGoogleOauthConfig() {
	ctx := context.Background()
	// Prepare Config with the information about our server, and OAuth provider.
	conf := &oauth2.Config{
		ClientID:     os.Getenv("CLIENT_ID"),
		ClientSecret: os.Getenv("CLIENT_SECRET"),
		RedirectURL:  "http://localhost:8080/auth/callback",
		Scopes:       []string{"email", "profile"},
		Endpoint:     google.Endpoint,
	}

	// use PKCE to protect against CSRF attacks
	// https://www.ietf.org/archive/id/draft-ietf-oauth-security-topics-22.html#name-countermeasures-6
	//
	// Generate a PKCE code verifier with 32 octets of randomness.
	verifier := oauth2.GenerateVerifier()

	// Create an unique OAuth URL, and redirect user to ask for permission, and consent
	// for the scopes specified above.
	//
	// Below, we are using "state" as the state variable parameter, as an example.
	// Always use a random string in real cases.
	//
	// For the second parameter of AuthCodeURL, we are using AccessTypeOffline to obtain
	// a refresh token, which can then be used to regenerate access tokens.
	//
	// The third argument takes the PKCE verifier that we create above, hashes it using SHA-256,
	// encodes it as Base64 URL. So, the url will contain something like this:
	// &code_challenge=G0XPRtz3fyPcN14e0TmX-Ct2-ZMW_cMssOyoHJ3GBPA
	//
	authUrl := conf.AuthCodeURL("state", oauth2.AccessTypeOffline, oauth2.S256ChallengeOption(verifier))
	fmt.Printf("Visit the URL for the auth dialog: %v", authUrl)

	fmt.Println("")
	fmt.Print("Please copy and paste the code from the redirect URL in the browser: ")

	// Copy the code from the redirect URL in the browser.
	var enterCode string
	// Wait for the user to type in code in the terminal
	if _, err := fmt.Scan(&enterCode); err != nil {
		log.Fatal(err)
	}

	code, err := url.QueryUnescape(enterCode) // decode %2F → /
	if err != nil {
		log.Fatal("Failed to decode code:", err)
	}

	// Send a request to the OAuth provider for the token.
	// Since, we are using PKCE, we have included a VerifierOption as the third argument.
	tok, err := conf.Exchange(ctx, code, oauth2.VerifierOption(verifier))
	if err != nil {
		log.Fatal(err)
	}

	// Attach access token to all the requests that are going to be made.
	client := conf.Client(ctx, tok)

	// Get the user public details from google API endpoint
	resp, err := client.Get("https://www.googleapis.com/oauth2/v2/userinfo")
	if err != nil {
		log.Fatalf("Error: %v \n", err)
	}

	defer resp.Body.Close()

	var v any

	// Read the JSON body using JSON decoder
	err = json.NewDecoder(resp.Body).Decode(&v)
	if err != nil {
		log.Fatalf("Error: %v \n", err)
	}

	// Print all the details of the user
	// (for demonstration only)
	fmt.Printf("%v", v)
}
```




# HTTP & REST APIs

---

## What is JSON and Why Is It Used?

**What is JSON (JavaScript Object Notation)?**

JSON is a lightweight, text-based format for storing and exchanging data, using key–value pairs. It’s language-independent but based on JavaScript syntax.

**How does it differ from regular JavaScript objects?**

- The syntax of JSON slightly differs from Javascript objects.

- JSON is in text format and JS object is an in-memory data structure.

- JSON doesn't include methods and executable logic whereas JS objects include.

- JSON requires Parsing and JS objects can be used directly.

**Why is JSON used so widely in APIs instead of formats like XML or plain text?**

- Lightweight → Smaller than XML.

- Easy to read/write → Human and Machine friendly.

- Language-independent → Works in almost all programming languages.

- Structured → Can represent complex nested data.

- Native to JS → JSON’s syntax is based on JavaScript object notation, so browsers (which run JavaScript) can easily understand it.

---

## Explore a Public REST API

### For GET request

- The HTTP method used : GET
- The request URL : https://jsonplaceholder.typicode.com/posts
- The status code of the response : 200 OK
- The response body (JSON):

```json
[
  {
    "userId": 1,
    "id": 1,
    "title": "sunt aut facere repellat provident occaecati excepturi optio reprehenderit",
    "body": "quia et suscipit\nsuscipit recusandae consequuntur expedita et cum\nreprehenderit molestiae ut ut quas totam\nnostrum rerum est autem sunt rem eveniet architecto"
  }
]
```

- Any headers you sent or received : Content-Type:application/json

### For POST request

- The HTTP method used : POST
- The request URL : https://jsonplaceholder.typicode.com/posts
- The status code of the response : 201 Created
- The request body (JSON):

```json
{
  "title": "My New Post",
  "body": "This is a test post",
  "userId": 1
}
```

- The response body (JSON):

```json
{
  "title": "My New Post",
  "body": "This is a test post",
  "userId": 1,
  "id": 101
}
```

- Any headers you sent or received : Content-Type:application/json

---

## Reflect on What You Observed

**What is the difference between GET, POST, PUT, and DELETE? Give real-world use cases.**

Create - POST (Create new data) Eg.: Add a new product.

Read - GET (Retrieve the data) Eg.: Show me all products.

Update - PUT (Update/replace existing data) Eg.: Change product #5’s details.

Delete - DELETE (Remove the data) Eg.: Delete product #5.

**What are the major categories of HTTP status codes, and why do they matter? List examples you encountered.**

Major categories of HTTP status codes are:

- 1xx : Informational
- 2xx : Successful
- 3xx : Redirection
- 4xx : Client Error
- 5xx : Server Error

These status code categories helps us to understand the type of response. Makes Debugging faster. Improves the Client-Server communication.

Examples that I have encountered are: 200 - OK, 302 - Found, 403 - Forbidden, 404 - Not Found, 503 - Service unavailable.

**What are headers in HTTP? What’s one header you observed during your API testing, and why is it important?**

Headers in HTTP are key–value pairs sent along with a request or response to share extra information about it.

One header I observed during my API testing is **Content-Type** Header. Eg: _Content-Type: application/json_

The Content-Type header identifies the media type of the content in the request body. For instance, Content-Type: application/json indicates that the request body contains JSON data. This information helps the server successfully interpret and process the payload.

---

# Key Takeaways

**How APIs work**

APIs work like a messenger between two systems:

- You send a request → Specify the API endpoint (URL), method (GET/POST/PUT/DELETE), and optional data.

- The API processes it → The server reads your request, performs the needed action (fetch, store, update, delete).

- You get a response → The API sends back data (often in JSON) along with a status code.

**What’s important in an HTTP request**

An HTTP request usually has these important parts:

- Method → Action type (GET, POST, PUT, DELETE).

- URL/Endpoint → The address of the resource.

- Headers → Extra info like Content-Type, authentication tokens.

- Body (optional) → Data you send (mainly in POST/PUT).

- Parameters → Query strings or path variables to filter or specify data.

**How understanding HTTP helps you as a developer**

- Knowledge about status codes, headers and requests helps in debugging.

- Connect frontends, backends, and third-party services.

- Know how to design endpoints, methods, and responses.

---

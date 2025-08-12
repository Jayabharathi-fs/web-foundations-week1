# CORS & Preflight Requests

## What is CORS and why it exists

CORS (Cross-Origin Resource Sharing) is a browser security mechanism that controls which websites can request resources (like APIs) from another domain.

### Explain from a security perspective (how it protects users).

Browsers follow the Same-Origin Policy — JavaScript running on one site can’t freely read responses from another site unless allowed. Without this rule, malicious websites could silently steal sensitive user data.

CORS prevents unauthorized reading of sensitive information across sites.

---

## What is an Origin

### Define origin as scheme + host + port.

An origin is the unique identity of where a web page or resource comes from, defined as:

origin = scheme (protocol) + host (domain) + port

### Give examples of same-origin vs cross-origin.

- Same-Origin

  Two URLs have the same origin only if all three parts match exactly.

        Examples:

            https://example.com:443/page1
            https://example.com:443/page2

- Cross-Origin

  If any part (scheme, host, or port) differs, it’s cross-origin.

        Examples:

            Different scheme: http://example.com vs https://example.com
            Different host: https://example.com vs https://api.example.com
            Different port: https://example.com:443 vs https://example.com:8080

---

## What is a Cross-Origin Request

A cross-origin request is any HTTP request from one origin (scheme + host + port) to a different origin.

### When does a request count as cross-origin?

It’s cross-origin if any of these differ between the page’s origin and the requested resource’s origin:

1. Scheme (e.g., http vs https)

2. Host (e.g., example.com vs api.example.com)

3. Port (e.g., 443 vs 8080)

---

## Simple Requests vs Preflighted Requests

### Define a “simple” request.

- A simple request in CORS is an HTTP request that meets specific criteria defined by the CORS standard so that the browser can send it directly to the server without asking for permission first.

- For a request to be simple, it must use one of the allowed HTTP methods (GET, POST, or HEAD), only include “simple” headers (Accept, Accept-Language, Content-Language, and Content-Type with the value application/x-www-form-urlencoded, multipart/form-data, or text/plain), and not use any custom headers or credentials unless specifically allowed.

### Define a “preflighted” request.

- A preflight request is a safety check the browser performs before sending certain cross-origin requests that could have side effects or higher security risks. This preflight check is done using an HTTP OPTIONS request sent to the server before the actual request.

- Preflights are triggered when requests use methods other than GET, POST, or HEAD, when they use non-simple headers, or when Content-Type is something other than the three “simple” types (like application/json).

---

## What is a Preflight OPTIONS Request

A preflight OPTIONS request is a special HTTP request the browser automatically sends before making certain cross-origin requests, to check if the target server allows them.

### What it is, and when the browser sends it.

- A Preflight OPTIONS Request is an HTTP request with the OPTIONS method that a web browser automatically sends to a server before making certain "complex" cross-origin HTTP requests.

- A preflight happens only if the request is NOT a “simple request” under CORS rules. That means it occurs when:

  - You use HTTP methods other than GET, POST, or HEAD.
  - You send non-simple headers (e.g., Authorization, X-Custom-Header).
  - You use a Content-Type other than:
    - application/x-www-form-urlencoded
    - multipart/form-data
    - text/plain

---

## What Triggers a Preflight

### Examples: custom headers, non-simple HTTP methods, non-simple content types.

Preflight requests are generally triggered by requests that are not simple GET, POST, or HEAD requests, or when specific conditions are met, such as using custom headers or certain Content-Type values. This adds a layer of security by ensuring that servers explicitly grant permission for potentially sensitive cross-origin operations.

- custom headers: Authorization, X-Custom-Header
- non-simple HTTP methods: PUT, DELETE, PATCH, or OPTIONS
- non-simple content types: application/json, application/xml

---

## Key CORS Response Headers

### Access-Control-Allow-Origin

- Purpose: Tells the browser which origin is allowed to access the resource.

- Value:

  - Specific origin → Access-Control-Allow-Origin: https://myapp.com

  - Any origin → Access-Control-Allow-Origin: \* (but not for requests with credentials)

### Access-Control-Allow-Methods

- Purpose: Lists the HTTP methods allowed for cross-origin requests.

- Used in: Preflight (OPTIONS) responses.

- Example:

  - Access-Control-Allow-Methods: GET, POST, PUT

### Access-Control-Allow-Headers

- Purpose: Lists which request headers can be used in the actual request.

- Used in: Preflight responses when the request has custom headers.

- Example:

  - Access-Control-Allow-Headers: Content-Type, Authorization

### Access-Control-Allow-Credentials (and rule against using \* with credentials)

- Purpose: Allows cookies, authorization headers, or TLS client certs to be sent in cross-origin requests.

- Value: true (must be exact string)

- Rule: You cannot use Access-Control-Allow-Origin: \* with credentials; you must specify an explicit origin.

- Example:

  - Access-Control-Allow-Credentials: true

---

## Why it works in Postman or curl but not in the browser

It works in Postman or curl but not in the browser because of CORS and who enforces it.

- CORS is a browser-only security rule
- Postman and curl don’t enforce CORS

### Explain the role of the browser in enforcing CORS, and why API clients like Postman or curl can bypass these restrictions.

- CORS (Cross-Origin Resource Sharing) is not a server-side security mechanism — it’s a browser-enforced security policy. Browsers enforce the Same-Origin Policy, which blocks JavaScript on one origin from making requests to another origin unless the target server explicitly says it’s okay via CORS headers (Access-Control-Allow-\*). This protects users from malicious websites that could run JavaScript to secretly read data from other sites where the user is logged in.

- Tools like Postman or curl are not browsers — they are API clients. They do not have a same-origin policy. They send HTTP requests directly and return responses exactly as received, without checking or enforcing Access-Control-Allow-\* headers. This is why you can successfully call an API in Postman but the same request fails in the browser.

---

# Practical Investigation

## Case 1: Simple GET – No preflight, allowed

- The browser sent a direct GET request to https://jsonplaceholder.typicode.com/posts/1 without an OPTIONS preflight.

- GET is a “simple” method. No custom headers or non-simple content type.

- Request succeeded because the API’s CORS policy allowed Access-Control-Allow-Origin: \* for simple cross-origin GETs.

## Case 2: Simple POST (form-encoded) – Avoids preflight, allowed

What happened: The browser sent a direct POST with Content-Type: application/x-www-form-urlencoded.

- POST is allowed as a “simple” method only if the content type is one of the allowed simple types (application/x-www-form-urlencoded, multipart/form-data, or text/plain). No custom headers.

- No OPTIONS request, request succeeded because the server’s CORS policy allowed it.

## Case 3: Non-simple POST (JSON) – Triggers preflight, allowed

- Browser first sent an OPTIONS request (preflight) before the main POST.

- Content-Type: application/json is a non-simple content type. Non-simple requests always trigger preflight.

- Server responded to the OPTIONS request with correct CORS headers, so the POST proceeded and succeeded.

## Case 4: Preflight + credentials – Blocked by CORS policy

- Access-Control-Allow-Origin: \* cannot be used when Access-Control-Allow-Credentials: true.

- With credentials: 'include', the origin must be explicit. Public APIs (like jsonplaceholder) often ignore credentials, so the browser won’t show a block unless credentials actually matter to the server.

- Testing with your own backend helps reproduce & understand CORS errors.

---

# Real-World CORS Problem – Learning from a Developer

## Problem:

While experimenting, she attempted to connect her frontend to the backend API and send requests that included cookies (credentials). However, the browser blocked the requests with a CORS error because the backend responded with Access-Control-Allow-Origin: \*, which is not permitted when credentials are involved.

## Solution Taken:

The backend configuration was changed to return the specific frontend origin instead of \* and to include Access-Control-Allow-Credentials: true. With these adjustments, the browser accepted the requests, and the frontend–backend integration worked successfully.

## My Takeaway:

CORS is a browser-enforced security mechanism that controls which websites can make requests to the backend and access the responses. When sending cross-origin requests with credentials (cookies, HTTP authentication, or client-side SSL certificates), the server cannot use Access-Control-Allow-Origin: \*. Instead, it must explicitly return the requesting origin in Access-Control-Allow-Origin and set Access-Control-Allow-Credentials: true.

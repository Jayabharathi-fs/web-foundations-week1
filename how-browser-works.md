# The Journey of a URL: From Address Bar to Page Render

This article contains my understanding of how the web works, from entering a URL in the Address Bar to Page render.

## What happens when a user types a URL and presses Enter

When the user types a URL (https://www.example.com) and press enter, a multistep process will occur to render the web page. And the steps are

- resolving domain name to an IP address
- connecting the client with server
- sending a request
- receiving a response
- rendering and displaying the page

## How the domain name is resolved to an IP address (DNS)

DNS resolution - translates human-readable domain name like google.com into numerical ip addresses like 172.217.160.142, handled by Domain Name System (DNS). When user types domain name into the browser, browser send the request to DNS resolver.

- first it checks for the ip in DNS Cache
- if not found, it initiates a recursive query to the root name server
- From root server, it looks in TLD server, and in Authoritative server.
  - DNS Resolver -> Root Name Server -> TLD Server -> Authoritative Name Server
- Then authoritative server responds with the ip address of the domain name.

## How the browser connects to the server securely (HTTPS)

- The browser connects to the server by a TCP Connection.
- The TLS Handshake begins in which it verifies the server's SSL certificate and exchange encryption keys securely.
- After this, the HTTPS connection is established.
- Now all the data exchanged in it is encrypted.

## How the response is received and parsed

- The browser sends an HTTP request like GET, POST, PUT, DELETE to the server.
- The server receives the request and give a HTTP response to the client.
- The web page contains HTML content, Links to CSS and other resources which is parsed by the browser.

## How the browser renders the page

Rendering the web page includes transforming raw code into visual content that user interacts with in the following ways:

- **DOM Construction**: To represent the logical structure of HTML.
- **CSSOM Construction**: To parse the CSS files of the page.
- **Render Tree Construction**: The combination of DOM and CSSOM that includes the elements that will be visually rendered on the page.
- **Layout**: It calculates the size and position of every elements in the page.
- **Painting**: Rendering pixels and adding visual details like colors, text, borders, images, etc.
- **Compositing**: It combines different layers to display the final interactive representation to the user.

## Why this understanding matters to a developer

- Performance optimization: faster page load times, smooth interactions and minimize unnecessary repaints.
- Security: To know about HTTPS and SSL.
- Debugging: To point the exact cause of rendering problems.
- Full stack development: Better collaboration between frontend and backend.

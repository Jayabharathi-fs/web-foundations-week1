# Understanding and Exploring Browser Storage

---

# Core concepts

## What is the purpose of browser storage?

Store data on the client-side to reduce server requests, improve performance, enable offline use, and persist user preferences or session state.

## localStorage

- **What**: Key–value storage in the browser.
- **Persistence**: Data stays until explicitly removed, even after browser restart.
- **Size limit**: ~5–10 MB (varies by browser).
- **Use cases**: User preferences, theme settings, cached small datasets.

## sessionStorage

- **What**: Key–value storage tied to a single browser tab.
- **Persistence**: Cleared when the tab or window closes.
- **Size limit**: ~5 MB.
- **Use cases**: Form data during a session, step-by-step progress in multi-page forms.

## Cookies

- **Size limit**: ~4 KB per cookie.
- **Auto-send**: Sent with every HTTP request to the same domain.
- **Use cases**: Authentication tokens, user tracking, server-required settings.
- **Security**: Can be `HttpOnly`, `Secure`, `SameSite` to prevent theft and CSRF.

## IndexedDB

- **What**: NoSQL database in the browser for structured data.
- **When to use**: Large datasets, complex queries, offline apps.
- **Advantages over localStorage**: Larger capacity (hundreds of MBs+), asynchronous, supports indexes and binary data.

## Cache API

- **What**: Stores HTTP responses (Request–Response pairs) in the browser.
- **Role in offline-first apps**: Lets service workers serve cached resources when offline, speeding up load times.

---

# Practical Exploration

## Store a key–value pair in localStorage and retrieve it

- Command used to set the value :

```js
localStorage.setItem("name", "Jayabharathi");
```

- Command used to retrieve the value :

```js
localStorage.getItem("name");
```

- Whether the data persists after refresh : **Yes**

- Whether the data persists after the browser closes : **Yes**

## Store a key–value pair in sessionStorage and retrieve it.

- Command used to set the value :

```js
sessionStorage.setItem("role", "Student");
```

- Command used to retrieve the value :

```js
sessionStorage.getItem("role");
```

- Whether the data persists after refresh : **Yes**

- Whether the data persists after the browser closes : **No**

## Set a cookie manually in the console and view it in the Cookies section.

- Command used to set the value :

```js
document.cookie = "username=John; path=/";
```

- Command used to retrieve the value :

```js
document.cookie;
```

- Whether the data persists after refresh : **Yes**

- Whether the data persists after the browser closes : **Yes if expiry date is set and no if not**

## Create an IndexedDB database with a simple store and add a record.

- Command used to set and retrieve the value :

```js
let req = indexedDB.open("MyDB", 1);
req.onupgradeneeded = () => {
  let db = req.result;
  db.createObjectStore("users", { keyPath: "id" });
};
req.onsuccess = () => {
  let db = req.result;
  let tx = db.transaction("users", "readwrite");
  tx.objectStore("users").add({ id: 1, name: "Jaya" });
};
```

- Whether the data persists after refresh : **Yes**

- Whether the data persists after the browser closes : **Yes**

## Inspect Cache Storage

- Command used to set the value :

```js
caches.open("my-cache").then((cache) => {
  cache.put("/example.txt", new Response("Hello Cache!"));
});
```

- Command used to retrieve the value :

```js
caches.open("my-cache").then((cache) => {
  cache.match("/example.txt").then((res) => res.text().then(console.log));
});
```

- Whether the data persists after refresh : **Yes**

- Whether the data persists after the browser closes : **Yes**

---

# Analysis

## Which storage types persist across sessions?

`localStorage`, `IndexedDB`, and `Cache API` are designed for long-term persistence.  
Cookies persist if they have an expiry/max-age, otherwise they behave like session cookies.  
`sessionStorage` is temporary and tied only to a single tab session.

## Which storage types are automatically sent to the server?

Only cookies integrate directly with HTTP, automatically being attached to requests.  
Other storage types are purely client-side and must be handled manually in code.

## Which storage type is most secure for sensitive information (and why)?

- **Cookies with HttpOnly + Secure flags**

- `HttpOnly` → prevents JavaScript access, so tokens can’t be stolen via XSS.
- `Secure` → ensures cookies are only sent over HTTPS.
- Other storages (`localStorage`, `sessionStorage`, `IndexedDB`, `Cache API`) are always accessible by JavaScript, which makes them vulnerable if XSS occurs.

## Which storage type is best for large datasets?

- **IndexedDB**

- Designed as a full client-side database.
- Supports structured storage, indexes, and transactions.
- Can handle **hundreds of MBs to GBs**, far beyond the limits of cookies (~4KB) or Web Storage (~5–10MB).

## Which should be avoided for authentication tokens (and why)?

- **localStorage / sessionStorage**

- Both are **accessible to JavaScript**, making them highly vulnerable to **XSS attacks**.
- Tokens stored here can be read and exfiltrated by attackers.
- Since they are not automatically sent to the server, developers often add them to headers manually, which increases risk.

---

# Key Takeaways

## How browser storage works.

- Browsers provide multiple storage options: `Cookies`, `localStorage`, `sessionStorage`, `IndexedDB`, and `Cache API`.
- They differ in **persistence**, **capacity**, **scope**, and whether data is automatically sent to the server.
- Storage is accessible via JavaScript APIs, except `HttpOnly` cookies.

## Differences in persistence, size, and scope.

- **Persistence:**
  - `sessionStorage` ends with tab close.
  - `localStorage`, `IndexedDB`, `Cache API`, and persistent cookies survive refresh and browser restart.
- **Size limits:**
  - Cookies ~4KB.
  - `localStorage` / `sessionStorage` ~5–10MB.
  - `IndexedDB` and `Cache API` support hundreds of MBs to GBs.
- **Scope:**
  - `sessionStorage` limited to one tab.
  - `localStorage` shared across all tabs of the same origin.
  - Cookies are domain/path scoped and automatically attached to HTTP requests.

## Best practices for secure and efficient storage.

- Use **HttpOnly + Secure cookies** for sensitive info (auth tokens).
- Use **IndexedDB** for large/structured datasets.
- Use **localStorage** or **sessionStorage** for simple, non-sensitive client-only data.
- Use **Cache API** for caching files/assets offline.
- Avoid storing authentication tokens in `localStorage` or `sessionStorage` due to XSS risks.
- Regularly clear or expire data to prevent stale or excessive storage use.

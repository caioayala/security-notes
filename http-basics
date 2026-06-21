# How the Web Works — HTTP, Headers and Cookies

## URL Structure

A URL (Uniform Resource Locator) is composed of several distinct components,
each serving a specific purpose in locating and accessing a resource on the web.

| Component    | Description |
|--------------|-------------|
| Scheme       | Defines the protocol to use, such as HTTP, HTTPS or FTP |
| User         | Some services require authentication, allowing credentials to be passed directly in the URL |
| Host         | The domain name or IP address of the target server |
| Port         | The port to connect to, typically 80 for HTTP and 443 for HTTPS, though any port between 1 and 65535 is valid |
| Path         | The file name or location of the resource being requested |
| Query String | Additional parameters appended to the path, for example `/blog?id=1` passes the value 1 to the id parameter |
| Fragment     | A reference to a specific location within the page, used to scroll directly to a section of content |

Security note: credentials embedded in URLs are visible in browser history, server
logs and referrer headers. This is a common source of credential exposure in
web application assessments.

## HTTP Methods

HTTP methods define the intended action of a request against a resource.

| Method | Description |
|--------|-------------|
| GET    | Retrieves information from a web server |
| POST   | Submits data to a web server, typically creating a new record |
| PUT    | Submits data to update an existing resource on the server |
| DELETE | Removes a resource or record from the server |

Security note: servers that do not restrict allowed methods may accept PUT or
DELETE requests on unintended endpoints. Method enumeration is a standard step
in web application penetration testing.

## HTTP Status Codes

Status codes are returned by the server to indicate the result of a request.
They are grouped into five ranges by their first digit.

| Range   | Category             | Description |
|---------|----------------------|-------------|
| 100-199 | Informational        | Request received, client should continue |
| 200-299 | Success              | Request completed successfully |
| 300-399 | Redirection          | Client is redirected to another resource |
| 400-499 | Client Errors        | Error caused by the client request |
| 500-599 | Server Errors        | Error occurring on the server side |

### Common Status Codes

| Code | Description |
|------|-------------|
| 200  | OK — request completed successfully |
| 201  | Created — a new resource has been created |
| 301  | Moved Permanently — resource has a new permanent location |
| 302  | Found — temporary redirect, location may change again |
| 400  | Bad Request — malformed or missing parameters in the request |
| 401  | Unauthorised — authentication is required to access this resource |
| 403  | Forbidden — access is denied regardless of authentication status |
| 404  | Not Found — the requested resource does not exist |
| 405  | Method Not Allowed — the HTTP method used is not accepted for this endpoint |
| 500  | Internal Server Error — the server encountered an unhandled error |
| 503  | Service Unavailable — the server is overloaded or under maintenance |

Security note: 403 and 401 are distinct. A 401 means the resource exists but
requires authentication. A 403 means access is explicitly denied. During recon,
receiving a 403 on a directory confirms its existence, which is useful information
even without access.

## HTTP Headers

Headers carry metadata about requests and responses. They are split into two
categories: request headers sent by the client, and response headers returned
by the server.

### Request Headers

| Header          | Description |
|-----------------|-------------|
| Host            | Specifies which website to serve when a server hosts multiple domains |
| User-Agent      | Identifies the browser and version, used by the server to format responses appropriately |
| Content-Length  | Tells the server how much data to expect in the request body |
| Accept-Encoding | Lists the compression methods the client supports |
| Cookie          | Sends stored cookie data back to the server with each request |

Security note: the User-Agent header is fully controlled by the client and can
be spoofed. Some applications use it for access control or bot detection, both
of which can be bypassed trivially.

### Response Headers

| Header           | Description |
|------------------|-------------|
| Set-Cookie       | Instructs the browser to store a cookie and send it back on future requests |
| Cache-Control    | Defines how long the browser should cache the response before requesting it again |
| Content-Type     | Tells the client what type of data is being returned, such as HTML, CSS, JSON or PDF |
| Content-Encoding | Specifies the compression method used on the response body |

### Security-Relevant Headers

| Header                    | Purpose | Risk if missing |
|---------------------------|---------|-----------------|
| Content-Security-Policy   | Restricts which resources the browser can load | XSS attacks are easier to execute |
| X-Frame-Options           | Prevents the page from being embedded in an iframe | Clickjacking attacks become possible |
| Strict-Transport-Security | Forces HTTPS connections | Traffic can be downgraded to HTTP and intercepted |
| X-Content-Type-Options    | Prevents browsers from guessing the content type | MIME sniffing attacks |
| Server / X-Powered-By     | Reveals server software and version | Information disclosure, aids fingerprinting |

Security note: the Server and X-Powered-By headers are response headers that
expose the underlying technology stack. In a penetration test, this information
is collected during reconnaissance to identify known vulnerabilities associated
with specific versions.

## Cookies

Cookies are small pieces of data stored in the browser and sent to the server
with every subsequent request. They are created when the server returns a
Set-Cookie response header, which the browser stores and attaches to future
requests for that domain. Common uses include session management, user
preferences and tracking.

### Cookie Security Flags

| Flag     | Description |
|----------|-------------|
| HttpOnly | Prevents JavaScript from accessing the cookie, mitigating session theft via XSS |
| Secure   | Ensures the cookie is only transmitted over HTTPS connections |
| SameSite | Controls whether the cookie is sent with cross-site requests, reducing CSRF risk |

Security note: cookies without the HttpOnly flag are accessible via
`document.cookie` in JavaScript. A successful XSS attack on a page without
this flag allows an attacker to steal session tokens and impersonate the user.

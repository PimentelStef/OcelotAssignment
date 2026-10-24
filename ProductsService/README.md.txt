Ocelot API Gateway with JWT Authentication

Project Overview

This project demonstrates an API Gateway using Ocelot in ASP.NET Core. The gateway routes requests to the Products, Orders, and Authentication services through a single entry point.

The gateway runs at http://localhost:5000 and handles request routing, JWT authentication, and login rate limiting.

Services and Ports

Service	Port	Purpose
Gateway	5000	Routes client requests and enforces access rules
Products Service	5001	Provides product CRUD operations
Orders Service	5002	Returns order information
Auth Service	5003	Issues JWT tokens through login

Gateway Routes

Gateway Endpoint	Access
GET /gateway/products	Public
GET /gateway/products/{id}	Public
POST /gateway/products	JWT required
PUT /gateway/products/{id}	JWT required, if configured
DELETE /gateway/products/{id}	JWT required, if configured
GET /gateway/orders	JWT required
POST /gateway/auth/login	Public, rate limited

Authentication

The Auth service issues JWT tokens after successful login. The gateway validates the token's signature, issuer, audience, and expiration before forwarding requests to protected routes.

Clients must send a valid token using the following HTTP header:

Authorization: Bearer <token>

Security and Gateway Behavior

Unauthenticated requests to protected product write operations and the Orders endpoint are rejected by the gateway with HTTP 401 Unauthorized. These requests are not forwarded to the downstream service.

Product read endpoints and the login endpoint remain accessible without an existing token. The login endpoint is rate limited to help prevent excessive login attempts.

The gateway provides a single client-facing entry point. However, the downstream services must also be appropriately network-restricted if they need to be inaccessible directly.

Rate Limiting

The login route is configured to allow up to five requests within the configured rate-limiting period. Excess requests should return HTTP 429 Too Many Requests when the configured limit is exceeded.

Testing

See TestResults.md for the request test matrix and the observed HTTP status codes.

Technologies Used
C#
ASP.NET Core Web API
Ocelot API Gateway
JWT Bearer Authentication
JSON configuration
In-memory order data

Running the Project

Restore and build each project.
Start the Products, Orders, and Auth services.
Start the Gateway.
Send requests through http://localhost:5000.
Verify public routes, protected routes, token validation, and rate limiting.
Author

Pimentel, Stefan T. BSIT

Main Rubric Answer: The gateway rejects unauthenticated product write requests and requests to view orders because those routes require the GatewayKey JWT authentication scheme. If the request does not contain a valid token, the gateway returns HTTP 401 Unauthorized before forwarding it to the downstream service. Product GET requests are public, while login is public but rate limited. This centralizes access control at the gateway.

One distinction matters: hiding the downstream ports from clients is not the same as disabling direct access to those ports. To enforce the security boundary, configure the services or network so clients cannot bypass the gateway.
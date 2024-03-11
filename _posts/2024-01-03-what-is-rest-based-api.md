---
title: What is a REST API ?
author: Milan Rawal
date: 2024-01-03 05:00:00 +0545
categories: [system design]
tags: [REST api] # TAG names should always be lowercase
---

A REST API (also called a RESTful API or RESTful web API) is an application programming interface (API) that conforms to the design principles of the representational state transfer (REST) architectural style. REST APIs provide a flexible, lightweight way to integrate applications and to connect components in microservices architectures.

First, defined in 2000 by computer scientist Dr. Roy Fielding in his doctoral dissertation, REST provides a relatively high level of flexibility, scalability and efficiency for developers. For these reasons, REST APIs have emerged as a common method for connecting components and applications in a microservices architecture.

[](#rest-design-principles)REST design principles
-------------------

At the most basic level, an API is a mechanism that enables an application or service to access a resource within another application or service. The application or service that accesses resources is the client, and the application or service that contains the resource is the server. Some APIs, such as SOAP or XML-RPC, impose a strict framework on developers. But developers can develop REST APIs using virtually any programming language and support a variety of data formats. The only requirement is that they align to these six REST design principles - also known as architectural constraints:

- Uniform interface: All API requests for the same resource should look the same, no matter where the request comes from. The REST API should ensure that the same piece of data, such as the name or email address of a user, belongs to only one uniform resource identifier (URI). Resources shouldn’t be too large but should contain every piece of information that the client might need.
- Client-server decoupling: In REST API design, client and server applications must be completely independent of each other. The only information that the client application should know is the URI of the requested resource; it can't interact with the server application in any other ways. Similarly, a server application shouldn't modify the client application other than passing it to the requested data via HTTP.
- Statelessness: REST APIs are stateless, meaning that each request needs to include all the information necessary for processing it. In other words, REST APIs do not require any server-side sessions. Server applications aren’t allowed to store any data related to a client request.
- Cacheability: When possible, resources should be cacheable on the client or server side. Server responses also need to contain information about whether caching is allowed for the delivered resource. The goal is to improve performance on the client side, while increasing scalability on the server side.
- Layered system architecture: In REST APIs, the calls and responses go through different layers. As a rule of thumb, don’t assume that the client, and server applications connect directly to each other. There may be a number of different intermediaries in the communication loop. REST APIs need to be designed so that neither the client nor the server can tell whether it communicates with the end application or an intermediary.
- Code on demand (optional): REST APIs usually send static resources, but in certain cases, responses can also contain executable code (such as Java applets). In these cases, the code should only run on-demand.

### [](#how-rest-work)How REST APIs work
REST APIs communicate through HTTP requests to perform standard database functions like creating, reading, updating and deleting records (also known as CRUD) within a resource.

For example, a REST API would use a GET request to retrieve a record. A POST request creates a new record. A PUT request updates a record, and a DELETE request deletes one. All HTTP methods can be used in API calls. A well-designed REST API is similar to a website running in a web browser with built-in HTTP functionality.

The state of a resource at any particular instant, or timestamp, is known as the resource representation. This information can be delivered to a client in virtually any format including JavaScript Object Notation (JSON), HTML, XLT, Python, PHP or plain text. JSON is popular because it’s readable by both humans and machines—and it is programming language-agnostic.

Request headers and parameters are also important in REST API calls because they include important identifier information such as metadata, authorizations, uniform resource identifiers (URIs), caching, cookies and more. Request headers and response headers, along with conventional HTTP status codes, are used within well-designed REST APIs.

### [](#rest-best-practices)REST API best practices
Although flexibility is a big advantage of REST API design, that same flexibility makes it easy to design an API that’s broken or performs poorly. For this reason, professional developers share best practices in REST API specifications.

The OpenAPI Specification (OAS) establishes an interface for describing an API in a way that allows any developer or application to discover it and fully understand its parameters and capabilities. This information includes available endpoints, allowed operations on each endpoint, operation parameters, authentication methods and more. The latest version, OAS3, includes with hands-on tools, such as the OpenAPI Generator, for generating API clients and server stubs in different programming languages.

Securing a REST API also starts with industry best practices. Use hashing algorithms for password security and HTTPS for secure data transmission. An authorization framework like OAuth 2.0 can help limit the privileges of third-party applications.

Using a timestamp in the HTTP header, an API can also reject any request that arrives after a certain time period. Parameter validation and JSON Web Tokens are other ways to ensure that only authorized clients can access the API.

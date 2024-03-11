---
title: Error handling on REST API.
author: Milan Rawal
date: 2024-01-05 10:00:00 +0545
categories: [system design]
tags: [REST api] # TAG names should always be lowercase
---

Error handling is a crucial part of working with APIs. When an API encounters an issue, such as invalid input data or missing resources, it can lead to errors. It is essential that these errors are correctly handled and clearly presented to the client or end-user.

The [API lifecycle](https://blog.codemilan.com/api-life-cycle) involves both the API’s producer and consumer. The API producer works on the server side and is responsible for API design and development. The API consumer, on the other hand, works on the client side and is responsible for integrating the API into various systems that end-users interact with. Errors can occur at any stage of this process. These errors, if not handled properly, can lead to downtime, bad user experiences, and lost money and time.

In this article, we’ll start by reviewing some best practices for handling API errors on the client and server-side—without focusing on any specific API architecture. We will then discuss architecture-specific best practices for API error handling—and explore how the Postman API Platform can help teams handle errors more efficiently.

What are some best practices for handling API errors on the server side?
------------------------------------------------------------------------

There are multiple API architectural patterns, and each one has a unique approach of handling errors. That being said, all API errors need to be presented in a uniform and logical way. It’s therefore important to adhere to the following best practices for server-side error handling—regardless of your API’s architectural pattern:

*   **Provide a clear and consistent structure for your error response:** When an error occurs, your API error response should follow an established structure that is consistent across all requests. Additionally, your API responses should be idempotent to make the structure and response more predictable.
*   **Use descriptive error messages:** Error messages should be clear and descriptive. The consumer of your API should be able to understand the problem and how to fix it from reading the error messages.
*   **Don’t leak sensitive data:** Be careful not to leak any sensitive information in your error messages. Error messages should only include information about the problem and possible fixes.
*   **Document common errors:**  A clear and well-documented API should include details about the structure of its error messages. The API documentation should include possible error codes, common error messages, and remediation suggestions.
*   **Implement logging and monitoring:** Some errors can be difficult to debug because they are the result of a series of multiple API calls. It is therefore important to implement API monitoring and logging, which makes it easier to trace API interactions and debug errors. In some APIs, the `requestId` and `timestamp` parameters are returned as part of the error response, and this information can assist with logging and debugging.

What are some best practices for handling API errors on the client side?
------------------------------------------------------------------------

Developers often run into errors during the API integration process. These errors might be caused by an incorrect implementation, a user action, or an internal server error on the API itself. It is important that developers handle these errors properly and present them to end-users in a direct, non-technical manner. The following best practices can lay the foundation for successful error handling during API integration—regardless of the API’s architectural pattern:

*   **Validate user input:** Users sometimes provide invalid input data, which can lead to errors. Client-side validations help prevent this issue. Validations not only ensure that the user can see and fix the problem quicker, but also help the client and server conserve resources that would otherwise be expended on extra network traffic.
*   **Provide user-friendly messages:** It’s important to avoid presenting error messages from the server directly to the end-user. Instead, these technical error messages should be simplified and made more user-friendly. They should also clearly tell the user how to fix the error.
*   **Handle multiple edge cases:** Developers should understand the full range of errors an API can produce so that they can handle every edge case. An unhandled edge case can result in a default or vague error message that might not help the user fix the issue.

### Error handling best practices for REST

REST provides a simple and uniform interface for sharing resources between a client and a server. It uses standard HTTP methods to perform CRUD (CREATE, READ, UPDATE, and DELETE) operations on resources. REST APIs make use of standard HTTP status codes that indicate the outcome of the request. These status codes can immediately be used to determine if the request was successful or if an error occurred.

Here are some best practices for handling errors when working with REST APIs:

*   **Use HTTP status codes properly:** REST APIs rely heavily on standard HTTP status codes to communicate the nature of an error. It’s important to ensure you use the appropriate status code for each scenario.
*   **Provide enough detail in error messages:** REST is stateless in nature. This means that all error messages must provide the necessary information for the client to understand the error without relying on the context of previous requests.
*   **Use a standardized error response format:** Maintain a consistent standard for error messages. For example, most REST APIs include fields like `error`, `message`, `code` (an internal error code specific to your application), and sometimes `details` for additional information.

As an example, let’s consider the following HTTP REST API request that is supposed to fetch information about a specific user by its ID:

`curl -i -X GET https://api.example.com/api/v1/users/12345`

If the provided user ID was wrong and a user with the given ID does not exist in the database, a properly formatted and appropriately detailed error message would look like this:

``` json
{
  "status": "error", // This can be 'error' or 'success'.
  "statusCode": 404,
  "error": {
    "code": "RESOURCE\_NOT\_FOUND",
    "message": "The requested resource was not found.",
    "details": "The user with the ID '12345' does not exist in our records.",
    "timestamp": "2023-12-08T12:30:45Z",
    "path": "/api/v1/users/12345",
    "suggestion": "Please check if the user ID is correct or refer to our documentation at https://api.example.com/docs/errors#RESOURCE\_NOT\_FOUND for more information."
  },
  "requestId": "a1b2c3d4-e5f6-7890-g1h2-i3j4k5l6m7n8",
  "documentation\_url": "https://api.example.com/docs/errors"
}
```

This response is effective because it:

*   Uses the `status` field to let you see at a glance whether the request failed or succeeded.
*   Provides a clear reason for the error, additional details, and a suggested fix, as well as a link to the documentation page for errors.
*   Has a clear, consistent, and standardized structure.

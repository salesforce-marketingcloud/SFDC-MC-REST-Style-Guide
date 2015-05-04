# Fuel API Standards

## Versioning in the API

The application protocol interface ("API") is versioned. Implementations MUST use an indicator in the uniform resource locator ("URL"). This indicator applies to the entire API as a whole. Resources MUST NOT be individually versioned.

	GET /{version}/{service}/{+resource}
    GET /v2/contacts/contact/42

### Version numbering schema

Versions in URL MUST start with the letter "v" and are followed by the version number, which is a whole number. The version number is not tired to product releases.

    version = "v" 1*DIGIT

## HTTP methods

The API uses the hyper text transfer protocol ("HTTP") and resources accept various HTTP methods on them.

### DELETE

The identified resource MUST be deleted.

### GET

The identified resource MUST be retrieved and MUST NOT produce any side-effects.

### POST
A service MAY optionally support POST. A service supporting POST may support one or both of Sync and Async.

#### Sync 
The resource contained in the request MUST be created.

The server MUST respond with a 201 status and the created resource. The server SHOULD include the created identifier for the resource.

#### Async 
The server MUST respond with a 202 status and the created async task resource. An identifier in the task resource MUST be available for querying the eventually result. The service for the task resource identifier is available by linkage in the result.

### PUT
Creates or replaces a resource. A server SHOULD respond with a 200 when updating an existing resource or a 202 when creating a resource.

### PATCH
Updates an existing resource in parts. Patch payload is defined elsewhere FIXME/TODO 

## HTTP status codes

### Implicit

The API uses the hyper text transfer protocol ("HTTP") and provides various implicit status codes.

* 100 Continue
* 206 Partial Content
* 404 Not Found
* 405 Method Not Allowed
* 406 Not Acceptable
	* Accept header
* 411 Length Required
* 412 Precondition Failed
* 413 Request Entity Too Large
* 414 Request URI Too Long
    * See also [async as header](justification/asyncAsHeader.md)
* 415 Unsupported Media Type
	* Unsupported Content-Type header
* 416 Requested Range Not Satisfiable
* 417 Expectation Failed
* 500 Internal Server Error
* 502 Bad Gateway
* 503 Service Unavailable
* 504 Gateway Timeout

### Explicit

* 200 OK
* 201 Created
* 202 Accepted
	* For async operations (accepted the submission)
* 400 Bad Request
* 401 Unauthorized
	* Request is not authenticated
* 403 Forbidden
	* Authenticated but lack permission to resource/operation
* 404 Not Found

### Non-used
Explicitly forbidden 
* 3xx

### Codes usage with HTTP methods
* 201 MUST only be used with PUT & POST
* 202 MUST only be used with Async requests
* 200 MUST NOT be used with POST


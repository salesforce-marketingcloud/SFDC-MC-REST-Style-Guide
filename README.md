# Fuel API Standards

# Versioning in the API

The application protocol interface ("API") is versioned. Implementations MUST use an indicator in the uniform resource locator ("URL"). This indicator applies to the entire API as a whole. Resources MUST NOT be individually versioned.

	GET /{version}/{service}/{+resource}
    GET /v2/data/contacts/42

See also [version location](jusification/versionlocation.md)

## Version numbering schema

Versions in URL MUST start with the letter "v" and are followed by the version
number, which is a whole number. The version number is not tired to product
releases.

    version = "v" 1*DIGIT

## Allowed modifications to a version 

Servers MAY add endpoints

Routes on a version MAY add properties to payload 

Routes on a version MAY add optional query string parameters. Routes on a version MUST NOT add required parameters. 

# HTTP methods

The API uses the hyper text transfer protocol ("HTTP") and resources accept various HTTP methods on them.

## DELETE

The identified resource MUST be deleted.

## GET

The identified resource MUST be retrieved and MUST be idempotent. i.e. MUST NOT produce any side-effects.

## POST
A service MAY optionally support POST. A service supporting POST MAY support one or both of Sync and Async.

### Sync 
The resource contained in the request MUST be created.

The server MUST respond with a 201 status and the created resource. The server
MUST include the created identifier for the resource.

### Async 
The server MUST respond with a 202 status and the created async task resource.
An identifier in the task resource MUST be available for querying the
eventually result. A single async query service will be available.

## PUT
Creates or replaces a resource. A server SHOULD respond with a 200 when
updating an existing resource or a 202 when creating a resource.

Server MUST NOT support PUT against collection FIXME/TOOD?

## PATCH
Updates an existing resource in parts. Patch payload is defined elsewhere FIXME/TODO 

## OPTIONS
Servers MUST only be for ALLOW header and the purpose of cross-origin resource
communication. Servers MUST NOT provide other information beyond allowed
operations and cross-orgin resource headers.

# HTTP status codes

## Implicit

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

## Explicit

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
	* Routes SHOULD return 403 if resource exists but user lacks access

## Caching
Servers MAY respond to if-modified and etag HTTP headers 
* 304

## Redirects
Servers MUST NOT send back with redirects

## Codes usage with HTTP methods
* 201 MUST only be used with PUT & POST
* 202 MUST only be used with Async requests
* 200 MUST NOT be used with POST

# Headers
A specific set of headers are supported. A server MUST NOT respect any header outside of the defined list.

## Cross origin resource sharing
Support for CORS provides these headers
* request: Origin
* response: Access-Control-Allow-Origin
* response: Access-Control- FIXME/TODO other ones

## Localization
No localization headers are accepted or returned. [Localization justification](justification/localization.md)

## Impersonation
FIXME/TODO oauth scope tokens?




# Response Body

## Localization
* Dates SHOULD include timezone
	* Server MAY lack timezone for **recurring** events 

## Errors
Error requests to a server SHOULD be idempotent (no side effect). Errors MUST be in the prescribed the error JSON format. HTTP content-type MUST NOT deviate from API's content-type.

### Error Format
Error MUST NOT have any more than following properties
* MUST "documentationUrl" - string - fully qualified URL to support site
	* Support site SHOULD be localized and the JSON is not localized
* MUST "statusCode" - int - MUST be the HTTP response code 
* MUST "errorCode" - string - MUST be English US-ASCII dot separated value (no whitespace)
	* Used codes MUST be registered with Salesforce API platform team process
* MUST "details" - array of "error detail object" 
	* Provides context for the error 
	* Validation errors SHOULD have detail object for each validation error

**Error Detail Object Format**
* MUST "documentationUrl" - string - fully qualified URL to support site
* MUST "errorCode" - string - MUST be English US-ASCII dot separated value (no whitespace)
* MAY "path" - JSON path format - definition of JSON path that caused issue

### Validation Details
Validation errors SHOULD utilize JSON path 

### Example
```json
{
	"documentationUrl" : "https://developer.salesforce.com/marketing_cloud/errors/server.failure.general",
	"statusCode" :  500
	"errorCode" : "server.failure.general"
	"details" : []
}
```

### Validation Example
**Request POST**
```json
[
	{
		"date" : "not valid date"
		"emailaddress" : "foo_not_valid@"
	},
	{
		"date" : "2015-06-02T12:00:00.000Z"
		"emailaddress" : "@bar_not_valid.com"
	}
]
```

**Response**
```json
{
	"documentationUrl" : "https://developer.salesforce.com/marketing_cloud/errors/validation.error.aggregate",
	"statusCode" :  400
	"errorCode" : "validation.error.aggregate"
	"details" : [
		{
			"documentationUrl" : "https://developer.salesforce.com/marketing_cloud/errors/validation.email.address",
			"errorCode" : "validation.email.address"
			"path" : "$.[0].emailaddress"
		},
		{
			"documentationUrl" : "https://developer.salesforce.com/marketing_cloud/errors/validation.date",
			"errorCode" : "validation.date"
			"path" : "$.[0].date"
		},
		{
			"documentationUrl" : "https://developer.salesforce.com/marketing_cloud/errors/validation.email.address",
			"errorCode" : "validation.email.address"
			"path" : "$.[1].emailaddress"
		}
	]
}
```

# Request Body
* Dates SHOULD include timezone
	* Dates MAY lack timezone for **recurring** events 

Servers MUST reject requests if the body has unexpected or undocumented
properties.  Servers MUST reject requests if the body has unexpected or
undocumented objects 




# URL Style

## Query string
* Servers MUST ignore requests with extra query string parameters
* Query string parameters MUST be camelCase

## Path
URI path parameters SHOULD be nouns

### Collections
Routes that return multiple objects are collections.
* Routes MUST have a plural named 
* Routes MUST return an empty set instead 
* Routes MUST always be an array even when a single object
* Routes SHOULD be a set of fully hydrated objects 


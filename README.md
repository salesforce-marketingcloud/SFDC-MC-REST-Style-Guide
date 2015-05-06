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

Routes on a version MAY add properties to payload. Route MUST NOT add required properties for creating a resource.

Routes on a version MAY add optional query string parameters. Routes on a version MUST NOT add required parameters. 

# HTTP methods

The API uses the hyper text transfer protocol ("HTTP") and resources accept various HTTP methods on them.

## DELETE

A service MAY support DELETE. The identified resource MUST be deleted.

## GET

The identified resource MUST be retrieved and MUST be idempotent. i.e. MUST NOT produce any side-effects.

## POST

A service MAY support POST.  The resource contained in the request MUST be created.

The server MUST respond with a 201 status and the created resource. The server
MUST include the created identifier for the resource.

## PUT

TOOD/FIXME Kris is unsure on create support

A service MAY support PUT. Creates or replaces a resource. A server SHOULD
respond with a 200 when updating an existing resource or a 202 when creating a
resource.

Server MUST NOT support PUT against collection FIXME/TOOD?

## PATCH

A service MAY support PATCH. Updates an existing resource in parts. Patch
payload is defined elsewhere FIXME/TODO 

## OPTIONS
In response to OPTIONS method servers
* MUST respond ALLOW header and the purpose of cross-origin resource
* MUST respond with valid methods
* MUST NOT expose any other data

# Async Interaction
A route/method MAY support Async. The server MUST respond with a 202 status and
"Location" header to query the async task request. At the "Location" header the
server MUST continue responding with a 202 until the response is read. Once
ready the "Location" URL MUST respond as the original request would have if
done synchronously.

The server MUST NOT respond with a 202 and a body.

# HTTP verb substitution
A server MUST support the HTTP method "POST" to allow HTTP methods a client may
not support.

POST query string "httpMethod" MUST be a value of 
* PUT
* PATCH
* DELETE

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
* 429 Too Many Requests
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

* SHOULD NOT - 204 No content
	* High volume use cases are useful
// FIXME/TOOD move to justification
	* Discouraged for consistency
		* deletes should attempt to return helpful information like "id"
		* PUT should return the created content

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
A specific set of headers are supported. A server MUST NOT respect any header
outside of the defined list.

## Cross origin resource sharing
Support for CORS provides these headers
* request: Origin
* response: Access-Control-Allow-Origin
* response: Access-Control- FIXME/TODO other ones

## Localization
No localization headers are accepted or returned. [Localization justification](justification/localization.md)

## Impersonation
FIXME/TODO oauth scope tokens?

## Logging
* request: MAY "Original-Request-Id"
* response: MUST "Request-Id"
* response: MUST "Original-Request-Id" if request included "Original-Request-Id"

# Response Body

## Envelope
Server response MUST correspond to the format 
* MUST "data" - array of data objects - contains response of route
* MAY "meta" - object -  TODO/FIXME

**Data Object**
* MUST "id" - string - identifier for resource

## Property names 
* all MUST be camelCase 
* properties containing a url MUST be suffixed with "Url"
* dates MUST be suffixed with "Date"

## Property types
**Date** 
* MUST always include date and time
* MUST be ISO-8601 style of 2015-05-04T15:39:03Z
	* Server MAY lack timezone for **recurring** events 
	* MUST NOT include any other ISO-8601 date style
	* requests MUST be in either 'Z' or plus/minus format

**Number**
* SHOULD NOT be quoted

**Array**
* MUST contain homogeneous values

**Relationship**
See also [Relationship definition](glossary.md)
See also [Relationship object](justification/relationshipobject.md)
* For 1-1 relationship MUST be an relationship object
* For \*-n relationship MUST be an array of relationship object
* Requests containing relationship objects MUST only modify relationship between the two resources
	* Servers SHOULD error if properties outside of "id' are present in request

**Relationship Object**
* MUST contain "id" - string - reference id by object 
* MUST NOT contain other object properties 
	* See also [Views](#pattern)

See pattern [Recurring events](pattern/recurringevent.md)

**Enumerations**
TODO/FIXME

## Metadata
Servers MAY include a meta object root level of envelop. 

**Meta Object**
* MAY "totalCount" - Number - the number of total records in a collection response
* MAY "links" - array of link objects - object defining relationships such as paging

**Link object**
MUST NOT include properties outside of set
* MUST "href" - string - href to next state or page
* MUST "name" - enum - description of href implying purpose
	* valid names are "prev", "next", "self"
	* MUST NOT use any other values
	* See also [HATEOAS](justerification/hateoas.md)
* MUST "path" - string - JSON path indicating the object link belongs to
* MAY "method" - string - HTTP method to use with "href"

### Example
```json
{
	// TODO/FIXME crap example
	"data" : [
		{
			"id" : "1",
			"fooString" : "bar",
			"fooDate" : "2015-06-02T12:00:00Z",
			"fooArray" : [
				"2015-06-02T12:00:00Z",
				"2016-06-02T12:00:00Z",
				"2017-06-02T12:00:00Z"
			],

			// 1-1 relation
			"post" : {
				"id" : "1"
			}
			// 1-n relation
			"users" : [
				 { "id" : "1" }
				,{ "id" : "2" }
			]
			// n-n relation
			"users" : [
				 { "id" : "1" }
				,{ "id" : "2" }
			]
		}
	],


	"meta" : {
		// optional
		"totalCount" : 1

		"links" : [
			{ "name" : "prev", "method": "GET", "href" : "object/1", "path" : "$.data.[0]" }
			{ "name" : "next", "method": "GET", "href" : "object/1", "path" : "$.data.[0]" }
		]
	}
}
```

## Localization

A route MAY be aware of localized values. A route MUST expose localized values
as a "relation" resource to callers of the resource. A route SHOULD allow a "filter"
to a single localized version. A route SHOULD have a "view" that expands localized
content relations providing the localized version AND supports the "filter" to one
locale.

Route MUST NEVER localize property names.

Route MUST error if "filter" contains unsupported values.

## Errors

Error requests to a server SHOULD be idempotent (no side effect). Errors MUST
be in the prescribed the error JSON format. HTTP content-type MUST NOT deviate
from API's content-type.

### Error Format
Error MUST NOT have any more than following properties
* SHOULD "requestId" - string - "requestId" of the request
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
	"data" : [
		{
			"requestId" : "random string for internal debugging",
			"documentationUrl" : "https://developer.salesforce.com/marketing_cloud/errors/server.failure.general",
			"statusCode" :  500,
			"errorCode" : "server.failure.general",
			"details" : []
		}
	],
	"meta" : null
}
```

### Validation Example
**Request POST**
```json
[
	{
		"date" : "not valid date",
		"emailaddress" : "foo_not_valid@"
	},
	{
		"date" : "2015-06-02T12:00:00.000Z",
		"emailaddress" : "@bar_not_valid.com"
	}
]
```

**Response**
```json
{
	"data" : [
		{
			"documentationUrl" : "https://developer.salesforce.com/marketing_cloud/errors/validation.error.aggregate",
			"statusCode" :  400,
			"errorCode" : "validation.error.aggregate",
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
	],
	"meta" : null
}
```

# Request Body
* Dates MUST include timezone

Servers MUST reject requests if the body has unexpected or undocumented
properties.  Servers MUST reject requests if the body has unexpected or
undocumented objects 



# URL Style

## Query string
* Servers MUST accept and ignore extra query string parameters
* Query string parameters MUST be camelCase

## Path
URI path parameters SHOULD be nouns

### Collections
Routes that return multiple objects are collections.
* Routes MUST have a plural named 
* Routes MUST return an empty array when not objects found
* Routes MUST always be an array even when a single object
* Routes SHOULD be a set of fully hydrated objects 


# Fuel API Standards

# Versioning in the API

The application protocol interface ("API") is versioned. Implementations MUST
use an indicator in the uniform resource locator ("URL"). This indicator
applies to the entire API as a whole. Resources MUST NOT be individually
versioned.

    GET /{version}/{service}/{+resource}
    GET /v2/data/contacts

See also [version location](jusification/versionlocation.md)

## Version numbering schema

Versions in URL MUST start with the letter "v" and are followed by the version
number, which is a whole number. The version number is not tied to product
releases.

    version = "v" 1*DIGIT

## Allowed modifications to a version 

Servers MAY add endpoints.

Routes on a version MAY add properties to a payload. Route MUST NOT add required
properties for creating a resource.

Routes on a version MAY add optional query string parameters. Routes on a
version MUST NOT add required parameters. 

See also [breaking changes](jusification/breakingchanges.md)

## Sub-Versioning Objects

Servers MUST respond with Content-Version in the Header for resources.  This version MUST be incremented with each change to an object.  E.g. when making breaking OR non-breaking changes to a response object for a resource. 

```
# Old Version
# Content-Version : 1
{
	foo: "string",
	bar: 1
}

# New Version
# Content-Version : 2
{
	foo: "string",
	bar: 1,
	baz: true
}
```


# HTTP verbs

The API uses the hyper text transfer protocol ("HTTP") and resources accept
various HTTP methods on them.

## DELETE

A resource MAY support DELETE. The identified resource MUST be deleted.

## GET

The identified resource MUST be retrieved and MUST be idempotent. i.e. MUST NOT
produce any side-effects.

## POST

A resource MAY support POST.  The resource contained in the request MUST be
created.

The server MUST respond with a 201 status and the created resource. The server
MUST include the created identifier for the resource.

## PUT

A resource MAY support PUT. SHOULD replace a resource and MUST NOT create a
resource.  A server SHOULD respond with a 200 when updating an existing
resource. 

Server MAY support PUT against a collection that is a relationship.

See also [Upserting](pattern/upserting.md)

## PATCH

A resource MAY support PATCH. Updates an existing resource in parts. Patch
payload is defined elsewhere FIXME/TODO 

## OPTIONS
In response to OPTIONS method servers
* MUST respond ALLOW header and the purpose of cross-origin resource
* MUST respond with valid methods
* MUST NOT expose any other data

## Custom actions

Resources MAY expose addition non-standard HTTP actions. Usage of "custom actions"
SHOULD be used sparingly for consistency among resources.

Routes MUST NOT allow verbs in resource paths.

Routes MAY support a request body and different query string parameters.

**Example**
An operation on a resource that doesn't fit with REST is rotate an image
resource on the server.  To Rotate the image in a RESTful way, would need to
GET the image, rotate it client side, then PUT it back on the server. It would
take at least two calls to perform one operation. Routes do not allow verbs in
resource paths the solution is to POST the action to the API to be executed
by/on the resource.

## Querying

Querying is a special custom action that **should** be used instead of searching and filtering. 

See also [Querying](pattern/querying.md)


# HTTP method/action substitution
A server MUST support the HTTP method "POST" to allow HTTP methods a client may
not support.

POST query string "action" MUST be a value of 
* PUT
* PATCH
* DELETE
* "Custom action"

```
POST {service}/{resources}?action={verb}
POST {service}/{resources}/{id}?action={name}
POST {service}/{resources}/{id}/{sub-resources}?action={name}
POST {service}/{resources}/{id}/{sub-resources}/{id}?action={name}
```

# Async Interaction
A route/method MAY support Async. The server MUST respond with a 202 status and
"Location" header to query the async task request. At the "Location" header the
server MUST continue responding with a 202 until the response is read. Once
ready the "Location" URL MUST respond as the original request would have if
done synchronously.

The server MUST NOT respond with a 202 and a body.

# HTTP status codes

## Implicit

The API uses the hyper text transfer protocol ("HTTP") and provides various
implicit status codes. Implicit codes are framework level. 

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

Explicit codes are related to business logic responses.

* 200 OK
* 201 Created
* 202 Accepted
	* For async operations (accepted the submission)
* 304 Not modified
	* Routes MAY respond to If-None-Match (etag) header
* 400 Bad Request
* 401 Unauthorized
	* Request is not authenticated
* 403 Forbidden
	* Authenticated but lack permission to resource/operation
* 404 Not Found
	* Routes SHOULD return 403 if resource exists but user lacks access

* SHOULD NOT - 204 No content
	* High volume use cases can be exceptions
	* Discouraged for consistency
		* deletes should attempt to return helpful information like "id"
		* PUT should return the created content

## Redirects
Routes MUST NOT return redirects

## Codes usage with HTTP methods
* 201 MUST only be used with POST
* 202 MUST only be used with Async requests
* 200 MUST NOT be used with POST

# Headers
A specific set of headers are supported. A server MUST NOT respect any header
outside of the defined list.

## Request
* Origin
	* Support for CORS
* Authorization
* Original-Request-Id
	* Client MAY provide a tracking identifier. MUST be less than 1024 characters. Characters
	  MUST be within US-ASCII.
* If-Match
	* Client MAY provide etag values on PUT/PATCH for concurrency problems
* If-None-Match
	* Client MAY provide etag values on GET for cache behavior of HTTP code 304 
* Content-Type
	* Client SHOULD provide "application/json"
	* Client MAY provide "multipart" to provide both "application/json" and
	  "binary data". MUST NOT support "form/urlencoded" sections
* Accept
	* Client MAY provide an "accept" header. Client's SHOULD expect only
	  "application/json" UNLESS the route indicates support for binary types

## Response
* Access-Control-Allow-Origin
	* Support for CORS
* Access-Control-\* FIXME/TODO other ones
	* Support for CORS
* Location
	* Async MUST respond header to query the async task request
	* Resource creation MUST respond with location of created resource

* RateLimit-Limit 
	* Rate limiting - Ceiling for the given resource. Value MUST start from
	  one(1).
* RateLimit-Remaining 
	* Rate limiting - Number of requests left for the window. Value MUST be
	  "RateLimit-Limit" - requests made in window. 
* RateLimit-Reset 
	* Rate limiting - When window is reset. Value MUST be a specific time in
	  UTC epoch seconds.
	
* Etag
	* Responses MUST respond to all requests with a "Etag" header that MUST be
	  less than 1024 characters. Characters MUST be within US-ASCII.

* Request-Id
	* Responses MUST include. MUST be less than 1024 characters. Characters
	  MUST be within US-ASCII.
* Original-Request-Id
	* MUST be back of Request's Original-Request-Id if provided.

* Content-Type
	* Responses SHOULD be "application/json"
	* See also [Binary Views](binary views)
* Content-Disposition
	* Response MAY be used when non "application/json"
	* See also [Binary Views](binary views)

No localization headers are accepted or returned. [Localization justification](justification/localization.md)

# Response
Responses from a route MUST have the prescribed envelope format.

Routes MUST provide all properties even when value is "null".

## Envelope
Server response MUST correspond to the format 
* MUST "data" - array of data objects - contains response of route
* MAY "meta" - object - contains all meta data for the response

**Data Object**
* MUST "id" - string - identifier for resource

## Header
Route MUST respond to a single resource request with an "etag" header.  The
"meta" section MUST contain the same "etag" value.

Collection routes MUST respond with an "etag" header that is representative of
their results. Collection routes MUST also respond with "etag" values for all
included resources in the "meta".

Routes MUST respond with "location" header when a resource was created. 

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
Servers MUST include a meta object at root level of the response envelope. 

**Meta Object**
MUST NOT include properties outside of set
* MUST "etags" - array of etag objects - object defining the version of returned resources 
* MAY "totalCount" - Number - the number of total records in a collection response
* MAY "links" - array of link objects - object defining relationships such as paging

**Link object**
MUST NOT include properties outside of set
* MUST "href" - string - href to next state or page
* MUST "name" - enum - description of href implying purpose
	* valid standard names are "prev", "next", "self", "first", "last"
	* Resource's custom action names are valid
	* Resource's view names are valid
	* MUST NOT use any other values
	* See also [HATEOAS](justerification/hateoas.md)
* MUST "path" - string - JSON path indicating the object link belongs to
* MAY "method" - string - HTTP method to use with "href"

**Etag object**
MUST NOT include properties outside of set
* MUST "etag" - string - identifier for current state of resource
	* See also [currency justification](justification/currency.md) 
* MUST "path" - string - JSON path indicating the object link belongs to

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
			},
			// 1-n relation
			"users" : [
				 { "id" : "1" }
				,{ "id" : "2" }
			],
			// n-n relation
			"users" : [
				 { "id" : "1" }
				,{ "id" : "2" }
			]
		},
		{
			"id" : "Kris is happy now"
		}
	],


	"meta" : {
		"totalCount" : 2,

		"links" : [
			{ "name" : "prev", "method": "GET", "href" : "object/1", "path" : "$.data" },
			{ "name" : "next", "method": "GET", "href" : "object/1", "path" : "$.data" }
		],

		"etags" : [
			{ "etag" : "gibberish", "path" : "$.data" },
			{ "etag" : "gibberishLikeLastModifiedDate", "path" : "$.data.[0]" },
			{ "etag" : "gibberishLikeVersionNumber",   "path" : "$.data.[1]" }
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
* MUST "message" - string
	* MUST NOT contain user input
	* Message can be empty
	* Message will not be localized
* MUST "details" - array of "error detail object" 
	* Provides context for the error 
	* Validation errors SHOULD have detail object for each validation error

**Error Detail Object Format**
MUST NOT have any more than following properties
* MUST "documentationUrl" - string - fully qualified URL to support site
* MUST "errorCode" - string - MUST be English US-ASCII dot separated value (no whitespace)
* MUST "path" - JSON path format - definition of JSON path that caused issue
	* Path can be empty
* MUST "message" - string 
	* MUST NOT contain user input
	* Message can be empty
	* Message will not be localized

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
			"message" : "The server experienced a general failure",
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
			"message" : "Multiple validation errors occred, see details",
			"details" : [
				{
					"documentationUrl" : "https://developer.salesforce.com/marketing_cloud/errors/validation.email.address",
					"errorCode" : "validation.email.address.lackdomain",
					"path" : "$.[0].emailaddress",
					"message" : "lacks domain name"
				},
				{
					"documentationUrl" : "https://developer.salesforce.com/marketing_cloud/errors/validation.date",
					"errorCode" : "validation.date",
					"path" : "$.[0].date",
					"message" : "Data is invalid"
				},
				{
					"documentationUrl" : "https://developer.salesforce.com/marketing_cloud/errors/validation.email.address",
					"errorCode" : "validation.email.address.lackuser",
					"path" : "$.[1].emailaddress",
					"message" : "Lacks user"
				}
			]
		}
	],
	"meta" : null
}
```

# Request Body

* Dates MUST be ISO-8601 style of 2015-05-04T15:39:03Z
	* MUST NOT include any other ISO-8601 date style
	* requests MUST be in either 'Z' or plus/minus format

Servers MUST reject requests if the body has unexpected or undocumented
properties OR objects.

## HTTP GET - Retrieving

Routes MUST NOT support a http body.

## HTTP POST - Creating

TODO/FIXME need opinions and examples of when POST to collection is a "good idea"

Routes supporting POST MUST support as a single resource having the same JSON
schema as an item in the collection of the data section of a GET.  Routes MAY
support POST as a collection of resources having the same JSON schema as the
data section of a GET.  

## HTTP Delete 

Routes MAY support a request body. 

See also [HTTP verb substitution](#HTTP verb substitution)

### Deleting a single resource

Routes supporting DELETE of a single resource MUST have the resource identified
in the URI.  Routes supporting DELETE of a nested relationship MUST only delete
the relationship not the resource.

### Deleting multiple resources

Collection routes MAY support DELETE. Request should be a collection of
resources to be deleted.

## Custom actions

Routes supporting "custom action" MAY have a request body. The request and
response MAY have a different JSON schema than the other HTTP methods for the
resource.

## Concurrency 

Routes MUST support "If-Match" header with PUT/PATCH. If "If-Match" header is different than 
the expected current version the route MUST fail the request.

If the "If-Match" header is omitted the route SHOULD attempt the satisfy the
request regardless of concurrency problems.

See also [currency justification](justification/currency.md) 
See also [creating etag](pattern/creating_etag.md) 

## HTTP PUT - Replacing

Routes supporting PUT MUST support as a single resource having the same JSON
schema as an item in the collection of the data section of a GET. The request
MAY contain "If-Match" header.  Routes MAY support PUT as an collection of
relationship objects.  

Routes MUST NOT support creation through PUT.

See also [Upserting](pattern/upserting.md)

## HTTP Patch - Update

Routes supporting PATCH MUST support as a single resource having the same JSON
schema as an item in the collection of the data section of a GET.  The request
URI MUST uniquely identify the resource. The request MAY contain "If-Match" header.
The request format MAY exclude properties that need not be updated. This
includes omitting "id".

Routes SHOULD error if request attempts to update immutable properties with
error 400 "Bad Request".

API callers MAY clear values by providing the property with a null. If a
property is not provided a route MUST NOT act on that property.

See also [Upserting](pattern/upserting.md)

#Pagination 

Collection routes SHOULD support pagination. Requests with no "limit" or "size"
MUST always either return all items OR return 400 Bad Request regardless of
the count of results.

Requests that have more than one pagination type request then error.

Routes supporting any pagination MUST support Offset.

Results MUST be limited to no more than 1000 results.

**Routes pages responses**
* links MUST NOT change "limit" or "size" from the value in the request
* links MUST include all other query string parameters in request
* MUST include link "next". MUST provide "prev" href value of null
* MUST include link "prev". MUST provide "prev" href value of null
* MAY include link "first" where result MUST include first resource
* MAY include link "last" where result MUST include last resource

## Offset

Routes supporting any pagination MUST support Offset. Requests MUST require both
"offset" and "limit". 

Query string parameter "offset" MUST be the number of results to skip

Query string parameter "limit" MUST be the number of results to return

**Examples**
* ?limit=50&offset=50 would begin with the 51st object in a collection
* ?limit=50&offset=0 would begin with the 1st object in a collection
* ?limit=50&offset=11 would begin with the 12th object in a collection

## Cursor

Routes supporting pagination MAY support cursor. Requests MUST require "limit" and
either "before" OR "after".

Query string parameter "after" MUST be an identifier of the result item to continue from.

Query string parameter "before" MUST be an identifier of the result item to reverse from.

Query string parameter "limit" MUST be the number of results to return.

Requests with "after" or "before" MAY be respond to with a 400 if the cursor has been expired. 

Requests with both "after" and "before" MUST 400 "Bad Request". 

## Traditional Paging

Routes supporting pagination MAY support traditional paging. Requests MUST require "page" and "size".

Query string parameter "page" MUST be an identifier of the result to continue from. The calculation 
of resources to skip is "page-1" * "size". i.e. MUST start at page 1

Query string parameter "size" MUST be the maximum number of results to return.

# Partial Responses

Partial Responses are limited version of a resource.  Routes SHOULD support
partial responses. Properties to **include** MUST be specified by the query
string "fields". The format is [Google Partial Response Field
Format](https://developers.google.com/custom-search/json-api/v1/performance#partial).

* **starts** at response `data` property
* comma-separated list to select multiple properties
* property1/property2 to select property2 that is nested within property1; the pattern can be continued 
* a sub-selector to request a set of specific sub-properties of arrays or objects by placing expressions in parentheses "( )"
	* For example: fields=items(id,author/email) returns only the item ID and author's email for each element in the items array. Can also specify a single sub-field, where fields=items(id) is equivalent to fields=items/id.
* a wildcard, "\*"  can be used for all properties within a sub-object
    * For example: fields=items/pagemap/\* selects all objects in a pagemap.

See also [Salesforce Style](http://www.salesforce.com/us/developer/docs/api_rest/Content/dome_get_field_values.htm)

**Example**
Request for a partial response: This HTTP GET request for the above resource that uses the fields parameter significantly reduces the amount of data returned.
```
https://www.googleapis.com/plus/v1/activities/z12gtjhq3qn2xxl2o224exwiqruvtda0i?fields=url,object(content,attachments/url)

{
 "url": "https://plus.google.com/102817283354809142195/posts/F97fqZwJESL",
 "object": {
  "content": "A picture... of a space ship... launched from earth 40 years ago.",
  "attachments": [
   {
    "url": "http://apod.nasa.gov/apod/ap110908.html"
   }
  ]
 }
}
```

# Views

Views are expanded version of a resource. Routes MAY support one or many views.
Routes MUST NOT be used as a means to aggregate non-related resources.  Route's
view SHOULD expand relationship properties in the data object. 

Views over collection resources MUST return the same "totalCount" as
the normal collection resource.  i.e. Route's views MUST NOT implement any
implicit filter.

Routes MUST contain a superset of the resource's properties or whole
collection of the data section of a GET.

Route's view MAY support "partial responses" to filter the expanded data.

## URI pattern

Routes MUST expose views under an reserved keyword in path "{resource}/views/{view name}".

Routes MUST ONLY support GET http method on views.

## Examples

```
GET {service}/{resources}/views/exampleView
GET {service}/{resources}/{id}/views/exampleView
GET {service}/{resources}/{id}/{sub-resources}/views/exampleView
GET {service}/{resources}/{id}/{sub-resources}/{id}/views/exampleView
```

## Binary Views

Binary Views are limited sections of a resource in a native binary format.
Routes MAY support one or many binary views.

A binary view response MUST have the correct "Content-Type" header for its
resulting type. A view MUST NOT preform content negation based on the request's
"Accept" header. A view MAY reject a request based on the "Accept" header.

A binary view MUST include a "Content-Disposition" header with a file name.
Routes MAY choose which disposition to use.

### URI pattern

Routes MUST expose binary views under an reserved keyword in path
"{resource}/views/{binary name}".

Routes MUST ONLY support GET http method on binary views.

See also [Binary URL pattern]("justification/binary_url.md")

### Examples

```
GET {service}/{resources}/views/asJpg
GET {service}/{resources}/{id}/views/asPng
GET {service}/{resources}/{id}/{sub-resources}/views/asXml
GET {service}/{resources}/{id}/{sub-resources}/{id}/views/asWord
```

# Sorting 

Routes MAY support sorting. Routes supporting sorting MUST only use instances
of the query string `sort`.  Routes MAY support a subset of properties in the
resource's data section.  A sort's property MUST be a comma separated list of
valid properties.

By default the sort order is ascending.  Routes MUST support a descending flag
on all valid properties with a leading "-". 

# Filtering

Filtering is to limit by properties. Clients and routes SHOULD use filtering
to limit the results in a structured and absolute way.

Routes MAY support filtering. Routes supporting filtering MUST only use
instances of the query string `f[{property}][{operation}]`.   A filter's
property MUST be one of the properties on within a resource's data section.
Routes MAY support a subset of those properties.

Routes supporting filtering MUST support all combinations of properties.

##Properties

If filter property operates on a nested property structure the
"fields" format specification applies.

See also [Partial Responses]("#Partial Responses")

##Operations
Operation MUST be one of 
* eq  - MAY support comma separated values double quoted - Equals
* not - MAY support comma separated values double quoted - Not Equals
* gt  - MUST ONLY be numbers and dates - Greater than
* gte - MUST ONLY be numbers and dates - Greater than or equals
* lt  - MUST ONLY be numbers and dates - Lesser than
* lte - MUST ONLY be numbers and dates - Lesser than or equals

Multiple filters MUST be and'ed together.

## Values
For the operations "eq" and "not" routes MAY support comma separated values. Multiple values MUST be combined with a "or" operation, i.e. a SQL "in".

Operations gt, gte, lt, lte MUST support only a single values.

##Examples
```
f[{property}][{operation}]

f[color][eq]=blue
f[cost][eq]=50
f[content/locale][eq]=en

f[cost][lte]=50
f[cost][gte]=50

f[cost][lte]=100&f[cost][gte]=50
WHERE cost <= 100
AND   cost >= 50

f[cost][lt]=50
f[cost][gt]=50

f[color][not]=blue
f[cost][not]=50

f[color][eq]=blue,green,red&f[cost][lte]=50
WHERE color IN ('blue', 'green', 'red' )
AND   cost <= 50
```

# Searching

Searching is to find results based on one or many properties. 

Routes MAY support a search.  Routes MUST return 400 when searching is
requested but not available.  Routes SHOULD use search as a means to find
results by a query.  Routes MUST only support searching through query string
parameter "q".  Search SHOULD be case insensitive.

Routes MAY return 400 "Bad Request" for a query. 

Routes MUST return an empty collection if no results were found. 

Routes MUST NOT change what properties are searched over in a version.

Routes SHOULD search in a properties contains fashion

Views MAY support search.

See also [Querying](pattern/querying.md)

# General Style

## Query string
* Servers MUST accept and ignore extra query string parameters
* Query string parameters MUST be camelCase
* Query string parameters MUST be case insensitive

## Remote field expansion
Routes MUST NOT support a query string to expand relationship objects. 

See also [Views](#views)

## Path
Path SHOULD be case sensitive. Resource names MUST be nouns. 

Route SHOULD NOT exceed two resource names in the URI.  Routes MUST NOT expose
resource past two nested levels, implementations SHOULD wrap and expose the
sub-resource.

Routes MUST NOT use reserve word(s) in resource name
* "views"

**Examples**
{service}/{resources}
{service}/{resources}/{id}
{service}/{resources}/{id}/{sub-resources}
{service}/{resources}/{id}/{sub-resources}/{id}

Routes MUST NOT exceed
{service}/{resources}/{id}/{sub-resources}/{id}

Routes SHOULD additionally exist
{service}/{**sub**-resources}/{id}

### Collections
Routes that return multiple objects are collections.
* Routes MUST have a plural named 
* Routes MUST return an empty array when not objects found
* Routes MUST always be an array even when a single object
* Routes SHOULD be a set of fully hydrated objects 


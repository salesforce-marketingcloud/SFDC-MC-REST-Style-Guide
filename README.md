# Fuel API Standards

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be
interpreted as described in [RFC 2119](http://www.ietf.org/rfc/rfc2119.txt).

## Introduction
Fuel API standards project aim is to normalize API interaction across public
APIs of the Salesforce Marketing Cloud. Including the documenting of capability
and input/output formats of APIs so SDks can be automatically constructed and used.

API authors and framework developers should take careful note of
[API Description Format](#api-description-format) as the requirements for schema information
are very rigorous and are difficult to achieve without framework support.

## Table of contents

* Fuel API Standards
	* Introduction
	* Table of contents
* Versioning in the API
	* Example
	* Version numbering schema
	* Allowed changes/updates to a Version
* HTTP status codes
	* Implicit
	* Explicit
* HTTP Methods
	* POST
		* Default Create
		* Method/action substitution
	* GET
	* DELETE
	* PUT
	* PATCH
	* OPTIONS
	* HTTP method/action substitution
* HTTP Compression
	* Request
	* Response
* Headers
	* Request
	* Response
* Style
	* Path
	* Request Body
	* Query string
	* Collections
	* Remote field expansion
	* Marketing Cloud Specific Properties
* Response Format
	* Header
	* Localization
	* Envelope
		* Data Object
			* Data Property Naming
			* Data Property Types
				* Dates
				* Numbers
				* Arrays
				* Enumerations
				* Boolean
				* Relationships
					* Relationship Object
					* Representing Relationships
					* Relationships in Requests
		* Meta Object
			* Meta Object Structure
			* Link Object
			* Etag Object
	* Example
* Errors
	* Error Envelope
		* Error Detail Object
	* Validation Details
	* Example
	* Validation Example
* Field Specification Format
* Sorting
	* Example
* Partial Responses
* Filtering
	* Properties
	* Operations
	* Comma Separated Values
	* Values
	* Examples
* Pagination
	* Offset
	* Cursor
	* Traditional Paging
* Searching
* Authentication
* API Description Format


# Versioning in the API

The application protocol interface ("API") is published in multipleversions.
Implementations MUST use an indicator in the uniform resource locator ("URL").
This indicator applies to the entire API as a whole. Resources MUST NOT expose
their own versions.

    GET /{version}/{service}/{+resource}

## Example:

    /v4/data/contacts

See also [version location](justification/versionlocation.md)

## Version numbering schema

Versions in URL MUST start with the letter "v" and are followed by the version
number, which is a whole number. The version number is not tied to product
releases.

    version = "v" 1*DIGIT
    e.g. - v4

## Allowed changes/updates to a Version

Servers MAY add endpoints to an existing Version.

Routes on a version MAY add properties to a request or response payload. Routes
MUST NOT add required properties to a request payload. Routes MUST NOT remove
existing properties from a request or response payload. Routes MUST NOT change
type or the meaning of existing properties.

Routes on a version MAY add new optional query string parameters. Routes on a
version MUST NOT remove query string parameters. Routes on a version MUST NOT
add new required query string parameters.

# HTTP status codes
The API uses the hyper text transfer protocol ("HTTP").

## Implicit

Implicit codes are framework level, and generally unused
by Service and Route developers.

* 100 Continue
	* Large requests
* 206 Partial Content
	* Large requests
* 404 Not Found
	* Route not defined
* 405 Method Not Allowed
	* Invalid HTTP methods on route
	* Invalid "action" http verb substitution
* 406 Not Acceptable
	* Not compatible in Accept header
* 411 Length Required
	* Request must have length; security denial of service concern
* 412 Precondition Failed
	* Http1.1 compatibility
* 413 Request Entity Too Large
	* Exceeding configuration of web server
* 414 Request URI Too Long
	* Exceeding configuration of web server
* 415 Unsupported Media Type
	* Unsupported Content-Type header
* 416 Requested Range Not Satisfiable
	* large request resume
* 417 Expectation Failed
	* Http1.1 compatibility (except header)
* 429 Too Many Requests
	* API rate limiting
* 500 Internal Server Error
	* unexpected uncontrolled error scenario
* 502 Bad Gateway
	* reverse proxy style deployments
* 503 Service Unavailable
	* reverse proxy style deployments
* 504 Gateway Timeout
	* reverse proxy style deployments

## Explicit

Explicit codes are related to business logic responses, and will generally be
used by Service and Route developers.

* 200 OK
	* SHOULD NOT be used with POST/create
* 201 Created
	* SHOULD be used with POST/create
* 202 Accepted
	* REQUIRED to only be used with async requests
* 204 No content
	* Usage NOT RECOMMENDED. When used MUST indicate request was successful and state submitted was accepted with no modifications or defaults. Discouraged for consistency e.g.
		* Deletes MAY return helpful information like "id" with a 200
		* PUT MAY return the replaced content with a 200
		* POST/create MAY return the created content with implict defaults and generated "id" with a 200
* 304 Not modified
	* REQUIRED to only be used for freshness negotiation
* 400 Bad Request
	* Generic validation errors
* 401 Unauthorized
	* Request is not authenticated
* 403 Forbidden
	* Authenticated but lack permission to resource/operation
* 404 Not Found
	* Routes SHOULD return 403 if resource exists but user lacks access
* 412 Precondition Failed
	* REQUIRED to only be used for concurrency failure (i.e. If-Matches "etag")

Routes MUST NOT return redirect status codes (3XX Codes excluding 304).

# HTTP Methods

The API uses the hyper text transfer protocol ("HTTP"). Resources accept
several HTTP methods.

## POST

### Default Create

A resource MAY support POST create.  Routes supporting POST create MUST support as a single
resource having the same JSON schema as an item in the collection of the data
section of a GET. The resource contained in the request MUST be created, OR
the server MUST respond with an error.

The server MUST respond with a 201 status and the created resource. The server
MUST include the created identifier for the resource.  The server MUST respond
with a Location: header pointing to the newly created resource.

```javascript
/* Creating a Single Resource */
// POST /v4/content/articles
{
	"name" : "A new Article"
}
// 201 Created
// Location: /v4/content/articles/1
// Etag: W/"fd66fa03845ebcc44a0357641a1f99ef"
{
	"data" : [{
		"id" : 1,
		"name" : "A new Article"
	}],
	"meta" : {
		"etags" : [
			{ "etag" : "fd66fa03845ebcc44a0357641a1f99ef",  "path" : "$.data[0]" }
		]
	}
}
/* A new article with id 1 is created */
```

```javascript
/* Creating a nested object, tags, that will be associated to an article */
// POST /v4/content/tags
{
	"tag" : "business"
}
// 201 Created
// Location: /v4/content/tags/2
// Etag: W/"d59c46e81cc47bddfbc0474557393886"
{
	"data" : [{
		"id" : "2",
		"tag" : "business"
	}],
	"meta" : {
		"etags" : [
			{ "etag" : "d59c46e81cc47bddfbc0474557393886",  "path" : "$.data[0]" }
		]
	}
}
/* A tag is created with ID 2 */
```

```javascript
/* Appending a relationship to a Resource */
// POST /v4/content/articles/1/tags
{
	"id" : "2"
}
// 201 Created
// Location: /v4/content/articles/1/tags
// Etag: W/"5053893c9d20cc2113264fcbf77b8377"
{
	"data" : [{
		"id" : "1",
		"name" : "A new Article",
		"tags" : [
			{ "id" : "2" }
		]
	}],
	"meta" : {
		"etags" : [
			{ "etag" : "5053893c9d20cc2113264fcbf77b8377",  "path" : "$.data[0]" }
		]
	}
}
/* tag 2 is associated to article 1 */
```

```javascript
/* Alternate Flow for creating new article with relationship */
// POST /v4/content/articles
{
	"name" : "A new Article"
	"tags " : [
		{ "id" : "2" }
	]
}
// 201 Created
// Location: /v4/content/articles/1
// Etag: W/"5053893c9d20cc2113264fcbf77b8377"
{
	"data" : [{
		"id" : "1",
		"name" : "A new Article",
		"tags" : [
			{ "id" : "2" }
		]
	}],
	"meta" : {
		"etags" : [
			{ "etag" : "5053893c9d20cc2113264fcbf77b8377",  "path" : "$.data[0]" }
		]
	}
}
/* Article 1 is created with tag 2 relationship */
```

### Method/action substitution

Routes MUST instead follow rules for the requested method/action in lieu of POST create rules.

## GET

The identified resource MUST be retrieved and MUST be idempotent. i.e. MUST NOT
produce any side-effects.

Routes MUST NOT support a http body.

```javascript
// GET /v4/content/articles/1
// 200 OK
// Etag: W/"0e39098733467763f2f4ee9ab29aa649"
{
	"data" : [{
		"id" : "1",
		"name" : "An Article",
		"tags" : [
			{ "id" : "2" }
		]
	}],
	"meta" : {
		"etags" : [
			{ "etag" : "0e39098733467763f2f4ee9ab29aa649",  "path" : "$.data[0]" }
		]
	}
}
/* returns article id 1 */


// GET /v4/content/articles/1/tags/2
// 200 OK
// Etag: W/"d59c46e81cc47bddfbc0474557393886"
{
	"data" : [{
		"id" : "2",
		"tag" : "business"
	}],
	"meta" : {
		"etags" : [
			{ "etag" : "d59c46e81cc47bddfbc0474557393886",  "path" : "$.data[0]" }
		]
	}
}
/* returns a tag related to article 1 */


// GET /v4/content/articles/1/tags
// 200 OK
// Etag: W/"c428d847ba365dcd2b10a995548857a2"
{
	"data" : [
	{
		"id" : "2",
		"tag" : "business"
	},
	{
		"id" : "3",
		"tag" : "technology"
	}
	],
	"meta" : {
		"totalCount" : 2,
		"etags" : [
			{ "etag" : "c428d847ba365dcd2b10a995548857a2",  "path" : "$.data" },
			{ "etag" : "7393c891e51f6c4e9db10a27dd11dd05",  "path" : "$.data[0]" },
			{ "etag" : "bdfec487dfd57d7d68ad6955e3d67692",  "path" : "$.data[1]" }
		],
		"links" : [
			{ "href" : null, "name" : "prev", "path" : "$.data", "method" : "GET" },
			{ "href" : null, "name" : "next", "path" : "$.data", "method" : "GET" }
		]
	}
}
/* returns a collection of tags related to article 1 */

```

## DELETE

A resource MAY support DELETE. The identified resource MUST be deleted.
Routes MAY support a request body for DELETE requests.

Routes supporting DELETE of a single resource MUST have the resource identified
in the URI.  Routes supporting DELETE of a nested relationship MUST only delete
the relationship not the nested resources.

In version 4.0 of the style guide, Collection routes MUST NOT support DELETE.

### Examples

```javascript
/* Deleting a single resource */
// DELETE /v4/data/articles/1
// 200 OK
{
	"data" : [{
		"id" : "1"
	}]
}
/* Deletes an article with ID 1 */
```

```javascript
/* Deleting a single resource relationship */
// GET /v4/data/articles/2
// 200 OK
// Etag : W/"9a7668f8e9b9b6685c0624f416ba69c4"
{
	"data" : [{
		"id" : "2",
		"tags": [
			{ "id" : "1" }
		]
	}],
	"meta" : {
		"etags" : [
			{ "etag" : "9a7668f8e9b9b6685c0624f416ba69c4",  "path" : "$.data[0]" }
		]
	}
}
/* A tag exists on article id 2, with tag id 1 */

// DELETE /v4/data/articles/2/tags/1
// 200 OK
{
	"data" : [{
		"id" : "1"
	}]
}
/* Deletes the RELATIONSHIP of tag 1 on article 2 */

// GET /v4/data/tags/1
// Etag : W/"4d8291e8196a6358b0e918e38ee4c0de"
// 200 OK
{
	"data" : [{
		"id" : "1",
		"articles": []
	}],
	"meta" : {
		"etags" : [
			{ "etag" : "4d8291e8196a6358b0e918e38ee4c0de",  "path" : "$.data[0]" }
		]
	}
}

// GET /v4/data/articles/2
// 200 OK
// Etag : W/"f362309f1c74a38ef40cb600c04786cb"
{
	"data" : [{
		"id" : "2",
		"tags": [ ]
	}],
	"meta" : {
		"etags" : [
			{ "etag" : "f362309f1c74a38ef40cb600c04786cb",  "path" : "$.data[0]" }
		]
	}
}

/* tag still exists, but does not relate to article 2 any longer */
```

See also [HTTP verb substitution](#HTTP verb substitution)


## PUT

A resource MAY support PUT. A PUT SHOULD replace a resource and MUST NOT create a
resource.  A server SHOULD respond with a 200 when updating an existing
resource.

Resources can have immutable or server-defined fields (e.g. id, createdDate, etc).
A valid PUT payload SHOULD either omit the values or pass in the current ones.
A server SHOULD respond with a 400 when trying to replace immutable fields with
new values.

Routes supporting PUT MUST support as a single resource having the same JSON
schema as an item in the collection of the data section of a GET. The request
MAY contain "If-Match" header.

In Version 4.0 of this Style Guide, A server must not support PUT against a collection.

Routes MUST NOT support creation through PUT.

### Examples
```javascript
// GET /v4/data/articles/2
// 200 OK
// Etag : W/"putexample1"
{
	"data" : [{
		"id" : "2",
		"name" : "An Article",
		"tags": [
			{ "id" : "1" }
		]
	}],
	"meta" : {
		"etags" : [
			{ "etag" : "putexample1",  "path" : "$.data[0]" }
		]
	}
}
/* A tag exists on article id 2, with tag id 1 */

// PUT /v4/data/articles/2
{
	"id" : "2",
	"name" : "A Renamed Article",
	"tags": [
		{ "id" : "1" }
	]
}
// 200 OK
// Etag: W/"putexample2"
{
	"data" : [{
		"id" : "2",
		"name" : "A Renamed Article",
		"tags": [
			{ "id" : "1" }
		]
	}],
	"meta" : {
		"etags" : [
			{ "etag" : "putexample2",  "path" : "$.data[0]" }
		]
	}
}
/* Update article id 2 */

// PUT /v4/data/articles/2
// If-Match: W/"putexample1"
{
	"id" : "2",
	"name" : "A Re-Renamed Article",
	"tags": [
		{ "id" : "1" }
	]
}
// 412 Precondition Failed
{
    "error" : {
        "requestId" : "1309da52-a2c5-4efb-ab87-349eeab3ae60",
        "documentationUrl" : "https://developer.salesforce.com/marketing_cloud/errors/client.failure.etagmismatch",
        "statusCode" :  412,
        "errorCode" : "client.failure.etagmismatch",
        "message" : "Invalid Request - Etag Mismatch",
        "details" : []
    }
}
/* PUT failed to update due to Etag mismatch */

// PUT /v4/data/articles/2
// If-Match: W/"putexample2"
{
	"id" : "2",
	"name" : "A Re-Re-Renamed Article",
	"tags": [
		{ "id" : "1" }
	]
}
// 200 OK
// Etag: W/"putexample3"
{
	"data" : [{
		"id" : "2",
		"name" : "A Re-Re-Renamed Article",
		"tags": [
			{ "id" : "1" }
		]
	}],
	"meta" : {
		"etags" : [
			{ "etag" : "putexample3",  "path" : "$.data[0]" }
		]
	}
}
/* PUT successfully updates article 2 while specificying which version of the document to modify */
```

See also [Upserting](pattern/upserting.md)


## PATCH

A resource MAY support PATCH. Updates an existing resource in parts.

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

### Examples
```javascript
// GET /v4/data/articles/2
// 200 OK
// Etag : W/"006b1b00e09eda9c9c95353ff1eb58f1"
{
	"data" : [{
		"id" : "2",
		"name" : "An Article",
		"description" : "A story with words.",
		"tags": [
			{ "id" : "1" }
		]
	}],
	"meta" : {
		"etags" : [
			{ "etag" : "006b1b00e09eda9c9c95353ff1eb58f1",  "path" : "$.data[0]" }
		]
	}
}
/* A tag exists on article id 2, with tag id 1 */

// PATCH /v4/data/articles/2
{
	"name" : "A Renamed Article"
}
// 200 OK
// Etag: W/"6ee3a8df40b4ed21287bbbb745794ed8"
{
	"data" : [{
		"id" : "2",
		"name" : "A Renamed Article",
		"description" : "A story with words.",
		"tags": [
			{ "id" : "1" }
		]
	}],
	"meta" : {
		"etags" : [
			{ "etag" : "6ee3a8df40b4ed21287bbbb745794ed8",  "path" : "$.data[0]" }
		]
	}
}
/* Update article id 2 */

// PATCH /v4/data/articles/2
{
	"description" : null
}
// 200 OK
// Etag: W/"b8d93a92c87ff73dea1a9fb0d1721321"
{
	"data" : [{
		"id" : "2",
		"name" : "A Renamed Article",
		"description" : null,
		"tags": [
			{ "id" : "1" }
		]
	}],
	"meta" : {
		"etags" : [
			{ "etag" : "b8d93a92c87ff73dea1a9fb0d1721321",  "path" : "$.data[0]" }
		]
	}
}
/* Sets description (and only description) field to null */

```


## OPTIONS
In response to OPTIONS method servers
* MUST respond with valid methods
* MUST NOT expose any other data
* Is reserved for cross-origin resource sharing (CORS)


## HTTP method/action substitution
A server MUST support the HTTP method "POST" to allow HTTP methods a client may
not support.

POST query string "action" MUST be a case sensitive value of
* PUT
* PATCH
* DELETE

Routes MUST respond with error to
* PuT
* Patch
* delete
* etc other case variations

### Examples
```
POST {service}/{resources}?action={name}
POST {service}/{resources}/{id}?action={name}
POST {service}/{resources}/{id}/{sub-resources}?action={name}
POST {service}/{resources}/{id}/{sub-resources}/{id}?action={name}

// POST /v4/content/articles/1?action=DELETE
// Method Substitution for DELETE, must behave exactly the same as:
// DELETE /v4/content/articles/1

// POST /v4/content/articles/1?action=delete
// 400 Bad Request
// Fails due to case sensitivity

```

# HTTP Compression

Routes can make important performance gains by utilizing built-in compression in HTTP.

## Request
Servers MUST support "gzip" for requests, optional section of HTTP1.1. Requests with a body and header
"Content-Encoding: gzip" are uncompressed before processing in accordance to [Content-Encoding RFC7231 Section 3.1.2.2](https://tools.ietf.org/html/rfc7231#section-3.1.2.2).

See also [Hypertext Transfer Protocol (HTTP/1.1): Semantics and Content RFC7231](https://tools.ietf.org/html/rfc7231)  
See also [Http2 Use of Compression](http://http2.github.io/http2-spec/#rfc.section.10.6)  

## Response
Servers MUST support "gzip" for responses, optional section of HTTP1.1. Requests with a header "Accept-Encoding: gzip" MUST be compresesed in accordance to
[Accept-Encoding RFC7231 Section 5.3.4](https://tools.ietf.org/html/rfc7231#section-5.3.4)

See also [Hypertext Transfer Protocol (HTTP/1.1): Semantics and Content RFC7231](https://tools.ietf.org/html/rfc7231)  
See also [Http2 Use of Compression](http://http2.github.io/http2-spec/#rfc.section.10.6)  

# Headers
A specific set of headers are supported. A server MUST NOT respect any header
outside of the defined list.

## Request
* Authorization
* Accept-Encoding
	* Client MAY provide for response body compression. Supported value(s) are "gzip"
* Content-Encoding
	* Client MAY provide for request body compression. Supported value(s) are "gzip"
* Origin, Access-Control-Request-Method, Access-Control-Request-Headers
	* Support for CORS
* Original-Request-Id
	* Client MAY provide a tracking identifier. 
	* MUST be less than 1024 characters. Characters MUST be within US-ASCII.
* If-Match
	* Client MAY provide etag values on PUT/PATCH for concurrency problems
* If-None-Match
	* Client MAY provide etag values on GET for cache behavior of HTTP code 304
* Content-Type
	* Client SHOULD provide "application/json; charset=utf-8"
* Accept
	* Client MAY provide an "accept" header. Routes MUST ignore the header.

## Response
* Access-Control-Allow-Origin, Access-Control-Allow-Methods, Access-Control-Allow-Headers, Access-Control-Max-Age, Access-Control-Expose-Headers, Access-Control-Allow-Credentials
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
	* MUST be included in all responses. MUST be less than 1024 characters.
	  Characters MUST be within US-ASCII.
* Original-Request-Id
	* MUST be back of Request's Original-Request-Id if provided.

* Content-Type
	* Responses MUST be "application/json; charset=utf-8"


# Style

## Path
Path SHOULD be case sensitive. Resource names MUST be plural nouns.

Route SHOULD NOT exceed two resource names in the URI.  Routes MUST NOT expose
resource past two nested levels, implementations SHOULD wrap and expose the
sub-resource.

Routes MUST NOT use reserve word(s) in resource name
* views
* files

### Examples

    {service}/{resources}
    {service}/{resources}/{id}
    {service}/{resources}/{id}/{sub-resources}
    {service}/{resources}/{id}/{sub-resources}/{id}

Routes MUST NOT exceed

    {service}/{resources}/{id}/{sub-resources}/{id}

Routes SHOULD additionally exist

    {service}/{**sub**-resources}/{id}

## Request Body

* Dates MUST be ISO 8601 style of 2015-05-04T15:39:03Z
	* MUST NOT include any other ISO 8601 date style
	* requests MUST be in either 'Z' or plus/minus format

* Servers MUST reject requests if the body has unexpected or undocumented
properties OR objects.

## Query string

* Servers MUST accept and ignore extra query string parameters
* Query string parameters MUST be documented and communicated to API users as camelCase
* Query string parameters MUST be interpreted as case insensitive

## Collections

Collections are Routes that return multiple objects.

* Routes MUST have a plural name
    * `/v4/content/articles`
    * **NOT**  `/v4/content/article`
* Routes MUST return 200 OK and an empty array when no objects are found
* Routes MUST always return an array even when only a single object is returned
* Routes SHOULD be a set of fully hydrated objects

## Remote field expansion

Routes MUST NOT support a query string to expand relationship objects.

## Marketing Cloud Specific Properties

When referencing an Enterprise, User or Business Unit, Routes MUST publish a relationship object.

TODO: figure this stuff out

* member - relationship - member, i.e. business unit, of the reference
	* Example ` "member" : { "id" : "20720" } `
	* Example ` "ownedBy" : [ { "id" : "20720" } ] `
* enterprise - relationship - enterprise that member sits under. For many customers this is the same as member
	* Example ` "enterprise" : { "id" : "20720" } `
* employee/user - string - specific user being references
	* Example ` "createdBy" : { "id" : "20720" } `
	* Example ` "employees" : [ { "id" : "20720" } ] `
	* Example ` "user" : { "id" : "20720" } `
	* Example ` "users" : [ { "id" : "20720" } ]`

Routes SHOULD NOT reference "member" and/or "employee/user" without referencing the parent "enterprise".

# Response Format

Responses from a route MUST have the prescribed envelope format. Routes MUST provide all properties even when value is "null".

## Header

Routes MUST respond to resource requests with an "etag" header.  The
"meta" section MUST contain the same "etag" value. Routes MUST use weak tags.

Collection routes MUST respond with an "etag" header that is representative of
their results. Collection routes MUST also respond with "etag" values for all
included resources in the "meta". Routes MUST only perform weak etag comparisons.

Routes MUST respond with a "Location" header describing the location of the
newly created resource when a resource is created.

## Localization

Routes adhering to Style Guide 4.0 MUST NOT provide localized values.

## Envelope

Routes MUST respond with the following JSON envelope. The envelope MUST contain at least
one Data or Error object.

| Property Name | Type   | Cardinality | Description                                    |
|---------------|--------|-------------|------------------------------------------------|
| Data          | Array  | 0 - 1       | A collection of Data objects, can be empty.    |
| Meta          | Object | 0 - 1       | Object containing metadata about the response. |
| Error         | Object | 0 - 1       | Object containing information about an error.  |

### Data Object

Routes that respond with data must contain that data in the Data Object.  Data objects
MUST contain an identifier, and any number of additional properties.

| Property Name | Type   | Cardinality | Description                                    |
|---------------|--------|-------------|------------------------------------------------|
| Id            | String | 1 - 1       | An identifier for the resource.                |
| *             | *      | *           | MAY have any number of additional properties.  |

#### Data Property Naming

* Property Names SHOULD be camelCase
* Properties containing a URL MUST be suffixed with "Url"
    * `imageUrl : "http://i0.kym-cdn.com/photos/images/original/000/038/262/hahaguy.jpg"`
* Properties containing a Date MUST be suffixed with "Date"
    * `createdDate : "2015-05-21T00:00:00Z"`

#### Data Property Types

Basic types must correspond to [JSON RFC 7159](https://tools.ietf.org/html/rfc7159)

##### Dates

* MUST include date, time and timezone.
* Responses MUST always be returned in UTC
* MUST be ISO 8601 style of 2015-05-04T15:39:03Z
    * Routes MUST NOT accept any other ISO 8601 date style
    * Requests MUST be in either 'Z' or plus/minus format
        * `2015-05-21T00:00:00Z`
        * `2015-05-21T08:00:00-08:00`
    * Server MAY accept no timezone for **recurring** events
        * See pattern [Recurring events](pattern/recurringevent.md)

##### Numbers

RECOMMENDED to be unquoted. Examples of exceptions are when exact precision must be maintained in access of double precision numbers [IEEE 754](http://grouper.ieee.org/groups/754/).

* Decimal One Million SHOULD be represented as:
	* `1000000.00`
	* NOT `"1000000.00"`
	* NOT `"1,000,000.00"`

##### Arrays

* MUST contain homogeneous values
    * `[1,2,3,4]`
    * `["a", "b", "c", "d"]`
    * NOT `[1, 'a', 2, 'b']`

##### Enumerations

Enumerations are a fixed list of strings that represent the complete options for a property. That values are contained by the list is documented and enforced through the required API description document. Routes MAY have enumeration strings that new objects can not be set to, i.e. retired values.

* REQUIRED for all values to be listed in API description document
* Value MUST be a string that is short and provides meaning in English
	* `"EXAMPLE_1"` or `"Example2"`
	* NOT `1` or

```javascript
// /v4/content/post
{
	"data" : [{
		"id" : "1",
		"name" : "Blog post",
		"type" : "Blog"
	}],
	"meta" : {
		"etags" : [
			{ "etag" : "f8b8a65f4462ec4264959ac6e367c815",  "path" : "$.data[0]" }
		]
	}
}

// /v4/content/article
{
	"data" : [{
		"id" : "2",
		"name" : "An Article",
		"type" : "Article"
	}],
	"meta" : {
		"etags" : [
			{ "etag" : "cfce3754f877c1d0f3672a195094a7ff",  "path" : "$.data[0]" }
		]
	}
}
```

See also [API Description Document](#api-description-document)

##### Boolean

Boolean types must be represented by `true` or `false` in accordance to JSON

```javascript
// a published article
{
	"data" : [{
		"id" : "1",
		"name" : "An Article",
		"published" : true
	}],
	"meta" : {
		"etags" : [
			{ "etag" : "ff494fb3fa996dfba5504c90f804bdc7",  "path" : "$.data[0]" }
		]
	}
}
```

##### Relationships

A relationship is a reference to an external object.  In this style guide,
relationships are always represented as objects.

In Version 4.0 of this style guide, relationship objects MUST contain ONLY an id.


###### Relationship Object

* MUST NOT contain properties other than Id.

| Property Name | Type   | Cardinality | Description                                    |
|---------------|--------|-------------|------------------------------------------------|
| Id            | String | 1 - 1       | An identifier for the related resource.        |

###### Representing Relationships

* To represent a 1-1 relationship, a single relationship object MUST be used.

```javascript
// Article has a 1-1 relationship to a single Author
{
	"data" : [{
		"id" : "1",
		"name" : "An Article",
		"Author" : {
		    "id" : "6"
	        }

	}],
	"meta" : {
		"etags" : [
			{ "etag" : "b7d64d81f61111fcad2994c44f4af86c",  "path" : "$.data[0]" }
		]
	}
}
```

* To represent a 1-N or M-N relationship, an array of relationship objects MUST be used.

```javascript
// Articles have M-N relationships to Tags
{
	"data" : [{
		"id" : "1",
		"name" : "An Article",
		"Tags" : [
			{ "id" : "1" }
			{ "id" : "2" }
		]
	}],
	"meta" : {
		"etags" : [
			{ "etag" : "c42821663ef83bce19b57c90d5221e02",  "path" : "$.data[0]" }
		]
	}
}
```

###### Relationships in Requests

* Requests containing relationship objects MUST only modify relationship
  between the two resources, and not the related objects themselves.
* Servers SHOULD respond with an error to requests containing any properties
  besides "id" a relationship object.

See also [Relationship definition](glossary.md)

See also [Relationship object](justification/relationshipobject.md)

### Meta Object

In Version 4.0, Routes MUST include a meta object at root level of the response envelope.


#### Meta Object Structure

* The Meta object MUST NOT include any properties outside of those defined below.

| Property Name | Type   | Cardinality | Description                                    |
|---------------|--------|-------------|------------------------------------------------|
| totalCount    | Integer| 0 - 1       | Total # of records available in a collection.  |
| etags         | Array  | 1 - 1       | An array of Etag objects.                      |
| links         | Array  | 0 - 1       | An array of Link objects.                      |

#### Link Object

* Link Objects MUST NOT include properties outside of those defined below.

| Property Name | Type   | Cardinality | Description                                    |
|---------------|--------|-------------|------------------------------------------------|
| href          | String | 1 - 1       | A URI or URL to a state of the resource.       |
| name          | String | 1 - 1       | Description of state being referenced. MUST be one of: (prev, next, self, first, last). See also [HATEOAS](justerification/hateoas.md) |
| path          | String | 1 - 1       | JSON Path of the object the link belongs to.   |
| method        | String | 0 - 1       | HTTP method to use with `href`                 |

#### Etag Object

* Etag Objects MUST NOT include properties outside of those defined below.

| Property Name | Type   | Cardinality | Description                                    |
|---------------|--------|-------------|------------------------------------------------|
| etag          | String | 1 - 1 | identifier for current state of resource. See also [Concurrency](justification/concurrency.md) |
| path          | String | 1 - 1 | JSON Path of the object the etag belongs to.         |

## Example
```javascript
// GET /v4/data/foos?offset=1&limit=2
// Etag: W/"a95ffbe406a5e4fb76a4d1c25c4b2995"
{
	"data" : [
		{
			"id" : "1",
			"fooString" : "bar",
			"fooDate" : "2015-06-02T12:00:00Z",
			"fooArray" : [
				"2015-06-02T12:00:00Z",
				"2016-06-02T12:00:00Z",
				"2017-06-02T12:00:00Z"
			]
		},
		{
			"id" : "2",
			"fooString" : "bar2",
			"fooDate" : "2015-06-02T12:00:00Z",
			"fooArray" : [
				"2015-06-02T12:00:00Z",
				"2016-06-02T12:00:00Z",
				"2017-06-02T12:00:00Z"
			]
		}
	],


	"meta" : {
		"totalCount" : 20,

		"links" : [
			{ "name" : "prev", "method": "GET", "href" : "/v4/data/foos?offset=0&limit=2", "path" : "$.data" },
			{ "name" : "next", "method": "GET", "href" : "/v4/data/foos?offset=3&limit=2", "path" : "$.data" }
		],

		"etags" : [
			{ "etag" : "a95ffbe406a5e4fb76a4d1c25c4b2995", "path" : "$.data" },
			{ "etag" : "a15e8ad4000684fdc8a04cd22ac835ca", "path" : "$.data.[0]" },
			{ "etag" : "754bd3129114a6eab71374bb12492896", "path" : "$.data.[1]" }
		]
	}
}
```


# Errors

Requests resulting in an Error SHOULD be idempotent (no side effect). Errors MUST
be in the prescribed error JSON format. If a Successful request to a route would NOT have responded with JSON, a different format MAY be used.

## Error Envelope

* The Error object MUST NOT include any properties outside of those defined below.

| Property Name    | Type   | Cardinality | Description                                 |
|------------------|--------|-------------|---------------------------------------------|
| requestId        | String | 0 - 1       | A unique id representing the failed request.|
| documentationUrl | String | 1 - 1       | Fully qualified URL to a localized support  |
|                  |        |             | site that describes the error that occurred.|
| statusCode       | Integer| 1 - 1       | The HTTP response code that was returned.   |
| errorCode        | String | 1 - 1       | MUST match errorCode format ABNF (i.e. error.code.snake\_casing). "errorCodes" are REQUIRED to be registered with Marketing Cloud Platform team. Example `validation.email.subject_empty` |
| message          | String | 1 - 1       | String describing the error that occurred.  MUST NOT contain user input. MAY be empty.  MUST NOT be localized. |
| details          | Array  | 1 - 1       | An array of Error Detail objects. Provides context for the error that occured.  Validation objects SHOULD have an Error.     Detail object for each validation error.    |


"errorCode" MUST follow the ABNF grammar. See also [RFC2234 Augmented BNF syntax](https://tools.ietf.org/html/rfc2234)

```abnf
error = category *(DOT category) DOT item

category = 3*LOWER
item = 3*( LOWER / LOWER UNDERSCORE LOWER )

DOT = %x2E
UNDERSCORE = %x5F
LOWER = %x61-7A
```


### Error Detail Object

* The Error Detail Object MUST NOT include any properties outside of those defined below.

| Property Name    | Type   | Cardinality | Description                                 |
|------------------|--------|-------------|---------------------------------------------|
| documentationUrl | String | 1 - 1       | Fully qualified URL to a localized support  site that describes the error that occurred.|
| errorCode        | String | 1 - 1       | MUST be a English US-ASCII value. errorCodes MUST be registered with Marketing.  Cloud Platform team.|
| path             | String | 1 - 1       | JSON path that identifies the property that caused the error. MAY be empty. |
| message          | String | 1 - 1       | String describing the error that occurred.  MUST NOT contain user input. MAY be empty.  MUST NOT be localized. |


## Validation Details

* Validation errors SHOULD utilize JSON path

## Example

```json
{
	"error" : {
		"requestId" : "e6f521f2-d8ae-4f37-82d7-c2fc1f9d5a9d",
		"documentationUrl" : "https://developer.salesforce.com/marketing_cloud/errors/server.failure.general",
		"statusCode" :  500,
		"errorCode" : "server.failure.general",
		"message" : "The server experienced a general failure",
		"details" : []
	}
}
```

## Validation Example

```javascript
// POST /v4/data/fakeRoute
{
	"date" : "not valid date",
	"date2" : "2015-06-02T00:00:00Z",
	"emailAddress" : "foo_not_valid@",
	"emailAddress2" : "@example.com",
}

// 400 Bad Request
{
	"error" :
		{
			"documentationUrl" : "https://developer.salesforce.com/marketing_cloud/errors/validation.error.aggregate",
			"statusCode" :  400,
			"errorCode" : "validation.error.aggregate",
			"message" : "Multiple validation errors occured, see details",
			"details" : [
				{
					"documentationUrl" : "https://developer.salesforce.com/marketing_cloud/errors/validation.email.address",
					"errorCode" : "validation.email.address_lackdomain",
					"path" : "$.emailAddress",
					"message" : "lacks domain name"
				},
				{
					"documentationUrl" : "https://developer.salesforce.com/marketing_cloud/errors/validation.date",
					"errorCode" : "validation.date",
					"path" : "$.date",
					"message" : "Date is invalid"
				},
				{
					"documentationUrl" : "https://developer.salesforce.com/marketing_cloud/errors/validation.email.address",
					"errorCode" : "validation.email.address_lackuser",
					"path" : "$.emailAddress2",
					"message" : "Lacks user"
				}
			]
		}
}
```

# Field Specification Format
The format is [Google Partial Response Field
Format](https://developers.google.com/custom-search/json-api/v1/performance#partial).

See Also: [Google's Description of Partial Response Field](pattern/google_partial_responses.md)

* **starts** at response `data` property
* property1/property2 to select property2 that is nested within property1; the pattern can be continued
* a sub-selector to request a set of specific sub-properties of arrays or objects by placing expressions in parentheses "( )"
	* For example: fields=items(id,author/email) returns only the item ID
	  and author's email for each element in the items array. Can also
	  specify a single sub-field, where fields=items(id) is equivalent to
	  fields=items/id.
* a wildcard, "\*"  can be used for all properties within a sub-object
    * For example: fields=items/pagemap/\* selects all objects in a pagemap.

# Sorting

Routes MAY support sorting. Routes supporting sorting MUST only use instances
of the query string `sort`.  Properties MUST be defined with the Field Specification Format.
Routes MAY support a subset of properties in the
resource's data section.  A sort's property MUST be a comma separated list of
valid properties.  A sort with a field specification wider than one property
MUST return 400 error.

By default the sort order is ascending.  Routes MUST support a descending flag
on all valid properties with a leading "-" on the field specification format.

## Example

```javascript
/* Sort by cores ascending */
// GET /v4/data/supercomputers?sort=cores
// 200 OK
// Etag: W/"185b27b1a0f81763fc88271e0524ea31"
{
	"data" : [
		{
			"id" : "10",
			"name" : "Government",
			"vendor" : "Cray Inc.",
			"cores" : 72800,
			"firstAppearance" : "2007-11-01T00:00:00Z",
			"tflops" : 3577.0
		},
		{
			"id" : "6",
			"name" : "Swiss National Supercomputing Centre (CSCS)",
			"vendor" : "Cray Inc.",
			"cores" : 115984,
			"firstAppearance" : "2011-11-01T00:00:00Z",
			"tflops" : 6271.0
		},
		{
			"id" : "9",
			"name" : "DOE/NNSA/LLNL",
			"vendor" : "IBM",
			"cores" : 393216,
			"firstAppearance" : "2005-11-01T00:00:00Z",
			"tflops" : 4293.3
		},
		{
			"id" : "8",
			"name" : "Forschungszentrum Juelich (FZJ)",
			"vendor" : "IBM",
			"cores" : 458752,
			"firstAppearance" : "1993-06-01T00:00:00Z",
			"tflops" : 5008.9
		},
		{
			"id" : "7",
			"name" : "Texas Advanced Computing Center/Univ. of Texas",
			"vendor" : "Dell",
			"cores" : 462462,
			"firstAppearance" : "2001-11-01T00:00:00Z",
			"tflops" : 5168.1
		},
		{
			"id" : "2",
			"name" : "DOE/SC/Oak Ridge National Laboratory",
			"vendor" : "Cray Inc.",
			"cores" : 560640,
			"firstAppearance" : "1993-06-01T00:00:00Z",
			"tflops" : 17590.0
		},
		{
			"id" : "4",
			"name" : "RIKEN Advanced Institute for Computational Science (AICS)",
			"vendor" : "Fujitsu",
			"cores" : 705024,
			"firstAppearance" : "2010-11-01T00:00:00Z",
			"tflops" : 10510.0
		},
		{
			"id" : "5",
			"name" : "DOE/SC/Argonne National Laboratory",
			"vendor" : "IBM",
			"cores" : 786432,
			"firstAppearance" : "1995-06-01T00:00:00Z",
			"tflops" : 8586.6
		},
		{
			"id" : "3",
			"name" : "DOE/NNSA/LLNL",
			"vendor" : "IBM",
			"cores" : 1572864,
			"firstAppearance" : "2005-11-01T00:00:00Z",
			"tflops" : 17173.2
		},
		{
			"id" : "1",
			"name" : "National Super Computer Center in Guangzhou",
			"vendor" : "NUDT",
			"cores" : 3120000,
			"firstAppearance" : "2012-06-01T00:00:00Z",
			"tflops" : 33862.7
		},
	],

	"meta" : {
		"totalCount" : 10,

		"links" : [
			{ "href" : null, "name" : "prev", "path" : "$.data", "method" : "GET" },
			{ "href" : null, "name" : "next", "path" : "$.data", "method" : "GET" }
		],

		"etags" : [
			{ "etag" : "185b27b1a0f81763fc88271e0524ea31",  "path" : "$.data" },
			{ "etag" : "ae1701637a41427410ddb839cc3fa1e2",  "path" : "$.data.[0]" },
			{ "etag" : "5ba5b9d9648b1709a7996fbbe0091a2b",  "path" : "$.data.[1]" },
			{ "etag" : "5e45d009514280229f8ea8dc982ac354",  "path" : "$.data.[2]" },
			{ "etag" : "cbd0dd679a7ce77acaa5059e101accdc",  "path" : "$.data.[3]" },
			{ "etag" : "53332c616504673196b953fc9d9ae194",  "path" : "$.data.[4]" },
			{ "etag" : "dc2e0302fae3ed17ce753fd8a73371d5",  "path" : "$.data.[5]" },
			{ "etag" : "26a96b47743ac2bb1822ed79753e15df",  "path" : "$.data.[6]" },
			{ "etag" : "4789647b72be508c7027d5d8921b2e75",  "path" : "$.data.[7]" },
			{ "etag" : "a9f739f2bb4735d9500259daa0e3a576",  "path" : "$.data.[8]" },
			{ "etag" : "681604816754c297dbbb895cb1bd1c74",  "path" : "$.data.[9]" }
		]
	}
}
```

```javascript
/* Sort by cores descending */
// GET /v4/data/supercomputers?sort=-cores
// 200 OK
// Etag: W/"68ec25c669bcc6a2b5326b723885cc7f"
{
	"data" : [
		{
			"id" : "1",
			"name" : "National Super Computer Center in Guangzhou",
			"vendor" : "NUDT",
			"cores" : 3120000,
			"firstAppearance" : "2012-06-01T00:00:00Z",
			"tflops" : 33862.7
		},
		{
			"id" : "3",
			"name" : "DOE/NNSA/LLNL",
			"vendor" : "IBM",
			"cores" : 1572864,
			"firstAppearance" : "2005-11-01T00:00:00Z",
			"tflops" : 17173.2
		},
		{
			"id" : "5",
			"name" : "DOE/SC/Argonne National Laboratory",
			"vendor" : "IBM",
			"cores" : 786432,
			"firstAppearance" : "1995-06-01T00:00:00Z",
			"tflops" : 8586.6
		},
		{
			"id" : "4",
			"name" : "RIKEN Advanced Institute for Computational Science (AICS)",
			"vendor" : "Fujitsu",
			"cores" : 705024,
			"firstAppearance" : "2010-11-01T00:00:00Z",
			"tflops" : 10510.0
		},
		{
			"id" : "2",
			"name" : "DOE/SC/Oak Ridge National Laboratory",
			"vendor" : "Cray Inc.",
			"cores" : 560640,
			"firstAppearance" : "1993-06-01T00:00:00Z",
			"tflops" : 17590.0
		},
		{
			"id" : "7",
			"name" : "Texas Advanced Computing Center/Univ. of Texas",
			"vendor" : "Dell",
			"cores" : 462462,
			"firstAppearance" : "2001-11-01T00:00:00Z",
			"tflops" : 5168.1
		},
		{
			"id" : "8",
			"name" : "Forschungszentrum Juelich (FZJ)",
			"vendor" : "IBM",
			"cores" : 458752,
			"firstAppearance" : "1993-06-01T00:00:00Z",
			"tflops" : 5008.9
		},
		{
			"id" : "9",
			"name" : "DOE/NNSA/LLNL",
			"vendor" : "IBM",
			"cores" : 393216,
			"firstAppearance" : "2005-11-01T00:00:00Z",
			"tflops" : 4293.3
		},
		{
			"id" : "6",
			"name" : "Swiss National Supercomputing Centre (CSCS)",
			"vendor" : "Cray Inc.",
			"cores" : 115984,
			"firstAppearance" : "2011-11-01T00:00:00Z",
			"tflops" : 6271.0
		},
		{
			"id" : "10",
			"name" : "Government",
			"vendor" : "Cray Inc.",
			"cores" : 72800,
			"firstAppearance" : "2007-11-01T00:00:00Z",
			"tflops" : 3577.0
		}
	],

	"meta" : {
		"totalCount" : 10,

		"links" : [
			{ "href" : null, "name" : "prev", "path" : "$.data", "method" : "GET" },
			{ "href" : null, "name" : "next", "path" : "$.data", "method" : "GET" }
		],

		"etags" : [
			{ "etag" : "68ec25c669bcc6a2b5326b723885cc7f",  "path" : "$.data" },
			{ "etag" : "681604816754c297dbbb895cb1bd1c74",  "path" : "$.data.[0]" },
			{ "etag" : "a9f739f2bb4735d9500259daa0e3a576",  "path" : "$.data.[1]" },
			{ "etag" : "4789647b72be508c7027d5d8921b2e75",  "path" : "$.data.[2]" },
			{ "etag" : "26a96b47743ac2bb1822ed79753e15df",  "path" : "$.data.[3]" },
			{ "etag" : "dc2e0302fae3ed17ce753fd8a73371d5",  "path" : "$.data.[4]" },
			{ "etag" : "53332c616504673196b953fc9d9ae194",  "path" : "$.data.[5]" },
			{ "etag" : "cbd0dd679a7ce77acaa5059e101accdc",  "path" : "$.data.[6]" },
			{ "etag" : "5e45d009514280229f8ea8dc982ac354",  "path" : "$.data.[7]" },
			{ "etag" : "5ba5b9d9648b1709a7996fbbe0091a2b",  "path" : "$.data.[8]" },
			{ "etag" : "ae1701637a41427410ddb839cc3fa1e2",  "path" : "$.data.[9]" }
		]
	}
}
```

```javascript
/* Sort by first appearance and cores  */
// GET /v4/data/supercomputers?sort=-firstAppearance,-cores
// 200 OK
// Etag: W/"773d2f209b482b58f48c24970c28c4c9"
{
	"data" : [
		{
			"id" : "1",
			"name" : "National Super Computer Center in Guangzhou",
			"vendor" : "NUDT",
			"cores" : 3120000,
			"firstAppearance" : "2012-06-01T00:00:00Z",
			"tflops" : 33862.7
		},
		{
			"id" : "6",
			"name" : "Swiss National Supercomputing Centre (CSCS)",
			"vendor" : "Cray Inc.",
			"cores" : 115984,
			"firstAppearance" : "2011-11-01T00:00:00Z",
			"tflops" : 6271.0
		},
		{
			"id" : "4",
			"name" : "RIKEN Advanced Institute for Computational Science (AICS)",
			"vendor" : "Fujitsu",
			"cores" : 705024,
			"firstAppearance" : "2010-11-01T00:00:00Z",
			"tflops" : 10510.0
		},
		{
			"id" : "10",
			"name" : "Government",
			"vendor" : "Cray Inc.",
			"cores" : 72800,
			"firstAppearance" : "2007-11-01T00:00:00Z",
			"tflops" : 3577.0
		},
		{
			"id" : "3",
			"name" : "DOE/NNSA/LLNL",
			"vendor" : "IBM",
			/* The bigger amount of cores comes first from the subsort */
			"cores" : 1572864,
			"firstAppearance" : "2005-11-01T00:00:00Z",
			"tflops" : 17173.2
		},
		{
			"id" : "9",
			"name" : "DOE/NNSA/LLNL",
			"vendor" : "IBM",
			"cores" : 393216,
			"firstAppearance" : "2005-11-01T00:00:00Z",
			"tflops" : 4293.3
		},
		{
			"id" : "7",
			"name" : "Texas Advanced Computing Center/Univ. of Texas",
			"vendor" : "Dell",
			"cores" : 462462,
			"firstAppearance" : "2001-11-01T00:00:00Z",
			"tflops" : 5168.1
		},
		{
			"id" : "5",
			"name" : "DOE/SC/Argonne National Laboratory",
			"vendor" : "IBM",
			"cores" : 786432,
			"firstAppearance" : "1995-06-01T00:00:00Z",
			"tflops" : 8586.6
		},
		{
			"id" : "2",
			"name" : "DOE/SC/Oak Ridge National Laboratory",
			"vendor" : "Cray Inc.",
			/* The bigger amount of cores comes first from the subsort */
			"cores" : 560640,
			"firstAppearance" : "1993-06-01T00:00:00Z",
			"tflops" : 17590.0
		},
		{
			"id" : "8",
			"name" : "Forschungszentrum Juelich (FZJ)",
			"vendor" : "IBM",
			"cores" : 458752,
			"firstAppearance" : "1993-06-01T00:00:00Z",
			"tflops" : 5008.9
		},
	],

	"meta" : {
		"totalCount" : 10,

		"links" : [
			{ "href" : null, "name" : "prev", "path" : "$.data", "method" : "GET" },
			{ "href" : null, "name" : "next", "path" : "$.data", "method" : "GET" }
		],

		"etags" : [
			{ "etag" : "773d2f209b482b58f48c24970c28c4c9",  "path" : "$.data" },
			{ "etag" : "681604816754c297dbbb895cb1bd1c74",  "path" : "$.data.[0]" },
			{ "etag" : "5ba5b9d9648b1709a7996fbbe0091a2b",  "path" : "$.data.[1]" },
			{ "etag" : "26a96b47743ac2bb1822ed79753e15df",  "path" : "$.data.[2]" },
			{ "etag" : "ae1701637a41427410ddb839cc3fa1e2",  "path" : "$.data.[3]" },
			{ "etag" : "a9f739f2bb4735d9500259daa0e3a576",  "path" : "$.data.[4]" },
			{ "etag" : "5e45d009514280229f8ea8dc982ac354",  "path" : "$.data.[5]" },
			{ "etag" : "53332c616504673196b953fc9d9ae194",  "path" : "$.data.[6]" },
			{ "etag" : "4789647b72be508c7027d5d8921b2e75",  "path" : "$.data.[7]" },
			{ "etag" : "dc2e0302fae3ed17ce753fd8a73371d5",  "path" : "$.data.[8]" },
			{ "etag" : "cbd0dd679a7ce77acaa5059e101accdc",  "path" : "$.data.[9]" }
		]
	}
}
```

# Partial Responses
Routes SHOULD support partial responses. Properties to **include** MUST be
specified by the query string "fields". The format MUST be "Field Specification format".

* comma-separated list to select multiple properties

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

# Filtering

Filtering MUST be restricted to exposed properties. Clients and routes SHOULD use filtering
to limit the results in a structured and absolute way.

Routes MAY support filtering. Routes supporting filtering MUST only use
instances of the query string `f[{property}][{operation}]`.   A filter's
property MUST be in the Field Specification Format.
Routes MAY support a subset of those properties.

A filter with a field specification wider than one property MUST return 400 error.

```
/* error as it has one property */
?f[parent/*][eq]=1
```

## Properties

If filter property operates on a nested property structure the
"fields" format specification applies.

See also [Partial Responses]("#Partial Responses")

## Operations
Operation MUST be one of
* eq  - MAY support comma separated values that support double quoted - Equals
* not - MAY support comma separated values that support double quoted - Not Equals
* gt  - MUST ONLY be numbers and dates - Greater than
* gte - MUST ONLY be numbers and dates - Greater than or equals
* lt  - MUST ONLY be numbers and dates - Lesser than
* lte - MUST ONLY be numbers and dates - Lesser than or equals

Multiple filters MUST be and'ed together.

## Comma Separated Values

Values in the "eq" and "not" operations MUST be treaded in accordance to the following ABNF grammar adapted from  [RFC4180 i.e. "CSV Mime type format"](https://tools.ietf.org/html/rfc4180). See also [RFC2234 Augmented BNF syntax](https://tools.ietf.org/html/rfc2234)

```abnf
value = item *(COMMA item)
item = (qd-item / text)
qd-item = DQUOTE *(qdtext / 2DQUOTE) DQUOTE
qdtext = text / COMMA / obs-text
text = HTAB / SP / %x21 / %x23-2B / %x2D-5B / %x5D-7E
obs-text = %x80-FF

COMMA = %x2C
DQUOTE = %x22
```

## Values
For the operations "eq" and "not" routes MAY support comma separated values.
Multiple values MUST be combined with an "or" operation, e.g.

    SELECT id FROM items WHERE items.value IN (1,2,3)
    SELECT id FROM items WHERE items.value = 1 OR items.value = 2 OR items.value = 3

Operations gt, gte, lt, lte MUST support only a single value. Routes MUST
treat all double quotes and commas as literals in these operations.

## Examples
```
/* Pattern */
f[{property}][{operation}]={value}
```


```
/* numbers */
f[cost][eq]=50
WHERE cost = '50'
```

```
/* sub-properties */
f[content/locale][eq]=en
WHERE locale = 'en'
```

```
/* comparsion equality */
f[cost][lte]=50
WHERE cost <= 50

f[cost][gte]=50
WHERE cost >= 50
```

```
/* not */
f[color][not]=blue
WHERE color <> 'blue'

f[cost][not]=50
WHERE cost <> 50
```

```
/* multiple filters */
f[cost][lte]=100&f[cost][gte]=50
WHERE cost <= 100
AND   cost >= 50

f[cost][gt]=50&f[cost][lt]=100
WHERE cost < 100
AND   cost > 50
```

```
/* complex example */
f[color][eq]=blue,"green","red"""&f[cost][lte]=50
WHERE color IN ('blue', 'green', 'red"' )
AND   cost <= 50
```

```
/* Quote handling */

/* no quotes */
f[color][eq]=blue
WHERE color = 'blue'

/* quotes are removed */
f[color][eq]="blue"
WHERE color = 'blue'

/* one instance of quote is retained */
f[color][eq]="""blue"
WHERE color = '"blue'

/* two instance of quotes escapes */
f[color][eq]="""blue"""
WHERE color = '"blue"'
```

```javascript
/* Filtering to one vendor */
// GET /v4/data/supercomputers?f[vendor][eq]=Cray%20Inc.
// 200 OK
// Etag: W/"764cfe1ae347004861290afd60ac651a"
{
	"data" : [
		{
			"id" : "2",
			"name" : "DOE/SC/Oak Ridge National Laboratory",
			"vendor" : "Cray Inc.",
			"cores" : 560640,
			"tflops" : 17590.0
		},
		{
			"id" : "6",
			"name" : "Swiss National Supercomputing Centre (CSCS)",
			"vendor" : "Cray Inc.",
			"cores" : 115984,
			"tflops" : 6271.0
		},
		{
			"id" : "10",
			"name" : "Government",
			"vendor" : "Cray Inc.",
			"cores" : 72800,
			"tflops" : 3577.0
		}
	],

	"meta" : {
		"totalCount" : 10,

		"links" : [
			{ "href" : null, "name" : "prev", "path" : "$.data", "method" : "GET" },
			{ "href" : null, "name" : "next", "path" : "$.data", "method" : "GET" }
		],

		"etags" : [
			{ "etag" : "764cfe1ae347004861290afd60ac651a",  "path" : "$.data" },
			{ "etag" : "f2b97c46e1fd59e1ffd8770a4443e5fb",  "path" : "$.data.[0]" },
			{ "etag" : "3512b84a84693263c0aa0b43aba56ad8",  "path" : "$.data.[1]" },
			{ "etag" : "e9e3658acab0e30691fac3c74df466ae",  "path" : "$.data.[2]" }
		]
	}
}
```
```javascript
/* Filtering to two vendors */
// GET /v4/data/supercomputers?f[vendor][eq]=Cray%20Inc.,IBM
// 200 OK
// Etag: W/"eba6eba9b26e61aa3e0b22154f1dc2f4"
{
	"data" : [
		{
			"id" : "2",
			"name" : "DOE/SC/Oak Ridge National Laboratory",
			"vendor" : "Cray Inc.",
			"cores" : 560640,
			"tflops" : 17590.0
		},
		{
			"id" : "3",
			"name" : "DOE/NNSA/LLNL",
			"vendor" : "IBM",
			"cores" : 1572864,
			"tflops" : 17173.2
		},
		{
			"id" : "5",
			"name" : "DOE/SC/Argonne National Laboratory",
			"vendor" : "IBM",
			"cores" : 786432,
			"tflops" : 8586.6
		},
		{
			"id" : "6",
			"name" : "Swiss National Supercomputing Centre (CSCS)",
			"vendor" : "Cray Inc.",
			"cores" : 115984,
			"tflops" : 6271.0
		},
		{
			"id" : "8",
			"name" : "Forschungszentrum Juelich (FZJ)",
			"vendor" : "IBM",
			"cores" : 458752,
			"tflops" : 5008.9
		},
		{
			"id" : "9",
			"name" : "DOE/NNSA/LLNL",
			"vendor" : "IBM",
			"cores" : 393216,
			"tflops" : 4293.3
		},
		{
			"id" : "10",
			"name" : "Government",
			"vendor" : "Cray Inc.",
			"cores" : 72800,
			"tflops" : 3577.0
		}
	],

	"meta" : {
		"totalCount" : 10,

		"links" : [
			{ "href" : null, "name" : "prev", "path" : "$.data", "method" : "GET" },
			{ "href" : null, "name" : "next", "path" : "$.data", "method" : "GET" }
		],

		"etags" : [
			{ "etag" : "eba6eba9b26e61aa3e0b22154f1dc2f4",  "path" : "$.data" },
			{ "etag" : "f2b97c46e1fd59e1ffd8770a4443e5fb",  "path" : "$.data.[0]" },
			{ "etag" : "848aa6ee22420808a2f189ccf099890c",  "path" : "$.data.[1]" },
			{ "etag" : "7f0e65bc2c27133019910adfa417b06a",  "path" : "$.data.[2]" },
			{ "etag" : "3512b84a84693263c0aa0b43aba56ad8",  "path" : "$.data.[3]" },
			{ "etag" : "d5ffe9f8043a443eda470a7d2ef1f912",  "path" : "$.data.[4]" },
			{ "etag" : "94ff6b88e91b4751a6fecfd5a1803dee",  "path" : "$.data.[5]" },
			{ "etag" : "e9e3658acab0e30691fac3c74df466ae",  "path" : "$.data.[6]" }
		]
	}
}
```

```javascript
/* Filtering to between 500,000 and 1,000,000 cores */
// GET /v4/data/supercomputers?f[cores][lt]=1000000&f[cores][gt]=500000
// 200 OK
// Etag: W/"10fd07d71b1f549beb6b0c706a142479"
{
	"data" : [
		{
			"id" : "2",
			"name" : "DOE/SC/Oak Ridge National Laboratory",
			"vendor" : "Cray Inc.",
			"cores" :  560,640,
			"tflops" : 17590.0
		},
		{
			"id" : "4",
			"name" : "RIKEN Advanced Institute for Computational Science (AICS)",
			"vendor" : "Fujitsu",
			"cores" :  705,024,
			"tflops" : 10510.0
		},
		{
			"id" : "5",
			"name" : "DOE/SC/Argonne National Laboratory",
			"vendor" : "IBM",
			"cores" : 786,432,
			"tflops" : 8586.6
		},
	],

	"meta" : {
		"totalCount" : 10,

		"links" : [
			{ "href" : null, "name" : "prev", "path" : "$.data", "method" : "GET" },
			{ "href" : null, "name" : "next", "path" : "$.data", "method" : "GET" }
		],

		"etags" : [
			{ "etag" : "10fd07d71b1f549beb6b0c706a142479",  "path" : "$.data" },
			{ "etag" : "f2b97c46e1fd59e1ffd8770a4443e5fb",  "path" : "$.data.[0]" },
			{ "etag" : "763dfaace31f258f5943e0bda6df3eff",  "path" : "$.data.[1]" },
			{ "etag" : "7f0e65bc2c27133019910adfa417b06a",  "path" : "$.data.[2]" },
		]
	}
}
```


```javascript
/* Super computer centers added to the list in the ninties */
// GET /v4/data/supercomputers?f[firstAppearance][gte]=1990-01-01T00:00:00Z&f[firstAppearance][lte]=2000-01-01T00:00:00Z
// 200 OK
// Etag: W/"c1c353fa20a853f36a038ce7f61c4d63"
{
	"data" : [
		{
			"id" : "2",
			"name" : "DOE/SC/Oak Ridge National Laboratory",
			"vendor" : "Cray Inc.",
			"cores" : 560640,
			"firstAppearance" : "1993-06-01T00:00:00Z",
			"tflops" : 17590.0
		},
		{
			"id" : "5",
			"name" : "DOE/SC/Argonne National Laboratory",
			"vendor" : "IBM",
			"cores" : 786432,
			"firstAppearance" : "1995-06-01T00:00:00Z",
			"tflops" : 8586.6
		},
		{
			"id" : "8",
			"name" : "Forschungszentrum Juelich (FZJ)",
			"vendor" : "IBM",
			"cores" : 458752,
			"firstAppearance" : "1993-06-01T00:00:00Z",
			"tflops" : 5008.9
		},
	],

	"meta" : {
		"totalCount" : 3,

		"links" : [
			{ "href" : null, "name" : "prev", "path" : "$.data", "method" : "GET" },
			{ "href" : null, "name" : "next", "path" : "$.data", "method" : "GET" }
		],

		"etags" : [
			{ "etag" : "c1c353fa20a853f36a038ce7f61c4d63",  "path" : "$.data" },
			{ "etag" : "dc2e0302fae3ed17ce753fd8a73371d5",  "path" : "$.data.[0]" },
			{ "etag" : "4789647b72be508c7027d5d8921b2e75",  "path" : "$.data.[1]" },
			{ "etag" : "cbd0dd679a7ce77acaa5059e101accdc",  "path" : "$.data.[2]" }
		]
	}
}
```


```javascript
/* Invalid filter 400 error */
// GET /v4/data/supercomputers?f[id][lt]=10
// 400 Invalid request
{
	"error" :
		{
			"requestId" : "74be80aa-31a0-453e-b2e3-a56e7bc1a468",
			"documentationUrl" : "https://developer.salesforce.com/marketing_cloud/errors/filter.invalid_operation.string",
			"statusCode" :  400,
			"errorCode" : "filter.invalid_operation.string",
			"message" : "id is type of string and only supports eq and not operations",
			"details" : []
		}
}
```


# Pagination

Collection routes SHOULD support pagination. Requests with no "limit" or "size"
MUST always either return all items OR return 400 Bad Request regardless of
the count of results.

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

Offset paging allows request to specify number of rows to skip and number of
rows to return after the skipped amount.

Routes supporting any pagination MUST support Offset. Requests MUST require both
"offset" and "limit".

Query string parameter "offset" MUST be the number of results to skip

Query string parameter "limit" MUST be the number of results to return

**Examples**
* ?limit=50&offset=50 would begin with the 51st object in a collection
* ?limit=50&offset=0 would begin with the 1st object in a collection
* ?limit=50&offset=11 would begin with the 12th object in a collection

```javascript
/* No prameters, defaults offset=0 limit=1000 but is limited to the 10 records that exist */
// GET /v4/data/supercomputers
// 200 OK
// Etag: W/"aef61df0a4ce482c9c16c8cd477de256"
{
	"data" : [
		{
			"id" : "1",
			"name" : "National Super Computer Center in Guangzhou",
			"vendor" : "NUDT",
			"cores" : 3120000,
			"firstAppearance" : "2012-06-01T00:00:00Z",
			"tflops" : 33862.7
		},
		{
			"id" : "2",
			"name" : "DOE/SC/Oak Ridge National Laboratory",
			"vendor" : "Cray Inc.",
			"cores" : 560640,
			"firstAppearance" : "1993-06-01T00:00:00Z",
			"tflops" : 17590.0
		},
		{
			"id" : "3",
			"name" : "DOE/NNSA/LLNL",
			"vendor" : "IBM",
			"cores" : 1572864,
			"firstAppearance" : "2005-11-01T00:00:00Z",
			"tflops" : 17173.2
		},
		{
			"id" : "4",
			"name" : "RIKEN Advanced Institute for Computational Science (AICS)",
			"vendor" : "Fujitsu",
			"cores" : 705024,
			"firstAppearance" : "2010-11-01T00:00:00Z",
			"tflops" : 10510.0
		},
		{
			"id" : "5",
			"name" : "DOE/SC/Argonne National Laboratory",
			"vendor" : "IBM",
			"cores" : 786432,
			"firstAppearance" : "1995-06-01T00:00:00Z",
			"tflops" : 8586.6
		},
		{
			"id" : "6",
			"name" : "Swiss National Supercomputing Centre (CSCS)",
			"vendor" : "Cray Inc.",
			"cores" : 115984,
			"firstAppearance" : "2011-11-01T00:00:00Z",
			"tflops" : 6271.0
		},
		{
			"id" : "7",
			"name" : "Texas Advanced Computing Center/Univ. of Texas",
			"vendor" : "Dell",
			"cores" : 462462,
			"firstAppearance" : "2001-11-01T00:00:00Z",
			"tflops" : 5168.1
		},
		{
			"id" : "8",
			"name" : "Forschungszentrum Juelich (FZJ)",
			"vendor" : "IBM",
			"cores" : 458752,
			"firstAppearance" : "1993-06-01T00:00:00Z",
			"tflops" : 5008.9
		},
		{
			"id" : "9",
			"name" : "DOE/NNSA/LLNL",
			"vendor" : "IBM",
			"cores" : 393216,
			"firstAppearance" : "2005-11-01T00:00:00Z",
			"tflops" : 4293.3
		},
		{
			"id" : "10",
			"name" : "Government",
			"vendor" : "Cray Inc.",
			"cores" : 72800,
			"firstAppearance" : "2007-11-01T00:00:00Z",
			"tflops" : 3577.0
		}
	],

	"meta" : {
		"totalCount" : 10,

		"links" : [
			{ "href" : null, "name" : "prev", "path" : "$.data", "method" : "GET" },
			{ "href" : null, "name" : "next", "path" : "$.data", "method" : "GET" }
		],

		"etags" : [
			{ "etag" : "aef61df0a4ce482c9c16c8cd477de256",  "path" : "$.data" },
			{ "etag" : "681604816754c297dbbb895cb1bd1c74",  "path" : "$.data.[0]" },
			{ "etag" : "dc2e0302fae3ed17ce753fd8a73371d5",  "path" : "$.data.[1]" },
			{ "etag" : "a9f739f2bb4735d9500259daa0e3a576",  "path" : "$.data.[2]" },
			{ "etag" : "26a96b47743ac2bb1822ed79753e15df",  "path" : "$.data.[3]" },
			{ "etag" : "4789647b72be508c7027d5d8921b2e75",  "path" : "$.data.[4]" },
			{ "etag" : "5ba5b9d9648b1709a7996fbbe0091a2b",  "path" : "$.data.[5]" },
			{ "etag" : "53332c616504673196b953fc9d9ae194",  "path" : "$.data.[6]" },
			{ "etag" : "cbd0dd679a7ce77acaa5059e101accdc",  "path" : "$.data.[7]" },
			{ "etag" : "5e45d009514280229f8ea8dc982ac354",  "path" : "$.data.[8]" },
			{ "etag" : "ae1701637a41427410ddb839cc3fa1e2",  "path" : "$.data.[9]" }
		]
	}
}
```

```javascript
/* Limit returns that count of rows, defaults offset=0  */
// GET /v4/data/supercomputers?limit=2
// 200 OK
// Etag: W/"f544328e483547e8615b62e854c961a3"
{
	"data" : [
		{
			"id" : "1",
			"name" : "National Super Computer Center in Guangzhou",
			"vendor" : "NUDT",
			"cores" : 3120000,
			"tflops" : 33862.7
		},
		{
			"id" : "2",
			"name" : "DOE/SC/Oak Ridge National Laboratory",
			"vendor" : "Cray Inc.",
			"cores" : 560640,
			"tflops" : 17590.0
		}
	],

	"meta" : {
		"totalCount" : 10,

		"links" : [
			{ "href" : null, "name" : "prev", "path" : "$.data", "method" : "GET" },
			{ "name" : "next", "method": "GET", "href" : "/v4/data/supercomputers?limit=2&offset=2", "path" : "$.data" }
		],

		"etags" : [
			{ "etag" : "f544328e483547e8615b62e854c961a3",  "path" : "$.data" },
			{ "etag" : "a1642ed98e14ce6a38157c405e936c9a",  "path" : "$.data.[0]" },
			{ "etag" : "f2b97c46e1fd59e1ffd8770a4443e5fb",  "path" : "$.data.[1]" },
		]
	}
}
```

```javascript
/* Next page */
// GET /v4/data/supercomputers?limit=2&offset=2
// 200 OK
// Etag: W/"1e20c4d502f360146baa0b98ad040697"
{
	"data" : [
		{
			"id" : "3",
			"name" : "DOE/NNSA/LLNL",
			"vendor" : "IBM",
			"cores" : 1572864,
			"tflops" : 17173.2
		},
		{
			"id" : "4",
			"name" : "RIKEN Advanced Institute for Computational Science (AICS)",
			"vendor" : "Fujitsu",
			"cores" : 705024,
			"tflops" : 10510.0
		},
	],

	"meta" : {
		"totalCount" : 10,

		"links" : [
			{ "name" : "next", "method": "GET", "href" : "/v4/data/supercomputers?limit=2&offset=4", "path" : "$.data" }
			{ "name" : "prev", "method": "GET", "href" : "/v4/data/supercomputers?limit=2&offset=0", "path" : "$.data" }
		],

		"etags" : [
			{ "etag" : "1e20c4d502f360146baa0b98ad040697",  "path" : "$.data" },
			/* etags of rows match previous values */
			{ "etag" : "763dfaace31f258f5943e0bda6df3eff",  "path" : "$.data.[0]" },
			{ "etag" : "7f0e65bc2c27133019910adfa417b06a",  "path" : "$.data.[1]" },
		]
	}
}
```

```javascript
/* Last page, non-starting offset */
// GET /v4/data/supercomputers?limit=4&offset=6
// 200 OK
// Etag: W/"5a3bb04baf2318afb62cfb18d5c674b5"
{
	"data" : [
		{
			"id" : "7",
			"name" : "Texas Advanced Computing Center/Univ. of Texas",
			"vendor" : "Dell",
			"cores" : 462462,
			"tflops" : 5168.1
		},
		{
			"id" : "8",
			"name" : "Forschungszentrum Juelich (FZJ)",
			"vendor" : "IBM",
			"cores" : 458752,
			"tflops" : 5008.9
		},
		{
			"id" : "9",
			"name" : "DOE/NNSA/LLNL",
			"vendor" : "IBM",
			"cores" : 393216,
			"tflops" : 4293.3
		},
		{
			"id" : "10",
			"name" : "Government",
			"vendor" : "Cray Inc.",
			"cores" : 72800,
			"tflops" : 3577.0
		}
	],

	"meta" : {
		"totalCount" : 10,

		"links" : [
			/* offset reflects api user current request; offset 2 */
			{ "name" : "prev", "method": "GET", "href" : "/v4/data/supercomputers?limit=4&offset=2", "path" : "$.data" }
			{ "href" : null, "name" : "next", "path" : "$.data", "method" : "GET" }
		],

		"etags" : [
			{ "etag" : "5a3bb04baf2318afb62cfb18d5c674b5",  "path" : "$.data" },
			/* etags of rows match previous values */
			{ "etag" : "19496405f44bd20931ba8d9f47ec2ba9",  "path" : "$.data.[0]" },
			{ "etag" : "d5ffe9f8043a443eda470a7d2ef1f912",  "path" : "$.data.[1]" },
			{ "etag" : "94ff6b88e91b4751a6fecfd5a1803dee",  "path" : "$.data.[2]" },
			{ "etag" : "e9e3658acab0e30691fac3c74df466ae",  "path" : "$.data.[3]" }
		]
	}
}
```

```javascript
/* Last page with over hanging offset just returns possible rows */
// GET /v4/data/supercomputers?limit=6&offset=9
// 200 OK
// Etag: W/"b5a571c0ad9a00dd7756824210c88461"
{
	"data" : [
		{
			"id" : "10",
			"name" : "Government",
			"vendor" : "Cray Inc.",
			"cores" : 72800,
			"tflops" : 3577.0
		}
	],

	"meta" : {
		"totalCount" : 10,

		"links" : [
			/* offset reflects api user current request; offset 3 */
			{ "name" : "prev", "method": "GET", "href" : "/v4/data/supercomputers?limit=6&offset=3", "path" : "$.data" },
			{ "href" : null, "name" : "next", "path" : "$.data", "method" : "GET" }
		],

		"etags" : [
			{ "etag" : "b5a571c0ad9a00dd7756824210c88461",  "path" : "$.data" },
			/* etags of rows match previous values */
			{ "etag" : "e9e3658acab0e30691fac3c74df466ae",  "path" : "$.data.[0]" }
		]
	}
}
```

```javascript
/* empty results are not an error */
// GET /v4/data/supercomputers?limit=1000&offset=1000
// 200 OK
// Etag: W/"9e913b5713774eb61d40d42dc37406a3"
{
	"data" : [],

	"meta" : {
		"totalCount" : 10,

		"links" : [
			/* offset reflects api user current request; offset 3 */
			{ "name" : "prev", "method": "GET", "href" : "/v4/data/supercomputers?limit=1000&offset=0", "path" : "$.data" },
			{ "href" : null, "name" : "next", "path" : "$.data", "method" : "GET" }
		],

		"etags" : [
			{ "etag" : "9e913b5713774eb61d40d42dc37406a3",  "path" : "$.data" },
		]
	}
}
```

## Cursor

Cursor paging allows clients to browse large and dynamic datasets without
invalidating data.

Routes following style guide 4.0 SHALL NOT support cursor paging.

See also [Facebook cursor paging](http://api-portal.anypoint.mulesoft.com/facebook/api/facebook-graph-api/docs/reference/pagination)
See also [Amazon cursor paging](http://docs.aws.amazon.com/cloudsearch/latest/developerguide/paginating-results.html#deep-paging)

## Traditional Paging

Traditional paging allows the client to avoid math by taking a page number and
page size value.

Routes following style guide 4.0 SHALL NOT support traditional paging.

# Searching

Searching is to find results based on one or many properties.

Routes MAY support a search.  Routes MUST return 400 when searching is
requested but not available.  Routes SHOULD use search as a means to find
results by a query.  Routes MUST only support searching through query string
parameter "q".  Search SHOULD be case insensitive.

Routes MAY return 400 "Bad Request" for a query.

Routes MUST return an empty collection if no results were found.

Routes MUST NOT remove properties that are searched in a version.

Routes MAY add properties that are searched in a version.

Routes SHOULD search properties in a contains (e.g. Left and Right side
wildcard, *foo*) fashion

See also [Querying](pattern/querying.md)

```javascript
/* search should be contains */
// GET /v4/data/supercomputers?q=SC
// 200 OK
// Etag: W/"0cfe3bd15b4b15755a556513ee19d886"
{
	"data" : [
		{
			"id" : "2",
			"name" : "DOE/SC/Oak Ridge National Laboratory",
			"vendor" : "Cray Inc.",
			"cores" : 560640,
			"tflops" : 17590.0
		},
		{
			"id" : "5",
			"name" : "DOE/SC/Argonne National Laboratory",
			"vendor" : "IBM",
			"cores" : 786432,
			"tflops" : 8586.6
		}
	],

	"meta" : {
		"totalCount" : 10,

		"links" : [
			{ "href" : null, "name" : "prev", "path" : "$.data", "method" : "GET" },
			{ "href" : null, "name" : "next", "path" : "$.data", "method" : "GET" }
		],

		"etags" : [
			{ "etag" : "0cfe3bd15b4b15755a556513ee19d886",  "path" : "$.data" },
			{ "etag" : "f2b97c46e1fd59e1ffd8770a4443e5fb",  "path" : "$.data.[0]" },
			{ "etag" : "7f0e65bc2c27133019910adfa417b06a",  "path" : "$.data.[1]" },
		]
	}
}
```

```javascript
/* search should be case insensitive */
// GET /v4/data/supercomputers?q=comp
// 200 OK
// Etag: W/"caf70db73cdd81db17cc1f3481a51bbd"
{
	"data" : [
		{
			"id" : "1",
			"name" : "National Super Computer Center in Guangzhou",
			"vendor" : "NUDT",
			"cores" : 3120000,
			"tflops" : 33862.7
		},
		{
			"id" : "4",
			"name" : "RIKEN Advanced Institute for Computational Science (AICS)",
			"vendor" : "Fujitsu",
			"cores" : 705024,
			"tflops" : 10510.0
		},
		{
			"id" : "6",
			"name" : "Swiss National Supercomputing Centre (CSCS)",
			"vendor" : "Cray Inc.",
			"cores" : 115984,
			"tflops" : 6271.0
		},
		{
			"id" : "7",
			"name" : "Texas Advanced Computing Center/Univ. of Texas",
			"vendor" : "Dell",
			"cores" : 462462,
			"tflops" : 5168.1
		}
	],

	"meta" : {
		"totalCount" : 10,

		"links" : [
			{ "href" : null, "name" : "prev", "path" : "$.data", "method" : "GET" },
			{ "href" : null, "name" : "next", "path" : "$.data", "method" : "GET" }
		],

		"etags" : [
			{ "etag" : "caf70db73cdd81db17cc1f3481a51bbd",  "path" : "$.data" },
			{ "etag" : "a1642ed98e14ce6a38157c405e936c9a",  "path" : "$.data.[0]" },
			{ "etag" : "763dfaace31f258f5943e0bda6df3eff",  "path" : "$.data.[1]" },
			{ "etag" : "3512b84a84693263c0aa0b43aba56ad8",  "path" : "$.data.[2]" },
			{ "etag" : "19496405f44bd20931ba8d9f47ec2ba9",  "path" : "$.data.[3]" },
		]
	}
}
```

```javascript
/* search can be multiple fields */
// GET /v4/data/supercomputers?q=el
// 200 OK
// Etag: W/"ef73743762a35bfd7adab0f1a5f76a18"
{
	"data" : [
		{
			"id" : "7",
			"name" : "Texas Advanced Computing Center/Univ. of Texas",
			/* D_el_l */
			"vendor" : "Dell",
			"cores" : 462462,
			"tflops" : 5168.1
		},
		{
			"id" : "8",
			/* Ju_el_ich */
			"name" : "Forschungszentrum Juelich (FZJ)",
			"vendor" : "IBM",
			"cores" : 458752,
			"tflops" : 5008.9
		}
	],

	"meta" : {
		"totalCount" : 10,

		"links" : [
			{ "href" : null, "name" : "prev", "path" : "$.data", "method" : "GET" },
			{ "href" : null, "name" : "next", "path" : "$.data", "method" : "GET" }
		],

		"etags" : [
			{ "etag" : "ef73743762a35bfd7adab0f1a5f76a18",  "path" : "$.data" },
			{ "etag" : "19496405f44bd20931ba8d9f47ec2ba9",  "path" : "$.data.[0]" },
			{ "etag" : "d5ffe9f8043a443eda470a7d2ef1f912",  "path" : "$.data.[1]" },
		]
	}
}
```

# Authentication

Authentication MUST only be done with "Authorization" header. Routes bearer token usage MUST NOT accept Form-encoded body; routes MUST NOT accept URI Query parameter tokens.

See also [OAuth 2.0 (RFC6749)](http://tools.ietf.org/html/rfc6749)  
See also [OAuth 2.0 Bearer Token Usage (RFC6750)](http://tools.ietf.org/html/rfc6750#section-2)  

# API Description Format

Routes MUST be fully documented in the [Swagger format 2.0](https://github.com/swagger-api/swagger-spec/blob/master/versions/2.0.md)

* REQUIRED to expose all resources
* REQUIRED to expose all supported headers on resource
* REQUIRED to expose all query string parameters for resources
* REQUIRED to document unique operationId for all operations
* REQUIRED to document summary and description for all operations
* REQUIRED to document produces and consumes sections
* REQUIRED to expose schema for responses for successful resources
* REQUIRED to expose schema for input for all operations to resources
	* REQUIRED to document required properties on object
	* REQUIRED to document default values
	* REQUIRED to document min/max values for int and string values
* SHALL NOT expose an "examples" object in response section
* SHOULD expose schemas for error responses for all errors possible on a resource
* REQUIRED to document security policy for API on whole (but not route level)
* MUST NOT document http verb substitution
* SHOULD document custom action substitution
	* MAY use extension to operation "x-actions" and response "x-action" to associate actions and their responses for a POST



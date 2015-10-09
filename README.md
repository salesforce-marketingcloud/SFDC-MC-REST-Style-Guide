# Salesforce Fuel REST API Style Guide

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be
interpreted as described in [RFC 2119](http://www.ietf.org/rfc/rfc2119.txt).

## Introduction

The Salesforce Fuel REST API Style Guide defines the standards which all REST APIs across the
Salesforce Marketing Cloud MUST adhere to. 

This document provides opinionated answers to developer questions surrounding
development of RESTful APIs. It aims to align Marketing Cloud development
organizations who want to expose RESTful APIs, both public and internal, under
a common style.

Consider this document a pragmatic, not dogmatic guide for RESTful APIs. It
will make assertions that conflict with an academic definition of REST. It's
goal is to provide developers with a predictable, simple and comprehensive
interface into the Marketing Cloud - not to be the most RESTful REST that ever
RESTed.

API authors and framework developers should take careful note of
[API Description Format](#api-description-format) as the requirements for
schema information are very rigorous and are difficult to achieve without
framework support.

## Table of contents

* Salesforce Fuel-API REST style guide
* 	* [Introduction](#introduction)
	* [Table of contents](#table-of-contents)
* [The Basics](#the-basics)
* [Versioning in the API](#versioning-in-the-api)
	* [Version numbering schema](#version-numbering-schema)
	* [Allowed changes/updates to a
	  Version](#allowed-changesupdates-to-a-version)
* [HTTP](#http)
	* [HTTP status codes](#http-status-codes)
		* [Implicit](#implicit)
		* [Explicit](#explicit)
	* [HTTP Methods](#http-methods)
		* [POST](#post)
		* [GET](#get)
		* [DELETE](#delete)
		* [PUT](#put)
		* [PATCH](#patch)
		* [OPTIONS](#options)
		* [HTTP method/action
		  substitution](#http-methodaction-substitution)
	* [Headers](#headers)
		* [Request Headers](#request-headers)
		* [Response Headers](#response-headers)
	* [HTTP Compression](#http-compression)
		* [Compression Request](#compression-request)
		* [Compression Response](#compression-response)
* [Authentication](#authentication)
* [Style](#style)
	* [Request and Response Bodies](#request-and-response-bodies)
	* [Identifiers](#identifiers)
	* [Plural Nouns](#plural-nouns)
	* [Path](#path)
	* [Query string](#query-string)
	* [Collections](#collections)
	* [Remote field expansion](#remote-field-expansion)
	* [Properties](#properties)
		* [Property Naming](#property-naming)
		* [Data Property Types](#data-property-types)
			* [Numbers](#numbers)
			* [Arrays](#arrays)
			* [Boolean](#boolean)
		* [Enumerations](#enumerations)
		* [Dates and Times](#dates-and-times)
		* [Marketing Cloud Specific Properties](#marketing-cloud-specific-properties)
	* [Relationships](#relationships)
		* [Relationship Object](#relationship-object)
		* [Representing Relationships](#representing-relationships)
		* [Relationships in Requests](#relationships-in-requests)
* [Request Format](#request-format)
* [Response Format](#response-format)
	* [Header](#header)
	* [Localization](#localization)
	* [Envelope](#envelope)
		* [Data Object](#data-object)
		* [Meta Object](#meta-object)
			* [Meta Object Structure](#meta-object-structure)
			* [Link Object](#link-object)
* [Errors](#errors)
	* [Error Envelope](#error-envelope)
		* [Error Detail Object](#error-detail-object)
	* [Validation Details](#validation-details)
* [Interaction Patterns](#interaction-patterns)
	* [Field Specification Format](#field-specification-format)
	* [Sorting](#sorting)
	* [Partial Responses](#partial-responses)
	* [Filtering](#filtering)
		* [Properties](#properties)
		* [Operations](#operations)
		* [Comma Separated Values](#comma-separated-values)
		* [Values](#values)
	* [Pagination](#pagination)
		* [Offset](#offset)
	* [Searching](#searching)

# The Basics

The Fuel REST API is an HTTP API that generally utilizes JSON.
We rely on these Standards and Specifications heavily. A general understanding
of these technologies is required for Fuel REST API development.

See Also:

[JSON.org](http://json.org)
[JSON RFC 7159](https://tools.ietf.org/html/rfc7159)
[HTTP RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616.html)


# Versioning in the API

The application protocol interface ("API") is published in multiple versions.
Implementations MUST use an indicator in the uniform resource locator ("URL").
This indicator applies to the entire API as a whole. Resources MUST NOT expose
their own versions.

    GET /{version}/{service}/{+resource}

## Example

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

### Examples

```javascript

/* Allowed Changes within a version */

// original, name is required
// POST /v4/content/articles
{
    "name" : "An Article Name",
}

// changed, length is optional 
// POST /v4/content/articles
// ! would NOT be allowed if length was required
{
    "name" : "An Article Name",
    "length" : 100
}

// original
// GET /v4/content/articles/1
{
    "data" : [{
    	"id" : "1",
	"name" : "An Article Name"
    }],
    "meta" : {}
}

// new, now returns optional parameter length 
// GET /v4/content/articles/1
{
    "data" : [{
    	"id" : "1",
	"name" : "An Article Name",
	"length" : 100
    }],
    "meta" : {}
}

// GET /v4/content/articles/?q=Article
// original, returns anything with "article" in the name

// GET /v4/content/articles/?q=Article&f[length][gte]=50
// added length filter, returns articles with word "Article" in name, and 
// length >= 50

/* Forbidden Changes within a Version */

// original
// GET /v4/content/authors/1
{
    "data" : [{
    	"id" : "1",
	"fname" : "George",
	"lname" : "Martin",
	"age"   : 76
    }],
    "meta" : {}
}

// breaking change, removed existing properties 
// GET /v4/content/authors/1
{
    "data" : [{
    	"id"   : "1",
	"name" : "George R.R. Martin",
	"age"  : 76
    }],
    "meta" : {}
}

// breaking change, changed type of properties (age)
// GET /v4/content/authors/1
{
    "data" : [{
    	"id"   : "1",
	"fname" : "George",
	"lname" : "Martin",
	"age"  : "76"
    }],
    "meta" : {}
}

// original
// GET /v4/content/authors/1?aFakeQSP=true
// aFakeQSP is an existing query string parameter

// breaking change
// GET /v4/content/authors/1?fakeQSP=1
// Cannot rename QSPs.  Cannot change Type of QSPs.

// Example of removing an HTTP Method on a resource
// original
// GET /v4/content/authors/1
{
    "data" : [{
    	"id" : "1",
	"fname" : "George",
	"lname" : "Martin",
	"age"   : 76
    }],
    "meta" : {}
}

// Breaking Change
// GET /v4/content/authors/1
// 404 Not Found



```

# HTTP 

## HTTP status codes

### Implicit

Implicit codes are framework level, and generally unused
by Service and Route developers.

* 100 Continue
	* Large requests
* 206 Partial Content
	* Large requests
* 401 Unauthorized
	* Request is not authenticated
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

### Explicit

Explicit codes are related to business logic responses, and will generally be
used by Service and Route developers.

* 200 OK
	* SHOULD NOT be used with POST/create
* 201 Created
	* SHOULD be used with POST/create
* 202 Accepted
	* REQUIRED to only be used with async requests
* 204 No content
	* Usage NOT RECOMMENDED. When used MUST indicate request was successful
	  and state submitted was accepted with no modifications or defaults.
	  Discouraged for consistency e.g.
		* Deletes MAY return helpful information like "id" with a 200
		* PUT MAY return the replaced content with a 200
		* POST/create MAY return the created content with implict
		  defaults and generated "id" with a 200
* 304 Not modified
	* REQUIRED to only be used for freshness negotiation
* 400 Bad Request
	* Generic validation errors
* 403 Forbidden
	* Authenticated but lack permission to resource/operation
* 404 Not Found
	* Routes SHOULD return 403 if resource exists in the user's tenant, but
	  user lacks access
* 409 Conflict
	* Indicates that the request could not be processed because of conflict
	  in the request, such as an edit conflict in the case of multiple
	  updates.
* 412 Precondition Failed
	* REQUIRED to only be used for concurrency failure (i.e. If-Matches
	  "etag")

Routes MUST NOT return redirect status codes (3XX Codes excluding 304).

## HTTP Methods

Resources accept several HTTP methods.

### POST

A resource MAY support POST Create.  Routes supporting POST Create MUST accept
a single object having the same JSON schema as an item in the collection of the
data section of a GET on that resource. The resource contained in the request
MUST be created, OR the server MUST respond with an error.

The server MUST respond with a 201 status and the created resource. The route
MUST respond with the `id` of the created resource.  The server MUST respond
with a Location: header pointing to the newly created resource.

#### Examples

```javascript
/* Creating a Single Resource */
// POST /v4/content/articles
{
	"name" : "A new Article"
}
// 201 Created
// Location: /v4/content/articles/1
{
	"data" : [{
		"id" : "1",
		"name" : "A new Article"
	}],
	"meta" : {
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
{
	"data" : [{
		"id" : "2",
		"tag" : "business"
	}],
	"meta" : {
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
{
	"data" : [{
		"id" : "1",
		"name" : "A new Article",
		"tags" : [
			{ "id" : "2" }
		]
	}],
	"meta" : {
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
{
	"data" : [{
		"id" : "1",
		"name" : "A new Article",
		"tags" : [
			{ "id" : "2" }
		]
	}],
	"meta" : {
	}
}
/* Article 1 is created with tag 2 relationship */
```

### GET

The identified resource MUST be retrieved and MUST be idempotent. i.e. MUST NOT
produce any side-effects.  A resource supporting GET SHOULD return the full representation
of the requested resource.  Related objects SHOULD NOT be returned unless those objects
are the minimum viable representation of the requested resource.  

GET requests MUST NOT support a request body.

See also [Relationships](#relationships)

#### Examples
```javascript
// GET /v4/content/articles/1
// 200 OK
{
	"data" : [{
		"id" : "1",
		"name" : "An Article",
		"tags" : [
			{ "id" : "2" }
		]
	}],
	"meta" : {
	}
}
/* returns article id 1 */


// GET /v4/content/articles/1/tags/2
// 200 OK
{
	"data" : [{
		"id" : "2",
		"tag" : "business"
	}],
	"meta" : {
	}
}
/* returns a tag related to article 1 */


// GET /v4/content/articles/1/tags
// 200 OK
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
		"links" : [
			{ "href" : null, "name" : "prev", "path" : "$.data", "method" : null },
			{ "href" : null, "name" : "next", "path" : "$.data", "method" : null }
		]
	}
}
/* returns a collection of tags related to article 1 */

```

### DELETE

A resource MAY support DELETE. The identified resource MUST be deleted, unless
it is a nested relationship to the resource.  Routes MAY support a request body
for DELETE requests.

Routes supporting DELETE of a single resource MUST have the resource identified
in the URI.  Routes supporting DELETE of a nested relationship MUST only delete
the relationship not the nested resources.

Collection routes MUST NOT support DELETE.

#### Examples

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
{
	"data" : [{
		"id" : "2",
		"tags": [
			{ "id" : "1" }
		]
	}],
	"meta" : {
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
// 200 OK
{
	"data" : [{
		"id" : "1",
		"articles": []
	}],
	"meta" : {
	}
}

// GET /v4/data/articles/2
// 200 OK
{
	"data" : [{
		"id" : "2",
		"tags": [ ]
	}],
	"meta" : {
	}
}

/* tag still exists, but does not relate to article 2 any longer */
```

See also [HTTP verb substitution](#HTTP verb substitution)


### PUT

A resource MAY support PUT. A PUT SHOULD replace a resource and MUST NOT create
a resource.  A server SHOULD respond with a 200 when updating an existing
resource.

Resources can have immutable or server-defined fields (e.g. id, createdDate,
etc).  A valid PUT payload SHOULD either omit the values or specify the current
values.  A server SHOULD respond with a 400 when trying to replace immutable
fields with new values.

Routes supporting PUT MUST accept a single object having the same JSON schema
as an item in the collection of the data section of a GET on that resource. The
request MAY contain an "If-Match" header.

A server must not support PUT against a
collection.

Routes MUST NOT support creation through PUT.

See also [Upserting](pattern/upserting.md)

#### Examples

```javascript
// GET /v4/data/articles/2
// 200 OK
{
	"data" : [{
		"id" : "2",
		"name" : "An Article",
		"tags": [
			{ "id" : "1" }
		]
	}],
	"meta" : {
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
{
	"data" : [{
		"id" : "2",
		"name" : "A Renamed Article",
		"tags": [
			{ "id" : "1" }
		]
	}],
	"meta" : {
	}
}

```

### PATCH

A resource MAY support PATCH. A PATCH request will update a portion of an
existing resource.

Routes supporting PATCH MUST accept a single object having the same JSON schema
as an item in the collection of the data section of a GET on that resource.
The request URI MUST uniquely identify the resource. The request MAY contain
an "If-Match" header. In version 4.0 of this document, the If-Match header MUST
not affect behavior of the route.  The request format MAY exclude properties
that need not be updated. This includes omitting "id".

PATCH requests SHOULD respond with error 400 "Bad Request" if the request
attempts to update immutable properties of the resource.

API consumers MAY set values to null by specifying the property with a null
value. API consumers MUST specify an empty string `""` if the value of a
property should be an empty string. If a property is not provided a route MUST
NOT act on that property.

See also [Upserting](pattern/upserting.md)

#### Examples

```javascript
// GET /v4/data/articles/2
// 200 OK
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
	}
}
/* A tag exists on article id 2, with tag id 1 */

// PATCH /v4/data/articles/2
{
	"name" : "A Renamed Article"
}
// 200 OK
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
	}
}
/* Update article id 2 */

// PATCH /v4/data/articles/2
{
	"description" : null
}
// 200 OK
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
	}
}
/* Sets description (and only description) field to null */

```


### OPTIONS
In response to OPTIONS method servers
* MUST respond with valid methods
* MUST NOT expose any other data
* Is reserved for cross-origin resource sharing (CORS)


### HTTP method/action substitution
A server MUST support the HTTP method "POST" to allow HTTP methods a client may
not support.

Routes MUST ONLY accept the following case-sensitive values for the `action`
convenience edge:

* PUT
* PATCH
* DELETE

#### Examples
```
POST {service}/{resources}/action/{name}
POST {service}/{resources}/{id}/action/{name}
POST {service}/{resources}/{id}/{sub-resources}/action/{name}
POST {service}/{resources}/{id}/{sub-resources}/{id}/action/{name}

// POST /v4/content/articles/1/actions/DELETE
// Method Substitution for DELETE, must behave exactly the same as:
// DELETE /v4/content/articles/1

// POST /v4/content/articles/1/actions/delete
// 400 Bad Request
// Fails due to case sensitivity

```

## Headers
A specific set of headers are supported. A server MUST NOT respect any header
outside of the defined list.

### Request Headers
* Authorization
* Accept-Encoding
	* Client MAY provide for response body compression. Supported value(s)
	  are "gzip"
* Content-Encoding
	* Client MAY provide for request body compression. Supported value(s)
	  are "gzip"
* Origin, Access-Control-Request-Method, Access-Control-Request-Headers
	* Support for CORS
* Original-Request-Id
	* Client MAY provide a tracking identifier. 
	* MUST be less than 1024 characters. Characters MUST be within US-ASCII.
* If-Match
	* Client MAY provide etag values on PUT/PATCH for concurrency problems
* If-None-Match
	* Client MAY provide etag values on GET for cache behavior of HTTP code
	  304
* Content-Type
	* Client SHOULD provide "application/json; charset=utf-8"
* Accept
	* Client MAY provide an "accept" header. Routes MUST ignore the header.

## Response Headers
* Access-Control-Allow-Origin, Access-Control-Allow-Methods,
  Access-Control-Allow-Headers, Access-Control-Max-Age,
  Access-Control-Expose-Headers, Access-Control-Allow-Credentials
	* Support for CORS
* Location
	* Async MUST respond header to query the async task request
	* Resource creation MUST respond with location of created resource
* RateLimit-Limit
	* Rate Limit Ceiling for the given resource. Value MUST start from
	  one(1).
* RateLimit-Remaining
	* Number of requests remaining within the Rate Limit window. 
* RateLimit-Reset
	* When Rate Limit window is reset. Value MUST be a specific time in
	  UTC epoch seconds.
* Request-Id
	* MUST be included in all responses. MUST be less than 1024 characters.
	  Characters MUST be within US-ASCII.
* Original-Request-Id
	* MUST be back of Request's Original-Request-Id if provided.
* Content-Type
	* Responses MUST be "application/json; charset=utf-8"

## HTTP Compression

Servers can make significant performance improvements by utilizing built-in
compression in HTTP. The guide includes both compression as a means for
clients, especially SDKs, to know that compression is always available. Request
compression is difficult to retroactively add to an API so it is important to
support within all routes in the first version at a framework level. Most
frameworks and stacks support this as a configuration option.

 * [Apache](http://httpd.apache.org/docs/2.4/mod/mod_deflate.html#input)
 * [Tomcat](http://predic8.com/gzip-compression-filter.htm)
 * [Ngix](http://nginx.com/resources/admin-guide/compression-and-decompression/)
 * [Node.js](https://github.com/expressjs/compression)
 * [IIS broken self roll it](http://stackoverflow.com/questions/16671216/how-do-i-enable-gzip-compression-for-post-upload-requests-to-a-soap-webservice)

### Compression Request
Servers MUST support "gzip" for requests, optional section of HTTP1.1. Requests
with a body and header "Content-Encoding: gzip" are uncompressed before
processing in accordance to [Content-Encoding RFC7231 Section
3.1.2.2](https://tools.ietf.org/html/rfc7231#section-3.1.2.2).

See also [Hypertext Transfer Protocol (HTTP/1.1): Semantics and Content
RFC7231](https://tools.ietf.org/html/rfc7231)  See also [Http2 Use of
Compression](http://http2.github.io/http2-spec/#rfc.section.10.6)  

### Compression Response
Servers MUST support "gzip" for responses, optional section of HTTP1.1.
Requests with a header "Accept-Encoding: gzip" MUST be compressed in accordance
with [Accept-Encoding RFC7231 Section
5.3.4](https://tools.ietf.org/html/rfc7231#section-5.3.4)

See also [Hypertext Transfer Protocol (HTTP/1.1): Semantics and Content
RFC7231](https://tools.ietf.org/html/rfc7231)  See also [Http2 Use of
Compression](http://http2.github.io/http2-spec/#rfc.section.10.6)  

# Authentication

Authentication MUST only be done with "Authorization" header. Routes bearer
token usage MUST NOT accept Form-encoded body; routes MUST NOT accept URI Query
parameter tokens.

See also [OAuth 2.0 (RFC6749)](http://tools.ietf.org/html/rfc6749)  
See also [OAuth 2.0 Bearer Token Usage
(RFC6750)](http://tools.ietf.org/html/rfc6750#section-2)  

# Style

## Request and Response Bodies
JSON Requests and Responses to the API MUST be in UTF-8.  

## Identifiers
All resources with Identifiers MUST return a property called `id`. `id` MUST
be a string, with a maximum length of 128 bytes. `id` MUST be treated
as opaque by clients of the API.

## Plural Nouns
Resources and Sub-Resources MUST be referenced as plural nouns.  Resources that
will always be singular MUST be plural nouns.  Plural nouns MUST end in either
the `-s` or `-es` suffix.

### Examples
```javascript 

// BAD 
   // Needs `-s` suffix
   /v4/data/extenstion
   /v4/content/article

   // needs `-es` suffix, not `-s`
   /v4/engagement/boxs

// GOOD  
   /v4/data/extensions
   /v4/content/articles/
   /v4/engagement/boxes

```

## Path
Path SHOULD be case sensitive. 

Route SHOULD NOT exceed two resource names in the URI.  Routes MUST NOT expose
resources beyond two nested levels. Implementations SHOULD wrap and expose the
sub-resource as a root level resource.

Routes MUST NOT use reserve word(s) in resource name
* views
* files

### Examples

    {service}/{resources}
    {service}/{resources}/{id}
    {service}/{resources}/{id}/{sub-resources}
    {service}/{resources}/{id}/{sub-resources}/{id}

Routes SHOULD NOT exceed

    {service}/{resources}/{id}/{sub-resources}/{id}

Routes SHOULD additionally exist

    {service}/{**sub**-resources}/{id}

## Query string

* Routes MUST accept and ignore unexpected query string parameters
* Query string parameters MUST be documented and communicated to API users as
  camelCase
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

## Properties

### Property Naming

* Property Names SHOULD be camelCase
* Properties containing a URL MUST be suffixed with "Url"
    * `imageUrl : "http://i0.kym-cdn.com/photos/images/original/000/038/262/hahaguy.jpg"`
* Properties containing a Date MUST be suffixed with "Date"
    * `createdDate : "2015-05-21T00:00:00Z"`

### Data Property Types

Basic types must correspond to [JSON RFC 7159](https://tools.ietf.org/html/rfc7159)


#### Numbers

RECOMMENDED to be unquoted. Examples of exceptions are when exact precision
must be maintained in access of double precision numbers [IEEE
754](http://grouper.ieee.org/groups/754/).

* Decimal One Million SHOULD be represented as:
	* `1000000.00`
	* NOT `"1000000.00"`
	* NOT `"1,000,000.00"`

#### Arrays

* MUST contain homogeneous values
    * Scalar values:
        * `[1,2,3,4]`
        * `["a", "b", "c", "d"]`
        * NOT `[1, 'a', 2, 'b']`
    * Objects MUST be arrays of the same type, e.g.

```javascript
/* array of Authors */
    [ 
        { 
	    "id" : "1", 
	    "name" : "George R.R. Martin" 
	}, 
	{ 
	    "id" : "2", 
	    "name" : "Isaac Asimov"
	}
    ]
```
    * Object arrays MUST not contain mixed objects, e.g.

```javascript
/* BAD : array of Authors mixed with Books */
    [ 
        { 
	    "id" : "1", 
	    "name" : "George R.R. Martin" 
	}, 
	{  
	    "id" : "1",
	    "title" : "Winds of Winter",
	    "releaseDate" : "2016-05-17T00:00:00Z"
	},
	{ 
	    "id" : "2", 
	    "name" : "Isaac Asimov"
	},
	{
	    "id" : "2",
	    "title" : "I, Robot.",
	    "releaseDate" : "1950-12-02T00:00:00Z"
	}
    ]
```



#### Boolean

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
	}
}
```

### Enumerations

Enumerations are a fixed list of strings that represent the complete options
for a property. Values contained by the list are documented and enforced
through the required API description document. Routes MAY have enumerations
that new objects can not be set to, i.e. retired values.

* REQUIRED for all values to be listed in API description document
* Value MUST be a string that is short and provides meaning in English
	* `"EXAMPLE_1"` or `"Example2"`
	* NOT `1` or `2`

```javascript
/* DON'T DO THIS - BAD EXAMPLES */

// GET /v4/content/posts/1
// 200 OK
{
	"data" : [{
		"id" : "1",
		"name" : "Blog post",
		"type" : "1"
		/* What is a '1'? */
	}],
	"meta" : {
	}
}

// GET /v4/content/articles/1
// 200 OK
{
	"data" : [{
		"id" : "2",
		"name" : "An Article",
		"type" : "2"
		/* What is a '2'? */
	}],
	"meta" : {
	}
}

/* DO THIS, AND DESCRIBE THESE ENUMS IN YOUR SWAGGER DOCS - GOOD EXAMPLES */

// GET /v4/content/posts/1
// 200 OK
{
	"data" : [{
		"id" : "1",
		"name" : "Blog post",
		"type" : "Blog"
		/* Oh, it's a blog */
	}],
	"meta" : {
	}
}

// GET /v4/content/articles/1
// 200 OK
{
	"data" : [{
		"id" : "2",
		"name" : "An Article",
		"type" : "Article"
	}],
	"meta" : {
	}
}
```

See also [API Description Document](API_Description_format.md)

### Dates and Times 

* MUST include date, time and timezone.
* Dates MUST be ISO 8601 style of:
    * `2015-05-04T15:39:03Z`
    * `2015-05-04T00:00:00T-0700`
* Dates in responses MUST be returned in UTC, or Zulu time
* Dates in requests MAY be accepted in non-UTC time, but MUST have an accompanying timezone offset
    * `2015-05-04T00:00:00+0700`
    * `2015-05-04T00:00:00-0300`
* Dates MUST NOT accept or respond with any other ISO 8601 date style
* Routes MAY accept no timezone for **recurring** events
    * See pattern [Recurring events](pattern/recurringevent.md)

See Also: [ISO 8601 Wikipedia](https://en.wikipedia.org/wiki/ISO_8601)

### Marketing Cloud Specific Properties

When including information about a Resource's Enterprise, Business Unit/Member,
Routes MUST include a relationship object.  These relationships must be objects
called `member` or `enterprise`.  Routes SHOULD NOT reference "member" without
referencing the parent "enterprise".

```javascript
/* Example where an Article Object is related to Enterprise abc123 */
{
	"data" : [{
		"id" : "1",
		"name" : "An Article",
		"published" : true,
		"enterprise" : {
			"id" : "abc123"
		},
		"authors" : [
			{ "id" : "1" },
			{ "id" : "2" }
		]
	}],
	"meta" : { }
}
	
/* Example where an Article Object is related to a Business Unit/Member xyz456 */
{
	"data" : [{
		"id" : "1",
		"name" : "An Article",
		"published" : true,
		"member" : {
			"id" : "xyz456"
		},
		"authors" : [
			{ "id" : "1" },
			{ "id" : "2" }
		]
	}],
	"meta" : { }
}

/* Example where an Article Object is related to a BU and an Enterprise */
{
	"data" : [{
		"id" : "1",
		"name" : "An Article",
		"published" : true,
		"member" : {
			"id" : "xyz456"
		},
		"enterprise" : {
			"id" : "abc123"
		},
		"authors" : [
			{ "id" : "1" },
			{ "id" : "2" }
		]
	}],
	"meta" : { }
}

```

## Relationships

A relationship is a reference to an external object. Relationships are ALWAYS represented as objects.

Relationship objects SHOULD ONLY contain an id.  Relationship objects SHOULD
only be hydrated with a related resource in cases where the base resource will
ALWAYS need the relationship object. It is up to the developer of the resource, 
with approval from the Platform team, to determine the appropiate minimum
viable resource to return.


### Nested Relationships

A nested relationship is a sub-resource on a root-level resource.  For Example:
    
    /v4/content/articles/1/authors/2

The root-level resource is "Articles".  The sub-resource is "Authors" and the 
entire URI represents a Nested Relationship between and Article and it's Author.
Deleting the Nested Relationship in this example does not delete the Author with
id 2, but instead removes the relationship between them.

```javascript

// GET /v4/content/articles/1
// 200 OK
{
	"data" : [{
		"id" : "1",
		"name" : "An Article",
		"published" : true,
		"authors" : [
			{ "id" : "1" },
			{ "id" : "2" }
		]
	}],
	"meta" : { }
}
/* An Article Object with 2 nested relationships */

// DELETE /v4/content/articles/1/authors/2
// 200 OK
/* Delete the relationship between author 2 and article 1 */

// GET /v4/content/articles/1
// 200 OK
{
	"data" : [{
		"id" : "1",
		"name" : "An Article",
		"published" : true,
		"authors" : [
			{ "id" : "1" }
		]
	}],
	"meta" : { }
}
/* The relationship is not reflected in the article */

// GET /v4/content/articles/1/authors/2
// 404 Not Found
/* Error Object Response Omitted for brevity */
/* The nested relationship does not exist. */

// GET /v4/content/authors/2
// 200 OK
{
	"data" : [{
		"id" : "2",
		"name" : "George R.R. Martin"
	}],
	"meta" : { }
}
/* But the Author still exists... at least until he finishes ASOIAF... */

```


### Relationship Object

* SHOULD NOT contain properties other than Id.

| Property Name | Type   | Cardinality | Description                                    |
|---------------|--------|-------------|------------------------------------------------|
| Id            | String | 1 - 1       | An identifier for the related resource.        |

### Representing Relationships

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
	}
}
/* The Author property is a Relationship Object */
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
	}
}
```

### Relationships in Requests

* Requests containing relationship objects MUST only modify relationship
  between the two resources, and not the related objects themselves.
* Servers SHOULD respond with an error to requests containing any properties
  besides "id" a relationship object.

See also [Relationship definition](glossary.md)

See also [Relationship object](justification/relationshipobject.md)


# Request Format 

* Servers MUST reject requests if the body has unexpected or undocumented
properties OR objects.

# Response Format

Responses from a route MUST be returned in the prescribed envelope format.
Response objects MUST include all properties on a resource, even when value is
"null".

## Header

Routes MUST respond with a "Location" header describing the location of the
newly created resource when a resource is created.

## Localization

Routes MUST NOT provide localized values.

## Envelope

Routes MUST respond with the following JSON envelope. The envelope MUST contain at least
one Data or Error object.

| Property Name | Type   | Cardinality | Description                                    |
|---------------|--------|-------------|------------------------------------------------|
| Data          | Array  | 0 - 1       | A collection of Data objects, can be empty. MUST
be an array, even if there are 1 or fewer objects being returned.                       |
| Meta          | Object | 0 - 1       | Object containing metadata about the response. |
| Error         | Object | 0 - 1       | Object containing information about an error.  |

### Data Object

Routes that respond with data must contain that data in the Data Object.  Data objects
MUST contain an identifier, and any number of additional properties.

| Property Name | Type   | Cardinality | Description                                    |
|---------------|--------|-------------|------------------------------------------------|
| Id            | String | 1 - 1       | An identifier for the resource.                |
| *             | *      | *           | MAY have any number of additional properties.  |


### Meta Object

Routes MUST include a meta object at root level of the response envelope.


#### Meta Object Structure

* The Meta object MUST NOT include any properties outside of those defined below.

| Property Name | Type   | Cardinality | Description                                    |
|---------------|--------|-------------|------------------------------------------------|
| totalCount    | Integer| 0 - 1       | Total # of records available in a collection.  |
| links         | Array  | 0 - 1       | An array of Link objects.                      |

#### Link Object

* Link Objects MUST NOT include properties outside of those defined below.

| Property Name | Type   | Cardinality | Description                                    |
|---------------|--------|-------------|------------------------------------------------|
| href          | String | 1 - 1       | A URI or URL to a state of the resource. MAY be 
null                                                                                    |
| name          | String | 1 - 1       | Description of state being referenced. MUST be 
one of: (prev, next, self, first, last). See also [HATEOAS](justerification/hateoas.md) |
| path          | String | 1 - 1       | JSON Path of the object the link belongs to.   |
| method        | String | 1 - 1       | HTTP method to use with `href`. If `href` is 
null, this value must be null                                                           |

## Example
```javascript
// GET /v4/data/foos?offset=1&limit=2
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
* Implementations SHOULD NOT attempt to aggregate errors that relate to  the
  syntax of the request. E.g. 
    * Only one missing comma at a time
    * Don't go beyond validating an invalid JSON payload
* Implementations SHOULD attempt to aggregate data validation errors within a
valid request. e.g. 
    * Validate that all email address are a valid syntax 
    * Validate that all numbers are within the predefined range.

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

# Interaction Patterns

## Field Specification Format
The Field Specification Format is used by a variety of Interaction Patterns for
identifying specific properties within a JSON object.

The format we've adopted is the [Google Partial Response Field
Format](https://developers.google.com/custom-search/json-api/v1/performance#partial).

See Also: [Google's Description of Partial Response Field](pattern/google_partial_responses.md)

* **Begins** at response `data` property, e.g. Json Path `$.data`
* Use `property_1/property_2` to select property_2 that is nested within property_1
    * this pattern can be continued, e.g.
        * `property_1/property_2/.../property_N`
* a sub-selector to request a set of specific sub-properties of arrays or
  objects by placing expressions in parentheses "( )", e.g.
	* `fields=items(id,author/email)` 
		* returns only the item ID and author's email for each element
		  in the items array. 
	* Can also specify a single sub-field, e.g., the following are equivalent
		* `fields=items(id)` 
		* `fields=items/id`
* a wildcard, "\*"  can be used for all properties within a sub-object, e.g., to select all objects in a pagemap:
    * `fields=items/pagemap/\*` 

## Sorting

Routes MAY support sorting. Routes supporting sorting MUST use the query string
parameter `sort`.  Properties to be sorted MUST be specified in the Field
Specification Format.   Routes MAY support a subset of all properties available
on a resource.  

By default, `sort` order MUST BE ascending.  Routes MUST support a descending
flag on all valid properties with a leading minus "-" on the field
specification format value.

### Multiple Sort Values

The `sort` QSP MAY accept multiple values. It MUST accept 1-and-only-1 property 
per sort tuple specified. You cannot specify a single `sort` value that would
resolve to multiple properties - However, you may specify multiple sort values that
resolve to a single property each. A `sort` with a value that resolves to more
than one property MUST return a 400 error.

#### Multiple Sort Values Examples
```    
    ?sort=articles(id),articles(author)  # GOOD
    ?sort=articles(id, author)           # BAD
    ?sort=-articles/id, -articles/author # GOOD
    ?sort=articles/*                     # BAD

```
### Example

```javascript
/* Sort by cores ascending */
// GET /v4/data/supercomputers?sort=cores
// 200 OK
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
			{ "href" : null, "name" : "prev", "path" : "$.data", "method" : null },
			{ "href" : null, "name" : "next", "path" : "$.data", "method" : null }
		]
	}
}
```

```javascript
/* Sort by cores descending */
// GET /v4/data/supercomputers?sort=-cores
// 200 OK
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
			{ "href" : null, "name" : "prev", "path" : "$.data", "method" : null },
			{ "href" : null, "name" : "next", "path" : "$.data", "method" : null }
		]
	}
}
```

```javascript
/* Sort by first appearance and cores  */
// GET /v4/data/supercomputers?sort=-firstAppearance,-cores
// 200 OK
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
			{ "href" : null, "name" : "prev", "path" : "$.data", "method" : null },
			{ "href" : null, "name" : "next", "path" : "$.data", "method" : null }
		]
	}
}
```

## Partial Responses
Routes SHOULD support partial responses. Properties to **include** MUST be
specified by the query string parameter `fields`. The format MUST be in the
"Field Specification format".

See also [Salesforce Style](http://www.salesforce.com/us/developer/docs/api_rest/Content/dome_get_field_values.htm)

### Example
Request for a partial response: This HTTP GET request for the above resource
that uses the fields parameter significantly reduces the amount of data
returned.  
```
// GET /v4/data/hydraProperties/1
// 200 OK
{
	"data" : [{
		"id" : 1,
		"word1" : "cut",
		"word2" : "off",
		"word3" : "one",
		"word4" : "head",
		"word5" : "two",
		"word6" : "more",
		"word7" : "shall",
		"word8" : "take",
		"word9" : "its",
		"word10" : "place"
	}],
	"meta" : {}
}
/* Full example of hydraProperty body */

// GET /v4/data/hydraProperties/1?fields=word1,word6
// 200 OK
{
	"data" : [{
		"id" : 1,
		"word1" : "cut",
		"word6" : "more"
	}],
	"meta" : {}
}
/* Partial Response */
```

## Filtering

Filtering MUST be restricted to exposed properties. Clients and routes SHOULD
use filtering to limit the results in a structured and absolute way.

Routes MAY support filtering. Routes supporting filtering MUST only use
instances of the query string `f[{property}][{operation}]`.   A filter's
property MUST be in the Field Specification Format, which MUST resolve to a
single property.  Routes MAY support a subset of those properties.

A filter with a field specification wider than one property MUST return a 400 error.

```
/* error as it has more than one property */
?f[parent/*][eq]=1
```

### Properties

Filtered Properties MUST adhere ot the Field Specification Format.  

See also [Partial Responses]("#Partial Responses")

### Operations

A Filter's Operation MUST be 1-and-only-1 of the following values. Multiple
filters MAY be supported.  If Multiple filters are specified, the Filters MUST
be combined as an "AND" operation.  If a route supports Filtering, it MUST support
all operations.

| Filter Operation | Operation                 | Additional Notes                       |
|------------------|---------------------------|----------------------------------------|
| eq               | =  Equals                 | MAY support comma separated values     | 
| not              | != Not equal              | MAY support comma separated values     | 
| gt               | >  Greater than           | MUST ONLY be a number or date          | 
| gte              | >= Greater than or equals | MUST ONLY be a number or date          | 
| lt               | <  Less than              | MUST ONLY be a number or date          | 
| lte              | <= Less than or equals    | MUST ONLY be a number or date          | 

### Values
For the operations `eq` and `not` routes MAY support comma separated values.
Operations `gt`, `gte`, `lt`, `lte` MUST support only a single value. Routes
MUST treat all double quotes and commas as literals in these operations.

### Comma Separated Values

If multiple values are specified in the "eq" or "not" operations , they MUST be
in double quoted CSV format, as defined in the following ABNF grammar adapted
from [RFC4180 i.e. "CSV Mime type
format"](https://tools.ietf.org/html/rfc4180). See also [RFC2234 Augmented BNF
syntax](https://tools.ietf.org/html/rfc2234)

Multiple values MUST be combined with an "OR" operation, e.g. similar to 

    SELECT id FROM items WHERE items.value IN (1,2,3)
    SELECT id FROM items WHERE items.value = 1 OR items.value = 2 OR items.value = 3

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

### Examples
```
/* Pattern */
f[{property}][{operation}]={value}

/* numbers */
f[cost][eq]=50
WHERE cost = '50'

/* sub-properties */
f[content/locale][eq]=en
WHERE locale = 'en'

/* comparsion equality */
f[cost][lte]=50
WHERE cost <= 50

f[cost][gte]=50
WHERE cost >= 50

/* not */
f[color][not]=blue
WHERE color <> 'blue'

f[cost][not]=50
WHERE cost <> 50

/* multiple filters */
f[cost][lte]=100&f[cost][gte]=50
WHERE cost <= 100
AND   cost >= 50

f[cost][gt]=50&f[cost][lt]=100
WHERE cost < 100
AND   cost > 50

/* complex example */
f[color][eq]=blue,"green","red"""&f[cost][lte]=50
WHERE color IN ('blue', 'green', 'red"' )
AND   cost <= 50

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
			{ "href" : null, "name" : "prev", "path" : "$.data", "method" : null },
			{ "href" : null, "name" : "next", "path" : "$.data", "method" : null }
		]
	}
}
```
```javascript
/* Filtering to two vendors */
// GET /v4/data/supercomputers?f[vendor][eq]=Cray%20Inc.,IBM
// 200 OK
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
			{ "href" : null, "name" : "prev", "path" : "$.data", "method" : null },
			{ "href" : null, "name" : "next", "path" : "$.data", "method" : null }
		]
	}
}
```

```javascript
/* Filtering to between 500,000 and 1,000,000 cores */
// GET /v4/data/supercomputers?f[cores][lt]=1000000&f[cores][gt]=500000
// 200 OK
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
			{ "href" : null, "name" : "prev", "path" : "$.data", "method" : null },
			{ "href" : null, "name" : "next", "path" : "$.data", "method" : null }
		]
	}
}
```


```javascript
/* Super computer centers added to the list in the ninties */
// GET /v4/data/supercomputers?f[firstAppearance][gte]=1990-01-01T00:00:00Z&f[firstAppearance][lte]=2000-01-01T00:00:00Z
// 200 OK
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
			{ "href" : null, "name" : "prev", "path" : "$.data", "method" : null },
			{ "href" : null, "name" : "next", "path" : "$.data", "method" : null }
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


## Pagination

Collection routes SHOULD support pagination.  Routes that do not support
pagination MUST return all items, regardless of the total number of results, or 400
Bad Request.  Requests without a `limit` or `size` query string parameter MUST
return the first page of results or 400 Bad Request.

Routes supporting any form of pagination MUST support Offset based pagination.

Results MUST be limited to no more than 1000 results.

## Pagination Query String Parameters
* links MUST NOT change `limit` or `size` from the value specified in the request.
* `limit` and `size` should be set to the route's default page size

## Pagination Metadata
* Routes supporting pagination MUST include a collection, `links` within the
  Response's Meta object.
* Objects in `links` MUST include all query string parameters
  that were specified in the request
* `links` MUST include the `next` and `prev` objects. 
    * the `href` attribute of `next` and `prev` MUST be null if there are no
      further pages before or after the currently specified page
* `links` MAY include an object `first` which MUST include a link to the first
  page of results. 
* `links` MAY include an object `last` which MUST include a link to the last 
  page of results. 

### Offset

Offset based pagination allows a request to specify a number of results to skip
in a result set, as well as a total number of results to return, after the
skipped amount.

Routes supporting any form pagination MUST also support Offset based pagination. 

Offset based pagination routes MUST support both `offset` and `limit` query string
parameters.

Query string parameter `offset` MUST be the number of results to skip.

Query string parameter `limit` MUST be the number of results to return.

#### Examples
* ?limit=50&offset=50 would begin with the 51st object in a collection, and
  would return no more than 50 results.
* ?limit=25&offset=0 would begin with the 1st object in a collection, would
  return 25 results.
* ?limit=1000&offset=11 would begin with the 12th object in a collection, and
  would return 1000 results.

##### Bad Examples 

* ?limit=1001&offset=0 would return 400 Bad Request, because routes cannot
  return more than 1000 results. 

```javascript
/* No parameters, defaults offset=0 limit=1000 but is limited to the 10 records that exist */
// GET /v4/data/supercomputers
// 200 OK
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
			{ "href" : null, "name" : "prev", "path" : "$.data", "method" : null },
			{ "href" : null, "name" : "next", "path" : "$.data", "method" : null }
		]
	}
}
```

```javascript
/* Limit returns that count of rows, defaults offset=0  */
// GET /v4/data/supercomputers?limit=2
// 200 OK
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
			{ "href" : null, "name" : "prev", "path" : "$.data", "method" : null },
			{ "name" : "next", "method": "GET", "href" : "/v4/data/supercomputers?limit=2&offset=2", "path" : "$.data" }
		]
	}
}
```

```javascript
/* Next page */
// GET /v4/data/supercomputers?limit=2&offset=2
// 200 OK
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
		]
	}
}
```

```javascript
/* Last page, non-starting offset */
// GET /v4/data/supercomputers?limit=4&offset=6
// 200 OK
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
			{ "href" : null, "name" : "next", "path" : "$.data", "method" : null }
		]
	}
}
```

```javascript
/* Last page with over hanging offset just returns possible rows */
// GET /v4/data/supercomputers?limit=6&offset=9
// 200 OK
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
			{ "href" : null, "name" : "next", "path" : "$.data", "method" : null }
		]
	}
}
```

```javascript
/* empty results are not an error */
// GET /v4/data/supercomputers?limit=1000&offset=1000
// 200 OK
{
	"data" : [],

	"meta" : {
		"totalCount" : 10,

		"links" : [
			/* offset reflects api user current request; offset 3 */
			{ "name" : "prev", "method": "GET", "href" : "/v4/data/supercomputers?limit=1000&offset=0", "path" : "$.data" },
			{ "href" : null, "name" : "next", "path" : "$.data", "method" : null }
		]
	}
}
```

### Cursor

Cursor based pagination allows clients to browse large, dynamic datasets
without missing results.

Routes SHALL NOT support cursor paging.

See also [Facebook cursor paging](http://api-portal.anypoint.mulesoft.com/facebook/api/facebook-graph-api/docs/reference/pagination)
See also [Amazon cursor paging](http://docs.aws.amazon.com/cloudsearch/latest/developerguide/paginating-results.html#deep-paging)

### Traditional

Traditional pagination allows clients to browse data sets by specifying a page
number and a page size.

Routes following SHALL NOT support traditional paging.

## Searching

Searching is to find resources based on one or many properties of that resource.

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

### Examples

```javascript
/* search should be contains */
// GET /v4/data/supercomputers?q=SC
// 200 OK
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
			{ "href" : null, "name" : "prev", "path" : "$.data", "method" : null },
			{ "href" : null, "name" : "next", "path" : "$.data", "method" : null }
		]
	}
}
```

```javascript
/* search should be case insensitive */
// GET /v4/data/supercomputers?q=comp
// 200 OK
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
			{ "href" : null, "name" : "prev", "path" : "$.data", "method" : null },
			{ "href" : null, "name" : "next", "path" : "$.data", "method" : null }
		]
	}
}
```

```javascript
/* search can be multiple fields */
// GET /v4/data/supercomputers?q=el
// 200 OK
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
			{ "href" : null, "name" : "prev", "path" : "$.data", "method" : null },
			{ "href" : null, "name" : "next", "path" : "$.data", "method" : null }
		]
	}
}
```



# Release Notes

The Fuel REST API Style Guide is a document describing the Developer Experience of
Fuel REST APIs.  

This Style Guide is versioned according to a set of features to be supported by
v4 of the REST API.  Each Sub-version (4.x) is meant to describe a set of functionality
described and made available via the Framework. It is a goal, but not a guarantee,
that described functionality in 4.X of the Style Guide will be backwards compatbile.  
All attempts will be made to withold breaking changes to the Style Guide for
future versions of the REST API.

## v4.0

* Versioning in the API
* HTTP Status Codes
    * Implicit
    * Explicit
* HTTP Methods
    * POST
    * GET
    * DELETE
    * PUT
    * PATCH
    * OPTIONS
    * HTTP Method Substitution
* HTTP Compression
    * Requests
    * Responses
* Headers
    * Requests
    * Responses
* General Style
    * Paths
    * Query Strings
    * Collections
    * Remote Field Expansion
    * Marketing Cloud Specific Properties
* Request Format
* Response Format
    * Header
    * Localization
    * Envelope
	* Data Object
	* Meta Object
* Errors
    * Error envelope
    * Validation Details
* Field Specification Format
* Sorting
* Partial Responses
* Filtering
* Pagination
    * Offset
* Searching
* Authentication

## v4.1 

* Convenience Edges
    * Actions
    * Views
* Etags
    * Concurrency
    * Caching
* Pagination
    * Cursor
    * Traditional



## v1.0.0 - April 28th, 2015
- [#1004](Link here) - Description here ([@USER](https://api.github.com/users/USER))

[Commits](Link here)

## Prior Versions

Description here

Instead of:

```js
template(context, helpers, partials, [data])
```

Use:

```js
template(context, {helpers: helpers, partials: partials, data: data})
```

[builds-page]: Link here
[cdnjs]: Link here
[components]: Link here
[npm]: Link here

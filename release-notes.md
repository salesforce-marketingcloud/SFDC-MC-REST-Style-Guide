# Release Notes

## Document Versions

| Author | Version | Date      | Changes                                    | 
|--------|---------|-----------|--------------------------------------------|
| kchant | 1       | 7/15/2015 | Initial Roadmap.                           |

## Introduction

The Fuel REST API Defintion is a document describing the Developer Experience
of Fuel REST APIs.  

This Definition is versioned according to a set of features to be supported by
v4 of the REST API.  Each Sub-version (4.x) is meant to describe a set of
functionality described and made available via Framework tooling. It is a goal,
but not a guarantee, that described functionality in 4.X of the Style Guide
will be backwards compatible.  All attempts will be made to withold breaking
changes to the Definition for future versions of the REST API.

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

##  v4.1 
* Paths
    * Referencing Resources with Multiple IDs / Keys
    * Identifying Resources with Composite Keys
* Querying
* Upserting
    * PUT
    * PATCH
    * POST

##  v4.2
* Convenience Edges
    * Actions
    * Views
* Pagination
    * Cursor
    * Traditional
* Asynchronous Operations

##  v4.3 and Beyond
* Etags
    * Concurrency
    * Caching
* Localization 
    * Localized Formatting
    * Localized Content
* Batching Requests
    * Operations on Collections
    * Multiple Requests
* Convenience Edges
    * Views
        * Binary Representations of Resources

## Framework Release Dates
Platform Teams at the Marketing Cloud will build frameworks
to support and enable channel application teams to build 
Fuel 4 Compliant APIs at the following cadence.

| Technology | 4.0     | 4.1     | 4.2     | 4.3     |
|------------|---------|---------|---------|---------|
| C# / .NET  | 2015-07 | 2016-01 | 2016-02 | 2016-03 |
| Java       | TBD     | -       | -       | -       |
| Ruby       | TBD     | -       | -       | -       |
| PHP        | TBD     | -       | -       | -       |

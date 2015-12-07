# Release Notes

## Document Versions

| Author | Version | Date      | Changes                                    | 
|--------|---------|-----------|--------------------------------------------|
| kchant | 1       | 7/15/2015 | Initial Roadmap.                           |
| kchant | 2       | 12/7/2015 | Replaced version numbers with greek chars. 
Added clarity to introduction.                                              |

## Introduction

The Salesforce Marketing Cloud REST API Definition is a document describing the
Developer Experience of our next-generation public and internal facing REST
APIs.  

In order to segment discussion and development of this definition, it has been
sub-versioned into discrete features.  Each sub-version is meant to describe a
subset of the full capability of the next-gen API. It is a goal, but not a
guarantee, that described functionality in these sub-versions of the Style
Guide will be backwards compatible.  All attempts will be made to withold
breaking changes to the Definition for future versions of the REST API.

## v. Alpha
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

##  v. Beta
* Paths
    * Referencing Resources with Multiple IDs / Keys
    * Identifying Resources with Composite Keys
* Querying
* Upserting
    * PUT
    * PATCH
    * POST

##  v. Gamma
* Convenience Edges
    * Actions
    * Views
* Pagination
    * Cursor
    * Traditional
* Asynchronous Operations

##  v. Delta and beyond
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

| Technology | Alpha   | Beta    | Gamma   | Delta   |
|------------|---------|---------|---------|---------|
| C# / .NET  | TBD     | -       | -       | -       |
| Java       | TBD     | -       | -       | -       |
| Ruby       | TBD     | -       | -       | -       |
| PHP        | TBD     | -       | -       | -       |

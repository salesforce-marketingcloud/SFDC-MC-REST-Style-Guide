# Glossary

## Server

The Server that is responsible for handling API requests and responses.  

## Resource
The "R" in URL. i.e. Any part of the URL is considered a resource identifier
for the purposes of this document. Whether the resource references a complete
collection, only partially identifies a collection, or a specific item is
immaterial. Views are not a resource.

## Query String
Should not be to long. Should not radically change a routes behavior.

## Collection Route
Any Route that returns sets of more than 1 resources.

## Relationship
Between two objects, a reference.  Only exists between a root level resource
and its sub-resources; exists for 1-n and n-n resources.

## Partial Responses
The ability to restrict the display of properties on a Resource.  Useful when an API consumer wants only a limited set of properties from a resource.

## Remote Field Expansion
The ability to "hydrate" a remote relationship object on a given resource.  

## Upserting
Dynamically updating an object, or creating it if it doesn't already exist in the system.

## Views
A view is an expanded representation of a resource.

## Filtering
Limiting a collection of resources returned by specifying simple operations on the
properties on that resource.

## Searching
Text-based searching of 1 or more properties on a given resource type.

## Querying
Advanced searching of 1 or more properties on 1 or more resource types.

## Asynchronous Operation
Request that is accepted by a Server but not immediately processed.

## Etags
A unique representation of a resource in a given state.  Used for caching
and concurrency detection.

## Pagination
A technique to distribute large groups of data into multiple pages of responses.

## Pagination, Cursor-based
A form of pagination that allows a consumer to guarantee position in the collection, 
and guarantees resources in a highly-dynamic collection will seen when traversing.

## Pagination, Offset-based
A simple form of pagination that allows a consumer to navigate a collection, specifying
the number of results to skip, and number of results to display. 

## Pagination, Traditional
A simple form of pagination that allows a consumer to navigate a collection, specifying
the number of pages to skip, and number of results to display per page. 

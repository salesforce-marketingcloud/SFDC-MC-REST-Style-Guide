## Source Filter Query Service
## Get Source Filter Queries

Returns a collection of source filter queries contained in a source filter.  Source Filters can be applied to topic filters.

<pre>GET /v3/sourceFilters/{sourceFilterId}/sourceFilterQueries</pre>

**Valid query parameters**

* **limit** - controls amount of source filter queries returned in 1 call, along with "offset" parameter allows to implement pagination of source filter queries. Default value is set to 50000.
* **offset** - skips that many records before beginning to return source filter queries, along with "limit" parameter allows to implement pagination of source filter queries. Default value is set to 0.
* **q** - allows to search by source filter query's title, at least 3 characters required to make a valid query.
* **orderBy** - enables sorting of source filter queries.

**Available values**

1. id
2. title
3. uri

**Directions of sorting**

1. ASC (default value, if not specified)
2. DESC

**Response**
```json
{
  "data": [
    {
      "id": "1",
      "title": "Source Filter Query Title",
      "uri": "my.source.filter.query.org"
    }
  ],
  "meta": {
    "totalCount": 1
  }
}
```
## Get Source Filter Query

Returns a Source Filter Query.  A source filter query is a contained in a source filter.  Source Filters can be applied to topic filters.

<pre>GET /v3/sourceFilters/{sourceFilterId}/sourceFilterQueries/{sourceFilterQueryId}</pre>

**Response**

See the sample response to get a list of source filter queries above.
    
## Create Source Filter Query

To create a new source filter query, you need to post the title (optional) and uri.

<pre>POST /v3/sourceFilters/{sourceFilterId}/sourceFilterQueries</pre>
```json
{
    "title": "Source Filter Title",
    "uri": "my.source.filter.query.org"
}
```
**Response**

* **201** Success.  Location response header that contains URL to the new source filter query resource.  
* **400** Bad Request.  Malformed JSON payload.  
* **500** Internal Server Error.  Unrecoverable error occurred during creation.  

## Update Source Filter Query

Updates the content of an existing source filter query.

<pre>PUT /v3/sourceFilters/{sourceFilterId}/sourceFilterQueries/{sourceFilterQueryId}</pre>

See the sample payload to create a source filter query above.

**Response**

* **204** No Content.  Request successfully processed.  
* **400** Bad Request.  Malformed JSON payload.  
* **500** Internal Server Error.  Unrecoverable error occurred during creation.  

## Delete Source Filter Query

Deletes an existing source filter query.

<pre>DELETE /v3/sourceFilters/{sourceFilterId}/sourceFilterQueries/{sourceFilterQueryId}</pre>

**Response**

* **204** No Content.  Request successfully processed.  
* **500** Internal Server Error.  Unrecoverable error occurred during creation.

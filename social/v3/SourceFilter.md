## Source Filter Service
### Get Source Filters

Returns a collection of source filters.  A source filter is a container for source filter queries.  Source Filters can be applied to topic filters.

<pre>GET /v3/sourceFilters</pre>

**Valid query parameters**

* **limit** - controls amount of source filters returned in 1 call, along with "offset" parameter allows to implement pagination of source filters. Default value is set to 50000.
* **offset** - skips that many records before beginning to return source filters, along with "limit" parameter allows to implement pagination of source filters. Default value is set to 0.
* **q** - search by title containing value, at least 3 characters required to make a valid query.
* **title** - search by exact title match.
* **status** - comma separated list of statuses. 0 - DEACTIVATED, 2 - ACTIVE, 3 - DRAFT.  
* **orderBy** - enables sorting of source filters.

**Available values**

1. id
2. title
3. visibility
4. status
5. creation (source filters will be ordered by creation date)
6. description
7. creator (source filters will be ordered by creator's display name) 

**Directions of sorting**

1. ASC (default value, if not specified)
2. DESC

**Response**
```json
{
  "data": [
    {
      "id": "1",
      "title": "Trent - Yahoo Answers SF",
      "description": null,
      "status": 2,
      "createdDate": "2009-12-22T17:22:08Z",
      "topicCount": 0,
      "public": false,
      "creator": {
        "id": "58",
        "link": "https://api.radian6.com/socialcloud/v2/users/58",
        "updated": null,
        "title": "Trent - Staging - GMail - CLID  = 1"
      }
    },
    ...
  ],
  "meta": {
    "totalCount": 706
  }
}
```
### Get Source Filter

Returns a Source Filter.  A source filter is a container for source filter queries.  Source Filters can be applied to topic filters.

<pre>GET /v3/sourceFilters/{sourceFilterId}</pre>

**Response**

See the sample response to get a list of source filters above.
    
### Create Source Filter

To create a new source filter, you need to post the title, description, public (optional).

<pre>POST /v3/sourceFilters</pre>

```json
{
    "title": "Source Filter Title",
    "description": "A description of what this source filter contains.",
    "public": false
}
```
**Response**

* **201** Success.  Location response header that contains URL to the new source filter resource.  
* **400** Bad Request.  Malformed JSON payload.  
* **500** Internal Server Error.  Unrecoverable error occurred during creation.  

### Update Source Filter

Updates the content of an existing source filter.

<pre>PUT /v3/sourceFilters/{sourceFilterId}</pre>

See the sample payload to create a source filter above.

**Response**

* **204** No Content.  Request successfully processed.  
* **400** Bad Request.  Malformed JSON payload.  
* **500** Internal Server Error.  Unrecoverable error occurred during creation.  

### Delete Source Filter

Deletes an existing source filter.

<pre>DELETE /v3/sourceFilters/{sourceFilterId}</pre>

**Response**

* **204** No Content.  Request successfully processed.  
* **500** Internal Server Error.  Unrecoverable error occurred during creation.

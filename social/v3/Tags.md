[linkename]: http://example.com/  "Optional Title Here"
[iso3166]: http://en.wikipedia.org/wiki/ISO_3166-1 "ISO 3166 Region Codes"
[timestamps]: http://example.com "Example"
[user]: ./User.md "User"

# Tags Service

Tags are a kind of metadata used to to describe objects and make them searchable. The tag service is used to create and manage tags on posts, authors, and sources.

## The Tag Object

### Attributes

|Attribute|Type|Description|
|---------|----|-----------|
|id|Numeric|The unique identifier of the tag.|
|updated|[Timestamp][timestamps]|The date and time when the region was added to the platform; this will not change.|
|title|Text|Type of tag|
|createdBy|[User][user]|User that created the tag|
|content|Text|Value of the tag|

### Sample Workflow Tag Response
```
{
  "data" : [
    {
      "id": "0",
      "link": "https://stg1-api/socialcloud/v3/posts/1352570476",
      "updated": "2014-10-31T12:44:43Z",
      "title": "Author Tag",
      "createdBy": {
        "id": "2999",
        "link": "https://stg1-api/socialcloud/v3/users/2999",
        "updated": "2014-11-03T14:40:55Z",
        "title": "user1",
        "email": "user1@nospam.org",
        "avatarURL": null
      },
      "content": {
        "value": "vino"
      }
    }
  ],
  "meta" : {
    "totalCount" : 1
  }
}
```

# Methods

## Adding a new tag to an author

<pre>POST /v3/authors/{authorId}/tags</pre>

Add a new tag to an author in the r6 platform. Successful calls return a 201 Created response with a Location response header that points to the new tag.

**Parameters**

|Name|Type|Description
|----|----|-----------
|id  |URL |The internal Radian6 author id|

**Example Request**
```
POST /v3/authors/{authorId}/tags

Content-Type:application/json
{
"value":"vino"
}
```

**Response**
* **201** Created with Location header of the tag

## Batch Create Author Tags

<pre>POST /v3/authors/{authorId}/tags</pre>

Add multiple tags to an author in the r6 platform. Successful calls return a 201 Created response with a Location response header that points to the author.

**Parameters**

|Name|Type|Description
|----|----|-----------
|id  |URL |The internal Radian6 author id|

**Example Request**
```
POST /v3/authors/{authorId}/tags

Content-Type:application/json
[{
  "value": "Some Tag Value 1"
},
{
  "value": "Some Tag Value 2"
},
{
  "value": "Some Tag Value 3"
}]
```

**Response**
* **201** Created with Location header of author

The body will contain an array with the ids of the newly created tags.

```json
[
  {
    "id": "154"
  },
  {
    "id": "155"
  },
  {
    "id": "156"
  }
]

```

**Errors**

|Status Code|Reason|
|---------|-----------|
|400|Invalid json was posted to the server.|
|404|Author with the given ID does not exist.|
|409|A tag with the same value exists on that author, or a duplicate tag was attempted |


## Remove Author Tag From Author

Removes a tag from the specified author. 

<pre>DELETE	/v3/authors/{authorId}/tags/{tagId}</pre>

**Errors**

|Code|Description
|----|----
|404  |if the tag was not found, or if the tag was not associated to the specified author.
|500  |error talking to db

**Response**
* **204** on successful removal.

## Remove All Author Tags From Author

Removes multiple tags from the specified author.

<pre>DELETE /v3/authors/1234/tags</pre>

**Errors**

|Code|Description
|----|----
|400  |if some of the tags were found, and others were not.
|404  |if none of tags was not found, or if none of the tags were associated to the specified author.
|500  |error talking to db

**Response**
* **204** on successful removal.

## Get Tags

Retrieves a list of TagValueResources of all tags that exist in the R6 platform for the client which the requesting user is assigned to.

<pre>GET /v3/tags</pre>

Valid Query Parameters

|Parameter|Description|Required/Default|
|---------|-----------|----------------|
|type|The type of tag|Currenlty only valid value is author. Required|
|limit|Limits the number of tags to be returned by this call. Can be used with the offset parameter to incorportate paging. Accepts values between 1 and 1000| Default = 1000|
|offset|Used with limit to implement paging|Default = 0|
|q|Allows to search by a tag value. At least 3 characters are required to make a valid query. Note - this is an exact query match.| Optional|
|sinceDate|Filters the resulting collection by those tags that were created on or after the specified date. Supports milliseconds since epoch and ISO 8601 datetime which can be fully or partially URL encoded, for example, "2014%2D02%2D03"|Optional|

**Errors**

|Code|Description|
|---|------------|
|400|If any query parameters are not valid values|
|500|If an unrecoverable error happened on the server|

**Response**
```json
{
  "data" : [
    {
        "content": {
            "value": "my tag"
        }
    },
    {
        "content": {
            "value": "my other tag"
        }
    },
    ...
  ]
  "meta" : {
    "totalCount" : 4
  }
}
```

# Authors Service

For Author Tags Service see: [Tags Service](Tags.md)

## Retrieve the author of a post by author id.

<pre>GET /v3/authors/{id}</pre>

**Parameters**

|Name    |Type    |Description|
|--------|--------|-----------|
|id      |URL     |The internal Radian6 author id|

**Response**
```json
{
    "data": [
        {
            "id": "2184",
            "title": "R6APITestUser1",
            "externalId": "429245766",
            "externalLegacyId": null,
            "authorFullName": "API 1 APIQE อาหารสั",
            "avatar": "http://pbs.twimg.com/profile_images/1815149549/b-410745-animated_dog_normal.jpg",
            "authorTags": [
                {
                    "id": "13434",
                    "value": "Sample tag"
                }
            ],
            "authorComments": [
                {
                    "id": "454545",
                    "value": "Sample note"
                }
            ],
            "bio": "Sample bioั",
            "verified": false,
            "authorData": {
                "blogId": "62550515"
            }
        }
    ],
    "meta": {
        "totalCount": 1
    }
}
```
**Errors**

|Status Code|Reason|
|-----------|------|
|404		|If the author does not exist|
|500		|If an internal error happened while fetching the author

## Add a comment to an author

<pre>POST /v3/authors/{id}/comments</pre>

**Parameters**

|Name|Type|Description
|----|----|-----------
|id  |URL |The internal Radian6 author id|

**Example Request**
```
POST https://api.radian6.com/socialcloud/v3/authors/{id}/comments

Content-Type:application/json
{
        "value": "i heart talk radio"
}
```

**Response**
* **201** Created with Location header

**Errors**

|Status Code|Reason|
|-----------|------|
|400	    |Invalid json posted to server|
|404	    |Author not found|
|500	    |Internal server error encountered while adding comment.|

## Delete the specified comment from the author resource

<pre>DELETE /v3/authors/{id}/comments/{commentId}</pre>

**Parameters**

|Name|Type|Description
|----|----|-----------
|id  |URL |The internal Radian6 author id|
|noteId  |URL |The id of the comment to delete.|

**Response**
* **204**	No Content. On success.

**Errors**

|Status Code|Reason|
|-----------|------|
|404	    |Author not found. Or the note has already been deleted. Or the note does not exist in the tenant.|
|500	    |Internal server error encountered while deleting note.|

## List all of the comments for a given blogpost author

<pre>GET /v3/authors/{id}/comments</pre>

**Parameters**

|Name    |Type    |Description|
|--------|--------|-----------|
|limit   |(Optional) Integer |Number of results to return, default 1000|
|offset  |(Optional) Integer |Index of the result to start with|

**Response**
```json
{
  "data" : [
    {
      "id": "2751",
      "authorId": 53,
      "value": "i heart talk radio",
      "deleted": null,
      "created":       {
         "userId": 26341,
         "clientId": 11084,
         "name": "APIQECLIENT1USER1",
         "date": "2015-07-27T13:53:48Z"
      }
   }
  ],
  "meta" : {
    "totalCount" : 1 
  }
}
```
* Deleted comments not returned.
* Max of 25 comments returned by default.
* Use limit and offset to page through the results.

**Errors**

|Status Code|Reason|
|-----------|------|
|404	    |Author not found|
|500	    |Internal server error encountered while deleting comment.|


# Media Type Service

## The Media Type Object

### Attributes

|Attribute|Type|Description|
|---------|----|-----------|
|id|Numeric|The unique identifier of the media type|
|updated|Timestamp|The date and time|
|title|Text| Description of the media type|
|isVisible|boolean|Visibility of media type in the system|
|classificationId|Numeric| The unique identifier of the media type classification|
|classficationType|Text|Description of the media type classification|

# Methods

## Get Media Types

Returns a collection of Media Types based on the supplied parameters.

<pre>GET /v3/mediaTypes</pre>

**Parameters**

|Name|Type|Description
|----|----|-----------
|private|boolean|(Optional) Indicate if only private media types are to be returned (Default is false)|
|classificationIds|String|(Optional) Comma delimited list of Media Type Classification Ids.  Returns Media Types belonging to the specified Media Type Classifications.|
|limit   |(Optional) Integer |Number of results to return, default 1000|
|offset  |(Optional) Integer |Index of the result to start with|

NOTE: Please note that when classification ids are supplied, the method will ignore the private query param.  So if the calling application posts /v3/mediaTypes/private=true&classificationIds=1,3 the response will contain all media types having a classification types of 1 and 3. 

**Status Codes**

|Status|Code|Reason|
|------|----|------|
|400|Bad Request|Invalid media type classification id supplied (must be numeric and valid).<br>Duplicate classification ids supplied|

**Response**
```json
{
  "data": [
    {
      "id": "1",
      "title": "Blogs",
      "isVisible": true,
      "classificationId": "1",
      "classificationType": "Broad Listening"
    },
    {
      "id": "2",
      "title": "Videos",
      "isVisible": true,
      "classificationId": "1",
      "classificationType": "Broad Listening"
    },
    ...
  ],
  "meta": {
    "totalCount": 12
  }
}
```
## Get Media Type

Returns a single media type.

<pre>GET /v3/mediaTypes/{mediaTypeId}</pre>

**Response**
```json
{
  "data": [
    {
      "id": "1",
      "title": "Blogs",
      "isVisible": true,
      "classificationId": "1",
      "classificationType": "Broad Listening"
    }
  ],
  "meta": {
    "totalCount": 1
  }
}
```

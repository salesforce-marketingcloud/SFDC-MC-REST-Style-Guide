[linkename]: http://example.com/  "Optional Title Here"
[timestamps]: http://example.com "Example"

# Language Service

The language service provides a read-only collection of the languages supported within the platform. Where language information is necessary within other objects this collection will be referenced.

## The Language Object

### Attributes

|Attribute|Type|Description|
|---------|----|-----------|
|id|Numeric|The unique identifier of the language.|
|link|URI|A direct API link to the language object.|
|updated|[Timestamp][timestamps]|The date and time when the language was added to the platform, this will not change.|
|title|Text|The english-language name of the language|
|code|Text|ISO Language Code|

## Methods

### Get All Languages

Returns a collection of language objects representing the languages contained in the platform.

<pre>GET /v3/languages</pre>

**Parameters**

|Name    |Type    |Description|
|--------|--------|-----------|
|limit   |(Optional) Integer |Number of results to return, default 1000|
|offset  |(Optional) Integer |Index of the result to start with|

**Response**
```json
{
  "data": [
    {
      "id": "1",
      "title": "English",
      "code": "en"
    },
    {
      "id": "2",
      "title": "French",
      "code": "fr"
    },
    {
      "id": "3",
      "title": "Spanish",
      "code": "es"
    },
    ...
  ],
  "meta": {
    "totalCount": 28
  }
}
```

### Get A Specific Language

Return a specific language object.

<pre>GET /v3/languages/{languageId}</pre>

No Valid Query Parameters.

**Response**
```json
{
  "data": [
    {
      "id": "1",
      "title": "English",
      "code": "en"
    },
  ],
  "meta": {
    "totalCount": 1
  }
}
```

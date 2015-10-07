[linkename]: http://example.com/  "Optional Title Here"
[iso3166]: http://en.wikipedia.org/wiki/ISO_3166-1 "ISO 3166 Region Codes"
[timestamps]: http://example.com "Example"

# Region Service

The region service provides a read-only collection of the regions within the platform. Where regional information is necessary within other objects this list will be referenced.

## The Region Object

### Attributes

|Attribute|Type|Description|
|---------|----|-----------|
|id|Numeric|The unique identifier of the region.|
|link|URI|A direct API link to the region itself.|
|updated|[Timestamp][timestamps]|The date and time when the region was added to the platform; this will not change.|
|title|Text|The name of the region|
|tld|Text|The top level domain of the region, if applicable.|
|isoCountryCode|TEXT|[ISO 3166][iso3166] Country Code|

## Methods

### Get All Regions

Returns a collection of region objects representing the regions contained in the platform.

<pre>GET /v3/regions</pre>

**Parameters**

|Name    |Type    |Description|
|--------|--------|-----------|
|limit   |(Optional) Integer |Number of results to return, default 1000|
|offset  |(Optional) Integer |Index of the result to start with|

**Response**
```
{
  "data": [
    {
      "id": "46",
      "title": "China",
      "regionTLD": ".cn",
      "isoCountryCode": "CN"
    },
    {
      "id": "78",
      "title": "French Southern Territories",
      "regionTLD": ".tf",
      "isoCountryCode": "TF"
    },
    {
      "id": "158",
      "title": "New Caledonia",
      "regionTLD": ".nc",
      "isoCountryCode": "NC"
    },
    ...
  ],
  "meta": {
    "totalCount": 249
  }
}
```
### Get A Specific Region

Return a specific region.

<pre>GET /v3/regions/{regionId}</pre>

No Valid Query Parameters.

**Response**
```
{
  "data": [
    {
      "id": "46",
      "title": "China",
      "regionTLD": ".cn",
      "isoCountryCode": "CN"
    }
  ]
  "meta": {
    "totalCount": 1
  }
}
```

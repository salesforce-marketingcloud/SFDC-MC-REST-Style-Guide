# TopicFilter Service

## Get Topic Filters

Returns a collection of Keyword Query Topic Filters to which the requesting user has access.

<pre>GET /v3/topics</pre>

Requires "auth_token" and "auth_appkey" HTTP headers to be set on request.
List of topic filters returned depends on user's ORG ROLE (super user has ability to list all public topic profiles across client id) and whether topic profiles are shared with user via project membership.
Nate that this endpoint is not intended to fetch information on keyword groups and queries.

**Response**
```json
{
  "data": [
    {
      "id": "4",
      "title": "Scott Test 3",
      "creator": {
        "id": "3",
        "link": "https://stg2-dmz3.dev.ca1.sfmc.co/socialcloud/v2/users/3",
        "updated": null,
        "title": "scott.enman@radian6.com (clientId = 1)"
      },
      "visibility": "private",
      "status": 1,
      "emv": 0,
      "languages": [],
      "mediaTypes": [],
      "regions": [],
      "keywordGroups": [],
      "billingCode": "",
      "includeSourceFilters": [],
      "excludeSourceFilters": [],
      "includeAllSourceFilters": [],
      "createdDate": "2009-10-09T19:27:58Z"
    },
    …
  ],
  "meta" : {
       "totalCount" : 52
  }
}
```
**Valid query parameters**

* **workspaceGroupId**- only topic profiles that are found in the specified workspace group will be returned
* **limit** - controls number serialized topic profiles returned in 1 call, along with "offset" parameter allows to implement pagination of topic profiles if required. Default limit is set to 50000.
* **offset** - says to skip that many topic filters before beginning to return topics, along with "limit" parameter allows to implement pagination of topic profiles if required. Default offset is set to 0.
* **q** - allows to search by topic filter's title, at least 3 chars required to make a valid query
* **includeQuickSearch** - whether include or ignore Quick Search topic profiles in response
* **orderBy** - enables sorting of topic filters. 
* **status** - list of valid integers that represent statuses of topic filter object. 
  * Permitted values are: 
    * 0 (Disabled)
    * 1 (Purchased or Active)
    * 2 (Trial)
    * 3 (Expired)
    * 4 (Draft)
    
*Only topic filters that have status listed in a "status" parameter will be returned*

**Available values**

1. title
2. visibility
3. status
4. creator (topics will be ordered alphabetically by creator's display name)
5. creation (topics will be ordered by topic's creation date)

**Directions of sorting**

1. ASC (default value, if not specified)
2. DESC

## Get Topic Filter

Returns a Topic Filter as specified by the topic filter id in the request if user is permitted to view that TP.

<pre>GET /v3/topics/{topicId}</pre>

Response has exactly the same format as noted above.

## Create Topic Filter

To create a new keyword query topic filter, you need to post the name, languages, mediaTypes, and regions in the request. To include all langugages, mediaTypes, and regions, you can leave out the field or use "all".
If there is a need to create a DRAFT topic profile, proper status value (4) has to be provided. For DRAFT topic profiles default keyword group won't be created.

<pre>POST v3/topics</pre>
```json
{
  "title": "test topic profile",
  "languages": [
    "1"
  ],
  "mediaTypes": [
    {
      "id": "5"
    },
    {
      "id": "1"
    },
    {
      "id": "10"
    }
  ]
}
```
**Response**

* **201** On success, and Location response header that contains URL to the new topic filter resource.
* **400** If the json is malformed, or ids are invalid (includes unauthorized mediatypes).
* **403** If tp creation is not permitted by their org role.
* **500** If an unrecoverable error happened on the server.

## Activate Topic Filter

Activates the given topic filter.

<pre>POST /v3/topics/{topicId}?action=activate&billingCode={billingCode}</pre>

**Response**

* **204** If the tp was successfully activated.
* **404** If the tp did not exist or if the user was not permitted to delete the tp.
* **500** If an unrecoverable error happened on the server.

## Delete Topic Filter

Deactivate the topic filter with the given id.

<pre>DELETE /v3/topics/{topicId}</pre>

**Response**

* **204** If the tp was successfull deleted.
* **404** If the tp did not exist or if the user was not permitted to delete the tp.
* **500** If an unrecoverable error happened on the server.

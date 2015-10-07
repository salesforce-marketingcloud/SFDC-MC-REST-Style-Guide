# Keyword Group Service

## Get Keyword Group

Returns single Keyword Group to which the requesting user has access.
Response contains full information regarding filter queries requested keyword group contains.

<pre>GET /v3/topics/keywordGroups/{id}</pre>

Only active keyword groups will be returned. If given KG was deleted(disabled/deactivated) HTTP status code 404 (NOT FOUND) will be returned with message: "Keyword group not found or is inactive".
JSON response contains 3 sections: "contains", "andContains" and "excludes". See sample response below.

**"contains"** section is the main section that hosts complex filter query with opertators AND, -(NOT).


**"andContains"** section is auxilliary block that may contain extra keywords user would like to include in the search in addition to the ones that were specified in the "contains" section. Operators are not permitted here. 

**"excludes"** section is auxilliary block to "contains" section. It lists extra keywords that user specifies to exclude documents that have those keywords in the body of the content. Operators are not permitted here. 

Data in sections is organized in a JSON array of KeywordQueryTokens.
Each token has its type. KeywordQueryTokens types are: KEYWORD_OR_PHRASE(0), PROXIMITY(1), AND(2), NOT(3).

KeywordQueryTokens-operators (AND and NOT) are permitted to have only "tokenType" attribute.

KEYWORD_OR_PHRASE token must have 2 attributes: "tokenType" and "value". Where value is a keyword or phrase user searches for.

PROXIMITY token must have 3 attributes: "tokenType", "value" and "meta". "value" attribute has same meaning as for KEYWORD_OR_PHRASE keyword query token. "meta" must be a valid number in the range [-1:20].
"meta" attribute is an actual proximity value.

For instance, following json snippet:
```json
    {
        "tokenType": 1,
        "value": "Coca Cola",
        "meta": "2"
    }
```
will be converted into filter query: "Coca Cola"~2


**Response**
```json
{
  "data" : [
    {
      "title" : "Beverages",
      "type" : 2,
      "active" : true,
      "contains" : [
        [
          {
            "tokenType": 1,
            "value": "Coca Cola",
            "meta": "2"
          },
          {
            "tokenType": 2
          },
          {
            "tokenType": 0,
            "value": "Fanta"
          }
        ],
        [
          {
            "tokenType": 1,
            "value": "Dr. Pepper",
            "meta": "3"
          },
          {
            "tokenType": 3
          },
          {
            "tokenType": 0,
            "value": "Pepsi"
          }
        ]
      ],
      "excludes": [
        {
          "tokenType": 0,
          "value": "7 Up"
        }
      ],
      "andContains": [
        {
          "tokenType": 0,
          "value": "Sprite"
        },
        {
          "tokenType": 0,
          "value": "Smart water"
        }
      ]
    }
  ],
  "meta" : {
      "totalCount" : 1
  }
}
```
"contains" section here consists of 2 elements(filter queries) and can be transformed into sphinx-friendly syntax as. Filter queries of type 0:
1. "Coca Cola"~2 AND "Fanta"
2. "Dr. Pepper"~3 -"Pepsi"

"excludes" section will generated 1 filter query of type 1:
"7 Up"

"andContains will produce 2 filter queries of type 2:
1. "Sprite"
2. "Smart water"

## Create new Keyword Group

Creates new keyword group without being associated with a topic filter.

<pre>POST /v3/keywordGroups</pre>

Keyword group will be created only if filter group JSON payload was successfully validated by the server.
What means that filter group name is not an empty string, keyword query sequence consists of valid tokens and is syntactically correct.
For an example of possible json payload, see json document above.

Part of the validation process is also verification of following:

1. keywords must not be amongst predefined list of stop words, which at the moment of writing this doc is:
a,b,c,d,e,f,g,h,i,j,k,l,m,n,o,p,q,r,s,t,u,v,w,x,y,z,1,2,3,4,5,6,7,8,9,0,!,@,#,$,^,*,(,),am,an,as,at,be,by,do,go,he,if,in,is,it,me,my,no,of,on,or,so,to,up,us,we,and,the,for,not,she,was,test
2. keywords must not contain invalid characters: ^"<>|*\\n
3. number of exclude keywords must not exceed max number per client. Determined by client attribute 46 (MAX_NOT_KEYWORDS_PER_KEYWORD_GROUP).
4. Overall number of keywords and phrases for the topic filter must not exceed max number per client. Determined by client attribute 45 (MAX_KEYWORDS_PER_TOPIC).

**Response**

* **201** On successful creation.
* **400** If json document is malformed.
* **500** If an unrecoverable error happened on the server.

## Update Keyword Group

Updates keyword group with the given id.

<pre>PUT /v3/keywordGroups/{id}</pre>

Keyword group will be updated only if filter group JSON payload was successfully validated by the server.
For list of verifications see POST method call.
In order to update particular keyword group user has to have permission to do so.

**Response**

* **200** On successful update.
* **400** If json document is malformed.
* **403** If user is not authorized to update keyword group.
* **500** If an unrecoverable error happened on the server.

## Assign Keyword Groups to a Topic Filter

<pre>PUT /v3/topics/{id}/keywordGroups</pre>

Keyword group will be updated only if filter group JSON payload was successfully validated by the server.

User has to have edit permission for a given topic profile id.

As a json payload has to be array of the KeywordGroupResources. However, attribute "id" is enough to perform assignment of a keyword group to the topic filter.
JSON payload example for this call:
```json
[
    {
        "id":"327898"
    },
    {
        "id":"327899"
    }
]
```

**Response**

* **200** On successful assignment.
* **403** If user is not authorized to perform action on either topic filter or keyword group.
* **404** If keyword group or topic filter with given id was not found.
* **500** If an unrecoverable error happened on the server.

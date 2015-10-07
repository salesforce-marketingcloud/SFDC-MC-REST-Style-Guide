# Social Property Service

## Get Social Properties

Returns a list of all Social Properties to which the user has access.  A social property is a new object that contains information about a Managed Account Topic Profile and its associated External User Account.

<pre>GET /v3/socialProperties</pre>

The status element indicates whether the social property is valid and currently crawled for new posts.   Values are:

status | state | description
------ | ----- | -----------
0 | NEW | The social property has been created but has not yet been crawled for new posts
1 | OK | The social property is actively crawled for posts
2 | INVALID | The social property was found to be invalid; that is, 5 consecutive crawling attempts failed.  The social property will require reconnection.

**Valid query parameters**

* **_view_** - Comma-separated list of different subsets of properties to be returned. When not specified, defaults to OWNED; only those properties created by the requesting user.

<table>
  <tr>
    <td>owned</td>
    <td>All social properties created by the requesting user.</td>
  </tr>
  <tr>
    <td>shared</td>
    <td>All social properties that are shared with the requesting user and not owned. </td>
  </tr>
</table>


* **registrationId** - only social properties with the specified registrationId will be returned
* **types** - only social properties with the specified registration type(s) will be returned
* **socialNetworkId** - only social properties with the specified social network id (asset id) will be returned
* **workspaceGroupId** - only social properties that are found in the specified workspace group will be returned
* **limit** - positive integer for the number of results to return, default 25
* **offset** - positive integer for the result to start with as described by MySQL rules for offset
  * *For example, to return the first page of 10, limit = 10, offset = 0.  To return the second page of 10, limit = 10, offset = 10*
* **status** - comma separated list of integers, only social properties with the specified status(es) will be returned. 
* **invalidAccountsFirst** - boolean defaults to false.  When true, return *invalid* first then *OK*, then *new*.
* **orderBy** - Sort order.  Available fields to sort by are title, creator, visibility, and status
* **q** - search string query; must be 3 characters minimum.  Results will contain social properties that have a partial case insensitive match on either the topic's title or creator name.
* **ids** - comma-delimited list of ids to return. They must be valid social property ids, and they must be visible to the user; otherwise, 404 will be returned. Duplicate ids will be normalized. All other query parameters are ignored.

**Response**
```json
{
  "data": [
    {
         "id": "121218",
         "displayName": "Jane's test software",
         "pageUrl": "https://facebook.com/pages/Janes-test-softwar/128544450650919",
         "networkTypeId": 4,
         "avatarUrl": "https://fbcdn-profile-a.akamaihd.net/hprofile-ak-xaf1/v/t1.0-1/c151.34.422.422/s50x50/552488_128545593954160_2159838340_n.jpg?oh=cb08f9353545f1bb437f96022466a235&oe=563DA948&__gda__=1448168411_3a661fa573372b8fc3b31058feeb6c8c",
         "slug": "pages/Janes-test-software/128544450650919",
         "valid": true
      },
            {
         "id": "111819",
         "displayName": "I Heart CBC NB",
         "pageUrl": "https://facebook.com/pages/I-Love-CBC-NB/376189652448432",
         "networkTypeId": 4,
         "avatarUrl": "https://fbcdn-profile-a.akamaihd.net/hprofile-ak-xfa1/v/t1.0-2/p50x50/305074_380514875349292_382752874_n.jpg?oh=02eb66e27be49c4d3a99576b4997493b&oe=5651FCB9&__gda__=1447361104_cbbb529d940f10185f2877dd306967de",
         "slug": "pages/I-Love-CBC-NB/376189652448432",
         "valid": true
      }
  ],
  "meta": {
    "totalCount": 2
  }
}
```
## Get Social Property

Returns a single Social Property based on the supplied social property in the request. 

*Note*: when requesting a single social property, the social property id is the same as the managed account topic filter id.

<pre>GET /v3/socialProperties/{socialPropertyId}</pre>

**Response**

See the sample response for get social properties above.

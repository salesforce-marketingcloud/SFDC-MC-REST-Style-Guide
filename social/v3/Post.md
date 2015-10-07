[topics]: http://example.com/  "Topic Profiles"
[socialaccounts]: SocialAccount.md  "Social Accounts"
[socialproperties]: SocialProperty.md  "Social Properties"
[restrictions]: http://example.com/ "Optional Title here"
[mediaprovider]: http://example.com/ "Optional Title here"
[source]: http://example.com/ "Optional Title here"
[author]: Authors.md "Blog Post Authors"
[parent]: http://example.com/ "Optional Title here"
[workflow]: Workflow.md "Workflow Events"
[postdynamics]: http://example.com/ "Optional Title here"
[metadata]: http://example.com/ "Optional Title here"
[sourceFilterMatches]: http://example.com/ "Optional Title here"
[languages]: Language.md "Languages"
[regions]: Region.md "Regions"
[poststatuses]: http://example.com/ "Optional Title here"
[timestamps]: http://example.com/ "Optional Title here"
[dynamics]: http://example.com/ "Optional Title here"
[collectionoptions]: http://example.com/ "Optional Title here"

# Post Service
The post service provides access to post data gathered using broad listening [topic profiles][topics] and [social accounts][socialaccounts]. As with most collections it provides [standard options][collectionoptions] for filtering, sorting and paging. There are also parameters to include additional data like [workflow actions][workflow] and [dynamics][dynamics].

A post collection must be filtered at least by a [social property][socialproperties] or [topic profile][topics] but the rest of the parameters are either optional or provide reasonable defaults when not included. These are described in the reference sections below.

**Get posts from a topic profile: **

<pre>GET https://mcsapi.radian6.com/v3/posts?topics={ID}</pre>

Where {ID} is the unique identifier of any [topic profiles][topics] or [social property][socialproperties] that you have available. This will return a collection of post objects, which are described below.
</pre>

## The Post Object
The post service contains methods for interacting with collections of post objects. A post object is defined by it's attributes as well as its contained objects and collections.

### Attributes

|Attribute|Type|Description|
|---------|----|-----------|
|id|Numeric|The unique identifier of the post object. Currently user to assign and access workflow associated with the post.|
|link|URI|Unique address of the post itself. *Note that this link does not currently look up an individual post and should only be used as an identifier.* This link can, however, be used with /workflow added in order to return workflow values for a specific post.|
|updated|[Timestamp][timestamps]|The date and time when the post was created on the social media channel. This date will not change.|
|title|Text|A short description of the post.|
|externalId|Numeric|Where applicable, the unique identifier of the post as given to it by its original social media channel (IE. in the case of a Facebook post this would be the unique identifier, on Facebook, of the post).|
|externalLink|URI|A link to the post at its origin.|
|sourceApplication|Text|The name of the application that created the post. Currently only has a value for twitter content.|
|content|Text|The text content of the post itself (what was written). This includes links added to the post. Please see the list of [post content restrictions][restrictions] in order to understand what content is provided via the API.|
|summaryContent|Text|For internal use only, will always be empty.|
|harvestDate|[Timestamp][timestamps]|The timestamp describing when the Marketing Cloud Social platform discovered and indexed this post.|
|languageId|Numeric|The identifier for the automatically determined language of the post. See [languages][languages] for information about the supported languages that may be returned.|
|regionId|Numeric|The identifier for the automatically determined region from which the post originated. See [regions][regions] for information about the supported regions that may be returned.|
|[postStatus][poststatuses]|Numeric|Post status can be one of three possible values: <ul><li>0: Normal Active Post</li><li>1: SPAM (auto-detected)</li><li>2: Deleted/Hidden Post (user action)</li></ul>|

### Workflow related attributes (requires includeWorkflow=true)

|Attribute|Type|Description|
|---------|----|-----------|
|sentiment|Numeric|Manually assigned sentiment value|
|postStatusException|Numeric|Manually assigned post status|
|engagement|Numeric|Identifier for the state of the post (ie: under review or closed)|
|classification|Numeric|Identifier for the classification group assigned to the post|
|assignedUser|Numeric|Idenitifier for the user this post has been assigned to|
|priority|Numeric|Priority value assigned to this post|
|tags|Array|A collection of post tags assigned to this post|
|comments|Array|A collection of post comments assigned to this post|

### Objects

|Object|Description|
|------|-----------|
|[mediaProvider][mediaprovider]|Detailed information about where the post came from and what kind of post it is within that original social network (post, comment, private message, etc). For more information see [mediaproviders][mediaprovider].|
|[source][source]|Information about the source of the post (the page, or feed from which it came). This is an internal object representing that source on The Marketing Cloud Social platform. For example, multiple posts made to the same Facebook page will result and the same source being associated with them. <b>Note</b> that the source object for Twitter posts has an additional verified element that does not appear in sources of posts from other media providers. This verified element contains a boolean value indicating if the post is Twitter verified. See [sources][source] for more information. **NOTE: source tags and comments will only be returned if the includeWorkflow query parameter is set to true**|
|[author][author]|Information about the individual (or company) that created the post, including social network profile information where available. See [authors][author] for more information. **NOTE: author tags and comments will only be returned if the includeWorkflow query parameter is set to true**|
|[parent][parent]|The internal (Marketing Cloud Platform) and external (social media channel) IDs of the parent post. A parent post can be an original Tweet in the case of a reply/retweet or in the case of a social media page it generally represents the original post to which a comment was made.|

### Collections

|Collection|Description|
|----------|-----------|
|[postDynamics][postdynamics]|Within Marketing Cloud Social, dyanmics are information that changes over time with regards to posts, authors and sources. Post dynamics are those dynamics about the post itself (for example, the number of comments on the the post). Dynamics are social media channel (social network) specific, for more inforamtion see [dynamics][dynamics]|
|[metadata][metadata]|A collection of additional meta-data items about the post. Most often this contains links, URLs to images and other media related to the post.|
|[topics][topics]|A collection of topics to which this post has been assigned. This will be limited to only topics that were used when making the request. See the 'topics' request parameter in the GET method(s) below.|
|[sourceFilterMatches][sourceFilterMatches]|A collection of source filters containing the urls to which the post matches.|
|[parentAuthor][author]|A single author object for the author of the post which is the parent of the current post.  This is currently only used for Twitter posts which are retweets; the author of the current post is the retweeter and the parentAuthor is the tweeter.|


### Sample Post Object

```json
{
  "id": "1828477438",
  "title": "Roger Federer visit Malawi - Federer Foundatio'",
  "externalId": null,
  "externalLink": "http://www.youtube.com/watch?v=nOw9cDh83Ks",
  "sourceApplication": null,
  "content": "",
  "summaryContent": "",
  "publishedDate": "2015-07-21T10:07:58Z",
  "harvestDate": "2015-07-21T11:23:23Z",
  "languageId": 1,
  "regionId": 249,
  "postStatus": 0,
  "mediaProvider": {
    "id": "2",
    "title": "YouTube",
    "mediaTypeId": 2,
    "extendedMediaType": null
  },
  "source": {
    "id": "2229562169",
    "title": "Roger Federer visit Malawi - Federer Foundatio'",
    "externalLink": "youtube.com",
    "language": "en",
    "region": null,
    "tags": [
      {
        "id": "7494",
        "value": "Sample source tag"
      }
    ],
    "comments": [
      {
        "id": "56436",
        "value": "Sample source comment",
        "noteTypeId": "4"
      }
    ]
  },
  "author": {
    "id": "-1",
    "title": "YouTube Channel",
    "externalId": "-1",
    "externalLegacyId": null,
    "authorFullName": "YouTube Channel",
    "avatar": null,
    "authorTags": [
      {
        "id": "5567567",
        "value": "Sample author tag"
      }
    ],
    "authorComments": [
      {
        "id": "556456456",
        "value": "Sample author comment"
      }
    ],
    "bio": null,
    "verified": false
  },
  "parent": null,
  "sentiment": null,
  "postStatusException": null,
  "engagement": 4564,
  "classification": 4,
  "tags": [
    {
      "id": "5466546",
      "value": "Sample tag"
    }
  ],
  "postDynamics": [
    {
      "fieldId": "5",
      "label": "View Count",
      "value": "0"
    },
    {
      "fieldId": "1",
      "label": "Comment Count",
      "value": "0"
    },
    {
      "fieldId": "2",
      "label": "Unique Commenters",
      "value": "0"
    },
    {
      "fieldId": "3",
      "label": "Engagement",
      "value": "0"
    },
    {
      "fieldId": "4",
      "label": "Likes and Votes",
      "value": "0"
    },
    {
      "fieldId": "7",
      "label": "Inbound Links",
      "value": "0"
    },
    {
      "fieldId": "21",
      "label": "Sentiment",
      "value": "0",
      "overridden": "false"
    }
  ],
  "metadata": null,
  "topics": [
    4526
  ],
    "sourceFilterMatches": null,
    "assignedUser": null,
    "comments": [
      {
        "id": "435345",
        "value": "Sample comment",
        "noteTypeId": "0"
      }
    ]
  }
}
```

## Methods

### Get Posts

Returns a collection of matching posts for the given filters.

<pre>GET /v3/posts</pre>

**Valid Query Parameters**

|Parameter|Description|Required/Default|
|---------|-----------|----------------|
|topics|A comma delimited list of the IDs of the topic profiles to include in the method call.|Required|
|limit|Limits the number of posts to be returned by this call, can be used with the offset parameter to incorporate paging. Accepts values between 1 and 1000.|Default is 25 posts.|
|sinceId|Used with limit to implement paging. Note that this is not an ordinal value within the collection but instead is the ID of the post itself. Including this parameter ensures that you get only posts with a higher ID than the value supplied.|Optional|
|beforeId|Used to include only posts with IDs up to and including the supplied ID. Posts with an ID higher than the supplied value will not be returned.|Optional|
|startDate|Filter the resulting collection by those posts that were published on the social media channel on or after the given date. This relates to the 'updated' attribute of the posts in the collection.|Default is 24 hours before the time the call was made.|
|endDate|Filter the resulting collection by those posts that were published on the social media channel on or before the given date. This relates to the 'updated' attribute of the posts in the collection.|Default is  the current time.|
|mediaTypes|A comma delimited list of the mediatype IDs of posts to be returned, can be used to filter only certain types of posts in a given method call (Twitter, Facebook, etc). For a list of valid media types see [Media Providers][mediaprovider]|Default is all applicable media types. Since topic profiles can be restricted to certain media types as well, this will be restricted to returning only posts from media types configured in the topic profiles supplied.|
|extendedMediaTypes|A comma delimited list of extended media type IDs. These are specific to each media type and a list of them can be found in [Media Providers][mediaprovider].|Default is all applicable extended media types.|
|includeWorkflow|A boolean values used to indicate whether or not to include workflow with the  returned posts. See [Workflow][workflow] for more information.|Default is false.
|includeSpam|A Boolean value indicating whether or not to include posts that were flagged as spam by Marketing Cloud platform.|Default is false.|
|includeAnchors|A boolean value used to indicate whether anchor tags should be included in content. When set to true, links within post content will be interpreted and presented as HTML anchor tags. Otherwise links will be included in the post content as plain text.|Default is plain text links.|
|highlight|A boolean value indicating whether or not to highlight the keywords. When set to true keywords (from the topic profile configuration) in the post content will be decorated with HTML style information so that they will be highlighted when presented on a web page.|Default is no highlighting/decoration for keywords.|
|keywords|Used to filter posts, returning only posts that contain one of the keywords. Up to 50 keywords supported in a comma-delimited list. Duplicates are ignored.|Optional|
|sortBy|Allows sorting the resulting collection, valid values are:<ul><li>publishedDate</li><li>publishedDate-ascending</li><li>blogPostId</li><li>blogPostId-ascending</li><li>rankBM25</li></ul>blogPostID is the ID attribute from the post object.<br> rankBM25 is a relevance ranking based on the frequency of the matched keywords.|Optional. Default is to sort by ID in descending order.|
|keywordGroups|A comma delimited list of filter group ids used to filter posts only belonging to those groups, See [Topics][topics] for information about getting keyword group ids for this parameter.|Default is all keyword groups in the topic profiles provided in the 'topics' parameter.|
|includeSourceFilterMatches|A boolean value used to indicate whether or not to include the source filters containing the urls each post matches.|Default is false|
|regions|A comma-delimited list of region ids|Default is to return all regions assigned to the topic|
|languages|A comma-delimited list of language ids|Default is to return all languages assigned to the topic|


***Workflow filters***
<p>In addition to the above list of parameters, there are a series of parameters available to filter the post collection by the workflow associated with the posts. Below is a list of these parameters.</p>

|Query Parameter|Description|
|---------------|-----------|
|assignedUser|Takes a comma-delimited list of userIds of users within the same client as the calling user. Returns only posts assigned to the specific user.|
|engagement|Returns all posts that have any of the engagement status' specified (comma-delimited list of ids). Engagement is described in [Workflow][workflow].|
|priority|Filters by posts with 1 or more priorties. Valid values are 1,2,3 (low, medium, high)|
|classification|Returns all posts that have any of the specified classification settings (comma-delimited list). Post classification is described in [Workflow][workflow].|
|eventsSince|Milliseconds since epoch (midnight 1/1/1970). Return posts that have had workflow events applied since the timestamp provided.  To facilitate this filter, the response to this call adds an HTTP header named Workflow-As-Of-Date that provides the current timestamp of the call.  By feeding the value of Workflow-As-Of-Date into a subsequent call’s eventsSince parameter, you can get only new workflow events that have been applied since a previous method call. As an optimization, this call will only consider workflow applied in the last 30 days.|
|unassigned|Boolean value to indicate if only unassigned post should be returned|
|sentiment|A comma-delimited list of sentiment ids to filter the posts by.Valid values are (from negative to positive) -10,-5,0,5,10,99.|
|authorTags|A comma-delimited list of author tags|

#### Errors
|Status Code|Reason|
|-----------|------|
|400		|If unassigned filter combined with assignedUser filter|
|400    |If unassigned filter combined with either a startDate or eventsSince filter where the date exceeds 120 days in the past|
|400    |If includeSourceFilterMatches combined with more than 1 topic filter|

#### Response Headers
|Header|Description|
|------|-----------|
|X-R6-Max-BlogPostId|Max blog post id currenlty in the system|

#### Examples

##### Get all posts matching topic profile #1234 for the last 24 hours.

<pre>https://mcsapi.radian6.com/v2/posts?topics=1234</pre>

#####All posts from a topic profile in the last 24 hours
The following request gets the most recent 25 posts from topic profile ID=1234 in reverse chronological order (newest post first):
<pre>https://mcsapi.radian6.com/v3/posts?topics=1234</pre>

##### Just Twitter, but get 50 of them
We'll now alter this request to get only twitter posts for the same time period; this time, however, we want to get the latest 50 posts. (we can go as high as 1000 here):
<pre>https://mcsapi.radian6.com/v3/posts?topics=1234&mediaTypes=8&limit=50</pre>

##### ...and now the next page
This time we want the second page of these results. We've taken the last result of the previous call and extracted the ID from that post. We'll now use this value to tell the request that we only want to see posts 'older' (with a smaller ID) than the last one we saw in the first request. The ID of the last post in our request above was: 777777
<pre>https://mcsapi.radian6.com/v3/posts?topics=1234&mediaTypes=8&limit=50&maxOffset=777777</pre>

### Get Post Workflow

Each post has a history of workflow that has been applied to it; in order to get the entire history (IE the post lifecycle) use this method with a post you've gotten from the above method. This method is useful to extract performance indicators in situations where you are treating social media posts as actionable support or community events.

For more information on the type of workflow objects in the resulting collection please see [workflow][workflow].

<pre>GET /v3/posts/{postId}/workflow</pre>

**There are no valid query parameters**

**Response**
```json
{
    "data": [
        {
            "id": "1828477438",
            "title": "Roger Federer visit Malawi - Federer Foundatio'",
            "externalId": null,
            "externalLink": "http://www.youtube.com/watch?v=nOw9cDh83Ks",
            "sourceApplication": null,
            "content": "",
            "summaryContent": "",
            "publishedDate": "2015-07-21T10:07:58Z",
            "harvestDate": "2015-07-21T11:23:23Z",
            "languageId": 1,
            "language": "en",
            "regionId": 249,
            "region": null,
            "postStatus": 0,
            "mediaProvider": {
                "id": "2",
                "title": "YouTube",
                "mediaTypeId": 2,
                "extendedMediaType": null
            },
            "source": {
                "id": "2229562169",
                "title": "Roger Federer visit Malawi - Federer Foundatio'",
                "externalLink": "youtube.com",
                "language": "en",
                "region": null
            },
            "author": {
                "id": "-1",
                "title": "YouTube Channel",
                "externalId": "-1",
                "externalLegacyId": null,
                "authorFullName": "YouTube Channel",
                "avatar": null,
                "tags": null,
                "bio": null,
                "verified": false
            },
            "parent": null,
            "sentiment": [
                {
                    "topicId": 4526,
                    "value": 0,
                    "overridden": false
                }
            ],
            "workflow": [],
            "postDynamics": [
                {
                    "fieldId": "5",
                    "label": "View Count",
                    "value": "0"
                },
                {
                    "fieldId": "1",
                    "label": "Comment Count",
                    "value": "0"
                },
                {
                    "fieldId": "2",
                    "label": "Unique Commenters",
                    "value": "0"
                },
                {
                    "fieldId": "3",
                    "label": "Engagement",
                    "value": "0"
                },
                {
                    "fieldId": "4",
                    "label": "Likes and Votes",
                    "value": "0"
                },
                {
                    "fieldId": "7",
                    "label": "Inbound Links",
                    "value": "0"
                },
                {
                    "fieldId": "21",
                    "label": "Sentiment",
                    "value": "0",
                    "overridden": "false"
                }
            ],
            "metadata": null,
            "topics": [
                4526
            ],
            "sourceFilterMatches": null
        },
        {
            "id": "1828401832",
            "title": "Jeremy Chardy v Paul Henri-Mathieu skistar swedish open 2015",
            "externalId": null,
            "externalLink": "http://www.youtube.com/watch?v=U1pEVzAOfFc",
            "sourceApplication": null,
            "content": “Soccer player scores goal,
            "summaryContent": "Great kick!",
            "publishedDate": "2015-07-21T09:47:04Z",
            "harvestDate": "2015-07-21T11:15:14Z",
            "languageId": 1,
            "language": "en",
            "regionId": 249,
            "region": null,
            "postStatus": 0,
            "mediaProvider": {
                "id": "2",
                "title": "YouTube",
                "mediaTypeId": 2,
                "extendedMediaType": null
            },
            "source": {
                "id": "2229560153",
                "title": "Jeremy Chardy v Paul Henri-Mathieu skistar swedish open 2015",
                "externalLink": "youtube.com",
                "language": "en",
                "region": null
            },
            "author": {
                "id": "-1",
                "title": "swedish open 2015",
                "externalId": "-1",
                "externalLegacyId": null,
                "authorFullName": "swedish open 2015",
                "avatar": null,
                "tags": null,
                "bio": null,
                "verified": false
            },
            "parent": null,
            "sentiment": [
                {
                    "topicId": 4526,
                    "value": 10,
                    "overridden": false
                }
            ],
            "workflow": [],
            "postDynamics": [
                {
                    "fieldId": "5",
                    "label": "View Count",
                    "value": "0"
                },
                {
                    "fieldId": "1",
                    "label": "Comment Count",
                    "value": "0"
                },
                {
                    "fieldId": "2",
                    "label": "Unique Commenters",
                    "value": "0"
                },
                {
                    "fieldId": "3",
                    "label": "Engagement",
                    "value": "0"
                },
                {
                    "fieldId": "4",
                    "label": "Likes and Votes",
                    "value": "0"
                },
                {
                    "fieldId": "7",
                    "label": "Inbound Links",
                    "value": "0"
                },
                {
                    "fieldId": "21",
                    "label": "Sentiment",
                    "value": "10",
                    "overridden": "false"
                }
            ],
            "metadata": null,
            "topics": [
                4526
            ],
            "sourceFilterMatches": null
        }
    ],
    "meta": {
        "totalCount": 2
    }
}
```

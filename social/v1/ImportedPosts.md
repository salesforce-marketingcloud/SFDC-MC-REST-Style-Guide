# Imported Posts API Documentation
---
## Table of Contents
---
1. [Imported Post Object](#imported_post_object)

	a. [Imported Post Object Properties](#imported_post_object_properties)
	
	b. [Imported Post Object Actions](#imported_post_object_actions)
	
2. [Imported Post Metadata Object](#imported_post_metadata_object)

	a. [Imported Post Metadata Object Properties](#imported_post_metadata_object_properties)
	
3. [Imported Post Service](#imported_post_service)
		
## <a name="imported_post_object"></a>Imported Post Object
---
An imported post is a piece of content created by the social account on any of the network that were imported into the platform.

### <a name="imported_post_object_properties"></a>Properties

***id:*** string - the unique ID of the imported post.

***created:*** string - the timestamp of when the imported post was created. Important: this timestamp refers to when the post was created on the network, NOT when it was created on the platform.

***deleted:*** boolean - whether or not the imported post is deleted.

***external_post_id:*** string - the external post ID for this imported post created on this social account. For example, if the social account is a Facebook account, the external post ID will be the Facebook post ID for this post.

***media_type:*** int - a number representing the type of media type of the imported post.

1. MEDIA_TYPE_ALBUM = 1
2. MEDIA_TYPE_LINK = 2
3. MEDIA_TYPE_PHOTO = 3
4. MEDIA_TYPE_STATUS = 4
5. MEDIA_TYPE_VIDEO = 5

***message:*** string - the content of the imported post.

***metadata:*** array - an array of [Imported Post Metadata Object](#imported_post_metadata_object).

***network_id:*** int - a number representing the network the social account is associated with.

1. NETWORK_FACEBOOK = 1
2. NETWORK_TWITTER = 2
3. NETWORK_PLUS = 3
4. NETWORK_LINKEDIN = 4

***networkasset_id:*** string - The ID of the social account.

***type:*** int - a number representing the type of the imported post.

1. TYPE_FACEBOOK_POST = 1
2. TYPE_FACEBOOK_COMMENT = 2
3. TYPE_FACEBOOK_COMMENT_REPLY = 3
4. TYPE_FACEBOOK_LIKE = 4
5. TYPE_TWITTER_TWEET = 100
6. TYPE_TWITTER_RETWEET = 101
7. TYPE_TWITTER_REPLY = 102
8. TYPE_TWITTER_FAVORITE = 103

***updated:*** string - the timestamp of when the imported post was last updated.

## <a name="imported_post_metadata_object"></a>Imported Post Metadata Object
---
An imported post metadata is a key-value representing any arbitrary data associated with the imported post provided by either the platform, the network, or third-party providers.

### <a name="imported_post_metadata_object_properties"></a>Properties

***id:*** int - the unique ID of the metadata.

***created:*** string - the timestamp when the metadata was created.

***deleted:*** boolean - whether or not the metadata was deleted.

***imported_id:*** string - the ID of the imported post this metadata is associated with.

***key:*** string - what the metadata is.

***provider:*** object - the Provider Object which contains information about who provided the metadata information.

***updated:*** string - the timestamp when the metadata was last updated.

***value:*** string - the value of the metadata.

## <a name="imported_post_service"></a>Imported Post Service

Below are the posts service endpoints.

| Method                             | Action | Endpoint           | Description                                                          |
| -----------------                  | ------ | ------------------ | -------------------------------------------------------------------- |
| [getCollection](#a_endpoint_posts) | GET    | /importedposts             | Returns a list of Posts in a workspace.                   |
                                         
<a name="a_endpoint_posts"></a>
####getCollection Endpoint
------------

#####Parameters

none.

#####Query String Parameters

| Parameter      | Description                                               | Type      | Example                                        | Default                                     | Required |
| -------------- | --------------------------------------------------------- | --------- | ---------------------------------------------- | ------------------------------------------- | -------- |
| workGroupId    | Work Group Id                                             | Int       | 1234                                           | --                                          | Yes      |
| workspace      | Workspace Id (to be deprecated in 198)                    | String    | 97b5e8b2-03f1-4dcc-9727-b5b68c0d27f0           | --                                          | No       |
| startDate      | Date to retrieve posts from (in user time zone)*          | Timestamp | 1428120000                                     | --                                          | Yes      |
| endDate        | Date to retrieve posts up to (in user time zone)*         | Timestamp | 1428724799                                     | --                                          | Yes      |
| network        | Social Network                                            | String    | [see supported networks](#a_networks)          | all                                         | No       | 
| socialProperty | Social properties to retrieve post from (comma delimited) | String    | 1234,1234                                      | All social properties included in workspace | No       |
| socialAsset    | Social Account Set id                                     | String    | 97b5e8b2-03f1-4dcc-9727-b5b68c0d27f0           | All social accounts included in workspace   | No       | 
| metrics        | Metrics required in the response (comma delimited)        | String    | [see supported metrics](#a_metrics)            | None                                        | No       |
| labels         | Filter by labels (comma delimited)                        | String    | abc,testing                                    | None                                        | No       |
| type           | Filter by media type                                      | String    | status                                         | None                                        | No       |
| orderBy        | Sort Parameter                                            | String    | date or any metric                             | date                                        | No       |
| order          | Order in which the posts are sorted                       | String    | ASC or DESC                                    | DESC                                        | No       |
| page           | Pagination parameter                                      | Integer   | 1                                              | 1                                           | No       |
| perpage        | Results to be shown per page                              | Integer   | 10                                             | 10                                          | No       |
| filter         | Publish status of the post                                | String    | unpublished                                    | --                                          | No       |

***\*Timezone that the user has saved in the application settings and NOT the browser time***

#####Request 1: *Passing just the workspace id in the url*

    $ curl http://cmapi.YOUR_USERNAME.test.dev.us1.sfmc.co/v1/importedposts?workspace=1

#####Response 1: *list of posts from all networks in last 7 days sorted by date in descending order*

    {
        "status": true,
        "response": [
            {
                "id": "35db3b27-edd5-4d35-8ef7-574b0f483524",
                "socialAsset": "200012913364042",
                "socialProperty": 8307,
                "slug": "/Buddy-The-Dog/200012913364042",
                "assets": {
                    "id": 46,
                    "created": "2013-08-12T10:48:28-0400",
                    "metadata": "{\"link_type\":\"link\",\"title\":\"CRM and Cloud Computing To Grow Your Business - Salesforce.com\",\"caption\":\"http://Salesforce.com\",\"description\":\"You can change stuff here\",\"images\":[\"http://www.sfdcstatic.com/common/assets/img/logo-company.png\"],\"image_index\":0,\"thumbnail\":true,\"share_link\":false,\"url\":\"http://Salesforce.com\",\"video_src\":null,\"type\":\"link\"}",
                    "post_id": "35db3b27-edd5-4d35-8ef7-574b0f483524",
                    "type": 3,
                    "updated": "2013-08-12T10:48:28-0400",
                    "url": "http://Salesforce.com"
                },
                "date": "2013-08-13 09:45:03",
                "message": "Salesforce.com is cool!",
                "network": "facebook",
                "targeting": [ ],
                "labels" : [ ],
                "type": "status",
                "socialAssetName": "Buddy - The Dog",
                "blogPostId": 1234
            },
            {
                "id": "b93ab12e-37cd-4344-bffa-3a59f5053580",
                "socialAsset": "200012913364042",
                "socialProperty": 8307,
                "slug": "/Buddy-The-Dog/200012913364042",
                "assets": [ ],
                "date": "2013-08-12 15:29:40",
                "message": "yahoo.com",
                "network": "facebook",
                "targeting": [ ],
                "labels" : [ ],
                "type": "status",
                "socialAssetName": "Buddy - The Dog",
                "blogPostId": 1234
            },
            {
                "id": "9bc25322-49ca-4b9c-ae81-20ff1b593edf",
                "socialAsset": "200012913364042",
                "socialProperty": 8307,
                "slug": "/Buddy-The-Dog/200012913364042",
                "assets": {
                    "id": 47,
                    "created": "2013-08-12T10:48:29-0400",
                    "metadata": "{\"link_type\":\"link\",\"title\":\"CRM and Cloud Computing To Grow Your Business - Salesforce.com\",\"caption\":\"http://Salesforce.com\",\"description\":\"You can change stuff here\",\"images\":[\"http://www.sfdcstatic.com/common/assets/img/logo-company.png\"],\"image_index\":0,\"thumbnail\":true,\"share_link\":false,\"url\":\"http://Salesforce.com\",\"video_src\":null,\"type\":\"link\"}",
                    "post_id": "9bc25322-49ca-4b9c-ae81-20ff1b593edf",
                    "type": 3,
                    "updated": "2013-08-12T10:48:29-0400",
                    "url": "http://Salesforce.com"
                },
                "date": "2013-08-12 09:45:03",
                "message": "Salesforce.com is cool!",
                "network": "facebook",
                "targeting": [ ],
                "labels" : [ ],
                "type": "status",
                "socialAssetName": "Buddy - The Dog",
                "blogPostId": 1234
            }
            ...
        ],
        "meta": {
           "total": 6,
           "perpage": 10,
           "current": 1,
           "prev": "",
           "next": ""
        }
    }

#####Request 2: *Passing workspace id and network in the url*

    $ curl http://cmapi.YOUR_USERNAME.test.dev.us1.sfmc.co/v1/importedposts?workspace=1&network=facebook

#####Response 2: *list of posts from facebook in last 7 days sorted by date in descending order*

    {
        "status": true,
        "response": [
            {
                "id": "35db3b27-edd5-4d35-8ef7-574b0f483524",
                "socialAsset": "200012913364042",
                "socialProperty": 8307,
                "slug": "/Buddy-The-Dog/200012913364042",
                "assets": {
                    "id": 46,
                    "created": "2013-08-12T10:48:28-0400",
                    "metadata": "{\"link_type\":\"link\",\"title\":\"CRM and Cloud Computing To Grow Your Business - Salesforce.com\",\"caption\":\"http://Salesforce.com\",\"description\":\"You can change stuff here\",\"images\":[\"http://www.sfdcstatic.com/common/assets/img/logo-company.png\"],\"image_index\":0,\"thumbnail\":true,\"share_link\":false,\"url\":\"http://Salesforce.com\",\"video_src\":null,\"type\":\"link\"}",
                    "post_id": "35db3b27-edd5-4d35-8ef7-574b0f483524",
                    "type": 3,
                    "updated": "2013-08-12T10:48:28-0400",
                    "url": "http://Salesforce.com"
                },
                "date": "2013-08-13 09:45:03",
                "message": "Salesforce.com is cool!",
                "network": "facebook",
                "targeting": [ ],
                "labels" : [ ],
                "type": "status",
                "socialAssetName": "Buddy - The Dog",
                "blogPostId": 1234
            },
            {
                "id": "9bc25322-49ca-4b9c-ae81-20ff1b593edf",
                "socialAsset": "200012913364042",
                "socialProperty": 8307,
                "slug": "/Buddy-The-Dog/200012913364042",
                "assets": {
                    "id": 47,
                    "created": "2013-08-12 10:48:29",
                    "metadata": "{\"link_type\":\"link\",\"title\":\"CRM and Cloud Computing To Grow Your Business - Salesforce.com\",\"caption\":\"http://Salesforce.com\",\"description\":\"You can change stuff here\",\"images\":[\"http://www.sfdcstatic.com/common/assets/img/logo-company.png\"],\"image_index\":0,\"thumbnail\":true,\"share_link\":false,\"url\":\"http://Salesforce.com\",\"video_src\":null,\"type\":\"link\"}",
                    "post_id": "9bc25322-49ca-4b9c-ae81-20ff1b593edf",
                    "type": 3,
                    "updated": "2013-08-12T10:48:29-0400",
                    "url": "http://Salesforce.com"
                },
                "date": "2013-08-12 09:45:03",
                "message": "Salesforce.com is cool!",
                "network": "facebook",
                "targeting": [ ],
                "labels" : [ ],
                "type": "status",
                "socialAssetName": "Buddy - The Dog",
                "blogPostId": 1234
            }
            ...
        ],
        "meta": {
           "total": 16,
           "perpage": 10,
           "current": 1,
           "prev": "",
           "next": ""
        }
    }
    
#####Request 3: *Passing workspace id, network and metrics in the url*

    $ curl http://cmapi.YOUR_USERNAME.test.dev.us1.sfmc.co/v1/importedposts?workspace=1&network=facebook&metrics=likes,comments

#####Response 3: *list of posts from facebook in last 7 days sorted by date in descending order*

    {
        "status": true,
        "response": [
            {
                "id": "35db3b27-edd5-4d35-8ef7-574b0f483524",
                "socialAsset": "200012913364042",
                "socialProperty": 8307,
                "slug": "/Buddy-The-Dog/200012913364042",
                "assets": {
                    "id": 46,
                    "created": "2013-08-12T10:48:28-0400",
                    "metadata": "{\"link_type\":\"link\",\"title\":\"CRM and Cloud Computing To Grow Your Business - Salesforce.com\",\"caption\":\"http://Salesforce.com\",\"description\":\"You can change stuff here\",\"images\":[\"http://www.sfdcstatic.com/common/assets/img/logo-company.png\"],\"image_index\":0,\"thumbnail\":true,\"share_link\":false,\"url\":\"http://Salesforce.com\",\"video_src\":null,\"type\":\"link\"}",
                    "post_id": "35db3b27-edd5-4d35-8ef7-574b0f483524",
                    "type": 3,
                    "updated": "2013-08-12T10:48:28-0400",
                    "url": "http://Salesforce.com"
                },
                "date": "2013-08-13 09:45:03",
                "message": "Salesforce.com is cool!",
                "network": "facebook",
                "targeting": [ ],
                "labels" : [ ],
                "type": "status",
                "socialAssetName": "Buddy - The Dog",
                "blogPostId": 1234,
                "metrics": {
                    "likes": 300,
                    "comments": 400
                }
            },
            {
                "id": "9bc25322-49ca-4b9c-ae81-20ff1b593edf",
                "socialAsset": "200012913364042",
                "socialProperty": 8307,
                "slug": "/Buddy-The-Dog/200012913364042",
                "assets": {
                    "id": 47,
                    "created": "2013-08-12T10:48:29-0400",
                    "metadata": "{\"link_type\":\"link\",\"title\":\"CRM and Cloud Computing To Grow Your Business - Salesforce.com\",\"caption\":\"http://Salesforce.com\",\"description\":\"You can change stuff here\",\"images\":[\"http://www.sfdcstatic.com/common/assets/img/logo-company.png\"],\"image_index\":0,\"thumbnail\":true,\"share_link\":false,\"url\":\"http://Salesforce.com\",\"video_src\":null,\"type\":\"link\"}",
                    "post_id": "9bc25322-49ca-4b9c-ae81-20ff1b593edf",
                    "type": 3,
                    "updated": "2013-08-12T10:48:29-0400",
                    "url": "http://Salesforce.com"
                },
                "date": "2013-08-12 09:45:03",
                "message": "Salesforce.com is cool!",
                "network": "facebook",
                "targeting": [ ],
                "labels" : [ ],
                "type": "status",
                "socialAssetName": "Buddy - The Dog",
                "blogPostId": 1234,
                "metrics": {
                    "likes": 100,
                    "comments": 800
                }
            }
            ...
        ],
        "meta": {
           "total": 16,
           "perpage": 10,
           "current": 1,
           "prev": "",
           "next": ""
        }
    }

#####Request 4: *Passing workspace id, network and metrics in the url and sorting by metrics*

    $ curl http://cmapi.YOUR_USERNAME.test.dev.us1.sfmc.co/v1/importedposts?workspace=1&network=facebook&metrics=likes,comments&orderBy=comments

#####Response 4: *list of posts from facebook in last 7 days sorted by comments in descending order*

    {
        "status": true,
        "response": [
            {
                "id": "35db3b27-edd5-4d35-8ef7-574b0f483524",
                "socialAsset": "200012913364042",
                "socialProperty": 8307,
                "slug": "/Buddy-The-Dog/200012913364042",
                "assets": {
                    "id": 46,
                    "created": "2013-08-12T10:48:28-0400",
                    "metadata": "{\"link_type\":\"link\",\"title\":\"CRM and Cloud Computing To Grow Your Business - Salesforce.com\",\"caption\":\"http://Salesforce.com\",\"description\":\"You can change stuff here\",\"images\":[\"http://www.sfdcstatic.com/common/assets/img/logo-company.png\"],\"image_index\":0,\"thumbnail\":true,\"share_link\":false,\"url\":\"http://Salesforce.com\",\"video_src\":null,\"type\":\"link\"}",
                    "post_id": "35db3b27-edd5-4d35-8ef7-574b0f483524",
                    "type": 3,
                    "updated": "2013-08-12T10:48:28-0400",
                    "url": "http://Salesforce.com"
                },
                "date": "2013-08-13 09:45:03",
                "message": "Salesforce.com is cool!",
                "network": "facebook",
                "targeting": [ ],
                "labels" : [ ],
                "type": "status",
                "socialAssetName": "Buddy - The Dog",
                "blogPostId": 1234,
                "metrics": {
                    "likes": 300,
                    "comments": 800
                },
                "blogPostId": 1234
            },
            {
                "id": "9bc25322-49ca-4b9c-ae81-20ff1b593edf",
                "socialAsset": "200012913364042",
                "socialProperty": 8307,
                "slug": "/Buddy-The-Dog/200012913364042",
                "assets": {
                    "id": 47,
                    "created": "2013-08-12T10:48:29-0400",
                    "metadata": "{\"link_type\":\"link\",\"title\":\"CRM and Cloud Computing To Grow Your Business - Salesforce.com\",\"caption\":\"http://Salesforce.com\",\"description\":\"You can change stuff here\",\"images\":[\"http://www.sfdcstatic.com/common/assets/img/logo-company.png\"],\"image_index\":0,\"thumbnail\":true,\"share_link\":false,\"url\":\"http://Salesforce.com\",\"video_src\":null,\"type\":\"link\"}",
                    "post_id": "9bc25322-49ca-4b9c-ae81-20ff1b593edf",
                    "type": 3,
                    "updated": "2013-08-12T10:48:29-0400",
                    "url": "http://Salesforce.com"
                },
                "date": "2013-08-12 09:45:03",
                "message": "Salesforce.com is cool!",
                "network": "facebook",
                "targeting": [ ],
                "labels" : [ ],
                "type": "status",
                "socialAssetName": "Buddy - The Dog",
                "blogPostId": 1234,
                "metrics": {
                    "likes": 100,
                    "comments": 600
                }
            }
            ...
        ],
        "meta": {
           "total": 16,
           "perpage": 10,
           "current": 1,
           "prev": "",
           "next": ""
        }
    }

<a name="a_networks"></a>
####Supported Networks
------------

| Network  |
| -------- |
| facebook |
| twitter  |
| linkedin |
| gplus    |
| youtube  |
| instagram  |


<a name="a_metrics"></a>
####Supported Metrics
------------

| Network  | Metrics                                                                                                                                   | 
| -------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| facebook | likes,comments,shares,reach,organic_reach,viral_reach,paid_reach,fan_reach,non_fan_reach,fan_paid_reach,engagement,clicks,engagement_rate |
| twitter  | retweets,replies,favorites,link_clicks,engagement,engagement_rate                                                                         |
| linkedin | likes,comments,link_clicks,engagement                                                                                                     |
| gplus    | plusone,reshares,comments,link_clicks,engagement                                                                                          |
| youtube  | views,likes,dislikes,comments,link_clicks,engagement                                                                                      |
| instagram  | likes,comments
				|

***\*Note: engagement_rate is only available for facebook and twitter***

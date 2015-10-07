Clips API Documentation
=====================

Table of Contents
-----------------

* [Introduction](#introduction)
* [Database Schema](#database-schema)
* [API](#api)
   1. [Create](#create)
       * [Create a new clip minimum](#newclipminimum)
       * [Create a new clip](#newclip)
       * [Create from a post](#postclip)
       * [Duplicate a clip](#duplicate)
   2. [Get](#get)
       * [Get a list of clips](#cget)
       * [Get a clip by id](#getone)
   3. [Delete](#delete)
   4. [Archive/Unarchive](#archive)
       * [Archive](#aclip)
       * [Unarchive](#uclip)
   5. [Share/Unshare](#share)
       * [Share](#sclip)
       * [Unshare](#unclip)
   6. [Recent activities](#recentactivity)
       * [Get recent activity](#getactivities)
       * [Post an activity](#postcomments)
   7. [Get Clip performance](#clipperformance)
       * [Workspace](#workspace)
       * [Post](#post)
       * [Network](#network)
       * [Usage](#usage)
   8. [Patch](#patch)

Introduction
-----
**Clips** are pieces of content that can be used to create social posts. Clips can be **shared** across your organization. Clips can be **monitored for usage and performance**.

Database Schema
-----
![alt text](images/clips/schema_clips_v1.jpg "Schema diagram for Clips")

API
---
### <a name="create"></a>Create

#### <a name="newclipminimum"> 1. Create a new clip with minimum required fields (workspace_id, workspaces and either
a message or assets value):

With message, workspaces and workspace_id:

```
POST /v1/clips
```
**Post Data:**
```
{
    "message": "This is a test message",
    "workspaces": ["c2e918d2-4309-11e3-bb55-1cb63b08732d"],
    "workspace_id": "584d3ad3-2f7b-11e3-b8e3-168170973bd5"
}
```

With assets, workspaces and workspace_id:

**Post Data:**
```
{
    "assets": [{"url":"http://www.foo.com", "type":2}}],
    "workspaces": ["c2e918d2-4309-11e3-bb55-1cb63b08732d"],
    "workspace_id": "584d3ad3-2f7b-11e3-b8e3-168170973bd5"
}
```


#### <a name="newclip"></a> 2. Create a new clip with assets of link type and share with workspaces

```
POST /v1/clips
```
**Post Data:**
```
{
    "message": "This is a test message",
    "workspace_id": "584d3ad3-2f7b-11e3-b8e3-168170973bd5",
    "description": "This is a test comment",
    "assets": [{"url":"http://www.foo.com", "type":3, "metadata":"{\"url\":\"http://www.foo.com\"}"}],
    "workspaces": ["c2e918d2-4309-11e3-bb55-1cb63b08732d"]
}
```

#### <a name="postclip"></a> 3. Create from a post with asset of image type

```
POST /v1/clips
```
**Post Data:**
```
{
    "post_id": "82e6c061-2c5a-11e3-b1a1-168170973bd5"
    "message": "This is a test message",
    "workspace_id": "584d3ad3-2f7b-11e3-b8e3-168170973bd5",
    "description": "This is a test comment",
    "assets": [{"url":"http://www.foo.com/images/foo.png", "type":2}],
    "workspaces": ["c2e918d2-4309-11e3-bb55-1cb63b08732d"]
}
```

#### <a name="duplicate"></a> 4. Duplicate a clip with asset of link type

```
POST /v1/clips
```
**Post Data:**
```
{
    "clip_id": "82e6c061-2c5a-11e3-b1a1-168170973bd5"
    "message": "This is a test message",
    "workspace_id": "584d3ad3-2f7b-11e3-b8e3-168170973bd5",
    "description": "This is a test comment",
    "assets": [{"url":"http://www.foo.com", "type":3}],
    "workspaces": ["c2e918d2-4309-11e3-bb55-1cb63b08732d"]
}
```

The workspace owner of a clip is identified by the workspace_id when creating a clip.  It is permissible to not
specify a workspace_id in which case there will be no owner for the workspace clip.

**Expected Response for all create api calls:**

```
# HTTP 201 Created
{
    status: true,
	response: {
		id: "35543aba-18b2-4995-bb76-ea1132fe544a",
	},
	meta: {},
}
```

### <a name="get"></a>Get

```
| Parameter     | Values                 | Notes                |
|---------------------------------------------------------------|
| workspace     | uuid of workspace      | Required             |
| filter        | all, owner or shared   | Defaults to all      |
| include       | metrics                | optional for data    |
| sort          | created, avg_engagement| Defaults to created  |
| direction     | asc or desc            | Defaults to desc     |
| page          | integer value          | Defaults to 1        |
| perpage       | integer value          | Defaults to 10       |
```

#### <a name="cget"></a>1. Get a list of clips

```
GET /v1/clips?workspace=584d3ad3-2f7b-11e3-b8e3-168170973bd5&filter=all&include=metrics
```
**Response:**

```
{
    "status": true,
    "response": [
        {
           "id": "35543aba-18b2-4995-bb76-ea1132fe544a",
           "source_type": "post",
           "source_id": "93023aba-18b2-4995-bb76-ea1132fe544a",
           "organiation_id": "10567",
           "archived": 0,
           "deleted": 0,
           "message": "Post message",
           "description": "Clip comment",
           "start_date": null,
           "end_date": null,
           "created": "2014-02-02 11:00:00",
           "updated": "2014-02-03 11:00:00",
           "assets": [
               {
                 "url": "https://d2ee3wvl22m8i7.cloudfront.net/_blue.jpg",
                 "type": "2",
                 "metadata": null
               }
           ],
           "owner_workspace":
           {
              "id": "75443f9c-349b-4bfa-a124-b188d4a8719f",
              "owner": "1",
              "name": "Buddy - The Dog",
              "logo": null
           },
           "shared_workspace_count": 0,
           "shared_workspaces": [
                {
                  "id": "adlskasdaf-dsafada",
                  "name": "workspace 1",
                  "logo": "http://image.jpg"
                },
                {
                  "id": "adlskasdaf-dsafada",
                  "name": "workspace 2",
                  "logo": "http://image.jpg"
                 }
           ],
           "post_count": 19,
           "metrics": {
                 "engagement": 354,
                 "average_engagement": 177,
                 "engagement_rate": {
                    "facebook": 1.4,
                    "twitter": 1.5
                 }
           }
        },
        {
            ...
        },
        ...
    ]
    "meta": {
        "total": 5,
        "perpage": 10,
        "current": 1,
        "prev": "",
        "next": "/clips?page=2&perpage=10"
    }
}
```

#### <a name="getone"></a>2. Get a clip by id

```
GET v1/clips/{id}
```

**Response:**

```
{
    "status": true,
    "response": {
           "id": "35543aba-18b2-4995-bb76-ea1132fe544a",
           "source_type": "post",
           "source_id": "93023aba-18b2-4995-bb76-ea1132fe544a",
           "organiation_id": "10567",
           "archived": 0,
           "deleted": 0,
           "message": "Post message",
           "description": "Clip comment",
           "start_date": null,
           "end_date": null,
           "created": "2014-02-02 11:00:00",
           "updated": "2014-02-03 11:00:00",
           "assets": [
               {
                 "url": "https://d2ee3wvl22m8i7.cloudfront.net/_blue.jpg",
                 "type": "2",
                 "metadata": null
               }
           ],
           "owner_workspace":
           {
              "id": "75443f9c-349b-4bfa-a124-b188d4a8719f",
              "owner": "1",
              "name": "Buddy - The Dog",
              "logo": null
           },
           "shared_workspace_count": 0,
           "shared_workspaces": [
                {
                  "id": "adlskasdaf-dsafada",
                  "name": "workspace 1",
                  "logo": "http://image.jpg"
                },
                {
                  "id": "adlskasdaf-dsafada",
                  "name": "workspace 2",
                  "logo": "http://image.jpg"
                 }
           ],
           "post_count": 19,
           "metrics": {
                 "engagement": 354,
                 "average_engagement": 177,
                 "engagement_rate": {
                    "facebook": 1.4,
                    "twitter": 1.5
                 }
           }
    },
    "meta": []
}
```

### <a name="delete"></a>Delete

```
DELETE v1/clips/{id}
```

**Response:**
```
# HTTP 200 Success
{
    "status": true,
    "response": {},
    "meta": {},
}
```

### <a name="archive"></a>Archive/Unarchive

#### <a name="aclip"></a>1. Archive

```
POST /clips/{id}?action=archive
```

**Response:**
```
# HTTP 200 Success
{
    "status": true,
    "response": {},
    "meta": {},
}
```

#### <a name="uclip"></a>2. Unarchive

```
POST /clips/{id}?action=unarchive
```

**Response:**
```
# HTTP 200 Success
{
    "status": true,
    "response": {},
    "meta": {},
}
```

### <a name="share"></a>Share/Unshare

#### <a name="sclip"></a>1. Share

```
POST /clips/{id}?action=share
```

**Post Data:**
```
{
    "workspaces": ["c2e918d2-4309-11e3-bb55-1cb63b08732d", "c2e918d2-4309-11e3-bb55-1cb63b08732c"]
}
```

**Response:**
```
# HTTP 200 Success
{
    "status": true,
    "response": {},
    "meta": {},
}
```

#### <a name="unclip"></a>2. Unshare

```
POST /clips/{id}?action=unshare
```

**Post Data:**
```
{
    "workspaces": ["c2e918d2-4309-11e3-bb55-1cb63b08732d", "c2e918d2-4309-11e3-bb55-1cb63b08732c"]
}
```

**Response:**
```
# HTTP 200 Success
{
    "status": true,
    "response": {},
    "meta": {},
}
```

### <a name="recentactivity"></a>Recent activities

#### <a name="getactivities"></a>1. Get recent activities

```
GET /clips/{id}/activities
```

**Response:**
```
# HTTP 200 Success
{
    "status": true,
    "response":[
    {
     "user_id": "6883",
     "organization_id": "10567",
     "activity_name": "audit.post.clip.used",
     "date": "2014-05-29 17:45:36",
     "metadata": {
    	"workspace": "My workspace",
    	"comment": null,
    	"changes":[]
     }
    },
    {
     "user_id": "6883",
     "organization_id": "10567",
     "activity_name": "audit.clip.comment",
     "date": "2014-05-29 17:45:36",
     "metadata": {
    	"workspace": null,
    	"comment": "This is so good",
    	"changes":[]
     }
    },
    {
     "user_id": "6883",
     "organization_id": "10567",
     "activity_name": "audit.clip.created",
     "date": "2014-05-29 17:45:36",
     "metadata": {
    	"workspace": "My workspace",
    	"comment": null,
    	"changes":[]
      }
    }
    ]
    "meta":[]
}
```

#### <a name="postcomments"></a>2. Post a comment

```
POST /clips/{id}/comments
```
**Post Data:**
```
{
    "workspace_id": "c2e918d2-4309-11e3-bb55-1cb63b08732d",
    "comment": "This is a test comment"
}
```

**Response:**
```
# HTTP 200 Success
{
    "status": true,
    "response": {},
    "meta": {},
}
```

### <a name="clipperformance"></a>Get Clip Performance

| Broken down by      | Sort Options                                | Default Sort   | Pagination   |
|---------------------|---------------------------------------------|----------------|--------------|
| workspace           | avg_engagement, enagement                   | avg_engagement | no           |
| post                | created, engagement, engagement_rate        | created        | yes          |
| network             | engagement, engagement_rate, avg_engagement | avg_engagement | no           |
| usage               | none                                        | none           | no           |

#### <a name="workspace"></a>1. Workspace

```
GET /clips/{id}/performance?action=workspace&sort=engagement
```

**Response:**
```
# HTTP 200 Success
{
    "status": true,
    "response":[
       {
           "id": "aa556431-71ce-494b-9e82-9d5e539802e5",
           "name": "Brrrrr",
           "logo": "https://d2ee3wvl22m8i7.cloudfront.net/1398882826058_guy_bourdin.jpg",
           "post_count": "1",
           "value": "25"
       },
       {
           "id": "ab8b7a66-6949-4d2e-aaf7-fe923421cd23",
           "name": "13thMay2014",
           "logo": null,
           "post_count": "4",
           "value": "0"
       },
       {
           "id": "1d4a953e-b44a-44dd-8602-56b5310aacbc",
           "name": "ANA",
           "logo": "https://d2ee3wvl22m8i7.cloudfront.net/1399663189853_456730_10150833224116583_591494996_o.jpg",
           "post_count": "14",
           "value": "0"
       },
       {
           "id": "e210b219-3eea-42e8-af8f-6725af02c668",
           "name": "5thMay",
           "logo": null,
           "post_count": "18",
           "value": "0"
       }
    ]
    "meta":[]
}
```

#### <a name="post"></a>2. Post

```
GET /clips/{id}/performance?action=post&sort=engagement&page=1&perpage=10&direction=desc
```

**Response:**
```
# HTTP 200 Success
{
    "status": true,
    "response":[
       {
           "id": "47f23f23-3cd8-4202-861f-6c0c4f2b2a95",
           "network": "Facebook",
           "name": "2011vg",
           "slug": "/2011vg/631660596857171",
           "logo": "https://fbcdn-profile-a.akamaihd.net/static-ak/rsrc.php/v2/yg/r/LjGkeBM0ISt.png",
           "engagement": "25",
           "engagement_rate": "0.5",
           "targeting": "global",
           "publish_date": "2014-05-19 17:01:19"
       },
       {
           "id": "f5fb2046-f6fd-4f5f-9caf-18205864b1dc",
           "network": "Twitter",
           "name": "SFDCBMIT41",
           "slug": "@SFDCBMIT41",
           "logo": "http://abs.twimg.com/sticky/default_profile_images/default_profile_3_normal.png",
           "engagement": "0",
           "engagement_rate": "0",
           "targeting": "global",
           "publish_date": "2014-05-19 18:29:18"
       },
       {
           "id": "97d5058a-e5ce-48ac-8e82-59bd3ba667be",
           "network": "Twitter",
           "name": "SFDCBMIT21",
           "slug": "@SFDCBMIT21",
           "logo": "http://pbs.twimg.com/profile_images/378800000867632840/75LTUnsB_normal.png",
           "engagement": "0",
           "engagement_rate": "0",
           "targeting": "global",
           "publish_date": "2014-05-19 18:29:16"
       },
       ...
    ]
    "meta":[
       "total": 37,
       "page": 1,
       "perpage": 10,
       "current": "/v1/clips/hiren-test-post-for-checking-metrics/performance?action=post&sort=engagement&page=1",
       "prev": "",
       "next": "/v1/clips/hiren-test-post-for-checking-metrics/performance?action=post&sort=engagement&page=2"
    ]
}
```

#### <a name="network"></a>3. Network

```
GET /clips/{id}/performance?action=network&sort=engagement
```

**Response:**
```
# HTTP 200 Success
{
    "status": true,
    "response":[
       {
           "network": "Facebook",
           "post_count": "33",
           "value": "25"
       },
       {
           "network": "Twitter",
           "post_count": "4",
           "value": "0"
       }
    ]
    "meta":[]
}
```

#### <a name="usage"></a>4. Usage

```
GET /clips/{id}/performance?action=usage
```

**Response:**
```
# HTTP 200 Success
{
    "status": true,
    "response":{
        "workspace": "5",
        "published": "37",
        "scheduled": "1",
        "draft": "0"
    },
    "meta":[]
}
```

### <a name="patch"></a>Patch

A limited PATCH operation is supported to accept a set id that unshares/shares the workspaces specified by the set id.
The API will retrieve the workspaces defined by the set id and compare it with the existing workspaces it is
currently shared with. Existing workspaces that are not present in the set will be unshared.  New workspaces not
already shared will be shared. It is expected that the set id should be created and is a workspace type set. If any
errors are encountered during the unsharing or sharing operation then that error response will be returned.

```
PATCH /clips/{id}
```
**Patch Data:**
```
{
    "set_id": "c2e918d2-4309-11e3-bb55-1cb63b08732d"
}
```

**Response:**
```
# HTTP 200 Success
{
    "status": true,
    "response": {
        "id": "9f8ecfbc-650b-4fe6-b830-c90276ff7b2b",
        "source_type": "1",
        "source_type_id": "6845",
        "organization_id": "10560",
        "archived": "0",
        "deleted": "0",
        "message": "This is a message",
        "description": null,
        "start_date": null,
        "end_date": null,
        "created": "2014-07-29 19:01:23",
        "updated": "2014-07-29 19:01:23",
        "author": {
            "id": "6845",
            "display_name": "Collin Lee",
            "avatar_url": null
        },
        "assets": [],
        "owner_workspace": {
            "id": "4073bc0d-f9b3-4de8-8978-03665226aa05",
            "owner": "1",
            "name": "One",
            "logo": null
        },
        "shared_workspace_count": 4,
        "shared_workspaces": [
            {
                "id": "808e0d8a-1258-4c44-9be3-54d5a162626a",
                "name": "!ANA-Clips",
                "logo": "https://d2ee3wvl22m8i7.cloudfront.net/33486e2b56386fc7dd8c1821c1311c9c7de21619/w/imgs/1403795296331_339539_10150424222266583_1907745049_o.jpg"
            },
            {
                "id": "ab8b7a66-6949-4d2e-aaf7-fe923421cd23",
                "name": "13thMay2014",
                "logo": "https://d2ee3wvl22m8i7.cloudfront.net/33486e2b56386fc7dd8c1821c1311c9c7de21619/w/imgs/1403291121206_2ndgoal.gif"
            },
            {
                "id": "c38cab01-0aa8-4222-a04b-1f0d76fd62a7",
                "name": "collin test 123",
                "logo": null
            },
            {
                "id": "e7c8d276-2e0b-45db-84f3-88ae19b797aa",
                "name": "Collin Test",
                "logo": null
            }
        ],
        "post_count": 0,
        "metrics": []
    },
    "meta": []
}
```
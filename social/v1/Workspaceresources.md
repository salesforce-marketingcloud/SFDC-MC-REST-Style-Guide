Workspaceresources API
======


| Method                        | Action | Endpoint              | Description                                                                  |
| ----------------------------- | ------ | --------------------- | ---------------------------------------------------------------------------- |
| [get](#wr_endpoint_get)       | GET    | /workspaceresources   | Returns a list of workspace resources specified by resource_type parameter.  |
| [create](#wr_endpoint_post)   | POST   | /workspaceresources   | Adds a workspace resource to a workspace group.                              |
| [delete](#wr_endpoint_delete) | DELETE | /workspaceresources   | Removes a workspace resource from a workspace group.                         |

<a name="wr_endpoint_get"></a>
####Get Endpoint
------------

#####Parameters

None.

#####Query String Parameters

- {resource_type} - *required* - A valid workspace group resource.
- {workspace_id} - *required* - The workspace id.
- {exclude_added} - *optional* - Will return resources not added to a workspace group.

#####Request

    GET /v1/workspaceresources?workspace_id=44dfccf0-ed00-4988-a256-0913281dcb89&resource_type=topic_profile

#####Response

    {
        "status": true,
        "response": {
            "topics": {
                "data": [
                    {
                        "id": "19212",
                        "link": "https://stg2-api.dev.ca1.sfmc.co/socialcloud/v2/topics/19212",
                        "updated": "2014-05-15T03:00:01Z",
                        "title": "This is a test",
                        "creator": {
                            "id": "7626",
                            "link": "https://stg2-api.dev.ca1.sfmc.co/socialcloud/v2/users/7626",
                            "updated": "2014-05-18T13:01:51Z",
                            "title": "super_rich_stage2"
                        },
                        "visibility": "public",
                        "access": "write",
                        "status": 3,
                        "emv": 44900,
                        "languages": [],
                        "mediaTypes": [],
                        "regions": [],
                        "groups": [
                            {
                                "id": "8152",
                                "link": "https://stg2-api.dev.ca1.sfmc.co/socialcloud/v2/workspaceGroups/8152",
                                "updated": "2014-05-18T13:01:51Z",
                                "title": "Richard's New Workspace"
                            }
                        ]
                    },
                    {
                        "id": "19213",
                        "link": "https://stg2-api.dev.ca1.sfmc.co/socialcloud/v2/topics/19213",
                        "updated": "2014-05-15T03:00:01Z",
                        "title": "This is a new one",
                        "creator": {
                            "id": "7626",
                            "link": "https://stg2-api.dev.ca1.sfmc.co/socialcloud/v2/users/7626",
                            "updated": "2014-05-18T13:01:51Z",
                            "title": "super_rich_stage2"
                        },
                        "visibility": "public",
                        "access": "write",
                        "status": 3,
                        "emv": 2900,
                        "languages": [],
                        "mediaTypes": [],
                        "regions": [],
                        "groups": []
                    }
                ],
                "structure": null
            }
        },
        "meta": []
    }


<a name="wr_endpoint_post"></a>
####Post Endpoint
------------

#####Parameters

None.

#####Query String Parameters

None.

#####Request

    POST /v1/workspaceresources

#####Post Data

    {
        "workspace_id": "44dfccf0-ed00-4988-a256-0913281dcb89",
        "resource_type": "topic_profile",
        "resource_id": "19212"
    }

#####Response

    {
        "status": true,
        "response": {
            "resource_type": "topic_profile",
            "resource_id": "19212",
            "workspace_group_id": "8152"
        },
        "meta": []
    }


<a name="wr_endpoint_delete"></a>
####Delete Endpoint
------------

#####Parameters

None.

#####Query String Parameters

None.

#####Request

    DELETE /v1/workspaceresources

#####Post Data

    {
        "workspace_id": "44dfccf0-ed00-4988-a256-0913281dcb89",
        "resource_type": "topic_profile",
        "resource_id": "19212"
    }

#####Response

    {
        "status": true,
        "response": {
            "resource_type": "topic_profile",
            "resource_id": "19212",
            "workspace_group_id": "8152"
        },
        "meta": []
    }
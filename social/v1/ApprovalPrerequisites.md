# Approval Prerequisites API

Defines any preconditions / rules of an approvalflow.  Used to determine if approvalprocedures need to be processed when a trigger match is detected from the approvalflow

## Methods

### GET /approvalprerequisites/:approvalflow

Gets the approvalprerequisites of an approvalflow

#### Params
##### Required  
###### Param    
    approvalflow=[string]
The approvalflow to get approvalprerequisites for  
Example: approvalflow=aab75fac-0453-11e3-8d6f-168170973bd5

##### Optional
###### hydrate    
    hydrate=argument

hydate is optional, for now support the argument. When hydrate is argument supplied in the query, will return the related entity info instead of the string or integer ids.

#### Success Response
With results  

```
{
    "status": true,
    "response": {
        "items": [
            {
                "criteria": "submitter",
                "operator": "equals",
                "argument": 123123
            },
            {
                "criteria": "social account",
                "operator": "equals",
                "argument": 83967
            },
            {
                "criteria": "social account",
                "operator": "equals",
                "argument": 84975
            }
        ],
        "query": "(1 AND 2) OR ( 1 AND 3 )"
    },
    "meta": []
}
```

When hydrate is argument is send in query

```
{
    "status": true,
    "response": {
        "items": [
            {
                "criteria": "submitter",
                "operator": "equals",
                "argument": {
                    "id": 7664,
                    "email": "qzhen@salesforce.com",
                    "title": "Qzhen_10100",
                    "avatar_url": null,
                    "timezone": "GMT",
                    "enabled": true,
                    "updated": "2014-02-04T15:50:42+0000",
                    "organization_id": 10100,
                    "roles": 1
                }
            },
            {
                "criteria": "social account",
                "operator": "in",
                "argument": [
                    {
                        "name": "Exercise_Your_Voting_Right",
                        "id": "541a6b23-21ff-4c2c-9ed0-ca81da98d71d",
                        "network": "Facebook",
                        "asset": "519296421491495",
                        "socialaccount": 94093,
                        "tokenStatus": 1,
                        "metadata": {
                            "pageLogo": "https://fbcdn-profile-a.akamaihd.net/static-ak/rsrc.php/v2/yg/r/LjGkeBM0ISt.png",
                            "provider": "FACEBOOK",
                            "userName": "/Exercise_Your_Voting_Right/519296421491495"
                        }
                    },
                    {
                        "name": "Chris Dev Page",
                        "id": "3a65a221-c16e-479b-8430-57b46fcb21f2",
                        "network": "Facebook",
                        "asset": "407001072724791",
                        "socialaccount": 94268,
                        "tokenStatus": 1,
                        "metadata": {
                            "pageLogo": "https://fbcdn-profile-a.akamaihd.net/static-ak/rsrc.php/v2/yg/r/LjGkeBM0ISt.png",
                            "provider": "FACEBOOK",
                            "userName": "/Chris-Dev-Page/407001072724791"
                        }
                    }
                ]
            },
            {
                "criteria": "social account",
                    "operator": "equals",
                    "argument": {
                        "id": "83967",
                        "link": "https://stg3-api.dev.ca1.sfmc.co/socialcloud/v2/socialProperties/83967",
                        "updated": "2015-04-20T14:45:35Z",
                        "title": "Ana-192",
                        "topic": {
                            "id": "83967",
                            "link": "https://stg3-api.dev.ca1.sfmc.co/socialcloud/v2/topics/83967",
                            "updated": "2015-02-19T17:06:49Z",
                            "title": "Ana-192",
                            "createdDate": "2015-02-19T17:06:49Z"
                        },
                        "creator": {
                            "id": "79798023",
                            "link": "https://stg3-api.dev.ca1.sfmc.co/socialcloud/v2/users/79798023",
                            "updated": "2015-04-20T14:45:35Z",
                            "title": "Hiren Gosalia",
                            "email": "hgosalia@salesforce.com",
                            "avatarURL": null
                        },
                        "projects": [],
                        "registration": {
                            "id": "16243",
                            "link": "https://stg3-api.dev.ca1.sfmc.co/socialcloud/v2/socialAccounts/16243",
                            "updated": null,
                            "title": "Mansi SFDC",
                            "lastRefreshedTime": null
                        },
                        "lastValidatedTime": null,
                        "avatar": "https://media.licdn.com/mpr/mpr/p/7/005/086/041/07b736a.png",
                        "usedBySalesforce": "false",
                        "visibility": "private",
                        "uniqueName": "ana-192",
                        "realName": "Mansi SFDC",
                        "socialNetwork": {
                            "id": "3880369",
                            "name": "LinkedIn Page",
                            "type": 25
                        },
                        "status": 0,
                        "tokens": {
                            "tokenStatusId": 0,
                            "tokenStatusLastUpdate": null
                        }
                    }
                }
            }
        ],
        "query": "1 AND ( 2 OR 3 )"
    },
    "meta": []
}
```

#### Error Response
If approvalflow is not found

```
{
    "status": false,
    "response": {
        "errors": [
            {
                "code": "error.not_found",
                "message": ""
            }
        ]
    },
    "meta": []
}
```  

If not authorized to view the approvalflow

```
{
    "status": false,
    "response": {
        "errors": [
            {
                "code": "error.forbidden",
                "message": ""
            }
        ]
    },
    "meta": []
}
```

#### Sample Call
Returns the approvalprerequisites matching the approvalflow 'aab75fac-0453-11e3-8d6f-168170973bd5'

```
curl '{HOST}/v1/approvalprerequisites/aab75fac-0453-11e3-8d6f-168170973bd5'
```

### PUT /approvalprerequisites/:approvalflow

Updates the approvalprerquisites of an approvalflow

#### Params
##### Required:  
###### Param
    approvalflow=[string]
Example: approvalflow=46e66860-25fe-11e3-b1a1-168170973bd5
###### Param
    JSON  
Part of the JSON payload

#### JSON Data

##### Arguments
###### Argument
    items[array]  
The items that will be used to build a query string that ties together a single rule  
###### Argument
    items.#.criteria[string]  
Operand subject derived from the entity that triggered the approvalflow  

A user id performing a submit action
   
    "criteria": "submitter"

A social property id that is assocaited with the object

    "criteria": "social account"

A gating language id that is associated with the object  
    
    "criteria": "language"
    
A gating country location id that is associated with the object  
  
    "criteria": "country"

Country and Language Code is fetched from [v3/targeting](../v3/Targeting.md)

###### Argument

    items.#.operator[string]  
    
Operator  
Accepts:

*   equals
*   not equals


###### Argument
    items.#.argument[mixed]  
Desired operand to match the item with the operator.
Argument in item could accept empty for label, country and language, not for submitter or social account.

###### Argument
    query[string]  
A query string that joins together the logic of each item  
Accepts:

*   integers
*   logical operators
    *   AND
    *   OR
*   grouping
    *   `( )`

To make query clear, this api doesn't allow "AND" and "OR" in the same level. so "1 AND 2 OR 3" will not be allowed. Parentheses needed for this case.

##### Example

```
{
    "items": [
        {
            "criteria": "submitter",
            "operator": "equals",
            "argument": 123123
        },
        {
            "criteria": "social account",
            "operator": "equals",
            "argument": 68304
        },
        {
            "criteria": "social account",
            "operator": "equals",
            "argument": 68647
        }
    ],
    "query": "(1 AND 2) OR ( 1 AND 3 )"
}
```

if the items and query are both null, will remove the approvalflow's prerequisite

#### Success Response
```
{
    "status": true,
    "response": true,
    "meta": []
}
```

#### Error Response
If approvalflow does not exist

```
{
    "status": false,
    "response": {
        "errors": [
            {
                "code": "error.not_found",
                "message": ""
            }
        ]
    },
    "meta": []
}
```

If not authorized to modify the approvalflow

```
{
    "status": false,
    "response": {
        "errors": [
            {
                "code": "error.forbidden",
                "message": ""
            }
        ]
    },
    "meta": []
}
```

If incorrect arguments are presented in the request

```
{
    "status": false,
    "response": {
        "errors": [
            {
                "code": "error.bad_request",
                "message": ""
            }
        ]
    },
    "meta": []
}
```


#### Sample Call
Updates the approvalprerequisites associated with the approvalflow 'aab75fac-0453-11e3-8d6f-168170973bd'    

```
curl -X PUT {HOST}/v1/approvalprerequisites/46e66860-25fe-11e3-b1a1-168170973bd5
```
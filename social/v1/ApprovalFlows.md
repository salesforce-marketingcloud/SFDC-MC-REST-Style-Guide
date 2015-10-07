# Approval Flow API

Management of approvalflows

*    [Creating an approvalflow for a workspace](#createworkspace)
*    [Retrieve a specific approvalflow](#singleget)
*    [Retrieve a list of approvalflows for a workspace](#workspacecollection)
*    [Updating an approvalflow](#updating)
*    [Removing an approval flow](#removing)

### <a name="createworkspace"></a>Create an approvalflow for a workspace

    POST /approvalflows

Creates a new approval flow

#### Params
##### Required

###### Param
Ties this approvalflow to belong to a specific workspace (ownerId)

    workspace=[string] "92ed3d44-5376-11e3-b8e3-168170973bd5"

Example: workspace=92ed3d44-5376-11e3-b8e3-168170973bd5

###### Param
    trigger=[string]
Example: trigger=publish  
Accepts:

*   publish

###### param
    name=[string]
Example: My+Approval+Flow  
   
   * Unique within a specific owner  
   * Cannot contain `<` or `>`

##### Optional
    priority=[integer] 0…n
Example: priority=1  
Default: n

#### Error Responses

   * [Bad argument](#badargument)
   * [Missing argument](#missingargument)
   * [Workspace does not exist](#workspacedoesnotexist)
   * [Unauthorized to add approvalflow](#cannotadd)
   * [Duplicate Entry](#duplicateentry)

#### Sample Call
##### Request

```
POST /v1/approvalflows HTTP/1.1
Host: HOST
Content-Type: application/x-www-form-urlencoded

trigger=publish&workspace=92ed3d44-5376-11e3-b8e3-168170973bd5&priority=9&name=My+Approval+Flow
```

##### Response
```
{
    "status": true,
    "response": {
        "id": "d460106d-794b-11e3-b8e3-168170973bd5"
    },
    "meta": []
}
```
### <a name="singleget"></a>Retrieve a specific approvalflow

    GET /approvalflows/:id

Get a single approvalflow

#### Params
##### Required  
###### Param    
    id=[string]
The resource identifier  
Example: id=d460106d-794b-11e3-b8e3-168170973bd5

##### Optional

###### Param
    include=[string]
    
include will give extra info for the workflow. Supporting prerequsite, procedure, approverCount, criteriacount. Get approvalflow prerequisite, or procedure, or approverCount, or criteriacount when not available return empty array.

Example: include=prerequisite,procedure,approvercount,criteriacount

    hydrate=[string]
    
hydrate will give in detail information about the argument object. Supporting argument now.

Example: hydrate=argument

#### Error Responses

   * [Bad argument](#badargument)
   * [Missing argument](#missingargument)
   * [Unauthorized to view workspace](#cannotviewworkspace)

#### Sample Call

Retrieve the approvalflow 'd460106d-794b-11e3-b8e3-168170973bd5' 
##### Request 

```
GET /v1/approvalflows/d460106d-794b-11e3-b8e3-168170973bd5 HTTP/1.1
Host: HOST
```

##### Response
```
{
    "status": true,
    "response": {
        "id": "d460106d-794b-11e3-b8e3-168170973bd5",
        "ownerId": "92ed3d44-5376-11e3-b8e3-168170973bd5",
        "ownerType": "workspace",
        "priority": 171,
        "triggerAction": "publish",
        "name": "My Approval Flow"
    }
}
```

With include Params

##### Response
```

{
    "status": true,
    "response": {
        "id": "e1c69b8c-6844-43fb-9eb3-0fe30dd92cc3",
        "ownerId": "7046e184-0bd1-4c88-9255-642d8a212f31",
        "ownerType": "workspace",
        "priority": 7,
        "triggerAction": "publish",
        "name": "testworkflowapi",
        "prerequisite": {
            "items": [
                {
                    "criteria": "submitter",
                    "operator": "equals",
                    "argument": 6721
                },
                {
                    "criteria": "social account",
                    "operator": "equals",
                    "argument": "1a4ebec4-7b11-4d0a-aad3-4fc859ba8e28"
                },
                {
                    "criteria": "social account",
                    "operator": "equals",
                    "argument": "d5594362-829d-49c6-b317-6675b9a280f8"
                }
            ],
            "query": "( 1 and 2 ) or 3"
        },
        "procedure": {
            "items": [
                {
                    "criteria": "user",
                    "operator": "equals",
                    "argument": 6721,
                    "action": "approve"
                },
                {
                    "criteria": "user",
                    "operator": "equals",
                    "argument": 6721,
                    "action": "approve"
                }
            ],
            "query": "1 or 2"
        },
        "meta": {
            "criteriacount": "4"
            "approvercount": "1"
        }
    },
    "meta": []
}
```

With include and hydrate Params
```
{
    "status": true,
    "response": {
        "id": "e1c69b8c-6844-43fb-9eb3-0fe30dd92cc3",
        "ownerId": "7046e184-0bd1-4c88-9255-642d8a212f31",
        "ownerType": "workspace",
        "priority": 7,
        "triggerAction": "publish",
        "name": "testworkflowapi",
        "prerequisite": {
            "items": [
                {
                    "criteria": "submitter",
                    "operator": "equals",
                    "argument": {
                        "id": 6721,
                        "email": "pboddu@salesforce.com",
                        "username": "pboddu@salesforce.com",
                        "title": "PraveenBoddu",
                        "display_name": "PraveenBoddu",
                        "timezone": "GMT",
                        "enabled": true,
                        "updated": "2014-02-26T20:14:47+0000",
                        "organization_id": 10545
                    }
                },
                {
                    "criteria": "social account",
                    "operator": "equals",
                    "argument": null
                },
                {
                    "criteria": "social account",
                    "operator": "equals",
                    "argument": null
                }
            ],
            "query": "( 1 and 2 ) or 3"
        },
        "procedure": {
            "items": [
                {
                    "criteria": "user",
                    "operator": "equals",
                    "argument": {
                        "id": 6721,
                        "email": "pboddu@salesforce.com",
                        "username": "pboddu@salesforce.com",
                        "title": "PraveenBoddu",
                        "display_name": "PraveenBoddu",
                        "timezone": "GMT",
                        "enabled": true,
                        "updated": "2014-02-26T20:14:47+0000",
                        "organization_id": 10545
                    },
                    "action": "approve"
                },
                {
                    "criteria": "user",
                    "operator": "equals",
                    "argument": {
                        "id": 6721,
                        "email": "pboddu@salesforce.com",
                        "username": "pboddu@salesforce.com",
                        "title": "PraveenBoddu",
                        "display_name": "PraveenBoddu",
                        "timezone": "GMT",
                        "enabled": true,
                        "updated": "2014-02-26T20:14:47+0000",
                        "organization_id": 10545
                    },
                    "action": "approve"
                }
            ],
            "query": "1 or 2"
        }
    },
    "meta": []
}
```


### <a name="workspacecollection"></a>Retrieve a list of approvalflows for a workspace
    GET /approvalflows?ownerId=:workspaceId&ownerType=workspace

Retrieves a filtered set of approvalflows in order of priorty

#### Params

##### Required

###### Param
    ownerId=[string]
ownerId can be workspace id (more types to follow)

Example: ownerId=92ed3d44-5376-11e3-b8e3-168170973bd5

###### Param
    ownerType=[string]
ownerType can be workspace (more types to follow
)
Example: ownerType=workspace

##### Optional

###### Param
    include=[string]
    
include will give extra info for the workflow. Supporting prerequsite, procedure, approvercount,criteriacount. Get approvalflow prerequisite, or procedure, or approvercount, or criteriacount when not available return empty array.

Example: include=prerequisite,procedure,approvercount,criteriacount

###### Param
    page=[integer]
    perpage=[integer]

page and perpage is used for pagination control. maximum perpage for approvalflows is 25 now.

#### Error Responses

   * [Bad argument](#badargument)
   * [Missing argument](#missingargument)
   * [Unauthorized to view workspace](#cannotviewworkspace)


#### Sample Call

##### Request

```
GET /v1/approvalflows?ownerId=a272b824-bbf7-4a8c-bb1e-3d9695f3032a&ownerType=workspace HTTP/1.1
Host: HOST
```

##### Response
```
{
    "status": true,
    "response": [
        {
            "id": "d460106d-794b-11e3-b8e3-168170973bd5",
            "ownerId": "92ed3d44-5376-11e3-b8e3-168170973bd5",
            "ownerType": "workspace",
            "priority": 1,
            "triggerAction": "publish",
            "name": "My Approval Flow"
        },
        {
            "id": "b64ee373-81f0-11e3-b8e3-168170973bd5",
            "ownerId": "92ed3d44-5376-11e3-b8e3-168170973bd5",
            "ownerType": "workspace",
            "priority": 2,
            "triggerAction": "publish",
            "name": "My Other Approval Flow"
        }
    ],
    "meta": {
        "total": 2,
        "page": 1,
        "perpage": 2,
        "current": "HOST/v1/approvalflows?workspace=92ed3d44-5376-11e3-b8e3-168170973bd5&page=1",
        "prev": "",
        "next": ""
    }
}
```

With include Params
```
{
    "status": true,
    "response": [
        {
            "id": "d460106d-794b-11e3-b8e3-168170973bd5",
            "ownerId": "92ed3d44-5376-11e3-b8e3-168170973bd5",
            "ownerType": "workspace",
            "priority": 99,
            "triggerAction": "publish",
            "name": "Workflow get with include",
            "prerequisite": {
                "items": [
                    {
                        "criteria": "submitter",
                        "operator": "equals",
                        "argument": 7664
                    },
                    {
                        "criteria": "submitter",
                        "operator": "equals",
                        "argument": 7641
                    },
                    {
                        "criteria": "language",
                        "operator": "equals",
                        "argument": 60
                    },
                    {
                        "criteria": "language",
                        "operator": "equals",
                        "argument": 61
                    }
                ],
                "query": "1 and 2 and ( 3 or 4 )"
            },
            "procedure": [],
            "meta": {
                "criteriacount": "4"
                "approvercount": "1"
            }
        }
    ],
    "meta": {
        "total": 1,
        "page": 1,
        "perpage": 1,
        "current": "HOST/v1/approvalflows?include=prerequisite,procedure&workspace=92ed3d44-5376-11e3-b8e3-168170973bd5&page=1",
        "prev": "",
        "next": ""
    }
}
```


### <a name="updating"></a>Updating an approvalflow

    PATCH /approvalflows/:id

Update an approvalflow

#### Params
##### Required:  
###### Param
    id=[string]
Example: id=d460106d-794b-11e3-b8e3-168170973bd5
##### Optional
###### Param
    priority=[integer] 0…n
Example: priority=1  
Default: n 

###### Param
    name=[string]  
Example: My+Modified+Approval+Flow  
   
   * Unique within a specific owner  
   * Cannot contain `<` or `>`

#### Success Response
```
{
    "status": true,
    "response": true,
    "meta": []
}
```

#### Error Responses

   * [Bad argument](#badargument)
   * [Approvalflow does not exist](#doesnotexist)
   * [Unathorized to modify approvalflow](#cannotmodify)
   * [Duplicate Entry](#duplicateentry)

#### Sample Call
Updates the approvalflow 'd460106d-794b-11e3-b8e3-168170973bd5' to a priority of '2'  

```
curl -X PATCH -d "priority=2" {HOST}/v1/approvalflows/d460106d-794b-11e3-b8e3-168170973bd5
```

### <a name="removing"></a>Removing an approval flow

    DELETE /approvalflows/:id

Deletes an approvalflow

#### Params
##### Required:  
###### Param
    id=[string]
Example: id=d460106d-794b-11e3-b8e3-168170973bd5


#### Error Responses

   * [Approvalflow does not exist](#doesnotexist)
   * [Unathorized to remove approvalflow](#cannotremove)
   * [Missing argument](#missingargument)


#### Sample Call
Deletes the approvalflow 'd460106d-794b-11e3-b8e3-168170973bd5'

##### Request

```
DELETE /v1/approvalflows/d460106d-794b-11e3-b8e3-168170973bd5 HTTP/1.1
Host: HOST
```

##### Response

```
{
    "status": true,
    "response": true,
    "meta": []
}
```

### <a name="errorresponses"></a>Error Responses

#### <a name="cannotmodify"></a>Unathorized to modify approvalflow

User does not currently have permission to modify the approvalflow.

```
{
    "status": false,
    "response": {
        "errors": [
            {
                "code": "error.forbidden",
                "message": "Unathorized to modify"
            }
        ]
    },
    "meta": []
}
```

#### <a name="cannotremove"></a>Unathorized to remove approvalflow

User does not currently have permission to remove the approvalflow.

```
{
    "status": false,
    "response": {
        "errors": [
            {
                "code": "error.forbidden",
                "message": "Unathorized to remove"
            }
        ]
    },
    "meta": []
}
```

#### <a name="cannotadd"></a>Unathorized to add approvalflow

User does not currently have permission to add an approvalflow.

```
{
    "status": false,
    "response": {
        "errors": [
            {
                "code": "error.forbidden",
                "message": "Unathorized to add"
            }
        ]
    },
    "meta": []
}
```

#### <a name="cannotview"></a>Unathorized to view approvalflow

User does not currently have permission to view the approvalflows(s).

```
{
    "status": false,
    "response": {
        "errors": [
            {
                "code": "error.forbidden",
                "message": "Unathorized to view"
            }
        ]
    },
    "meta": []
}
```

#### <a name="cannotviewworkspace"></a>Unauthorized to view workspace

User does not currently have permission to view the workspace(s) supplied.

```
{
    "status": false,
    "response": {
        "errors": [
            {
                "code": "error.forbidden",
                "message": "Unathorized to view workspace"
            }
        ]
    },
    "meta": []
}
```

#### <a name="doesnotexist"></a>Approvalflow does not exist

User supplied approvalflows(s) that do not exist.

```
{
    "status": false,
    "response": {
        "errors": [
            {
                "code": "error.not_found",
                "message": "Approvalflow not found"
            }
        ]
    },
    "meta": []
}
```

#### <a name="workspacedoesnotexist"></a>Workspace does not exist

User supplied workspace(s) that do not exist.

```
{
    "status": false,
    "response": {
        "errors": [
            {
                "code": "error.not_found",
                "message": "Workspace not found"
            }
        ]
    },
    "meta": []
}
```

#### <a name="badargument"></a>Bad argument

User supplied an invalid or unsupported argument key/value

```
{
    "status": false,
    "response": {
        "errors": [
            {
                "code": "error.bad_request",
                "message": "An invalid parameter/value was supplied"
            }
        ]
    },
    "meta": []
}
```

#### <a name="missingargument"></a>Missing argument

An expected argument was not found

```
{
    "status": false,
    "response": {
        "errors": [
            {
                "code": "error.bad_request",
                "message": "An expected argument was not supplied"
            }
        ]
    },
    "meta": []
}
```

#### <a name="duplicateentry"></a>Duplicate Entry

An entry attempting to be inserted/updated already has the same unique criteria within the system.

```
{
    "status": false,
    "response": {
        "errors": [
            {
                "code": "error.conflict",
                "message": "A duplicate entry was detected when saving"
            }
        ]
    },
    "meta": []
}
```

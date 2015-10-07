# Workspaces API

## Methods

### POST /workspaces

Creates a new workspace entity

#### Params
##### Required    
    name=[string] Maxlength 255 characters (doesn't include stripped characters)
*   Leading / trailing / whitespaces will be remove  
*   Multiple consecutive whitespaces will become a single whitespace  

Example: name=A new workspace for the system

##### Optional
    visibility=[string] 'public' or 'private'
Example: visibility=public  
Default: private

    description=[string]
Example: description=A brand new workspace.  
Default: (empty string)

    logo=[string]
Example: logo=workspace_logo.png  
Default: (empty string)

#### Success Response
Returns the id of the newly inserted entry

```
{
    "status": true,
    "response": {
        "id": "95b75fac-0453-11e3-8d6f-168170973bd5"
    },
    "meta": []
}
```

#### Error Responses
If name already exists in a public or private workspace  

```
{
    "status": false,
    "response": {
        "errors": [
            {
                "code": "error.conflict",
                "message": ""
            }
        ]
    },
    "meta": []
}
```    

If a bad parameter is passed in  

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
```
curl -X POST -d "name=A new workspace for the system" -d "visibility=private" -d "logo=workspace_logo.png" -d "description=A brand new workspace." {HOST}/v1/workspaces
```

### GET /workspaces?page=:page&search=:search&perpage=:perpage&orderBy=:orderBy&order=:order&visibility=:visibility&user=:user&ids=:ids

Get a paginated listing of workspaces that match the search term in the name

#### Params
###### Optional  
    
    page=[integer] > 0
Default: 1    

    perpage=[integer] > 0 and <= 25 
Default: 10  

    search=[string]
To search workspaces by names that start with search   
Example: search=sales
Returns: "Salesforce", "Sales attack"
But would not return: "My Salesforce"

    orderBy=[string] 'name' or 'updated'
Ability to sort by workspace name or last modified timestamp  
Default: name

    order=[string]  'asc' or 'desc'  
asc to sort in ascending order  
desc to sort in descending order   
Default: asc

    user=[string]  'default'
Only return workspaces that contain the requestor  
If set as anything besides 'default' it is ignored.  
Example: default  
Example: 123  

Note: The requestor must be an organization administrator if passing in a value other than `default`, otherwise an `error.forbidden` will be returned  

    isMember=[boolean]  'true'
When paired with `user` only the workspaces the user has a role in will appear  
Example: true  

    visibility=[string]  'public' or 'private'
Ability to filter by public / private visibility

    include=[string] comma separated of additional information to include  
See [GET /workspaces/:id](#singleget) for values and return

Note: When paired with `user`, the include returns the permissions of the user that is making the call, not for the `user` in the query.

    ids=[string] comma separated workspace-ids. It returns workspaces that match the passed in ids depends what other filters are used.

#### Success Response
With results  

```
{
    "status": true,
    "response": [
        {
            "name": "workspace A",
            "id": "46e66860-25fe-11e3-b1a1-168170973bd5",
            "visibility": "public",
            "logo": "a_logo.png",
            "description": "A great workspace",
            "group_id": "123"
        },
        {
            "name": "Workspace B",
            "id": "46f96860-25fe-11e3-b1a1-168170973bd5",
            "visibility": "public",
            "logo": "b_logo.png",
            "description": "B successful workspace",
            "group_id": "234"
        }
    ],
    "meta": {
        "total": 400,
        "page": 2
        "perpage": 2,
        "current": "{HOST}/v1/workspaces?page=2&perpage=2&visibility=public",
        "prev": "{HOST}/v1/workspaces?page=1&perpage=2&visibility=public",
        "next": "{HOST}/v1/workspaces?page=3&perpage=2&visibility=public"
    }
}
```

No workspaces found  

```
{
    "status": true,
    "response": [],
    "meta": {
        "total": 0,
        "page": 1,
        "perpage": 10,
        "current": "{HOST}/v1/workspaces?page=1&perpage=10&visibility=public",
        "prev": "",
        "next": ""
    }
}
```

#### Error Response
If a bad argument is passed in  

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
Returns page 1 of the all workspaces results that contain 'salesforce' in their name

```
curl '{HOST}/v1/workspaces?visibility=public&page=2&perpage=2'
```

### <a id="singleget"></a>GET /workspaces/:id?include=permissions

Get a single workspace entity.

#### Params
##### Required:  

    id=[string]
Example: id=46e66860-25fe-11e3-b1a1-168170973bd5

##### Optional:

    include=[string] Comma separated info to include  
    
If `permissions` is present:  
Includes the user's permissions on the workspace in the response  

If `defaultPublishProfile` is present:  
Includes default publish profile the workspace in the response 

*   `view` Can view all socialassets, posts  
*   `edit_content` All permissions `view` allows, but also allows the creation of content such as posts
*   `admin` All permissions of `view` and `edit_content` with the additional permissions to perform administration actions on the workspace.
*   `delete` Abiltiy to delete a workspace.  

If `membercount` is present the total # of users in the workspace is returned  
If `socialassetcount` is present the total # of users in the workspace is returned

If `engage` is present the user's engage permissions on the workspace is returned

Example: include=permissions,defaultPublishProfile

#### Success Response
```
{
    "status": true,
    "response": {
        "name": "A new workspace for the system",
        "id": "46e66860-25fe-11e3-b1a1-168170973bd5",
        "visibility": "private",
        "logo": "workspace_logo.png",
        "description": "A brand new workspace!",
        "group_id": "123"        
    },
    "meta": []
}
```  

If workspace is private and requestor does not have access  

```
{
    "status": true,
    "response": {
        "name": "PRIVATE WORKSPACE",
        "id": "46e66860-25fe-11e3-b1a1-168170973bd5",
        "visibility": "private"
    },
    "meta": []
}
```  

If include contains permissions,membercount,socialassetcount,engage

```
{
    "status": true,
    "response": {
        "id": "46e66860-25fe-11e3-b1a1-168170973bd5",
        "visibility": "public",
        "name": "A new workspace for the system",
        "logo": "",
        "description": "",
        "group_id": "123",
        "permissions": [
            "view",
            "edit_content",
            "admin",
            "delete"
        ],
        "membercount": 100,
        "socialassetcount": 50,
        "engage": [
            "view",
            "edit"
        ]
    },
    "meta": []
}
```

#### Error Response
If workspace was not found

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

If invalid argument for id

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
Gets information for workspace with id of 46e66860-25fe-11e3-b1a1-168170973bd5

```
curl {HOST}/v1/workspaces/46e66860-25fe-11e3-b1a1-168170973bd5
```

### PATCH /workspaces/:id

Update a workspace entity

#### Params
##### Required:  

    id=[string]
Example: id=46e66860-25fe-11e3-b1a1-168170973bd5
##### Optional

    name=[string] Maxlength 255 characters (doesn't include stripped characters)
*   Leading / trailing / whitespaces will be remove  
*   Multiple consecutive whitespaces will become a single whitespace  

Example: name=A new workspace for the system

    visibility=[string] 'public' or 'private'
Example: visibility=public  
Default: private

    description=[string]
Example: description=An updated workspace!  
Default: (empty string)

    logo=[string]
Example: logo=workspace_logo.png  
Default: (empty string)

    defaultPublishProfile=[string]
Example: defaultPublishProfile=21ceb39b-f23f-4acd-9de3-8a369a5d8fa5  
Default: (empty string)

#### Success Response
```
{
    "status": true,
    "response": true,
    "meta": []
}
```

#### Error Response
If workspace with id not found   

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

If user does not have permission modify the workspace  

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
Changes a workspace with id 46e66860-25fe-11e3-b1a1-168170973bd5 to visibiltiy private  

```
curl -X PATCH -d "visibility=private" {HOST}/v1/workspaces/46e66860-25fe-11e3-b1a1-168170973bd5
```

### DELETE /workspaces/:id

Deletes a workspace entity

#### Params
##### Required:  

    id=[string]
Example: id=46e66860-25fe-11e3-b1a1-168170973bd5

#### Success Response
```
{
    "status": true,
    "response": true,
    "meta": []
}
```

#### Error Response
If workspace was not found

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

If required parameter contraints not met

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

If user does not have permission modify the workspace  

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
Deletes the workspace with an id of 46e66860-25fe-11e3-b1a1-168170973bd5

```
curl -X DELETE http://cmapi-mdiemer.test.dev.us1.sfmc.co/v1/workspaces/46e66860-25fe-11e3-b1a1-168170973bd5
```

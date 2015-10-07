# Publishprofiles API

## Methods

### Creating a publishProfile
    POST /publishProfiles

Create a publish profile

#### Params
###### Param
    JSON  
Part of the JSON payload

#### JSON Data

##### Arguments
Example:

```
    {
        "name": "My Publish Profile",
        "description": "My Publish Profile Description",
        "socialPropertyIds": ['3211', '4567'],
        "youtubePlaylists": [
            {
                "key" : "3211",
                "value" : "11"
            },
            {
                "key" : "3211",
                "value" : "22"
            }
        ],
        "targetingIds": ['li_ag', 'fb_nj'],
        "webAnalytics": [
            {
                "key" : "sample_key",
                "value" : "sample_value"
            },
            {
                "key" : "sample_key2",
                "value" : "sample_value2"
            }
        ]
    }
```
#### Success Response
Returns the id of the newly inserted entry

```
{
    "id":"5239534f-0b58-11e3-8d6f-168170973bd5"
}
```

#### Error Responses
Bad request

```
{
    "status": 400,
    "code": "error.bad_request",
    "message": "Invalid data supplied."
}
```

#### Sample Call
```
curl -X POST  {HOST}/v3/publishProfiles
```

### sharing a Publishprofile to a workspace
    POST /publishProfiles/:id?action=share

Share a publishporifle to given workspaces

#### Params
##### Required: 

    id=[string]
Example: id=740a25ea-0b57-11e3-8d6f-168170973bd5 

    workspaceIds=array
Example: workspaceIds=[array of strings] as json payload
        { "workspaceIds":["aa9220d5-8e8c-4514-b759-2a80dcaa37d8"]}

#### Success Response
Returns the id of the newly inserted entry

```
true
```

#### Error Responses
If bad argument is passed in
```
{
    "status": "400",
    "code": "error.bad_request",
    "message": "Invalid data supplied"
}
```

If Publishprofile was not found

```
{
    "status": "404",
    "code": "error.not_found",
    "message": "publish profile not found"
}
```

If user does not have permission to remove the Publishprofile

```
{
    "status": "403",
    "code": "error.FORBIDDEN",
    "message": "Unauthorized to view profile"
}
```

#### Sample Call
```
curl -X POST  {HOST}/v3/publishProfiles/:id/share
```




### adding parameters to a publishProfile
### it adds all or none, it follows atomic transaction
    PATCH /publishProfiles

add parameters to a publish profile

#### Params
###### Param
    JSON  
Part of the JSON payload

#### JSON Data

##### Arguments
Example:"youtubePlaylists": 

```
  [
    {"op":"add", "path":"/targetingIds", "value":[5001,2345]},
    {"op":"add", "path":"/socialPropertyIds", "value":[6,4567]},
    {"op":"add", "path":"/youtubePlaylists", "value":[{"key" : "3211","value" : "11"},{"key" : "3211","value" : "22"}],
    {"op":"remove", "path":"/socialPropertyIds", "value":[4]},
    {"op":"remove", "path":"/youtubePlaylists", "value":[{"key" : "3211","value" : "11"},{"key" : "3211","value" : "22"}],
    {"op":"replace", "path":"/publishProfile", "name":"My Publish Profile1", "description":"My Publish Profile Description2"}
  ]
```

#### Success Response
Returns true

```
{
    true
}
```

#### Error Responses
Bad request

```
{
    "status": 400,
    "code": "error.bad_request",
    "message": "Invalid JSON supplied."
}
```

If Publishprofile was not found

```
{
    "status": "404",
    "code": "error.not_found",
    "message": "publish profile not found"
}
```

If user does not have permission to remove the Publishprofile

```
{
    "status": "403",
    "code": "error.FORBIDDEN",
    "message": "Unauthorized to view profile"
}
```

If transaction fails due to duplicate entry

```
{
    "status": "500",
    "code": "error.CONFLICT",
    "message": "Duplicate entry found"
}
```

If transaction fails due to other errors

```
{
    "status": "500",
    "code": "error.INTERNAL_SERVER_ERROR",
    "message": "An error occurred when saving"
}
```

### Getting all Publishprofiles of a workspace 
    GET /publishProfiles?page=:page&search=:search&limit=:limit&orderBy=:orderBy&order=:order&workspace=:workspace

Get a listing of Publishprofiles that have been added to your organization, ordered by Publishprofile

#### Params
##### Required  

    workspace=[string]
Workspace to find Publishprofiles for  
Example: 95b75fac-0453-11e3-8d6f-168170973bd5

##### Optional  
    
    page=[integer] > 0
Default: 1    

    limit=[integer] > 0
Default: 20
    
    orderBy=[string]
Default: name
choices: name

    order=[string]
Default: asc
choices: asc, desc

    search=[string]
Get a paginated listing of Publish Profile that match the search term in the name   


#### Success Response
```
{
    "publishProfiles": [
        {
            “id”: “000ca3cf-3ed1-4b7d-8253-0d50fe1e93fd”,
            "type": "publish",
            "name": "My Publish Profile",
            "description": "testing",
            "owner": 6834,
            "created": 'created_date',
            "socialPropertyIds": '/v3/publishProfiles/:id/socialPropertyIds',
            "youtubePlaylists": '/v3/publishProfiles/:id/youtubePlaylists',
            "targetingIds": '/v3/publishProfiles/:id/targetingIds'
        },
        {
            “id”: “000ca3cf-3ed1-4b7d-8253-0d50fe1e93fd”,
            "type": "publish",
            "name": "My Publish Profile",
            "description": "testing",
            "owner": 6834,
            "created": 'created_date',
            "socialPropertyIds": '/v3/publishProfiles/:id/socialPropertyIds',
            "targetingIds": '/v3/publishProfiles/:id/targetingIds'
        }
    ],
    "count": 4,
    "next": "http://cmapi.dev.us1.sfmc.co/v3/publishProfiles?limit=2&workspace=:workspaceId&page=2"
}
```

#### Error Response
If a bad argument is passed in  

```
{
    "status": "400",
    "code": "error.bad_request",
    "message": "Invalid data supplied"
}
```

If no publish profile are found using criteria  

```
{
    "publishProfiles": []
}
```
curl '{HOST}/v3/publishProfiles?workspace=028e2d63-0914-11e3-8d6f-168170973bd5&page=2&limit=2'


### Retrieving a single Publishprofile
    GET /publishProfiles/:id

Get a single Publishprofile entity.

#### Params
##### Required:  

    id=[string]
Example: id=740a25ea-0b57-11e3-8d6f-168170973bd5

#### Success Response
```
{
    “id”: “000ca3cf-3ed1-4b7d-8253-0d50fe1e93fd”,
    "type": "publish",
    "name": "My Publish Profile",
    "description": "testing",
    "owner": 6834,
    "created": 'created_date',
    "socialPropertyIds": '/v3/publishProfiles/:id/socialPropertyIds',
    "targetingIds": '/v3/publishProfiles/:id/targetingIds'
}
```

### Retrieving a geting all data of a given type of a given Publishprofile Id
    GET /publishProfiles/:id/:type

Get a all targetingIds of a given Publishprofile Id.

#### Params
##### Required:  

    id=[string]
Example: id=0151bc2e-ee3d-4108-b7cf-b3bb0d207b91
    type=[string]
Example: type=targetingIds [other options socialPropertyIds]

#### Success Response
##### default (No fields supplied)
```
{
    "targetingIds": [
        "li_geo_af.dz",
        "li_geo_af.eg"
    ],
    "count": 9,
    "next": "{HOST}/v3/publishProfiles/0151bc2e-ee3d-4108-b7cf-b3bb0d207b91/targetingIds&page=2"
}
```

For Web Analytucs, the return will include key and values, which is different then others, sample like below

```
{
    "webAnalytics": [
        {
            "key": "another one",
            "value": "another value"
        },
        {
            "key": "third one",
            "value": "third value"
        },
        {
            "key": "ytm_dd",
            "value": "ytm_value"
        }
    ],
    "count": 3,
    "next": null
}
```

#### Error Response

If bad argument is passed in
```
{
    "status": "400",
    "code": "error.bad_request",
    "message": "Invalid data supplied"
}
```

If Publishprofile was not found

```
{
    "status": "404",
    "code": "error.not_found",
    "message": "publish profile not found"
}
```

If user does not have permission to remove the Publishprofile

```
{
    "status": "403",
    "code": "error.FORBIDDEN",
    "message": "Unauthorized to view profile"
}
```

#### Sample Call
Gets information for Publishprofile 740a25ea-0b57-11e3-8d6f-168170973bd5

```
curl {HOST}/v3/publishProfiles/740a25ea-0b57-11e3-8d6f-168170973bd5
```

### Removing a Publishprofile from workspace
    DELETE /publishProfiles/:id

Removes the Publishprofiles

#### Params
##### Required:  

    id=[string]
Example: id=740a25ea-0b57-11e3-8d6f-168170973bd5


#### Success Response
```
{
    true
}
```

#### Error Response
If Publishprofile was not found

```
{
    "status": "404",
    "code": "error.not_found",
    "message": "publish profile not found"
}
```

If user does not have permission to remove the Publishprofile

```
{
    "status": "403",
    "code": "error.FORBIDDEN",
    "message": "Unauthorized to view profile"
}
```

#### Sample Call
Deletes the Publishprofile with an id of 740a25ea-0b57-11e3-8d6f-168170973bd5

```
curl -X DELETE {HOST}/v3/publishProfiles/740a25ea-0b57-11e3-8d6f-168170973bd5
```

### Unsharing a Publishprofile from workspace
    POST /publishProfiles/:id?action=unshare

Unshare a publishporifle from workspace

#### Params
##### Required: 

    id=[string]
Example: id=740a25ea-0b57-11e3-8d6f-168170973bd5 

Example: workspaceIds=[array of strings] as json payload
        { "workspaceIds":["aa9220d5-8e8c-4514-b759-2a80dcaa37d8"]}

#### Success Response
```
{
    true
}
```

#### Error Response
If Publishprofile was not found

```
{
    "status": "404",
    "code": "error.not_found",
    "message": "publish profile not found"
}
```

If user does not have permission to remove the Publishprofile

```
{
    "status": "403",
    "code": "error.FORBIDDEN",
    "message": "Unauthorized to view profile"
}
```

# Approval Procedure API

## Methods

### GET /approvalprocedures/:approvalflow

Retrieves the approvalprocedures of an approvalflow

#### Params
##### Required  

###### Param    
    approvalflowid=[string]
The approvalflow to get prerequisites for  
Example: approvalflow=aab75fac-0453-11e3-8d6f-168170973bd5

##### Optional
###### hydrate    
    hydrate=argument

hydate is optional, for now supports the "argument". When hydrate is argument supplied in the query, will return the related entity info instead of the string or integer ids.

#### Success Response
With results  

```
{
    "status": true,
    "response": {
        "items": [
            {
                "criteria": "user",
                "operator": "equals",
                "argument": 123123,
                "action": "approve"
            },
            {
                "criteria": "user",
                "operator": "equals",
                "argument": 8765,
                "action": "approve"
            },
            {
                "criteria": "user",
                "operator": "equals",
                "argument": 425548144218779,
                "action": "approve"
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
                "criteria": "user",
                "operator": "equals",
                "argument": {
                    "id": 6721,
                    "email": "pboddu@salesforce.com",
                    "username": "pboddu@salesforce.com",
                    "title": "PraveenBoddu",
                    "display_name": "PraveenBoddu",
                    "avatar_url": null,
                    "timezone": "GMT",
                    "enabled": true,
                    "updated": "2014-02-07T18:59:47+0000",
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
                    "avatar_url": null,
                    "timezone": "GMT",
                    "enabled": true,
                    "updated": "2014-02-07T18:59:47+0000",
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
                    "avatar_url": null,
                    "timezone": "GMT",
                    "enabled": true,
                    "updated": "2014-02-07T18:59:47+0000",
                    "organization_id": 10545
                },
                "action": "approve"
            }
        ],
        "query": "1 and ( 2 or 3 )"
    },
    "meta": []
}
```

#### Error Response
If approvalflow cannot be found

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
Returns the approvalprocedures associated with the approvalflow 'aab75fac-0453-11e3-8d6f-168170973bd'  

```
curl '{HOST}/v1/approvalprocedures/aab75fac-0453-11e3-8d6f-168170973bd5'
```

### PUT /approvalprocedures/:approvalflow

Updates the approvalprerquisites for an approvalflow

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
Accepts:

*   user

###### Argument
    items.#.operator[string]  
    
Operator  
Accepts:

*   equals
*   not equals

###### Argument
    items.#.argument[mixed]  

Desired operand to match the item with the operator

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

##### Example

```
{
    "items": [
        {
            "criteria": "user",
            "operator": "equals",
            "argument": 123123,
            "action": "approve"
        },
        {
            "criteria": "user",
            "operator": "equals",
            "argument": 8765,
            "action": "approve"
        },
        {
            "criteria": "user",
            "operator": "equals",
            "argument": 425548144218779,
            "action": "approve"
        }
    ],
    "query": "(1 AND 2) OR ( 1 AND 3 )"
}
```

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

If malformed data is present  

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
Updates the approvalprocedures associated with the approvalflow 'aab75fac-0453-11e3-8d6f-168170973bd'  

```
curl -X PUT {HOST}/v1/approvalprocedures/46e66860-25fe-11e3-b1a1-168170973bd5
```
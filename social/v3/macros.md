Radian6 API v2 Macros Documentation

## Overview

All methods on macro resources use the following standard socialcloud header values:

* auth_token: The token of the user executing the operation.  All operations performed will be done under this identity

* auth_token_keyalias: The key alias used to generate the token (none required if /auth/authenticate was used to generate.)

* auth_appkey: The application key of the caller.

## Methods

### Create Macro

Creates a macro, owned by the caller.

Verb: POST

URL: /macros

Parameters: None

Body: [Macro Definition]

### Update Macro

Overwrites the specified macro with the definition provided in the update body.  Some information cannot be updated in this way, such as createdDate, owner, client, etc.

Verb: PUT

URL: /macros/<macro id>

Parameters: None

Body: [Macro Definition]

### Delete Macro

Deletes the specified macro.  Any macros deleted in this way are no longer accessible to any clients.

Verb: DELETE

URL: /macros/<macro id>

Parameters: None

Body: None

### List/Get Macros

Retrieves a list of all macros in the same tenant as the caller.  Note that this call has no awareness
of workspaces; it is meant presently to be called by super users in the tenant for macro management.

Verb: GET

URL: /macros or /macros?workspaceGroupId=<workspace group id> or /macros?workspaceId=<workspace uui>

Parameters: workspaceGroupId - optional parameter to return a list that only contains macros for the specified workspace group id.

Parameters: workspaceId - optional parameter to return a list that only contains macros matching the specific workspace id (workspace uuid)

Body: None

## Execute Macro

Verb: POST

URL: /macros/<macro id>?method=execute

Parameters: None

Body: [Execution Request (macro)]

## Execute Actions (ad-hoc macro)

Verb: POST

URL: /macros?method=executeactions

Parameters: None

Body: [Execution Request (ad-hoc)]

## JSON Objects

### Macro Definition

client, createdDate, and modifiedDate are *read-only* and cannot be modified

```
{
  "id": <id>,
  "name": "<name>",
  "description": "<description>",
  "owner": <user id>,
  "client": <client id>,
  "createdDate": "<created date>",
  "modifiedDate": "<modified date>",
  "actions": [
     <action definition>
  ]
}
```

### Action Definition

See [Actions](#actions) for a list of all available actions, as well as descriptions of the expected value parameter.

```
{
  "type": "<action type name>",
  "value": "<value>",
  "parameters": [
    {
      "name": "<param name>",
      "value": "<param value>"
    }
  ]
}
```

### Execution Request (ad-hoc)

```
{
  "posts": [<post id>]
  "topics": [<topic id>]
  "actions": [
    [Action Definition]
  ]
}
```

### Execution Request (macro)

```
{
  "posts": [<post id>]
  "topics": [<topic id>]
}
```

### Execution Response

```
{
  "results": [
    {
      "postId": <postId>,
      "action": [Action Definition],
      "result": "SUCCESS | FAILURE",
      "error": "<error if applicable>"
    }
  ]
}
```

## Actions

<table>
  <tr>
    <th>Workflow Operation</th>
    <th>Action Type String</th>
    <th>Expected Value</th>
    <th>v1 Set Call</th>
  </tr>
  <tr>
    <td>Set Classification</td>
    <td>SetClassification</td>
    <td>Integral classification value (as retrieved by /lookup/workflow)</td>
    <td>POST /post/workflow/classification</td>
  </tr>
  <tr>
    <td>Add Author Tag</td>
    <td>AddAuthorTag</td>
    <td>Textual author tag value</td>
    <td>POST v2/authors/{authorId}/tags</td>
  </tr>
  <tr>
    <td>Add Post Tag</td>
    <td>AddPostTag</td>
    <td>Textual post tag value</td>
    <td>POST /post/workflow/tags</td>
  </tr>
  <tr>
    <td>Assign post to user</td>
    <td>AssignUser</td>
    <td>Integer ID of the user to whom the post is being assigned</td>
    <td>POST /post/workflow/assign</td>
  </tr>
  <tr>
    <td>Set Engagement (status)</td>
    <td>SetEngagement</td>
    <td>Integer ID of the engagement level being set</td>
    <td>POST /post/workflow/engagement</td>
  </tr>
  <tr>
    <td>Set Priority</td>
    <td>SetPriority</td>
    <td>Integer ID of the priority level being set</td>
    <td></td>
  </tr>
  <tr>
    <td>Add Post Note</td>
    <td>AddPostNote</td>
    <td>Textual value of the note to be added</td>
    <td>POST /post/workflow/note</td>
  </tr>
  <tr>
    <td>Set Spam Status</td>
    <td>SetSpamStatus</td>
    <td>0 - Post is not spam <br />
2 - Post is spam</td>
    <td></td>
  </tr>
  <tr>
    <td>Add Source Tag</td>
    <td>AddSourceTag</td>
    <td>Textual value of the source tag being added</td>
    <td>POST /blog/workflow/note</td>
  </tr>
  <tr>
    <td>Set Sentiment</td>
    <td>SetSentiment</td>
    <td>Integral identifier of the sentiment level to be set</td>
    <td>POST /post/workflow/sentiment</td>
  </tr>
  <tr>
    <td>Send To Salesforce (SCS)</td>
    <td>SendToSalesforceScs</td>
    <td>Identifier of the org to which the post is to be sent <br /> Additional Parameters: <br /> case: on/off indicating whether to also create a case (default: off)<br /> lead: on/off indicating whether to also create a lead (default: off) </td>
    <td>N/A</td>
  </tr>
</table>



# User Service

The user represents your identify within your organization. In addition to your display name and avatar, it includes settings like the user's preferred language and timezone as well as their role within the organization.
## Get Users

Returns a list of all the users for the authenticated users’ client.

<pre>GET /v3/users</pre>

Standard limit and offset parameters enable paging over the set of users, where limit represents the number of users brought back and offset represent the start point for the page.

### Parameters

|Parameter|Description|Required/Default|
|---------|-----------|----------------|
|q        |filter list of users in the client by name. Will return all users that have a have a partial case insensitive match in ascending order.| Optional. Must be 3 characters minimum.|
|enabled  |true to return only active users; false to return disabled users in the client| Optional|
|limit    | the maximum number of users to return | Optional. Defaults to 100000 if none is provided.|
|offset   | Specifies the first row to return (0-based)| Optional|

**Response**
```json
{
  "data": [
    {
      "id": "3339",
      "title": "Admin",
      "username": "admin",
      "displayName": "Admin",
      "email": "testuser@radian6.com",
      "timeZone": "America/Goose_Bay",
      "enabled": true,
      "avatarURL": "http://a0.twimg.com/profile_images/2373088574/zdkanvw2160o0sm6prgd_normal.jpeg",
      "userRoleId": 6,
      "orgRoleId": 1,
      "languageId": 1,
      "createdDate": "2014-05-09T03:00:00Z",
      "internalUserAvatarUrl": null,
      "userAvatarUrl": null,
      "clientId": 1
    },
    …
  ],
  "meta": {
    "totalCount" : 52
  }
}
```
## Get User

Gets a user based on the input user id* providing the caller is in the same client id.

<pre>GET /v3/users/{userId}</pre>

`*` *Starting in 196, you can also use the "me" shorthand for the current user in place of the {userId} path parameter.* 

**Response**
```json
{
  "data": [
    {
      "id": "7025",
      "title": "00mcadmintest",
      "username": "mcadmintest",
      "displayName": "00mcadmintest",
      "email": "mcadmintest@salesforce.com",
      "timeZone": "America/Halifax",
      "enabled": true,
      "avatarURL": null,
      "userRoleId": 1,
      "orgRoleId": 2,
      "languageId": 1,
      "createdDate": "2014-07-09T18:42:22Z",
      "internalUserAvatarUrl": null,
      "userAvatarUrl": null,
      "clientId": 1
    }
  ],
  "meta": {
    "totalCount": 1
  }
}
```
## Reset User Password

Resets a user’s password and sends a notification email containing a link where the user can set a new password. This action requires administrative privileges.

<pre>POST /v3/users/{userId}/password?action=reset</pre>

**Response**

* **204** No Content

Note:  An email from support@radian6.com will be sent to the email of the user account requesting a password reset.

`*` *you can also use the "me" shorthand for the current user in place of the {userId} path parameter.* 

## Update User

Updates the user of the input user id*. Super users are allowed to update any user in the organization.  

<pre>PUT /v3/users</pre>

`*` *you can also use the "me" shorthand for the current user in place of the {userId} path parameter.* 

Required fields:
```json
{
  "username": "Matt.Daigle@Radian6.com",
  "displayName": "Matt Daigle",
  "email": "Matt.Daigle@Radian6.com",
  "timeZone": "Europe/London",
  "enabled": true,
  "orgRoleId": 2
}
```

Sample JSON for a PUT that includes required and optional fields:
```json
{
  "username": "Matt.Daigle@Radian6.com",
  "displayName": "Matt Daigle",
  "email": "Matt.Daigle@Radian6.com",
  "timeZone": "Europe/London",
  "enabled": true,
  "avatarURL": "http://a0.twimg.com/profile_images/1284202467/laptop-vector-model-122171296611479Nq0_normal.png",
  "userRoleId": 6,
  "orgRoleId": 1,
  "languageId": 1,
  "internalUserAvatarUrl": "http://socialstudio.s3.amazonaws.com/avatars/ad816b59936d93dc3ec36a43e15cbb8c"
}
```

NOTE: "avatarURL" is the legacy avatar used by EC. "userAvatarURL" is the v2 avatar that users can upload via Social Studio

**Response**

* **204** No Content – The server successfully processed the request but is not returning any content.

# Client Service
The client represents your company's organization within the Marketing Cloud. 
## Get Clients

Returns a collection of clients. Currently, the only object in that collection is the client of the authenticated user.

<pre>GET /v3/clients</pre>

**Response**
```json
{
  "data": [
    {
      "id": "1",
      "title": "Radian6 Technologies",
      "email": null,
      "timeZone": "America/Halifax",
      "languageId": 1,
      "sessionTimeoutMillis": 28800000,
      "aviaryEnabled": false,
      "apiSelfServeEnabled": true
    }
  ],
  "meta": {
    "totalCount": 1
  }
}
```

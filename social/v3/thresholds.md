# Thresholds

## Create Threshold

Creates a new threshold based upon the provided content.

**Verb**: POST

**URL**: /v2/thresholds

**Parameters**: None

**Content**: [Threshold JSON (Create/Update)]

## Get Threshold

Retrieves the threshold specified by the ID in the URL. Only thresholds created by users of the same tenant as the caller can be retrieved.

**Verb**: GET

**URL**: /v2/thresholds/{id}

**Parameters**: None

**Content**: [Threshold JSON (Retrieval)]

## List Thresholds

Retrieves a list of all thresholds created by users of the same tenant as the caller. The response contains an array of fully formed threshold entities.

**Verb**: GET

**URL**: /v2/thresholds

**Parameters**: None

**Content**: [Threshold JSON (Retrieval)]

## Update Threshold

Overwrites the threshold whose ID is specified in the URL with the provided threshold definition. Only thresholds created by users of the same tenant as the caller can be updated.

**Verb**: PUT

**URL**: /v2/thresholds/{id}

**Parameters**: None

**Content**: [Threshold JSON (Create/Update)]

## Delete Threshold

Deletes the threshold identified in the URL. Only thresholds created by users of the same tenant as the caller can be deleted.

**Verb**: DELETE

**URL**: /v2/thresholds/{id}

**Parameters**: None

**Content**: None

## Resume a threshold

Resumes the threshold identified in the URL.  Resuming a threshold already in a resumed state has no effect. Only thresholds created by users of the same tenant as the caller can be resumed.

**Verb**: POST

**URL**: /v2/thresholds/{id}?method=resume

**Parameters**:

method: Must be "resume".

**Content**: None

## Suspend a threshold

Suspends the threshold identified in the URL.  Suspending a threshold already in a suspended state has no effect. Only thresholds created by users of the same tenant as the caller can be suspended.

**Verb**: POST

**URL**: /v2/thresholds/{id}?method=suspend

**Parameters**:

method: Must be "suspend".

**Content**: None

## Threshold JSON (Create/Update)

```
{
	"title": <title>,
	"description": <description>,
	"type": "absolute|percentile",
	"value": <value of threshold>,
	"timespan": {
		"scale": "days|hours"
		"measure": <units of time>
	},
	"emailFrequency": <frequency in minutes>,
	"notifications": [{
		"type": "email",
		"address": <address>,
		"subject": <subject text>,
		"sender": <sender name>
	}]
}
```

**Notes**
* "value" must not be zero.
* "value" allows positive values for "absolute" type. 
* "value" allows positive and negative values for "percentile" type. Use negative values to monitor descreased volumes. 
* "value" must not be less than -100 when "percentile" type is used.
* "timespan" can not exceed 7 days. 

## Threshold JSON (Retrieval)

```
{
	"id": <id>,
	"link": "https://<api-server>/socialcloud/v2/thresholds/<threshold-id>",
	"created": <created date>,
	"createdBy": {
		"id": <user id>,
		"clientId": <client id>,
		"clientName": <client name>,
		"username": <username>,
		"email": <email>
	},
	"updated": <last update date>,
	"updatedBy": {
		"id": <user id>,
		"clientId": <client id>,
		"clientName": <client name>,
		"username": <username>,
		"email": <email>
	},
	"status": "running|suspended",
	"lastFired": <last fired date>,
	"title": <title>,
	"description": <description>,
	"type": "absolute|percentile",
	"value": <value of threshold>,
	"timespan": {
		"scale": "days|hours"
		"measure": <units of time>
	},
	"emailFrequency": <frequency in minutes>,
	"notifications": [{
		"type":"email",
		"address": <address>,
		"subject": <subject text>,
		"sender": <sender name>
	}]
}
```

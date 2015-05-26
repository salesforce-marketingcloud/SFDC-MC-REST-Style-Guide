# API Description Format

Routes MUST be fully documented in the [Swagger format 2.0](https://github.com/swagger-api/swagger-spec/blob/master/versions/2.0.md)

* REQUIRED to expose all resources
* REQUIRED to expose all supported headers on resource
* REQUIRED to expose all query string parameters for resources
* REQUIRED to document unique operationId for all operations
* REQUIRED to document summary and description for all operations
* REQUIRED to document produces and consumes sections
* REQUIRED to expose schema for responses for successful resources
* REQUIRED to expose schema for input for all operations to resources
	* REQUIRED to document required properties on object
	* REQUIRED to document default values
	* REQUIRED to document min/max values for int and string values
* SHALL NOT expose an "examples" object in response section
* SHOULD expose schemas for error responses for all errors possible on a resource
* REQUIRED to document security policy for API on whole (but not route level)
* MUST NOT document http verb substitution
* SHOULD document custom action substitution
	* MAY use extension to operation "x-actions" and response "x-action" to associate actions and their responses for a POST



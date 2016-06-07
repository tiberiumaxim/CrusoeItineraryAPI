# Crusoe Itinerary API
Crusoe Itinerary API documentation

## Environments
Use the appropriate environment based on your stage

1. Production

  1. Web App: <http://mycrusoe.com>

  2. REST API: <http://mycrusoe.com/api>

2. Testing

  1. Web App: <http://robinson:voyager2016@dev.mycrusoe.com>

  2. REST API: <http://robinson:voyager2016@dev.mycrusoe.com/api>


## Steps to access the API
1. Go to `/auth/signup` and sign up for Crusoe
2. Access `/manage-api` and create at least one API key
3. Use the created API key to make requests to our servers

## Authentication
Every request made to our API should have a header named `api-key` which should contain the API key generated from your account.

## Request structure
Our API can be accessed using the HTTP protocol & REST architecture.

Our API accepts JSON encoded data for POST requests.

## Response structure
The API responds with JSON encoded data and has the following structure

- For valid, successful requests:

```json
{
  "success": true,
  "statusCode": 200,
  "data": { ... }
}
```

- For invalid or failed requests:

```json
{
  "success": false,
  "statusCode": 400,
  "message": "One or more validation errors found",
  "data": {
    "validationErrors": [ ... ]
  }
}
```

## API Endpoints

### [POST] `/itinerary/create`
Send requests to this route with data describing how your itinerary should look and get back a url to our app with the visual representation of the itinerary

## Errors
1. AUTH_REQUIRED

```json
{
	"statusCode": 401,
	"errorType": "AUTH_REQUIRED",
	"message": "Missing API auth header"
}
```

2. INVALID_AUTH

```json
{
	"statusCode": 401,
	"errorType": "INVALID_AUTH",
	"message": "Invalid API key"
}
```

3. VALIDATION_ERROR

```json
{
	"statusCode": 400,
	"errorType": "VALIDATION_ERROR",
	"message": "One or more validation errors occured",
	"data": {
		"validationErrors": [{
		  "name": "title",
		  "type": "required",
		  "value": null
		}]
	}
}
```

The `validationErrors` key is present only for this type of error and has the following meaning
* `name`: property name
* `type`: name of violated validation
* `value`: the value that was sent

4. UNEXPECTED_ERROR

```json
{
	"statusCode": 500,
	"errorType": "UNEXPECTED_ERROR",
	"message": "Internal server error"
}
```

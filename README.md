# CrusoeItineraryAPI
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

## Routes

### [POST] `/itinerary/create`
Send requests to this route with data describing how your itinerary should look and get back a url to our app with the visual representation of the itinerary

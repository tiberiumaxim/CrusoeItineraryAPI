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
1. Go to `/auth/signup` in the Web App and sign up for Crusoe
2. Go to `/create-company` in the Web App and create a company
3. Access `/manage-api` in the Web App and create at least one API key
4. Use the created API key to make requests to our servers

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
#### Description
Send requests to this route with data describing how your itinerary should look and get back a url to our app with the visual representation of the itinerary

#### Request

1. Main itinerary properties

```json
{
	"title" : "Trip to Barcelona",
	"subtitle" : "Bucharest to Barcelona",
	"dateInterval" : {
		"from" : "19.05.2016",
		"to" : "26.05.2016"
	},
	"milestones" : {
		"start" : "Trip starts",
		"end" : "Trip ends"
	},
	"price" : {
		"type" : "dynamic",
		"fixedPriceAmount" : 0,
		"currency" : "EUR"
	},
	"segments" : [ ... ]
}
```

+ title (string, required) - Title of the itinerary
+ subtitle: `Berlin to Barcelona` (string, optional) - Subtitle of itinerary, ideally start and end locations
+ dateInterval (object, required) - object containing the start and end dates of the itinerary
    + from: `19.05.2016` (string, required) - string formatted like this: day.month.year
    + to: `19.05.2016` (string, required) - string formatted like this: day.month.year
+ introText: (string, optional) - Introductory text for the itinerary
+ milestones (object, optional) - Text to be displayed as the start/end labels of the itinerary
    + start: Trip starting (string, optional)
    + end: Trip ends (string, optional)
    + price (object, required) - object which contains data about pricing
        + currency: `EUR` (enum[string], required) - the currency in which the prices are displayed
            + Posible values: RON, USD, EUR
        + type: `dynamic` (enum[string], required) - type of pricing. can be either per each card, or a global price per trip
       	    + Posible values: dynamic, fixed
        + fixedPriceAmount: (number, required) - the actual price of the trip, only required if `price.type = fixed`

2. Segments
Segments are the itinerary cards.
There are 4 types of segments: flights, accommodation, car ride and experience.

**Flight**
```json
{
	"type" : "flight",
	"options" : [{
		"price" : {
			"discounted" : true,
			"currentAmount" : 70,
			"beforeAmount" : 90
		},
		"title" : "Bucharest to Barcelona via Frankfurt",
		"duration" : 19200,
		"stops" : 0,
		"layovers" : 1,
		"flightSegments" : [{
			"code" : "LH 1419",
			"serviceInfo" : "Lufthansa",
			"duration" : 9000,
			"stops" : 0,
			"pointA" : {
				"name" : "Bucharest",
				"code" : "OTP",
				"date" : "19.05.2016",
				"time" : "13:45 PM"
			},
			"pointB" : {
				"name" : "Frankfurt",
				"code" : "FRA",
				"date" : "19.05.2016",
				"time" : "15:15 PM"
			}
		}, {
			"code" : "LH 1132",
			"serviceInfo" : "Lufthansa",
			"duration" : 7200,
			"stops" : 0,
			"pointA" : {
				"name" : "Frankfurt",
				"code" : "FRA",
				"date" : "19.05.2016",
				"time" : "16:05 PM"
			},
			"pointB" : {
				"name" : "Barcelona",
				"code" : "BCN",
				"date" : "19.05.2016",
				"time" : "18:05 PM"
			},
			"layoverTime" : 3000
		}]
	}]
}
```

+ type (enum[string], required) - Type of card
	+ Posible values: flight, accommodation, carRide, experience
+ milestone (string, required) - text label displayed before the card (the first segment doesn't need this, because of the start milestone of the itinerary)
+ options (array) every card may have more than one option, which means the client can choose from different variants
	+ title (string) - title of card
	+ price (object, required) - price info; must be set if global `price.type = dynamic`; if omitted, no price tag will be shown on the card
		+ currentAmount (number) - price of service; if global itinerary property `price.type = dynamic` this must be set
		+ discounted (boolean) - if the price has a discount; connected to `price.beforeAmount`
		+ beforeAmount (number) - the price before was discounted; must work in concordance with `price.discounted`; if `discounted = true` this property should be set as well 
	+ info (string) - informational text; could be about additional services and costs
	+ duration: 3600 (number, required) - the total duration of flight segments on the card in seconds
	+ stops: 3 (number, required) - total number of stops
	+ layovers: 1 (number, required) - total number of layovers
	+ flightSegments (array, required) - the flight segments
		+ code: (string, required) - company and flight code
		+ serviceInfo: Ryan Air economy class (string, optional) - text to describe the flight; may be company and class
		+ duration: (number, required) - amount of time described in seconds
		+ stops: (number, required) - amount of stops (not layovers)
		+ pointA (object, required) - origin
			+ name: Berlin (required) - name of airport
			+ code: BXN (required) - airport code
			+ date: 19.05.2016 (required) - string of format `DD.MM.YYYY`
			+ time: 10:20 (required) - string of format `HH:mm A`
		+ pointB (object, required) - destination
			+ name: Berlin (required) - name of airport
			+ code: BXN (required) - airport code
			+ date: 19.05.2016 (required) - string of format `DD.MM.YYYY`
			+ time: 10:20 (required) - string of format `HH:mm A`
		+ layoverTime: (number, required) - amount of seconds; first flight segment doesn't need this property to be set

**Accommodation**

```json
{
	"type" : "accommodation",
	"milestone" : "Arrived at Point B",
	"options" : [{
		"price" : {
			"discounted" : false,
			"currentAmount" : 200,
			"beforeAmount" : 0
		},
		"title" : "SmartRoom Barcelona",
		"dateInterval" : {
			"from" : "19.05.2016",
			"to" : "26.05.2016"
		},
		"bookingId": "1146179",
		"tripadvisorId": "7006624",
		"image" : "http://aff.bstatic.com/images/hotel/max500/474/47494189.jpg",
		"address" : "Olzinelles 56-58",
		"description" : "SmartRoom Barcelona is a budget hotel located in central Barcelona, 1 km from Sants Train Station. Decorated in a modern style, each room here will provide you with a flat-screen Smart TV with satellite channels.",
		"latitude" : 41.3720250384618,
		"longitude" : 2.13703218836054
	}]
}
```

+ type (enum[string], required) - Type of card
	+ Posible values: flight, accommodation, carRide, experience
+ milestone (string, required) - text label displayed before the card (the first segment doesn't need this, because of the start milestone of the itinerary)
+ options (array) every card may have more than one option, which means the client can choose from different variants
	+ title (string) - title of card
	+ price (object, required) - price info; must be set if global `price.type = dynamic`; if omitted, no price tag will be shown on the card
		+ currentAmount (number) - price of service; if global itinerary property `price.type = dynamic` this must be set
		+ discounted (boolean) - if the price has a discount; connected to `price.beforeAmount`
		+ beforeAmount (number) - the price before was discounted; must work in concordance with `price.discounted`; if `discounted = true` this property should be set as well 
	+ info (string) - informational text; could be about additional services and costs
	+ dateInterval (object, required)
	    + from: 19.05.2016 (string, required)
	    + to: 26.05.2016 (string, required)
	+ image: `https://en.wikipedia.org/wiki/Barcelona#/media/File:Barcelona_collage.JPG` (string, required) - not required if `bookingId` is provided
	+ description (string, required) - not required if `bookingId` is provided
	+ address (string, required) - not required if `bookingId` is provided
	+ latitude: 41.37202503846175 (number, required) - not required if `bookingId` is provided
	+ longitude: 2.1370321883605357 (number, required) - not required if `bookingId` is provided
	+ bookingId (string, optional) - if this is provided, image, description, address, latitude, longitude will be replaced by booking.com data, if the hotel is found in our db
	+ tripadvisorId (string, optional) - if this is provided, and bookingId is not, image, description, address, latitude, longitude will be replaced by tripadvisor data. else, it's used just to get reviews info

**Car ride**

```json
{
	"type" : "carRide",
	"milestone" : "Arrived in Barcelona",
	"options" : [
		{
			"price" : {
				"discounted" : false,
				"currentAmount" : 15,
				"beforeAmount" : 0
			},
			"title" : "Barcelona airport to Point B",
			"serviceInfo" : "UberSelect",
			"duration" : 1800,
			"pointA" : {
				"name" : "Barcelona airport",
				"date" : "19.05.2016",
				"time" : "09:15 AM"
			},
			"pointB" : {
				"name" : "Point B",
				"date" : "19.05.2016",
				"time" : "09:45 AM"
			}
		}
	]
}
```

+ type (enum[string], required) - Type of card
	+ Posible values: flight, accommodation, carRide, experience
+ milestone (string, required) - text label displayed before the card (the first segment doesn't need this, because of the start milestone of the itinerary)
+ options (array) every card may have more than one option, which means the client can choose from different variants
	+ title (string) - title of card
	+ price (object, required) - price info; must be set if global `price.type = dynamic`; if omitted, no price tag will be shown on the card
		+ currentAmount (number) - price of service; if global itinerary property `price.type = dynamic` this must be set
		+ discounted (boolean) - if the price has a discount; connected to `price.beforeAmount`
		+ beforeAmount (number) - the price before was discounted; must work in concordance with `price.discounted`; if `discounted = true` this property should be set as well 
	+ info (string) - informational text; could be about additional services and costs
	+ serviceInfo: UberX (string, optional) - text to describe the service
	+ duration: 1800 (number, required) - amount of time described in seconds
	+ pointA (object, required) - origin
		+ name: Berlin (required) - name of airport
		+ date: 19.05.2016 (required) - string of format `DD.MM.YYYY`
		+ time: 10:20 (required) - string of format `HH:mm A`
	+ pointB (object, required) - destination
		+ name: Berlin (required) - name of airport
		+ date: 19.05.2016 (required) - string of format `DD.MM.YYYY`
		+ time: 10:20 (required) - string of format `HH:mm A`

**Experience**

```json
{
	"type" : "experience",
	"milestone" : "Things to do in Berlin",
	"options" : [
		{
			"title" : "Tibidabo Amusement Park",
			"image" : "https://upload.wikimedia.org/wikipedia/commons/thumb/a/ae/Tibidabo_%287923343396%29.jpg/1280px-Tibidabo_%287923343396%29.jpg",
			"description" : "Tibidabo Amusement Park, a magical place to be."
		}
	]
}
```

+ type (enum[string], required) - Type of card
	+ Posible values: flight, accommodation, carRide, experience
+ milestone (string, required) - text label displayed before the card (the first segment doesn't need this, because of the start milestone of the itinerary)
+ options (array) every card may have more than one option, which means the client can choose from different variants
	+ title (string) - title of card
	+ price (object, required) - price info; must be set if global `price.type = dynamic`; if omitted, no price tag will be shown on the card
		+ currentAmount (number) - price of service; if global itinerary property `price.type = dynamic` this must be set
		+ discounted (boolean) - if the price has a discount; connected to `price.beforeAmount`
		+ beforeAmount (number) - the price before was discounted; must work in concordance with `price.discounted`; if `discounted = true` this property should be set as well 
		+ image: `https://en.wikipedia.org/wiki/Tibidabo#/media/File:Tibidabo_(7923343396).jpg` (string, required)
		+ description (string, required)

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

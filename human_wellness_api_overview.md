## Overview
The [Human Wellness API](https://reference.humanapi.co/reference#wellness-api-introduction) is the portion of the
API that pulls data from the [wellness data sources](https://bitbucket.org/snippets/suntorytime/rAGXar/wellness-sources-overview)
which includes wearables.  There is also the [Medical API](https://reference.humanapi.co/reference#medical-api-introduction)
which provides access to user's health care records.  This investigation ignored the medical api and focused **ONLY**
on the Wellness API portion.

## Overview of API

Use a bearer token to authenticate such as `-H "Authorization: Bearer demo"`.  See
[authentication users web-guide](https://reference.humanapi.co/docs/web-guide) for details on how to generate these
tokens but this is outside the scope of this document which is just investigating the API once a token has been
generated.

Standard paging and basic filtering can be added to most queries it looks like.  See
[docs for query options](https://reference.humanapi.co/docs/patterns-and-conventions-reference#section-query-options)
for details.

## Sample data queries and responses:

There is [demo data](https://reference.humanapi.co/docs/demo-data) available to see the data format.  These sample
queries use that demo data.

- `curl -H "Authorization: Bearer demo" https://api.humanapi.co/v1/human/medical/test_results`
- `curl -H "Authorization: Bearer demo" https://api.humanapi.co/v1/human/medical/encounters`
- `curl -H "Authorization: Bearer demo" https://api.humanapi.co/v1/human/heart_rate`

Add `|jq --color-output . |less` to more easily process large results on the command line.


## Getting Bearer token for actual users

Documentation on creating a token for the data api can be found at the very bottom of https://reference.humanapi.co/docs/web-guide
most of the info at the top is not related to calling the Data API and is for other use cases (such as connecting 
a user's wearable via your app).  In these cases the portal was used to create the users and the links were manually
emailed to users to complete the connection of their wearables.  This works for testing but obviously in a real
 application the user must be able to connect their wearable fully self-serve via the app.

A request such as the following must be made to generate a token for a specific user.
All values come from `https://portal.humanapi.co/`.  Login to portal and click the Citizen_Health app.
* Click the gear icon on left after logging in to get:
    * client_id
    * client_secret
* Click the users icon on left to get:
    * client_user_id - this is the value in the "UNIQUE ID" column, each user has a different value.


    POST https://auth.humanapi.co/v1/connect/token HTTP/1.1
    Content-Type: application/json
    Cache-Control: no-cache
    
    {
      "client_id"      : "{{client_id}}",
      "client_user_id" : "{{client_user_id}}",
      "client_secret"  : "{{client_secret}}",
      "type"           : "access"
    }
    
The response if successful will look like:

    {
      "access_token": "token here",
      "expires_in": 86400,
      "token_type": "Bearer",
      "refresh_token": "atrt-xbFx9IiL4dhhJpYE8uu_oghfxdWC2bo9EEb_e6eLKYI"
    }
    
If you wish to make API requests for different users you must use the above to generate a token for each user and then
use the appropriate user token when making the Data API requests.

## Users
User 1 and user 2 had connected to human API Apple Health (which can be seen in the json data file attachments) and
 user 3 had connected to human API only Fitbit

### Heart Rate
Docs:  https://reference.humanapi.co/reference#heart-rate

The `https://api.humanapi.co/v1/human/heart_rate` seems to be the most recent data point and 
`https://api.humanapi.co/v1/human/heart_rate/readings` seems to be multiple points (historical) data starting 
with the most recent data first (descending order by time, which of the 3 timestamp fields the docs don't seem to say).

#### Single Heart Rate Readings
Using the command got demo heart rate data

    curl -H "Authorization: Bearer demo" https://api.humanapi.co/v1/human/heart_rate

    {
      "id": "550b8a8e834dd16f259683b2",
      "userId": "52e20cb2fff56aac62000001",
      "timestamp": "2015-03-19T22:48:26.000Z",
      "tzOffset": "+00:00",
      "value": 66,
      "unit": "bpm",
      "source": "ihealth",
      "sourceData": {},
      "createdAt": "2015-03-20T02:48:46.945Z",
      "updatedAt": "2015-03-20T02:48:46.945Z",
      "humanId": "5dc2527186aaf9de560e5841f1a44bd6"
    }

Using this command get actual user's heart rate data (this is user 2 in the files in
 this same directory)

    curl -H "Authorization: Bearer <actual token here>" https://api.humanapi.co/v1/human/heart_rate
  
    {
      "id": "5e97a87541de73e324b9fc6e",
      "userId": "5e932aefe8c4a91fb10c54ac",
      "timestamp": "2020-04-16T00:28:46.519Z",
      "tzOffset": "-05:00",
      "value": 55,
      "unit": "bpm",
      "source": "5541a9678e7192cb4fb34fed",
      "sourceData": {
        "sampleType": "HKQuantityTypeIdentifierHeartRate",
        "originalSource": "com.apple.health"
      },
      "createdAt": "2020-04-16T00:36:05.404Z",
      "updatedAt": "2020-04-16T00:36:05.404Z",
      "humanId": "9cdbc8ba22164289a60aed8ebca2294f"
    }

    
#### Heart Rate Readings
These commands were used to generate the files of example data

    curl -H "Authorization: Bearer demo" https://api.humanapi.co/v1/human/heart_rate/readings |jq
     . >heart_rate_readings_demo_user.json

    curl -H "Authorization: Bearer <actual token here>" https://api.humanapi.co/v1/human/heart_rate/readings |jq
     . >heart_rate_readings_apple-health_real_user_1.json
     
    curl -H "Authorization: Bearer <actual token here>" https://api.humanapi.co/v1/human/heart_rate/readings |jq
    . >heart_rate_readings_apple-health_real_user_2.json

User 3 (had only connected a fitbit) and the heart_rate/readings returned empty list

#### Heart Rate Summaries

    curl -H "Authorization: Bearer <actual token here>" https://api.humanapi.co/v1/human/heart_rate/summaries |jq . >heart_rate_readings_fitbit_summaries_real_user_3.json
    
Summaries were not returned for user 1 and 2 (Apple Health source connected only), empty lists were returned.

One example of a single summary from user 3 (Fitbit source connected only) can be seen here (see file heart_rate_summaries_fitbit_real_user_3.json
for full list of data returned):

      {
        "id": "5e91ed666b8235aee48c3468",
        "userId": "5e91e8bfddf9e63c196b0432",
        "date": "2020-04-11",
        "zones": [
          {
            "name": "Out of Range",
            "max": 93,
            "min": 30,
            "duration": 73020,
            "caloriesBurned": 2202.23
          },
          {
            "name": "Fat Burn",
            "max": 130,
            "min": 93,
            "duration": 11760,
            "caloriesBurned": 1024.16
          },
          {
            "name": "Cardio",
            "max": 158,
            "min": 130,
            "duration": 60,
            "caloriesBurned": 9.47
          },
          {
            "name": "Peak",
            "max": 220,
            "min": 158,
            "duration": 0,
            "caloriesBurned": 0
          }
        ],
        "restingHR": 65,
        "source": "54dc427aaa6b4cb7d6202f01",
        "sourceData": {},
        "createdAt": "2020-04-11T16:16:38.852Z",
        "updatedAt": "2020-04-15T04:12:15.442Z",
        "humanId": "15a86c6f8d63280fdca7784397b8b1ec"
      }

### Body Temperature

I couldn't find any mention of body temperature nor does the
[wellness data sources](https://bitbucket.org/snippets/suntorytime/rAGXar/wellness-sources-overview) show body
temperature as supported.  **This is probably a good question for Human API support staff**.

### Location
Docs: https://reference.humanapi.co/reference#locations

'curl -H "Authorization: Bearer demo" "https://api.humanapi.co/v1/human/locations?limit=2" |jq .'

    [
      {
        "id": "551aa2e4794b3108001226ea",
        "userId": "52e20cb2fff56aac62000001",
        "startTime": "2015-03-29T13:26:48.000Z",
        "endTime": "2015-03-29T13:37:50.000Z",
        "tzOffset": "+03:00",
        "name": "",
        "source": "moves",
        "location": {
          "lat": 54.8927673773,
          "lon": 23.9181339794
        },
        "createdAt": "2015-03-31T13:36:36.921Z",
        "updatedAt": "2015-03-31T13:36:36.921Z",
        "humanId": "5dc2527186aaf9de560e5841f1a44bd6"
      },
      {
        "id": "551aa2e4794b310800122694",
        "userId": "52e20cb2fff56aac62000001",
        "startTime": "2015-03-27T18:06:26.000Z",
        "endTime": "2015-03-29T13:14:34.000Z",
        "tzOffset": "+02:00",
        "name": "Home",
        "source": "moves",
        "location": {
          "lat": 54.906589823,
          "lon": 23.9631395983
        },
        "createdAt": "2015-03-31T13:36:36.514Z",
        "updatedAt": "2015-03-31T13:36:36.514Z",
        "humanId": "5dc2527186aaf9de560e5841f1a44bd6"
      }
    ]
    
Note that user 1, 2 (apple health), nor user 3 (fitbit) returned ANY locations (all empty lists returned)

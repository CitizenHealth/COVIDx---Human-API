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

### Heart Rate
Docs:  https://reference.humanapi.co/reference#heart-rate

Using the command `curl -H "Authorization: Bearer demo" https://api.humanapi.co/v1/human/heart_rate` will output:

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

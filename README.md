# COVIDx & Human API
How we're getting user wearable data

***Urgent***

Someone needs to work with https://www.humanapi.co/wearable-api so we can get wearable connections going.

Here is the API documentation: https://reference.humanapi.co/reference#wellness-api-introduction

How this will work in COVIDx:

A user has two choices to submit data: 1) Self-reporting 2) Connect a wearable

When they click "Connect a Wearable," the Human API embedded interface ([Human Connect](https://reference.humanapi.co/v2.1/docs/overview-of-human-connect)) will be activated to allow the user to choose their wearable source: 

The Human Connect overview explains this process well: https://reference.humanapi.co/v2.1/docs/overview-of-human-connect

We want to query data for Heart Rate/Summaries, Activity Segments/Summaries, Sleep/Summaries, Blood Oxygen, Weight, & Height.

We will start running models on this data to predict when someone is getting ill. 

In the MVP, the user will see their resting heart rate & estimated temperature (user submitted).

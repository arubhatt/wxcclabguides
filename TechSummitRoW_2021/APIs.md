---
title: 'Lab 8: # Webex Contact Center APIs'
---

# Table of Contents

- [All New Webex Contact Center APIs (new version)](#all-new-webex-contact-center-apis-new-version)

  - [Part 1: Introduction to the New Webex CC APIs](#part-1-introduction-to-the-new-webex-cc-apis)
    - [1. Walkthrough of Auth Flow](#1-walkthrough-of-auth-flow)
    - [2. Initial Setup of Sample App](#2-initial-setup-of-sample-app)
  - [Part 2: Reporting APIs](#part-2-reporting-apis)
    - [1. Executing "Get Tasks" API](#1-executing-get-tasks-api)
    - [2. Executing "Get Queue Statistics" API](#2-executing-get-queue-statistics-api)
    - [3. Executing "Get Agent Activities" API](#3-executing-get-agent-activities-api)
    - [4. Executing "Get Agent Statistics" API](#4-executing-get-agent-statistics-api)
  - [Part 3: Call Recording APIs](#part-3-call-recording-apis)
    - [Working with "List Captures" API](#working-with-list-captures-api)
  - [Part 4: Configuration APIs](#part-4-configuration-apis)
    - [1. Creating a Site with "Create Sites" API](#1-creating-a-site-with-create-sites-api)
    - [2. Changing the Site name with "Update Site By ID" API](#2-changing-the-site-name-with-update-site-by-id-api)
    - [3. Practicing with Bulk Upload](#3-practicing-with-bulk-upload)

- [APIv1 (Legacy version)](#apiv1-legacy-version)
  - [Part 1: Legacy 1.0 APIs: CSR, CSR Query](#part-1-legacy-10-apis-csr-csr-query)
    - [1. Verify that you have an Analyzer Report created and a Call recording exists](#1-verify-that-you-have-an-analyzer-report-created-and-a-call-recording-exists)
    - [2. Verify you have Postman Installed](#2-verify-you-have-postman-installed)
    - [3. Create a GET CSR Request](#3-create-a-get-csr-request)
    - [4. Plug these into Postman as described in the video](#4-plug-these-into-postman-as-described-in-the-video)
    - [5. Inspect the Browser to create a CSR Query](#5-inspect-the-browser-to-create-a-csr-query)
    - [6. Copy only the content after query and before the ending braces. I.e this content as an example\*\*](#6-copy-only-the-content-after-query-and-before-the-ending-braces-ie-this-content-as-an-example--)
    - [7. Encode this in a URL encoded format. i.e Copy this string and paste it in an encoder](#7-encode-this-in-a-url-encoded-format-ie-copy-this-string-and-paste-it-in-an-encoder)
    - [8. Plug this into the query parameters. Execute the Query](#8-plug-this-into-the-query-parameters-execute-the-query)
    - [9. Add an Accept Header of text/csv](#9-add-an-accept-header-of-textcsv)
  - [Part 2: Legacy 1.0 APIs: CARs and activity chains](#part-2-legacy-10-apis-cars-and-activity-chains)
    - [1. Understand Contact Start and Contact End Timestamps are in Unix/ Epoch format](#1-understand-contact-start-and-contact-end-timestamps-are-in-unix-epoch-format)
    - [2. Copy the Same CSR Request GET CSR Request and tweak it to a CAR request.](#2-copy-the-same-csr-request-get-csr-request-and-tweak-it-to-a-car-request)
    - [3. Complete Building out the above requests.](#3-complete-building-out-the-above-requests)

# Introduction

**Important:** Changes to the API Lab.

- The New Webex Contact Center APIs are now available on **[developer.webex-cx.com](https://developer.webex-cx.com)** and use OAuth2 as opposed to One-time API Keys.
- The Legacy APIs (v1) are still available and are documented in the later section of this lab.
- The lab assumes that you will use the All New Webex Contact Center APIs which are documented below.
- For a quick start and overview, refer to the Overview / Quick start section.

# All New Webex Contact Center APIs (New Version)

## Part 1: Introduction to the New Webex CC APIs

> This is the introduction video. It explains what API we have, what application and authorization mechanisms exist and how to get started.

<iframe width="1024" height="500" src="https://www.youtube-nocookie.com/embed/EiCXsxX2EHI?rel=0" title="WxCC Lab #8 Part 1:Introduction to the New Webex Contact Center APIs and Sample Ap" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

### 1. Walkthrough of Auth Flow

- To develop applications with the new APIs, you must build an integration with Webex.

- See **[Contact Center Dev Portal Docs](https://developer.webex-cx.com/documentation/getting-started)** for all the details.

Here is a summary:

**API Access- Registering an Application(Early Access)**

Submit a Request to Obtain Access

- The New Webex Contact Center APIs are accessed by using Webex OAuth2

- OAuth2 by design, requires a client ID, clinet secret, callback URL(Redirect URL)

- Fill up the **[API App Request Form](https://app.smartsheet.com/b/form/7af787e752834bbfba604bfc85a5eff1)** with your details to register your app with webex contact center. We will provide you with a Client ID and Client Secret.

**Note:** `This is a temporary form for Early Access. The Automated Sign up and App Management & Integration is coming soon to the developer portal.`

- Use the Client ID, Client Secret, Redirect URI (callback URL) to obtain access_token for your Org. Use the Access token to hit the API inside of the Authorization header.

`Authorization: Bearer yourAccessToken`

- Use the refresh_token before it expires to renew your access_token - and get a new set of Access and Refresh Tokens.

`By Default, the access_token is valid for 12 hours and the refresh token is valid for 60 days, however using another call for the refresh token will give you a new set of tokens. Maintaining this access is important for perpetual access. One can write a simple scheduler that performs this background refresh activity.`

**OAuth2 Mechanism - Implications**

- With the OAuth2 support- Will require a "One Time Onboarding" User interface to obtain access to an org

- "Login with Webex" > Button to access an org

- You will register an App with us and use it for N orgs

- Have each Org administrator login into your App at least once to obtain the tokens

- This is a "Required Onboarding " Process

- Once you obtain an Org's access_token and refresh_token, you can access their data

**OAuth2 Access Token Flow**

- Your App redirects to authorize with Webex (GET)

1. GET `https://webexapis.com/v1/authorize?client_id=______&response_type=code&redirect_uri=_https://your-app/auth___&scope=cjp:config_read&state=set_state_here`

- Admin user COMPLETES login.Redirect back to your App (GET)

2. GET https://your-app/auth?code=\_\_\_unique_code_sent

- Your App usess this code - API Endpoint - and then sends a POST request back to Webex

- Request Access Token AND/OR Refresh Tokens (POST)

3. POST https://webexapis.com/v1/access_token`
   application/x-www-form-urlencoded

```javascript
{"access_token":"ZDI3MGEyYzQtNmFlNS00NDNhLWFlNzAtZGVjNjE0MGU1OGZmZWNmZDEwN2ItYTU3",
"expires_in":1209600,
"refresh_token":"MDEyMzQ1Njc4OTAxMjM0NTY3ODkwMTIzNDU2Nzg5MDEyMzQ1Njc4OTEyMzQ1Njc4",
"refresh_token_expires_in":7776000 }
```

> Expiry in seconds and required x-www-form-urlencoded values include

> grant*type = \_authorisation_code OR refresh_token*

> client_id

> client_secret

> code

> redirect_uri

### 2. Initial Setup of Sample App

- Login with the POD admin account to https://developer.webex-cx.com/.

- Go to > Documentation > API Reference.

- Follow the instructions in the video to request access to your sample app.

- The sample app is available here :https://github.com/arubhatt/webexcc-api-sample

- Follow the instructions in the video to clone the repository and run the app.

## Part 2: Reporting APIs

> In this video we will go through the developer portal and execute your first API calls through Postman.

<iframe width="1024" height="576" src="https://www.youtube-nocookie.com/embed/CND6N_HpHs8?rel=0" title="WxCC Lab #4 Part 4:Reporting APIs" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

- Login with the POD admin account to https://developer.webex-cx.com/.

- Go to > Documentation > API Reference.

### 1. Executing "Get Tasks" API

- Go to the Tasks section.

- Click on GET Tasks > Try Out

- Fetch all the tasks using the "From Date/time" in Epoch milliseconds.

- To get the Epoch time use [epochconverter](https://www.epochconverter.com)

- Enter the Org ID and channel type > Press Run

The same request can be drafted on Postman using your personal Bearer Token.

Example of a GET Task: https://api.wxcc-us1.cisco.com/v1/tasks

- Create a new collection in Postman

- Under that add a request

- Enter the request URL copied from the developer portal. Make changes to the from and to times and the org ID to refelect your tenant.

- Under Headers enter the _Key:_ _Authorization_ and _Value _: _Bearer 'Your Bearer token copied from the developer portal'_

- Under Parameters enter the values you wil be passing as shown in the video

- Click on Send

### 2. Executing "Get Queue Statistics" API

- Go to the Queues section.

- Click on Get Queue Statistics > Try Out

- Enter the from date, to date and the interval

- Click on Run

To try this out in Postman follow the steps outlined in the previous example

### 3. Executing "Get Agent Activities" API

- Go to the Agents section.

- Click on get Agent Activities > Try Out

- Enter the required parameters as explained in the video .Click on Run

To try this out in Postman follow the steps outlined in the previous example

### 4. Executing "Get Agent Statistics" API

- Go to the Agents section.

- Click on get Agent Statistics > Try Out

- Enter the required parameters as explained in the video .Click on Run

To try this out in Postman follow the steps outlined in the previous example

## Part 3: Call Recording APIs

> In this video we will concentrate on "Captures" API. Captures are audio recordings that can be downloaded through API.

<iframe width="1024" height="576" src="https://www.youtube-nocookie.com/embed/iehZnNvRzVc?rel=0" title="WxCC Lab #5 Part 5:Call Recording APIs" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

### Working with "List Captures" API

- Run the Get Tasks API as explained in the video and get teh required task IDs

- Go to Captures section

- Click on List Captures > Try Out

- Enter the parameters :orgid, taskids,urlexpiration

- Click on Run

- Use the Filepath in the response to download and listen to the recording

- Enter the required parameters as explained in the video .Click on Run

To try this out in Postman follow the steps outlined in the previous example

## Part 4: Configuration APIs

> This is the bonus section of New WxCC APIs where you will be able to create/change settings in the Webex Contact Center by using the Configuration APIs.

<iframe width="1024" height="576" src="https://www.youtube-nocookie.com/embed/W5C3AvTEYmY?rel=0" title="WxCC Lab #5 Part 6:Configuration APIs" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

### 1. Creating a Site with "Create Sites" API

- Go to Sites section

- Click on Create Sites > Try Out

- Enter the parameters :active,orgid, multimediaprofileid,name

- Click on Run

To try this out in Postman follow the steps outlined in the previous example

### 2. Changing the Site name with "Update Site By ID" API

- Go to Sites section

- Click on Update Site by ID > Try Out

- Enter the parameters orgid and the id (The site ID that was created in the previous exercise)

- In the request body update the orgid,id, multimediaprofileid, name

- Click on Run

To try this out in Postman follow the steps outlined in the previous example

### 3. Practicing with Bulk Upload

Follow the instructions in the vdeo to use Postman and create mutiple sites using a csv file

# APIv1 (legacy version)

The following section covers the `Older Webex Contact Center APIs (Legacy) - Common to Webex Contact Center 1.0 and the New platform.`
Although these APIs are available today - we recommend to only use the New Webex Contact Center APIs documented in the new Developer portal.

## Lab Objective

- This lab is designed to ensure you are able to retrieve reporting data using the Legacy APIs from version 1.0 - carried forward into the New Webex Contact Center platform.

- We will explore both the flavors of accessing reporting and call recording data - authenticating using an API Key using the legacy API and using a Bearer token with the new Webex Contact Center APIs on the developer portal.

## Pre-requisite

Before you begin this lab:

1. You already have test calls made (Lab 1 and Lab 2).
2. You understand how to create Analyzer reports. Review the analyzer lab if you need more information (Lab 6).
3. Call Recordings are visible under recording management.
4. You already have an API key generated for `admin1podX` where X is your pod number. View the video below to verify this from the Portal. Reach out to the lab proctors if you require any information around this.
5. You have the Postman client downloaded and installed to make queries to the Webex Contact Center APIs. Download it here: [Download and Install Postman](https://www.postman.com)

### Quick Links

> Control Hub: **[https://admin.webex.com](https://admin.webex.com){:target="\_blank"}**\
> Portal: **[https://portal.wxcc-us1.cisco.com/portal](https://portal.wxcc-us1.cisco.com/portal){:target="\_blank"}**\
> WxCC Analyzer: **[https://analyzer.wxcc-us1.cisco.com/analyzer/home](https://analyzer.wxcc-us1.cisco.com/analyzer/home){:target="\_blank"}**\
> HMAC Generator: **[https://www.liavaag.org/English/SHA-Generator/HMAC/](https://www.liavaag.org/English/SHA-Generator/HMAC/){:target="\_blank"}**\
> Legacy V1.0 endpoint - Resource: **[https://rest.wxcc-us1.cisco.com/aws/api/{record-type}/{id}](https://rest.wxcc-us1.cisco.com/aws/api/{record-type}/{id}){:target="\_blank"}**\
> Legacy V1.0 endpoint - Query: **[https://rest.wxcc-us1.cisco.com/aws/api/{record-type}?q={your-query}](https://rest.wxcc-us1.cisco.com/aws/api/{record-type}?q={your-query}){:target="\_blank"}**. Resources can be of type: `csrs`, `cars`, `asrs`, `aars`\
> Developer Portal: **[https://developer.webex-cx.com/](https://developer.webex-cx.com/){:target="\_blank"}**\
> The Official Developer docs: **[https://developer.webex-cx.com](https://developer.webex-cx.com){:target="\_blank"}**\

## Part 1: Legacy 1.0 APIs: CSR, CSR Query

> The following video outlines the pre-requisites as well as Part 1

<iframe width="1024" height="576" src="https://www.youtube-nocookie.com/embed/S0OO6rxciqo?rel=0" title="WxCC Lab #8 Part 1: Legacy 1.0 APIs: CSR, CSR Query" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

### 1. Verify that you have an Analyzer Report created and a Call recording exists

- Follow the steps outlined in the Analizer Lab to create an analyzer report by logging into Analyzer and creating a CSR report. It is assumed you already have completed the Analyzer Lab in the previous sections.

### 2. Verify you have Postman Installed

- Install Postman from the link.

- Create a Folder to store your requests.

- Create variables to hold critical information in all the requests. From Header and the Authorization Header.

### 3. Create a GET CSR Request

- Review the CSRs in your setup using Analyzer.

- Draft the GET CSR request using the format

**Authorization API Key**:
To generate the HMAC (used in the Header of the API call), you will need to encode your user ID with the API key, using the HMAC SHA1 algorithm.

You can use any HMAC generator online to convert the API Key into an HMAC Authorization Token that can be used in the header of the HTTPS request - for any operations against the API.
(E.g: https://www.liavaag.org/English/SHA-Generator/HMAC/)

Input:

From (UserID) : `userID@portal-controlhub.com`

TEXT

Key:

API Key: `myRandomApiKeY`

TEXT

Encoding: SHA1

Output: Base64

Headers after HMAC encoding:

    Authorization: `EnCoDeDaPiKeY=`;tenantId=xx
    ** xx is the tenant ID. To identify your respective tenantID, pls open any one of the Analyzer reports and in the URL you will have the tId

    From: `userID@portal-controlhub.com`

### 4. Plug these into Postman as described in the video

- Run the GET CSR request using:

`https://rest.wxcc-us1.cisco.com/aws/api/csrs/{your-example-contact-session-id-here}`

### 5. Inspect the Browser to create a CSR Query

- On the Analyzer Report for CSRs Today - Inspect the report - Right Click > Inspect.

- Go to the network tab > Refresh > Search for a request in the format: `dataQuery?GET_DATA=`

- Look at the response.

- Copy this response out into a t`ext editor.

- Name the file `analyzer.json` to enable automatic formatting as shown below, for ease of analysis.

- Copy the content after the `query: { ... }` tags. That is your Query that you will URL encode to fetch the required reporting metadata.

Example:

```javascript
"query": {
			"anchorId": "11894",
			"dateBegin": [
				1612155600000
			],
			"dateEnd": [
				1612328400000
			],
			"timezone": "America/New_York",
			"numberOfRecords": 1000000,
			"lastNRecords": false,
			"activityType": "INTERACTION",
			"aggregateQueryProperties": {
				"computeInterval": 0,
				"computeIntervalUnit": "NONE",
				"lookbackCount": 0,
				"frequency": 0,
				"movingWindow": false,
				"cumulative": false,
				"rowSegmentSet": [],
				"columnSegmentSet": [],
				"queryType": "TEMPORAL",
				"requestType": "PROFILE",
				"intervalAxis": "ROW",
				"computeSummaries": true
			},
			"aggregations": [
				{
					"id": 0,
					"filterGroups": [
						{
							"operator": "AND",
							"valueFilters": []
						}
					],
					"aggregationType": "VALUE",
					"computeColumnName": "sid"
				}
			]
		}

```

### 6. Copy only the content after `query` and before the ending braces. I.e this content as an example\*\*

```javascript
{
			"anchorId": "11894",
			"dateBegin": [
				1612155600000
			],
			"dateEnd": [
				1612328400000
			],
			"timezone": "America/New_York",
			"numberOfRecords": 1000000,
			"lastNRecords": false,
			"activityType": "INTERACTION",
			"aggregateQueryProperties": {
				"computeInterval": 0,
				"computeIntervalUnit": "NONE",
				"lookbackCount": 0,
				"frequency": 0,
				"movingWindow": false,
				"cumulative": false,
				"rowSegmentSet": [],
				"columnSegmentSet": [],
				"queryType": "TEMPORAL",
				"requestType": "PROFILE",
				"intervalAxis": "ROW",
				"computeSummaries": true
			},
			"aggregations": [
				{
					"id": 0,
					"filterGroups": [
						{
							"operator": "AND",
							"valueFilters": []
						}
					],
					"aggregationType": "VALUE",
					"computeColumnName": "sid"
				}
			]
}
```

### 7. Encode this in a URL encoded format. i.e Copy this string and paste it in an encoder

Example Site (3rd Party):
https://meyerweb.com/eric/tools/dencoder/

- Paste the content.

- Click Encode.

It will give you this text.

```
%7B%0A%09%09%09%22anchorId%22%3A%20%2211894%22%2C%0A%09%09%09%22dateBegin%22%3A%20%5B%0A%09%09%09%091612155600000%0A%09%09%09%5D%2C%0A%09%09%09%22dateEnd%22%3A%20%5B%0A%09%09%09%091612328400000%0A%09%09%09%5D%2C%0A%09%09%09%22timezone%22%3A%20%22America%2FNew_York%22%2C%0A%09%09%09%22numberOfRecords%22%3A%201000000%2C%0A%09%09%09%22lastNRecords%22%3A%20false%2C%0A%09%09%09%22activityType%22%3A%20%22INTERACTION%22%2C%0A%09%09%09%22aggregateQueryProperties%22%3A%20%7B%0A%09%09%09%09%22computeInterval%22%3A%200%2C%0A%09%09%09%09%22computeIntervalUnit%22%3A%20%22NONE%22%2C%0A%09%09%09%09%22lookbackCount%22%3A%200%2C%0A%09%09%09%09%22frequency%22%3A%200%2C%0A%09%09%09%09%22movingWindow%22%3A%20false%2C%0A%09%09%09%09%22cumulative%22%3A%20false%2C%0A%09%09%09%09%22rowSegmentSet%22%3A%20%5B%5D%2C%0A%09%09%09%09%22columnSegmentSet%22%3A%20%5B%5D%2C%0A%09%09%09%09%22queryType%22%3A%20%22TEMPORAL%22%2C%0A%09%09%09%09%22requestType%22%3A%20%22PROFILE%22%2C%0A%09%09%09%09%22intervalAxis%22%3A%20%22ROW%22%2C%0A%09%09%09%09%22computeSummaries%22%3A%20true%0A%09%09%09%7D%2C%0A%09%09%09%22aggregations%22%3A%20%5B%0A%09%09%09%09%7B%0A%09%09%09%09%09%22id%22%3A%200%2C%0A%09%09%09%09%09%22filterGroups%22%3A%20%5B%0A%09%09%09%09%09%09%7B%0A%09%09%09%09%09%09%09%22operator%22%3A%20%22AND%22%2C%0A%09%09%09%09%09%09%09%22valueFilters%22%3A%20%5B%5D%0A%09%09%09%09%09%09%7D%0A%09%09%09%09%09%5D%2C%0A%09%09%09%09%09%22aggregationType%22%3A%20%22VALUE%22%2C%0A%09%09%09%09%09%22computeColumnName%22%3A%20%22sid%22%0A%09%09%09%09%7D%0A%09%09%09%5D%0A%7D
```

### 8. Plug this into the query parameters. Execute the Query

For example, the query is :

**https://rest.wxcc-us1.cisco.com/aws/api/csrs?q=`your_query_here`**

i.e

**https://rest.wxcc-us1.cisco.com/aws/api/csrs?q=`%7B%0A%09%09%09%22anchorId%22%3A%20%2211894%22%2C%0A%09%09%09%22dateBegin%22%3A%20%5B%0A%09%09%09%091612155600000%0A%09%09%09%5D%2C%0A%09%09%09%22dateEnd%22%3A%20%5B%0A%09%09%09%091612328400000%0A%09%09%09%5D%2C%0A%09%09%09%22timezone%22%3A%20%22America%2FNew_York%22%2C%0A%09%09%09%22numberOfRecords%22%3A%201000000%2C%0A%09%09%09%22lastNRecords%22%3A%20false%2C%0A%09%09%09%22activityType%22%3A%20%22INTERACTION%22%2C%0A%09%09%09%22aggregateQueryProperties%22%3A%20%7B%0A%09%09%09%09%22computeInterval%22%3A%200%2C%0A%09%09%09%09%22computeIntervalUnit%22%3A%20%22NONE%22%2C%0A%09%09%09%09%22lookbackCount%22%3A%200%2C%0A%09%09%09%09%22frequency%22%3A%200%2C%0A%09%09%09%09%22movingWindow%22%3A%20false%2C%0A%09%09%09%09%22cumulative%22%3A%20false%2C%0A%09%09%09%09%22rowSegmentSet%22%3A%20%5B%5D%2C%0A%09%09%09%09%22columnSegmentSet%22%3A%20%5B%5D%2C%0A%09%09%09%09%22queryType%22%3A%20%22TEMPORAL%22%2C%0A%09%09%09%09%22requestType%22%3A%20%22PROFILE%22%2C%0A%09%09%09%09%22intervalAxis%22%3A%20%22ROW%22%2C%0A%09%09%09%09%22computeSummaries%22%3A%20true%0A%09%09%09%7D%2C%0A%09%09%09%22aggregations%22%3A%20%5B%0A%09%09%09%09%7B%0A%09%09%09%09%09%22id%22%3A%200%2C%0A%09%09%09%09%09%22filterGroups%22%3A%20%5B%0A%09%09%09%09%09%09%7B%0A%09%09%09%09%09%09%09%22operator%22%3A%20%22AND%22%2C%0A%09%09%09%09%09%09%09%22valueFilters%22%3A%20%5B%5D%0A%09%09%09%09%09%09%7D%0A%09%09%09%09%09%5D%2C%0A%09%09%09%09%09%22aggregationType%22%3A%20%22VALUE%22%2C%0A%09%09%09%09%09%22computeColumnName%22%3A%20%22sid%22%0A%09%09%09%09%7D%0A%09%09%09%5D%0A%7D`**

### 9. Add an Accept Header of text/csv

- Add another header of `Accept` of type `text/csv` to accept the raw CSV data from Analyzer for those CSR records.

  `Accept:` `text/csv`

## Part 2: Legacy 1.0 APIs: CARs and activity chains

<iframe width="1024" height="576" src="https://www.youtube-nocookie.com/embed/PSTexkK2cxs?rel=0" title="WxCC Lab #8 Part 2: Legacy 1.0 APIs: CARs and activity chains" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

### 1. Understand Contact Start and Contact End Timestamps are in Unix/ Epoch format

- Epoch time for fields `cstts` (Contact Start Time Stamp) and `cetts` (Contact end Time Stamp) can be understood by looking at the Epoch converter [https://www.epochconverter.com/](https://www.epochconverter.com/){:target="\_blank"}

### 2. Copy the Same CSR Request GET CSR Request and tweak it to a CAR request.

- A CAR (Contact Activity Record) is in the format : https://rest.wxcc-us1.cisco.com/aws/api/cars/{contactsession}-{timestamp}-{event}

- Contact Session Record : CSR

- Timestamp : The `cstts` Contact start time stamp of the event

- Event: the event name: `new`, `ivr-connected`, `parked`, `ended` etc.

Few examples:

https://rest.wxcc-us1.cisco.com/aws/api/cars/74d98c29-39b4-4e1e-81fa-0ce0ae5aebb0-1612238768144-new

https://rest.wxcc-us1.cisco.com/aws/api/cars/74d98c29-39b4-4e1e-81fa-0ce0ae5aebb0-1612238769933-ivr-connected

https://rest.wxcc-us1.cisco.com/aws/api/cars/74d98c29-39b4-4e1e-81fa-0ce0ae5aebb0-1612238790724-parked

> Substitute your values by Chaining the activity event types like `new` > `ivr-connected` > `parked` and retrieve the `cetts` for those events to plug in the epoch time stamps.

### 3. Complete Building out the above requests.

- Understand that you can start the Analyzer request chain by pulling a CSR report.

- From there, you can fetch the list of CSRs iteratively, if doing this programmatically.

- For Each CSR, you can retrieve the details of every CAR that is an activity within the session. Each CAR has an associated state.

- Every CAR has a reference to the CSR, which is in the `session` attribute within the object.
-

### Congratulations, you have completed **ALL section**. Well done!!!

### We would like to keep track of your progress and make sure that we are giving you effective support. Please take approximately one minute to complete the short survey.

<script>
function celeButton() 
	{
	window.open("https://app.smartsheet.com/b/form/42c2c1f4e71940088ad0ea8053ac3006", '_blank');
	document.body.style.backgroundImage="url('https://media.giphy.com/media/PMV7yRpwGO5y9p3DBx/giphy.gif')";
	}
</script>

<div id="button-row">
	<button onclick="celeButton()" style="
  border-radius: 5px;
  background-color: rgb(116,191,75);
  padding: 10px;">Take Survey and Finish Labs</button>
</div>

<br />
<br />
&nbsp;
&nbsp;

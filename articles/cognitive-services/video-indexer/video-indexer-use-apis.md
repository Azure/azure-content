---
title: Use Azure Video Indexer API | Microsoft Docs
description: This article shows how to get started using the Video Indexer API.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre

ms.service: cognitive-services
ms.topic: article
ms.date: 08/08/2017
ms.author: juliako

---
# Use Azure Video Indexer API

Video Indexer consolidates various audio and video artificial intelligence (AI) technologies offered by Microsoft in one integrated service, making development simpler. The APIs are designed to enable developers to focus on consuming Media AI technologies without worrying about scale, global reach, availability, and reliability of cloud platform. You can use the API to upload your files, get detailed video insights, get URLs of insight and player widgets in order to embed them into your application, and other tasks.

This article shows how the developers can take advantage of the [Video Indexer API]https://api-portal.videoindexer.ai/). To read a more detailed overview of the Video Indexer service, see the [overview](video-indexer-overview.md) article.

## Subscribe to the API

1. Sign in.

	To start developing with Video Indexer, you must first Sign In to the [Video Indexer](https://api-portal.videoindexer.ai/) portal. 
	
	![Sign up](./media/video-indexer-use-apis/video-indexer-api01.png)

	If signing in with an AAD account (for example, alice@contoso.onmicrosoft.com) you must go through two preliminary steps: 
	
	1. 	Contact us at visupport@microsoft.com to register your AAD organization’s domain (contoso.onmicrosoft.com).
	2. 	Your AAD organization’s admin must first sign in to grant the portal permissions to your org. To do this, the organization's admin must navigate to https://api-portal.videoindexer.ai/signin-callback?provider=Aad, sign in and give consent.
	
2. Subscribe.

	Select the [Products](https://api-portal.videoindexer.ai/products) tab. Then, select Authorization and subscribe. 
	
	![Sign up](./media/video-indexer-use-apis/video-indexer-api02.png)
	
	Once you subscribe, you will be able to see your subscription and your primary and secondary keys. The keys should be protected. The keys should only be used by your server code. They should not be available on the client side (.js, .html, etc.).

	![Sign up](./media/video-indexer-use-apis/video-indexer-api03.png)


## Obtaining access token using the Authorization API

Once you subscribed to the Authorization API, you will be able to obtain access tokens. These access tokens are used to authenticate against the Operations API. 

Each call to the Operations API should be associated with an access token, matching the authorization scope of the call.
- User level -  user level access tokens let you perform operations on the user level. E.g. get associated accounts.
- Account level – account level access tokens let you perform operations on the account level. E.g. Upload video, list all videos, create a language model, etc.
- Video level – video level access tokens let you perform operations on specific videos. E.g. get video insights, download captions, get widgets, etc. 

Access tokens expires after 1 hour. Make sure your access token is valid before using the Operations API. If expires, call the Authorization API again to get a new access token.

 
You are ready to start integrating with the API. Find [the detailed description of each Video Indexer REST API](https://videobreakdown.portal.azure-api.net/docs/services/582074fb0dc56116504aed75/operations/5857caeb0dc5610f9ce979e4).

## Recommendations

This section lists some recommendations when using Video Indexer API.

- If you are planning to upload a video, it is recommended to place the file in some public network location (for example, OneDrive). Get the link to the video and provide the URL as the upload file param. 
- When you call the API that gets video breakdowns for the specified video, you get a detailed JSON output as the response content. [See details about the returned JSON in this topic](video-indexer-output-json.md).

## Code sample

The following C# code snippet demonstrates the usage of all the Video Indexer APIs together.

```csharp
    var apiUrl = "https://api.videoindexer.ai";
    var accountId = "..."; 
    var location = "westus2";
    var apiKey = "..."; 

    System.Net.ServicePointManager.SecurityProtocol = System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

    // create the http client
    var handler = new HttpClientHandler(); 
    handler.AllowAutoRedirect = false; 
    var client = new HttpClient(handler);
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey); 

    // obtain account access token
    var accountAccessTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/AccessToken?allowEdit=true").Result;
    var accountAccessToken = accountAccessTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // upload a video
    var content = new MultipartFormDataContent();
    Debug.WriteLine("Uploading...");
    var videoUrl = "..."; // replace with the video url 
    var uploadRequestResult = client.PostAsync(string.Format($"{apiUrl}/{location}/Accounts/{accountId}/Videos?accessToken={accountAccessToken}&name=some_name&description=some_description&privacy=private&partition=some_partition&videoUrl={videoUrl}"), content).Result;
    var uploadResult = uploadRequestResult.Content.ReadAsStringAsync().Result;

    // get the video id from the upload result
    var videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
    Debug.WriteLine("Uploaded");
    Debug.WriteLine("Video ID: " + videoId);           

    // obtain video access token            
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
    var videoTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/Videos/{videoId}/AccessToken?allowEdit=true").Result;
    var videoAccessToken = videoTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key"); //workaround

    // wait for the video index to finish
    while (true)
    {
	Thread.Sleep(10000);

	var videoGetIndexRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/Index?accessToken={videoAccessToken}&language=English").Result;
	var videoGetIndexResult = videoGetIndexRequestResult.Content.ReadAsStringAsync().Result;

	var processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

	Debug.WriteLine();
	Debug.WriteLine("State:");
	Debug.WriteLine(processingState);

	// job is finished
	if (processingState != "Uploaded" && processingState != "Processing")
	{
	    Debug.WriteLine();
	    Debug.WriteLine("Full JSON:");
	    Debug.WriteLine(videoGetIndexResult);
	    break;
	}

    }

    // search for the video
    var searchRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/Search?accessToken={accountAccessToken}&id={videoId}").Result;
    var searchResult = searchRequestResult.Content.ReadAsStringAsync().Result;
    Debug.WriteLine();
    Debug.WriteLine("Search:");
    Debug.WriteLine(searchResult);

    // get insights widget url
    var insightsWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/InsightsWidget?accessToken={videoAccessToken}&widgetType=Keywords&allowEdit=true").Result;
    var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
    Debug.WriteLine("Insights Widget url:");
    Debug.WriteLine(insightsWidgetLink);

    // get player widget url
    var playerWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/PlayerWidget?accessToken={videoAccessToken}").Result;
    var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
    Debug.WriteLine();
    Debug.WriteLine("Player Widget url:");
    Debug.WriteLine(playerWidgetLink);

```

## Next steps

[Examine details of the output JSON](video-indexer-output-json.md).

## See also

[Video Indexer overview](video-indexer-overview.md)

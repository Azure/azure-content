
## What is Protected Materials Detection (Public Preview)? 
The protected materials text describes language that matches known text content (e.g song lyrics, articles, recipes, selected web content). Protected Materials Detection with LLMs encompasses model behaviors that potentially violate ownership rights over content that is potentially protected by copyright.  
Many customers and end users are concerned about the risk of IP infringement claims when integrating and using Generative AI.  This is due to inquiries by authors and artists regarding how their own work is being used in conjunction with AI models and services. To address the above concerns, this feature detects Protected Materials in English content including songs, news, recipes, and selected web content (i.e. WebMD).  


## Prerequisites

* An Azure subscription - [Create one for free](https://azure.microsoft.com/free/cognitive-services/) 
* Once you have your Azure subscription, <a href="https://aka.ms/acs-create"  title="Create a Content Safety resource"  target="_blank">create a Content Safety resource </a> in the Azure portal to get your key and endpoint. Enter a unique name for your resource, select the subscription you entered on the application form, and select a resource group, supported region, and supported pricing tier. Then select **Create**.
  * The resource takes a few minutes to deploy. After it finishes, Select **go to resource**. In the left pane, under **Resource Management**, select **Subscription Key and Endpoint**. The endpoint and either of the keys are used to call APIs.
* [cURL](https://curl.haxx.se/) installed

## Analyze Text Content for Protected Materials Detection

The following section walks through a sample request with cURL. Paste the command below into a text editor, and make the following changes.

1. Replace `<endpoint>` with the endpoint URL associated with your resource.
1. Replace `<your_subscription_key>` with one of the keys that come with your resource.
1. Optionally, replace the `"text"` field in the body with your own text you'd like to analyze.
    > [!TIP]
    > Text size and granularity
    >
    > The default maximum length for text submissions is **1K** characters. Protected Materials Detection only runs on the LLM completion. 
```shell
curl --location --request POST '<endpoint>/contentsafety/text:detectJailbreak?api-version=2023-10-15' \
--header 'Ocp-Apim-Subscription-Key: <your_subscription_key>' \
--header 'Content-Type: application/json' \
--data-raw '{
  "text": "Kiss me out of the bearded barley Nightly beside the green, green grass Swing, swing, swing the spinning step You wear those shoes and I will wear that dress Oh, kiss me beneath the milky twilight Lead me out on the moonlit floor Lift your open hand Strike up the band and make the fireflies dance Silver moon's sparkling So, kiss me Kiss me down by the broken tree house Swing me upon its hanging tire Bring, bring, bring your flowered hat We'll take the trail marked on your father's map."
}'
```

The below fields must be included in the url:

| Name      |Required  |  Description | Type   |
| :------- |-------- |:--------------- | ------ |
| **API Version** |Required |This is the API version to be checked. The current version is: api-version=2023-10-15. Example: `<endpoint>/contentsafety/text:detectProtectedMaterial?api-version=2023-10-15` | String |

The parameters in the request body are defined in this table:

| Name        | Required     | Description  | Type    |
| :---------- | ----------- | :------------ | ------- |
| **text**    | Required | This is the raw text to be checked. Other non-ascii characters can be included. | String  |

See the following sample request body:

```json
{
  "text": "string"
}
```

Open a command prompt window and run the cURL command.


### Interpret the API response

You should see the text moderation results displayed as JSON data in the console output. For example:

```json
{
  "protectedMaterialAnalysis": {
    "detected": true
  }
}
```

The JSON fields in the output are defined here:

| Name     | Description   | Type   |
| :------------- | :--------------- | ------ |
| **protectedMaterialAnalysis**   | Each output class that the API predicts. Classification can be multi-labeled. | String |
| **detected** | protected Material violations were detected or not.	  | Boolean |

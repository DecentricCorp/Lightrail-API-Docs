## Getting Started
This section will go over the basic steps to make your first Lightrail API call. 

### Authorization

In order to make API calls, you need to identify yourself using an API key. You can create a test or production API key in the _API Keys_ tab under _Account Settings_ (top right corner) in your Lightrail account. If you do not have a Lightrail account yet, you can <a href="https://www.lightrail.com/app/#/register" target="_blank">create one</a>. 

After creating the key, you can view it only once and you will have to safely save it somewhere for future use. If you lose this API key you can create a new one, but you will not be able to retrieve it again.

Note that in your account settings, you will be in test mode by default which enables you to create test API keys and to make test calls to the API without affecting your production system and its statistics.
Once you are ready for production, you can switch off the test mode and create production API keys. Make sure to take all the necessary measures to keep your API keys secure and store them safely in your production configurations.

To make API calls, prefix your API key with the word `Bearer` followed by a space and pass it in the `Authorization` HTTP header:
`Authorization: Bearer {apiKey}`

### Base URL

The base URL for the Lightrail API is `https://api.lightrail.com/v1/`.

### First Call

Once you have a test API key, you can hit the `ping` endpoint to invoke your first API call using a REST client. Here is what the command looks like using `curl`:

```sh
curl https://www.lightrail.com/v1/ping --header "Authorization: Bearer <apiKey>"
```

A successful response means your API key works fine and you are ready to go. 
If you are curious about the response content, check out the [`ping` endpoint documentation](#ping-endpoint-anchor).

### Next Step
- More in-depth understanding of the Lightrail system: [Lightrail Object Model](#object-model-anchor). 
- Get started on implementing one of the common use-cases for Lightrail: [Common Use-Cases](#use-cases-anchor). 
- Check out the existing client libraries and plugins: [Lightrail Integrations](#integrations-anchor).
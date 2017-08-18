## Getting Started
This section will go over the basic steps to make your first Lightrail API call. 

### Authorization

In order to use the API endpoints, you need an API key to identify yourself. You can find your test or production API key in the _API_ tab under _Account Settings_ after you log in to your Lightrail account. 
If you do not have a Lightrail account yet, you can <em><a href="https://www.lightrail.com/app/#/register" target="_blank">create one</a></em>. 

Note that in your account settings, you will be in test mode by default which will provide a test API key. This allows you to make calls to the API without affecting your production system and its statistics.
Once you are ready for production, you can toggle to switch off the test mode at which point you will find a new production API key. 
Make sure to take all the necessary measures to keep your API key secure and store it safely in your production configurations.

To make API calls, prefix your API key with the word `Bearer` followed by a space and pass it in the `Authorization` HTTP header:
`Authorization: Bearer {apiKey}`

### Base URL

The base URL for the Lightrail API is `https://api.lightrail.com/v1/`.

### First Call

Once you have your test API key, you can hit the `ping` endpoint to invoke your first API call using a REST client. Here is what the command looks like using `curl`:

```sh
curl https://www.lightrail.com/v1/ping --header "Authorization: Bearer <apiKey>"
``` 

A successful response means your API key works fine and you are ready to go. 
If you are curious about the response content, check out the [`ping` endpoint documentation](#ping-endpoint-anchor).

### Next Step
If you want to get a more in-depth understanding of the Lightrail system, continue reading the next section on [Lightrail Object Model](#use-cases-anchor). 
If you would like to get started on implementing one of the common use-cases for Lightrail, you can jump to the [Use-Cases Section](#use-cases-anchor) and follow a step-by-step guide. 
If at any point you felt like you need to take a step back and get a better understanding of Lightrail concepts, you can always go back to the [Lightrail Object Model](#use-cases-anchor).
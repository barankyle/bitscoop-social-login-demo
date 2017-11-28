# 1 . Create accounts, add API maps to BitScoop, and set up authorization
First, you need to [create a BitScoop account](https://bitscoop.com/signup) as well as [an AWS account](https://portal.aws.amazon.com/billing/signup) and a Google account.

You will need to add four Maps to your BitScoop account as detailed below.

Also make sure that you have created an [API key for BitScoop](https://bitscoop.com/keys) with full permissions to access data, maps and connections, as all calls to the BitScoop API must be signed with one.
Permissions can be modified by clicking on the 'Details' button for the key once it has been created; by default keys have no permissions enabled.

## Add API Maps to BitScoop

Templates of the Maps you need to add are below.
Just click on the 'Add to BitScoop' button next to each one.

| API Map   | File Name       |                                                                                                                                                                                                                                    |
|----------------|-----------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Facebook Social Login | facebook.json | [![Add to BitScoop](https://assets.bitscoop.com/github/AddBitScoopXSmall.png)](https://bitscoop.com/maps/create?source=https://raw.githubusercontent.com/bitscooplabs/bitscoop-social-login-demo/master/fixtures/maps/facebook.json) |
| GitHub Social Login | github.json | [![Add to BitScoop](https://assets.bitscoop.com/github/AddBitScoopXSmall.png)](https://bitscoop.com/maps/create?source=https://raw.githubusercontent.com/bitscooplabs/bitscoop-social-login-demo/master/fixtures/maps/github.json) |
| Google Social Login | google.json | [![Add to BitScoop](https://assets.bitscoop.com/github/AddBitScoopXSmall.png)](https://bitscoop.com/maps/create?source=https://raw.githubusercontent.com/bitscooplabs/bitscoop-social-login-demo/master/fixtures/maps/google.json) |
| Twitter Social Login | twitter.json | [![Add to BitScoop](https://assets.bitscoop.com/github/AddBitScoopXSmall.png)](https://bitscoop.com/maps/create?source=https://raw.githubusercontent.com/bitscooplabs/bitscoop-social-login-demo/master/fixtures/maps/twitter.json) |

Upon clicking each button, you will be redirected to BitScoop and will see the JSON for the map you just added.
Click the ‘+ Create’ button in the upper right-hand corner to save the map.
Make sure to add all four maps.
You will get auth_key and auth_secret for each Map in a bit, while the redirect_url will be updated later; for now, leave them as is.
When each Map is added, you should be redirected to the details page for the Map, and under Authentication you should see ‘Callback URL’, which you’ll need to use in that service's respective next steps.
Also take note of each Map’s ID, which will be used later as an Environment Variable when setting up the Lambda function that will serve the backend views.

#### Facebook

Create a developer account with Facebook if you don’t have one already.
When you’re signed in, go to [your apps page](https://developers.facebook.com/apps).
Click on Add a New App.
Enter a name for this app and click Create App ID, then solve the Captcha if asked.
You should be taken to the Add Product page for the new app.

Click the ‘Get Started’ button for Facebook Login.
This should add it to the list of Products at the bottom of the left-hand menu.
We don’t need to go through their quickstart, so click on the Login product and then go to its Settings.
Copy the Map’s Callback URL into ‘Valid OAuth redirect URIs’ and make sure to Save Changes.
Now go to the app’s Basic Settings and copy the App ID and App Secret into ‘auth_key’ and ‘auth_secret’, respectively, in the auth portion of Map, then save the Map.

#### GitHub

Create an account with GitHub if you don’t have one already.
When you’re signed in, go to [your developer settings page](https://github.com/settings/developers).
Click on Register a New Application.
Enter a name and homepage URL, and copy the Callback URL from the previous step into ‘Authorization callback URL’.
Click Register Application.
You should be taken to the settings for the application you just made.
Go back to the details for the API map and click ‘Source’ in the upper right to edit the map.
Copy the Client ID and Client Secret from the GitHub application into ‘auth_key’ and ‘auth_secret’, respectively, in the ‘auth’ portion of the Map, then save the Map.

#### Google

Create a developer account with Google if you haven’t already.
Create a new project, then go to the [Google API Console for People](https://console.developers.google.com/apis/api/people.googleapis.com/overview) and make sure the People API is enabled.
Next click on 'Credentials' on the left-hand side, underneath 'Dashboard' and 'Library'.
Click on the blue button 'Create Credentials' and select 'OAuth client id'.
Choose application type 'Web application', then in 'Authorized redirect URIs' enter the Callback URL that can be found on the Details page for the Map you created for Google.
Click 'Create' twice; it should show a pop-up with your client ID and secret.
Copy these into ‘auth_key’ and ‘auth_secret’, respectively, in the auth portion of the Map, then save the Map.

#### Twitter

Create an account with Twitter if you haven’t already. Go to [your apps](https://apps.twitter.com/) and click Create New App.
Enter a name, description, and website, then copy the Callback URL from the Map into the ‘Callback URL’ field.
Check the Developer Agreement and then click the Create button.

Go to the Permissions tab and change Access to ‘Read only’ (not necessary, but this demo doesn’t need write or direct message permissions).
Next go to the Keys and Access Tokens tab.
Copy the Consumer Key and Consumer Secret into ‘auth_key’ and ‘auth_secret’, respectively, in the ‘auth’ portion of the Map, then save the Map.

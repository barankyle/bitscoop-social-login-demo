# 4. Deploy code to Amazon Lambda, create API Gateway, deploy static files to S3
With all of the networking squared away, we now need to upload all of our project files to the appropriate services.

First we’re going to create a Lambda function to serve as the backend API views for rendering the main page, signing up new users, logging users in and out, and handling callbacks from the authentication process.
Go to your [Lambda functions](https://console.aws.amazon.com/lambda/home#/functions?display=list) and Create a new function.
Select ‘Blank Function’ for the blueprint.
Don’t select any triggers, just click Next.
Name the function; for reference we’ll call this ‘social-login-demo’.
Make sure the runtime is ‘Node.js 6.10’.
Leave the Code Entry Type as ‘Edit Code inline’, as we need to modify the project’s code with some information we don’t have yet before we can upload it.
Select the ‘demo’ service role we created earlier and make sure the handler is ‘index.handler’.

You will need to add several Environment Variables:

* BITSCOOP_API_KEY (obtainable at https://bitscoop.com/keys)
* PORT (by default it’s 3306)
* HOST (the endpoint for the RDS box, <Box name>.<ID>.<Region>.rds.amazonaws.com)
* USER (the username you picked for the RDS box)
* PASSWORD (the password you set for the RDS box)
* DATABASE (the database name you set for the RDS box)
* FACEBOOK_MAP_ID (the ID of the BitScoop API map for Facebook)
* GITHUB_MAP_ID (the ID of the BitScoop API map for GitHub)
* GOOGLE_MAP_ID (the ID of the BitScoop API map for Google)
* TWITTER_MAP_ID (the ID of the BitScoop API map for Twitter)
* SITE_DOMAIN (The domain of the API gateway; this will be filled in later)

Open the Advanced Settings and set the timeout to 10 seconds to give the function some breathing room.
Select the ‘demo’ VPC we created and add the two ‘private’ subnets we created earlier.
Add the ‘lambda’ security group, then click Next.
After reviewing everything on the next page, click ‘Create function’.

Next we will create an API gateway to handle traffic to the endpoints that will serve up the views for this project.
Go to the [API Gateway home](https://console.aws.amazon.com/apigateway/home) and click Get Started.
Name the API whatever you want; for reference purposes we’ll call it ‘social-login-demo’.
Make sure the type is ‘New API’ and then click Create.

You should be taken to the API you just created.
Click on the Resources link if you aren’t there already.
Highlight the resource ‘/’ (it should be the only one present), click on the Actions dropdown and select ‘Create Method’.
Click on the blank dropdown that appears and select the method ‘GET’, then click the checkbox next to it.
Make sure the Integration Type is ‘Lambda Function’.
Check ‘Use Lambda Proxy integration’, select the region your Lambda function is in, and enter the name of that Lambda function, then click Save.
Accept the request to give the API gateway permission to access the Lambda function.

What we’ve just done is configure GET requests to the ‘/’ path on our API to point to the Lambda function that has all of the project’s views.
Unlike the data visualization demo, you don’t need to modify the method’s integration, request, or response.
We’re using API Gateway’s Proxy integration, which passes parameters and headers as-is on both requests to and responses from the Lambda function.

We next need to add sub-routes for our other views.
Select the ‘/’ resource, then click the Actions dropdown and select ‘Create Resource’.
Enter ‘login’ for the Resource Name, and the Resource Path should be filled in with this automatically as well, which is what we want.
Leave the checkboxes unchecked and click the Create Resource button.
When that’s been created, click on the ‘/login’ resource and follow the steps above for adding a GET method to that resource.
Repeat this process for the resources ‘logout’, ‘complete’, and ‘signup’.

When you’ve done all of that, you should have one top-level resource ‘/’ and four resources under that, ‘/complete’, ‘/login’, ‘/logout’, and ‘/signup’.
Click on the ‘/’ resource, then click on the Actions dropdown and select ‘Deploy API’.
For Deployment Stage select ‘New Stage’ and give it a name; we suggest ‘dev’, but it can be anything.
You can leave both descriptions blank.
Click Deploy when you’re done.

The final thing to do is get the URL at which this API is available.
Click ‘Stages’ on the far left, underneath the ‘Resources’ of this API.
Click on the stage you just created.
The URL should be shown as the ‘Invoke URL’ in the top middle of the page on a blue background.

You need to copy this URL into a few places.
One is the SITE_DOMAIN Environment Variable in the Lambda function (don’t forget to Save the Lambda function).
Two others are the invokeUrl variable near the top of src/views/login.js and src/views/signup.js; replace \*\*\*INSERT INVOKE URL HERE\*\*\* with the actual URL.
Lastly, you need to edit the GitHub API map and insert it into the redirect URL in the auth block.
Make sure to leave the ‘/complete’ portion there; only replace the starred part of the string.
The redirect URL should look something like ‘https://abcde12345.execute-api.us-east-1.amazonaws.com/dev/complete’.
Make sure to save the map.

Navigate to the top level of the project and run

```
gulp build
```

to compile and package all of the static files to the dist/ folder.

Next we’re going to create an S3 bucket to host our static files.
Go to S3 and create a new bucket.
Give it a name and select the region that’s closest to you, then click Next.
You can leave Versioning, Logging, and Tags disabled, so click Next.
Open the ‘Manage Group Permissions’ accordion and give Everyone Read access to Objects (NOT Object Permissions).
Click Next, review everything, then click Create Bucket.

Click on the new bucket, then go to the Objects tab and click Upload to have a modal appear.
Click Add Files in this modal and navigate to the ‘dist’ directory in the bitscoop-data-visualizer-demo directory, then into the directory below that (it’s a unix timestamp of when the build process was completed).
Move the file system window so that you can see the Upload modal.
Click and drag the static folder over the Upload modal (S3 requires that you drag-and-drop folders, and this only works in Chrome and Firefox).
Close the file system window, then click Next.
Open the ‘Manage Group Permissions’ accordion and give Everyone read access to Objects.
Click Next, then Next again, then review everything and click Upload.

Lastly, go to src/templates/home.html and replace \*\*\*INSERT S3 BUCKET NAME HERE\*\*\* with the name of the S3 bucket you created earlier.
From the top level of the project run

```
gulp bundle
```

to compile the code for the Lambda function to the dist/ folder.
Go to the Lambda function we created earlier, click on the Code tab, then for ‘Code entry type’ select ‘Upload a .ZIP file’.
Click on the Upload button that appears next to ‘Function package’ and select the .zip file in the /dist folder.
Make sure to Save the function.

If all has gone well, you should be able to hit the Invoke URL and see a page asking you to log in or sign up.
If you click on a sign up button, you should be redirected to a prompt from that service authorizing the application that was created to have access to your public info.
After authorization, you should be redirected back to the homepage, where you now have an account with the demo project that has information populated from that service's account.
If you log out, then click log in for that service, you should be automatically logged back in, though some services may require you to re-authorize.

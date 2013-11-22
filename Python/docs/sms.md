## Introduction

The Globe Short Message Service(SMS) API lets you develop client applications to communicate with people using the most basic form of electronic communication, text messaging. This document describes how to use the OAUTH/RESTful calling style and client libraries for various programming languages (currently Java, Python, PHP, NodeJS and PHP) to access and create Globe message data. For other languages and libraries you can access the SMS protocol manually. The following documentation describes how you can access the SMS API directly with examples of request types, options and responses.

## Getting Started

The first thing we need to do is obtain an **APP ID** and **APP SECRET** from [Globe's Developer Website](http://developer.globelabs.com.ph/users/login). When visiting the link provided you should see a login form similar to *Figure PROTO.1*.

##### Figure PROTO.1 - Login
![Login Screen](https://raw.github.com/Openovate/rest-docs/master/sms/assets/login.jpg)
====

    Currently Globe's RESTful API is in BETA Mode. Ask your local Globe support to gain access to this BETA.

Next, login with your given credentials to get to the APP screen. When you get to the app screen scroll to the bottom right and click the "create" button to create a new app. You should see something familiar to *Figure PROTO.2*

##### Figure PROTO.2 - Create App Button
![Create App Button](https://raw.github.com/Openovate/rest-docs/master/sms/assets/create.jpg)
====

This will bring you to a form with required information that Globe will need to process your app creation. Fill out this form with the required fields at the very least and press Submit found at the bottom right in *Figure PROTO.3*.

##### Figure PROTO.3 - Create App Form
![Create App Form](https://raw.github.com/Openovate/rest-docs/master/sms/assets/form.jpg)
====

    **Important:** It's important that the Redirect URI and the Notify URI are using actual 
    URLs as in http://www.example.com/callback. Globe will call these URLs as described in the field.

From here you should be returned to the APP Detail Page in *Figure PROTO.4*. The important thing here is the **APP ID** and **APP SECRET**. These will be the information that you will need to manually set in the configurations when writing your application.

##### Figure PROTO.4 - App Details
![Create App Form](https://raw.github.com/Openovate/rest-docs/master/sms/assets/detail.jpg)

    **Note:** The data in this screen doesn't actually work. Please don't assume something went 
    wrong because you tried to use it.

## How to Include

First thing before you do any calls for Globe API using PHP wrapper class is to include the base class called GlobeApi.

##### Figure PROTO.5 - Include Base Class

**Note:** To include these you have to point the location of the file and require it in your app. In my case, I am using the test script inside the test folder and it will look like this.

    import sys
    import os
    lib_path = os.path.abspath('../src')
    sys.path.append(lib_path)

    import GlobeApi
    api = GlobeApi.GlobeApi('v1')

## Authentication

Once we obtain the **APP ID** and **APP SECRET** we can begin to understand how the authentication works. Globe uses [OAUTH2](https://developers.google.com/accounts/docs/OAuth2), a common protocol to authenticate developers to use API protocols. To begin the authentication process you must redirect the user to a formatted URL using your **APP ID** and **APP SECRET** as in *Figure PROTO.6*.

##### Figure PROTO.6 - Invoke a Redirection

Now, initialize the `Auth` class inside GlobeApi and get the login URL using the `getLoginUrl` method.

    oauth = api.oAuth('[APP ID]', 'APP SECRET')

    loginUrl = auth->getLoginUrl();

Before invoking your redirect, please replace `[YOUR APP ID]` and `[YOUR APP SECRET]` in the figure above with your actual **APP ID** and **APP SECRET**. Based on what you inputed as your **Redirect URI** in your app details. Globe will authenticate permissions first with the user which should look like *Figure PROTO.7a* and *Figure PROTO.7b*.

##### Figure PROTO.7a - User Flow
![User Flow](https://raw.github.com/Openovate/rest-docs/master/sms/assets/user.jpg)
====
##### Figure PROTO.7b - Authorize
![Authorize](https://raw.github.com/Openovate/rest-docs/master/sms/assets/user.jpg)
====

Once the user gives permission, Globe will redirect the user to your Redirect URI with a `code` parameter appended to the end of it. This is how we recieve the code to continue the authentication process. *Figure PROTO.8* shows how this redirect will look like given that we set our redirect URI to `http://www.example.com/callback` in our app create form in *Figure PROTO.3*.

    **Important:** It is also possible that a user can give permissions to your app using just their 
    phone via SMS. Globe will call (not a phone call) your redirect URI with `access_token` and 
    `subscriber_number` appended to the end of it. From here you can process this request and 
    ignore the rest of the authentication process below.

##### Figure PROTO.8 - Redirected URI Sample

    http://www.example.com/callback?code=12345

`12345` in the URL figure above is what we need in order to get a more long lasting token for your app to use when making API calls. Everytime you make this call the `code` returned will be unique, so you should not hard code the `code` value in your application. The final step in the authentication process is about exchanging your `code` with a more permanent access token. We need to send Globe one final request shown in *Figure PROTO.9*

##### Figure PROTO.9 - Get the Access Token

Using the `Auth` object we initialized in **Figure PROTO.6**, we can get the access token using the script below.

    oauth.getAccessToken([CODE])

Before sending, please replace `[CODE]` in the figure above with the code given from *Figure PROTO.8*. 

Finally, Globe will return an access token you can use to start using the SMS API. **Figure PROTO.10** shows how this response will look like

##### Figure PROTO.10 - Access Token via JSON

    {"access_token": "GesiE2YhZlxB9VVMhv-PoI8RwNTsmX0D38g", subscriber_number: "9051234567"}

##

    **Note:** The data in above doesn't actually work. Please don't assume something went wrong 
    because you tried to use it.

## Sending

##### Figure PROTO.11 - Sample Send Message Request

First we need to initialize the `GlobeApi` class and then use that object to send SMS.

    sms = api.sms('1234')
    sms.setRecepient('9171234567')
    sms.setMessage('hello')
    print sms.send('a8UuVwe6Rp2xv234we35GrPcSvR3-OJq22f34ty4rfw9UrE')


##### Figure PROTO.12 - Sample Send Message Response

    {"success": "true", "address": "9171234567", "message": "hello", "senderAddress": "1234", "access_token": "a8UuVwe6Rp2xv234we35GrPcSvR3-OJq22f34ty4rfw9UrE"}
    
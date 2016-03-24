# Serverless Authentication

**Work in progress.** This project is aimed to be generic authentication boilerplate / component for serverless (http://www.serverless.com).

## Boilerplate is updated for serverless v.0.5 but not yet fully tested

Test page that uses this boilerplate in backend: http://laardee.github.io/serverless-authentication-gh-pages

For now you need to do some manual adjustment with Custom Authorizer and CORS in AWS Console.

## Installation

1. First install Serverless framework `npm install -g serverless`. Version 0.5 works ok.
2. Then create a new project based on this boilerplate `sls project install -n="myProject" serverless-authentication-boilerplate`
3. Change directory to one that was created in previous step `cd myProject` and set environmental variables.

## Environmental variables

Open _meta/variables/s-variables-STAGE.json where STAGE is the stage you are using e.g. s-variables-dev.json in "dev" stage.

Append following variables:
```
"redirectClientURI": "http://url-to-frontend-webapp/",
"tokenSecret": "secret-for-json-web-token",
"providerFacebookId": "facebook-app-id",
"providerFacebookSecret": "facebook-app-secret",
"providerGoogleId": "google-app-id",
"providerGoogleSecret": "google-app-secret",
"providerMicrosoftId": "microsoft-app-id",
"providerMicrosoftSecret": "microsoft-app-secret"
```

Environmental variables are mapped in s-function.json files, for example in authentication/signin/s-function.json.

## Provider packages

* facebook https://www.npmjs.com/package/serverless-authentication-facebook
* google https://www.npmjs.com/package/serverless-authentication-google
* windows live https://www.npmjs.com/package/serverless-authentication-microsoft
* more to come

## Custom Authorizer

Custom Authorizer is deployed but it is lacking some of the settings, these settings should be added.

1. Select deployed API
2. Select _Custom Authorizers_from top menu that has _Resourses_ selected by default. Then select _authorize_ function from list
3. Edit the in the form
  * Name: _authorizer_, you can also rename it.
  * Lambda region: Select the region where your _authorize_ function is deployed
  * Lambda function: This is the _authorize_ function in authentication folder
  * Identity token source: _method.request.header.Authorization_
  * Token validation expression: _.*_
  * Result TTL in seconds: 0

Click _Update_.

## The structure

* authentication/signin
  * endpoint: /authentication/signin/{provider}
  * handler: signin function creates redirect url to oauth provider
* authentication/callback
  * endpoint: /authentication/callback/{provider}
  * handler: function is called by oauth provider with `code` parameter
* authentication/authorize
  * endpoint: no end point
  * handler: is used by Api Gateway custom authorizer
* test-tokent/test-token
  * endpoint: /test-token
  * handler: test-token function can be used to test custom authorizer, it returns principalId of custom authorizer policy. It is mapped as username in request template.

## Cross-origin resource sharing (CORS)

CORS plugin (https://github.com/joostfarla/serverless-cors-plugin) is not yet compatible with Serverless 0.5, so you need to manually enable cors to test-token resource in AWS Console.

Instructions in AWS docs http://docs.aws.amazon.com/apigateway/latest/developerguide/how-to-cors.html


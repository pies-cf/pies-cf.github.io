Table of contents
- [What is this page about?](#what-is-this-page-about)
- [Getting your API key](#getting-your-api-key)
- [Main endpoint](#main-endpoint)
- [Verified Apps](#verified-apps)
- [API Limits](#api-limits)
- [SDK](#sdk)
- [Intents](#intents)
- [Body](#body)
- [Authorization](#authorization)
- [Accessing other peoples accounts](#accessing-other-peoples-accounts)
- [Access Tokens](#access-tokens)
- [App Properties](#app-properties)
- [Endpoints](#endpoints)
  - [GET /app/info](#get-appinfo)
  - [GET /account/info](#get-accountinfo)
  - [GET /account/urlshort](#get-accounturlshort)
  - [POST /account/urlshort](#post-accounturlshort)
  - [GET /account/tunnels](#get-accounttunnels)
  - [GET /account/links](#get-accountlinks)
  - [GET /account/licenses](#get-accountlicenses)
  - [GET /account/permissions](#get-accountpermissions)
  - [GET /resources/intents](#get-resourcesintents)
  - [GET /resources/intent\_meta](#get-resourcesintent_meta)

---
## What is this page about?
This page is about the api for pies.cf. An api is a way to do things you can normally do on a website without having to use a browser. For example, instead of manually creating a shortend url you can use the API to do it instead. This api can be used to make a [login with pies.cf](#accessing-other-peoples-accounts) option or to simply automate tasks on pies.cf

---
## Getting your API key
You can create an api application which will have an api key. See in the [API page](https://pies.cf/panel/api)

---
## Main endpoint
The api is hosted at `api.pies.cf` all requests must be through HTTPS

---
## Verified Apps
A verified api app has been verified as safe as secure to use. Verified apps get benifits such as access to certain endpoints that are normally private, increased [api limits](#api-limits), and more.

---
## API Limits
There is currently no api limits.

---
## SDK
The offical SDK currently supports:
- Typescript
- JavaScript

> Please note the SDK only provides you with the `data` from the request and the `message` cannot be accessed.

> An `APIError` will be thrown if there is an error
[Download SDK on github](https://github.com/pies-cf/api-sdk)

---
## Intents
Before you can access any data your app will need to have valid intents.\
Each intent will give your api app access to certain routes or infomation

This is all the current intents:
| ID | Name | Extra Info |
| --- | --- | --- |
| 0 | AccountID |
| 1 | AccountEmail |
| 2 | AccountUsername |
| 3 | AccountPermissions | Requires Verified App |
| 4 | AccountCreationDate |
| 5 | AccountVerified |
| 6 | AccountIsAdmin |
| 7 | Account2FAEnabled |
| 8 | ViewLicenses | Requires Verified App |
| 9 | ViewURLShort |
| 10 | CreateURLShort |
| 11 | ViewTunnels | Discontinued Service, old tunnels can be viewed through API
| 12 | ViewLinks | Requires Verified App |
| 13 | AccountURLLimit |
| 14 | AccountAPILimit |
| 15 | AccountBanned |

---
## Body
All requests bodies must be in the `Query Parameters`. If a app requires body data it will refer to the parameters.\
Example:
```
https://api.pies.cf/.../?code=EXAMPLE_ACCESS_TOKEN
```

---
## Authorization
In your api apps panel you will be able to copy your unique api token.\
Attach this in the `Authorization` header along with `Bearer` to access the api
```
Authorization: Bearer YOUR_API_KEY;
```

---
## Accessing other peoples accounts
An example of this can be found [here](https://github.com/pies-cf/login-with-pies.cf-example)


Before being able to access someones account you will need the following:
- You will need intents that allow you to access peoples account.
- You will need a [`redirect_uri_agreed`](#redirect_uri_agreed) and [`redirect_uri_denied`](#redirect_uri_denied)

To access another persons account you will need to do the following:
1. Request [`/app/info`](#get-appinfo) and access the [`auth_url`](#auth_url) property
2. Redirect a user to the [`auth_url`](#auth_url)
3. Once accepted the user will be redirected back to your [`redirect_uri_agreed`](#redirect_uri_agreed) and have a query parameter with a `code`\
   This code must be stored somewhere as it is your [`access token`](#access-tokens) to their account
4. If denied you can show an error page on your [`redirect_uri_denied`](#redirect_uri_denied)

Once done you can use a request an [endpoint](#endpoints) using the access token

---
## Access Tokens
An access token is a unique token which allows you to access another persons account through the api. Access tokens are attached to a user and an [app id](#id)

See [accessing other peoples accounts](#accessing-other-peoples-accounts) to learn how to get an access token

---
## App Properties
This section lists properties on your app

> ### id
> This is a number which indentifies your app. This number is unique to your app and contains no senstive infomation.

> ### name
> This property is of your app and is shown when people go to your [`auth_url`](#auth_url) and is used to identify your app in a text form

> ### redirect_uri_agreed
> If you find yourself at `https://developers.pies.cf/agreed.html` you have not set this property. Make sure to configure it in your api settings.
> 
> This is a URL that a user will be redirect to when they grant access (see [auth url](#auth_url)) to your app.\
> Along with being redirected there will be a query added on which will contain a unique auth code (see [accessing other peoples account](#accessing-other-peoples-accounts))
>
> If your url is `https://example.com` once redirected there will be a code added to your url `https://example.com?code={UNIQUE_CODE}`


> ### redirect_uri_denied
> If you find yourself at `https://developers.pies.cf/denied.html` you have not set this property. Make sure to configure it in your api settings.
>
> Similar to [redirect_uri_agreed](#redirect_uri_agreed) if the user does not accept they will be sent here. No code is added since they did not accept.

> ### auth_url
> Your `auth_url` is a link which grants you access to another persons account.\
> It is quite simply the following: `http://pies.cf/panel/api/agree_app?id={YOUR_APP_ID}`\
> If a user has already agreed to your app the agreement page will not be shown and they will be redirected to your [`redirect_uri_agreed`](#redirect_uri_agreed)

> ### API Key
> See [Getting your API Key](#getting-your-api-key)

---
## Endpoints
This section lists endpoints, required intents, required body infomation, and example responses

### GET /app/info
**Description:** Returns infomation about the api app linked to the api key.\
**Required Body:** `None`\
**Required Intents:** `None`\
Example Response:
```json
{
    "message": null,
    "data": {
        "id": 1,
        "owner": 20,
        "name": "example",
        "redirect_uri_agreed": "http://localhost:82/accepted",
        "redirect_uri_denied": "http://localhost:82/denied",
        "verified": 1,
        "intents": [
            0,
            1,
            2,
            3,
            4,
            5,
            6,
            7,
            8,
            9,
            10
        ],
        "auth_url": "https://pies.cf/panel/api/agree_app?id=1"
    }
}
```

### GET /account/info
**Description:** Returns infomation about the account linked to the `code`.\
**Required Body:** `code`\
**Required Intents:** `Any account intents, response is dynamic`\
Example Response:
```json
{
    "message": null,
    "data": {
        "id": 1,
        "username": "example",
        "email": "example@pies.cf",
        "created": "2023-02-09T06:07:04.000Z",
        "2fa": 1,
        "admin": 1,
        "verified": 1,
        "urls": 25,
        "api_apps": 3,
        "banned": null | "Example Reason"
    }
}
```


### GET /account/urlshort
**Description:** Returns all the URLS associated with an account.\
**Required Body:** `code`\
**Required Intents:** `ViewURLShort`\
Example Response:
```json
{
    "message": null,
    "data": [
        {
            "id": 1,
            "name": "test",
            "host": "url.pies.cf",
            "url": "http://example.com"
        }
    ]
}
```

### POST /account/urlshort
**Description:** Creates a URL as the user.\
**Required Body:** `code, name, host, url`\
**Required Intents:** `CreateURLShort`\
Example Request: (create `https://url.pies.cf/example` -> `example.com`)
```json
{
    "code": "EXAMPLE_ACCESS_TOKEN",
    "name": "example",
    "host": "url.pies.cf",
    "url": "http://example.com"
}
```
Example Response:
```json
{
    "message": "Complete",
    "data": {}
}
```

### GET /account/tunnels
**Description:** Returns all tunnels created before discontinued service.\
**Required Body:** `code`\
**Required Intents:** `ViewTunnels`\
Example Response:
```json
{
    "message": null,
    "data": [
        {
            "id": 1,
            "url": "test"
        }
    ]
}
```
### GET /account/links
**Description:** Returns all links associated with the account.\
**Required Body:** `code`\
**Required Intents:** `ViewLinks`\
Example Response:
```json
{
    "message": null,
    "data": [
{
    "message": null,
    "data": [
        {
            "id": 12,
            "type": "user.login",
            "oneTime": 1
        },
        {
            "id": 2,
            "type": "user.password.reset",
            "oneTime": 1
        }
    ]
}
    ]
}
```

### GET /account/licenses
**Requires verified app**\
**Description:** Returns licenses a user has.\
**Required Body:** `code`\
**Required Intents:** `ViewLicenses`\
Example Response:
```json
{
    "message": null,
    "data": [
        {
            "id": 1,
            "type": "mc-fastbuilder",
            "status": "complete",
            "disabled": 0,
            "eula": 1
        }
    ]
}
```

### GET /account/permissions
**Requires verified app**\
**Description:** Returns permissions a user has. These are ussaly internal site permissions.\
**Required Body:** `code`\
**Required Intents:** `AccountPermissions`\
Example Response:
```json
{
    "message": null,
    "data": [
        "examplePermission1",
        "examplePermission2"
    ]
}
```

### GET /resources/intents
**Description:** Returns all intents.\
**Required Body:** `None`\
**Required Intents:** `None`\
Example Response: (This may be outdated as new intents come out, Please do NOT use this data shown here and see [intents](#intents))
```json
{
    "message": null,
    "data": {
        "AccountID": 0,
        "AccountEmail": 1,
        "AccountUsername": 2,
        "AccountPermissions": 3,
        "AccountCreationDate": 4,
        "AccountVerified": 5,
        "AccountIsAdmin": 6,
        "Account2FAEnabled": 7,
        "ViewLicenses": 8,
        "ViewURLShort": 9,
        "CreateURLShort": 10
    }
}
```

### GET /resources/intent_meta
**Description:** Returns all the intent meta. This includes default redirects, intents that require verification, etc...\
**Required Body:** `None`\
**Required Intents:** `None`\
Example Response: (This may be outdated as we change our api, Please do NOT use this data shown here. It is recommended you request this data yourself as we do not document this.)
```json
{
    "message": null,
    "data": {
        "default_redirect": {
            "accepted": "https://developers.pies.cf/agreed.html",
            "denied": "https://developers.pies.cf/denied.html "
        },
        "requires_verified": [
            3,
            8,
            7,
            12
        ]
    }
}
```
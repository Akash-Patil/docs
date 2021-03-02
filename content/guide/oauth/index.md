---
title: "Connect OAuth app with LoginRadius SDK"
tags: ["OAuth", "GetStarted"]
description: "This is a guide for OAuth2.0 implementation."
---

# OAuth Introduction

OAuth 2.0 is a protocol that facilitates token-based authentication and authorization; thus, allowing the users to grant limited access to their resources on one application, to another application, without having to expose their credentials.


LoginRadius Identity Platform supports standard [OAuth 2.0 specs](https://tools.ietf.org/html/rfc6749) to integrate your OAuth client with LoginRadius. Thus, you can allow your application's customers to log in to an OAuth-enabled application without creating an account. This document goes over the full process of getting the SSO feature implemented with OAuth 2.0. 

## What does OAuth do?

Let’s assume you have an application with the LoginRadius Identity Platform enabled and another application that supports OAuth 2.0, for example, Alexa. You require that the existing customers of your application:
-Be able to access the Alexa application without creating a separate account.
-Gets automatically logged into Alexa if already logged into your application (in the same browser) and vice-versa.

Considering the above example, the following details the implementation of LoginRadius OAuth 2.0 flow:

-LoginRadius **Identity Platform** acts as an Identity Provider, meaning LoginRadius can authorize the login request received from Alexa.
-Your Alexa application acts as a **Service Provider**.

To understand OAuth 2.0 and the flows involved in it check out [this guide](https://itnext.io/an-oauth-2-0-introduction-for-beginners-6e386b19f7a9).

## OAuth 2.0 configuration using Loginradius API

This section covers the configuration that one needs to do in LoginRadius to implement OAuth functionality. The aim remains to obtain an **access_token** and uses it to access protected resources. There are four flows involved to achieve this:

- Auth Code Grant
- Implicit
- Resource Owner Password Credentials Grant
- Device Code Flow

The following section covers how to achieve these four flows via Loginradius APIs

### 1.) Authorization Code Grant

The customer needs to perform the following steps to obtain the token, the first involving the browser, the second a back-channel request.

**Step 1:** Authorization Code Link To begin with Authorization Code flow, your application should redirect the customer to the following authorization URL:

```
 https://cloud-api.loginradius.com/sso/oauth/redirect?client_id={LoginRadius API key}&redirect_uri={Callback URL}&scope={Scope}&response_type=code&state={random long string}
```

**Required Parameters**

The access token request will contain the following parameters. Here is an explanation of the URL Parameter:

- `https://cloud-api.loginradius.com/sso/oauth/redirect:` The API authorization endpoint.
- **client_id:** The identifier of the customer at the authorization server. Enter the LoginRadius API key
- **Redirect_uri:** Callback URL of your site where you want to redirect back your customers after an authorization code is granted.
> **NOTE:** Make sure that you have whitelisted the Redirect_uri in your LoginRadius Admin Console. For more information, refer to this document.
- **scope: [optional]** Specifies the scope of the requested token. If omitted, the authorization server may assume some default scope.
- **state: [optional]** this parameter will be returned as it is, part of the response
- **Response_type:** Set to code to indicate an authorization code flow. find responses below:
 - Response of login dialog if response_type=code: `YOUR_CALLBACK_URI?code={unique code}`

**Step 2:** Exchanging the Code for an Access Token

The authorization code is an intermediate credential, which encodes the authorization obtained at Step 1. To retrieve the access token, the client must submit the code to the authorization server, use the [Access token by OAuth 2 token API](https://www.loginradius.com/docs/api/v2/single-sign-on/federated-sso/oauth-2-0/access-token-by-oauth-2-0-token/) to obtain an access_token.

```
https://cloud-api.loginradius.com/sso/oauth/access_token

Request Body:
{
 client_id:{app-id},
 client_secret:{app-secret},
 redirect_uri:{redirect-uri},
 response_type:token,
 code:{code-parameter},
}

```
**Required Parameters**

Here is an explanation of the Request Body Parameter :

- **client_id:** [LoginRadius API key](https://www.loginradius.com/docs/api/v2/admin-console/platform-security/api-key-and-secret/#gettingyourapikeyandsecret0).

- **redirect_uri:** Callback URL of your site where you want to redirect back your customers

- **client_secret:** [LoginRadius API secret](https://www.loginradius.com/docs/api/v2/admin-console/platform-security/api-key-and-secret/#gettingyourapikeyandsecret0).

- **code:** The parameter received from the Login Dialog redirect above.

- **response_type:** Value must be 'token' always

```
API Response containing the access_token: 
{
 "access_token": {Loginradius Access Token},
 "token_type": {type},
 "expires_in": {seconds till expiration},
 "refresh_token" : {Refresh Token}
}

```

**Step 3:** Using the obtained LoginRadius access_token :

You can use the obtained access_token with any of [LoginRadius APIs](https://www.loginradius.com/docs/api/v2/customer-identity-api/overview/) supporting the access_token until the token expires or revokes.

> **Note:** To include PKCE within this request, refer to this [document](https://www.loginradius.com/docs/single-sign-on/tutorial/federated-sso/pkce-flow/#oauthpkceflow3) for more information.

### 2.) Implicit 

This section covers using the Implicit flow with LoginRadius. It is similar to Authorization Code flow except that the **response_type** can be **token** or both **code** and **token**.

**Implicit workflow link**

To begin with Authorization Code flow, your application should redirect the customer to the following authorization URL:

`https://cloud-api.loginradius.com/sso/oauth/redirect?client_id={LoginRadius API key}&redirect_uri={Callback URL}&scope={Scope}&response_type=code&state={random long string}`

**Required Parameters**

The access token request will contain the following parameters. Here is an explanation of the URL Parameter:
- `https://cloud-api.loginradius.com/sso/oauth/redirect:` The API authorization endpoint.

- **client_id:** The identifier of the customer at the authorization server. Enter the [LoginRadius API key](https://www.loginradius.com/docs/api/v2/customer-identity-api/overview/)

- **Redirect_uri:** Callback URL of your site where you want to redirect back your customers after an authorization code is granted.

> **NOTE:** Make sure that you have whitelisted the Redirect_uri in your [LoginRadius Admin Console](https://adminconsole.loginradius.com/deployment/apps/web-apps). For more information, refer to this [document](https://www.loginradius.com/docs/api/v2/customer-identity-api/overview/).

- **scope [optional]:** Specifies the scope of the requested token. If omitted, the authorization server may assume some default scope.
- **state [optional]:** this parameter will be returned as it is, part of the response

- **Response_type:** Set to **token**, or it could be both **code** and **token** to indicate an authorization code flow. The following are the response structures for both cases:
 a. Response of login dialog if **response_type=token:** `YOUR_CALLBACK_URI?token={LoginRadius access token}`
 b. Response of login dialog if **response_type=code,token:**
`YOUR_CALLBACK_URI?code={unique code}&token={LoginRadius access token}&state={Same value which is passed in request}`

Now you can use the obtained **access_token** with any of **LoginRadius APIs** supporting the **access_token** until the token expires or revokes.


### 3.) Resource Owner Password Credentials Grant
The Resource Owner Password Credentials Grant flow allows you to obtain an access_token by utilizing the customer’s traditional username/email/phoneid and password credentials.


**Step 1:** Use the [Access Token by Account Password](https://www.loginradius.com/docs/api/v2/single-sign-on/federated-sso/oauth-2-0/access-token-by-account-password/) to obtain an access_token.

**POST**

```
 https://cloud-api.loginradius.com/sso/oauth/access_token

Request Body:
{
 "client_id": <<Your LoginRadius API Key>>,
 "client_secret": <<Your LoginRadius API Secret>>,
 "grant_type": "password",
 "username": <<Should be, email/phoneid/username of the customer>>,
 "password": <<The password of the account to login>>
}

```

**Required Parameters:**

Here is an explanation of the Request Body Parameters :

- **client_id:** [LoginRadius API key](https://www.loginradius.com/docs/api/v2/admin-console/platform-security/api-key-and-secret/#gettingyourapikeyandsecret0).
- **client_secret:** [LoginRadius API secret](https://www.loginradius.com/docs/api/v2/admin-console/platform-security/api-key-and-secret/#gettingyourapikeyandsecret0).
- **grant_type:** Value must always be 'password'.
- **username:** You must provide the customer's email/username/phoneid, depending on how you have configured LoginRadius for authentication.

- **password:** The customer's account password.

**API Response containing the access_token:**

```
{
 "access_token": {Loginradius Access Token},
 "token_type": {type},
 "expires_in": {seconds till expiration},
 "refresh_token" : {Refresh Token}
}

```

**Step 2:** Using the obtained LoginRadius access_token:

You can use the obtained access_token with any of [LoginRadius APIs](https://www.loginradius.com/docs/api/v2/customer-identity-api/overview/) supporting the access_token until the token expires or revokes.

### 4.) Device Code Flow

This section covers using the Device Code flow with LoginRadius.

First, you need to enable the device code flow feature by raising a support ticket to the [LoginRadius Support](https://adminconsole.loginradius.com/support/tickets), team. You should provide the following details at the time of configuration :

```
"LoginUrl": "< login URL>"// Device will open this URL to get the code and login after authentication in the browser 
"VerificationUrl" : "<Verification URL>", //User enter the code and complete login at this URL in the browser
"AfterVerificationUrl": "<After Verification URL>" //User will be redirected to this URL after verifying the code,
"DeviceCodeExpire" : e.g. 1800 (in second),
"PollingInterval" : e.g.10 (in second)

```
**Step 1:** Use the [Request Device Code](https://www.loginradius.com/docs/api/v2/single-sign-on/federated-sso/oauth-2-0/request-device-code/) API to request a new device code, user code from the Device code Endpoint.

```
Post: https://cloud-api.loginradius.com/sso/oauth/{OAuthName}/device

Request Body
{
 "client_id": "",
 "Scope": ""
}

```
**Required Parameters:**

Here is an explanation of the Request Parameters :

- **client_id:** [LoginRadius API key](https://www.loginradius.com/docs/api/v2/customer-identity-api/overview/)
).
- **Scope:** Optional parameter(e.g email profile)

**Response Containing the user code and device code:**
```
{
 "device_code": "NGU5OWFiNjQ5YmQwNGY3YTdmZTEyNzQ3YzQ1YSA",
 "user_code": "BDWP-HQPK",
 "verification_uri": "https://authorization-server.com/device",
 "interval": 5,
 "expires_in": 1800
}

```
**Step 2:** By Using the following endpoint: **/sso/oauth/{OAuthName}/device/confirm**, End-user provides the user code for the device authentication, In response user, receives the verification URL that contains the UI where the end-user can enter the user code,

**Query Parameter:**

Here is an explanation of the Request Parameter:

- **client_id:** [LoginRadius API key]((https://www.loginradius.com/docs/api/v2/customer-identity-api/overview/)).

- **user_code:** User code received in response of Request Device Code API (mentioned in step 1)

**API Response:** When the end-user enters the valid user code then it will be redirected to the Hosted page to be authenticated, after the user authenticates, it redirects back to the return to the device page.

```
https://<hosted-page>/auth.aspx
?scope=device&
return_url=URLENCODE(https://cloud-api.lrinternal.com/sso/oauth/device/callback?state=<Random>)

```
**Step 3:** Use the [Request Token API](https://www.loginradius.com/docs/api/v2/single-sign-on/federated-sso/oauth-2-0/request-tokens/) to accept the authorization request.

```
 https://cloud-api.loginradius.com/sso/oauth/{OAuthName}/access_token
 Request Body
 {
 "client_id": "",
 "device_Code": "",
 "grant_type": "urn:ietf:params:oauth:grant-type:device_code"
}

```
**Required Parameters:**

Here is an explanation of the Request Parameters :

* **client_id:** LoginRadius API key.
* **device_code:** Enter the device code
* **Grant-Type:** Value should be used as **urn:ietf:params:oauth:grant-type:device_code**



**API Response Should be:**

- **Authorization Pending:**

The authorization request is still pending as the end-user hasn't yet completed the user code authentication. The client SHOULD repeat the Access Token Request to the token endpoint using polling. 

```

{
 "error":"authorization_pending",
 "error_description":""

}

```
- **Slow Down:**

```
Before each new request, the client MUST wait at least the number of seconds specified by the "interval" parameter of the Device Authorization Response. otherwise returns
{
 "error":"slow_down",
 "error_description":""
}

```
- **Expire Token:** 

```
When an access token is expired
{
 "error":"expired_token",
 "error_description":""
}

```
- **Access Denied:**

```
If the user denied the authorization
{
 "error":"access_denied",
 "error_description":""
}

```
- **Containing the access_token:**

```
{
 "access_token": "1fae8e10-f15a-4738-b475-8ca4f75a1052",
 "refresh_token": "2f5565610-f15a-4738-b475-fghyeruh7654",
 "token_type": "bearer",
 "expires": 3600
}

```
## Part 3 - Refresh Token

Once you have obtained an **access_token**, you can use the Refresh Access Token API to refresh the access_token.

**Required Parameters:**

Here is an explanation of the Request Body Parameter:

* **client_id:** [LoginRadius API key](https://www.loginradius.com/docs/api/v2/admin-console/platform-security/api-key-and-secret/#gettingyourapikeyandsecret0). 

* **client_secret:** [LoginRadius API secret](https://www.loginradius.com/docs/api/v2/admin-console/platform-security/api-key-and-secret/#gettingyourapikeyandsecret0).

* **grant_type:** The grant_type needs to be refresh_token.

* **refresh_token:** *This is the refresh_token you received when you used the 'Access token by OAuth 2 token' and 'Access Token by Account Password' API call

**API Response containing the refresh access_token:**

```
{
 "access_token": {Loginradius Access Token},

 "token_type": {type},

 "expires_in": {seconds till expiration},

 "refresh_token": {Refresh Token}

}

```




[Go Back to Home Page](https://lr-developer-docs.netlify.app)






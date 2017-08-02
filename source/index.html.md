---
title: API Reference

toc_footers:
  - <a target="_blank" href='https://www.mydigipass.com'>MYDIGIPASS</a>
  - <a target="_blank" href='https://developer.mydigipass.com'>MYDIGIPASS Developer site</a>
  - <hr/>
  - © 2010 - 2017
  - <a target="_blank" href='https://www.vasco.com'>VASCO Data Security International GmbH</a>
  - All Rights Reserved (1.5.57)
  - <hr/>

search: true

---


# OpenID Connect

> Examples will be shown in this column

Here are the changes made to MYDIGIPASS oauth endpoints to support [OpenID Connect Core](http://openid.net/specs/openid-connect-core-1_0.html).

# Configuration Endpoint

## GET /.well-known/openid-configuration

```sh
curl -X GET "https://www.mydigipass.com/.well-known/openid-configuration"
```

```json
{
  "issuer": "https://www.mydigipass.com",
  "authorization_endpoint": "https://www.mydigipass.com/oauth/authenticate",
  "token_endpoint": "https://www.mydigipass.com/oauth/token",
  "jwks_uri": "https://www.mydigipass.com/oauth/v1/jwks",
  "response_types_supported": [
    "code"
  ],
  "id_token_signing_alg_values_supported": [
    "RS256"
  ],
  "subject_types_supported": "public",
  "userinfo_signing_alg_values_supported": [
    "RS256"
  ],
  "acr_values_supported": []
}
```



This new endpoint returns a set of Claims about the OpenID Connect Provider's configuration, including all necessary endpoints and public key location information.


# JWS

```txt
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjVlMDJlNjMxLTc3ZjMtNGM3Yy1iMzZhLTVhMWVjMTNhNDhmYiJ9.eyJpc3MiOiJodHRwczovL215ZGlnaXBhc3MuY29tIiwic3ViIjoidGhlX3VzZXJfYWNjb3VudF91dWlkIiwiYXVkIjoieW91cl9hcHBsaWNhdGlvbnNfY2xpZW50X2lkIiwiaWF0IjoxNDk5NzU2NDcxLCJleHAiOjE0OTk3NTY3NzF9.YRnhRxrlLK5EGSG2U2L9dG0t04vCuAa4pKT5_RvwZkKb31Hrj17TeT_bzi8ljdgBI5KVksSjZOEf9b9LNeAOam9a0Q2NPWEHnnxOjLnMPaUyxtYS4ZW20A_gfPfJsjAchSYd6v-P6FR8ZgjzdMopLiVO0xfO5sNCBOK-Eg0kQoahNrih9FnmVpg7guy1EZA1JaVKBaLHHmENFYvduXbyhoIjb0NaWGz1I3xJinnbzEHHQ1Kt5vwe2Rn34y-ODC5aELqYMIQwYkwHOUn7GExZu8FUCgoL_ThFLkpirJI96-YUhgB0rUOGEyhACCwpTFwqsoFD7pGFJZgY95FNRfIW2g
```

A JWS is a string made of 3 parts separated by `.` as defined in [rfc7515](https://tools.ietf.org/html/rfc7515)

- JOSE Header
- JWS Payload
- JWS Signature



## JOSE header

```json
{
"typ": "JWT",
"alg": "RS256",
"kid": "5e02e631-77f3-4c7c-b36a-5a1ec13a48fb"
}
```

JSON object containing the parameters describing the cryptographic operations and parameters employed.

Key | Description
--- | ---
typ | type of token
alg | algorithm used to sign the JWT
kid | key id, the id of the public key used to sign the JWT the key itself is defined in the [](#jwks-endpoint) endpoint

## JWS Payload

The JSON object containing the requested data.

```json
{
"iss": "https://mydigipass.com",
"sub": "the_user_account_uuid",
"aud": "your_applications_client_id",
"iat": 1499756471,
"exp": 1499756771
}
```

## JWS Signature

Digital signature of the JWS Protected Header + the JWS Payload.

The public key required to verify this signature can be found on the [JWKS endpoint](#jwks-endpoint).

> For this example the public key in pem format is the following:

```txt
-----BEGIN PUBLIC KEY-----
MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAwBgyI8kB0BkWfTxIBsBc
1PpU9Dqag6uEw5HvsfFBj7bAzQpm8eG3uoD7YYqtE+TRIrY3FUTH6djn3rqpL+Us
O4th9QTOJQlvP0dkWCk7CfBcRLGAEht4zBUHa09Yu2rpkSednQ05eyV2SefymrW+
mINEDSSMrmHg0EyaMkkbqlBdCV8Potp5vtHXhESV1CMz9+81PdFBH+eEFO2f72FG
hgxhiqKmcvUqn1ImY7iqQZ4C1h/Y4lvkw/AViL/m9IL2p4zJ8LWYwRHu4HfDExJB
4LTn/XOdWrNQW4aTrbECAEKx/QTWWxnQ+odG46d0826CxpiEr6FKmVQWrWPEemBG
jwIDAQAB
-----END PUBLIC KEY-----
```

# JWKS Endpoint

## GET /oauth/v1/jwks

```sh
curl -X GET -H 'Accept: application/jwk-set+json' "https://www.mydigipass.com/oauth/v1/jwks"
```

```json
{
  "keys": [
    {
      "kty": "RSA",
      "e": "AQAB",
      "n": "wBgyI8kB0BkWfTxIBsBc1PpU9Dqag6uEw5HvsfFBj7bAzQpm8eG3uoD7YYqtE-TRIrY3FUTH6djn3rqpL-UsO4th9QTOJQlvP0dkWCk7CfBcRLGAEht4zBUHa09Yu2rpkSednQ05eyV2SefymrW-mINEDSSMrmHg0EyaMkkbqlBdCV8Potp5vtHXhESV1CMz9-81PdFBH-eEFO2f72FGhgxhiqKmcvUqn1ImY7iqQZ4C1h_Y4lvkw_AViL_m9IL2p4zJ8LWYwRHu4HfDExJB4LTn_XOdWrNQW4aTrbECAEKx_QTWWxnQ-odG46d0826CxpiEr6FKmVQWrWPEemBGjw",
      "kid": "5e02e631-77f3-4c7c-b36a-5a1ec13a48fb",
      "use": "sig"
    }
  ]
}
```

This endpoint returns a set of [JSON Web Keys](https://tools.ietf.org/html/rfc7517) that can be used to verify the signature of any [JWS](#jws) issued by MYDIGIPASS.

Key | Description
--- | ---
kid | id of the JWK, will be referenced in the JOSE header of any JWS document delivered by MYDIGIPASS
n   | modulus of the JWK
e   | exponent of the JWK
use | used for signature only

Note that these headers are set in the response and should also be set on your own JWKS endpoint:

Header          | Value
---             | ---
Content-Type  | `application/jwk-set+json`
Cache-Control | the amount of time you can cache the response e.g  `max-age=14947200, public`

# Authorization Endpoint

## GET or POST /oauth/authenticate

> This is most commonly done by adding `opendid` to the `data-scope` attribute of the MYDIGIPASS button.

The `openid` scope can be passed to our authorization endpoint.

# Token Endpoint

## POST /oauth/token

```sh
curl -X POST -H "Content-Type: application/json" -d '{"code": "your_authorization_code", "client_assertion": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJjbGllbnRfaWQiLCJhdWQiOiJodHRwOi8vbWRwLnRlc3Q6MzAwMC9vYXV0aC90b2tlbiIsImV4cCI6MTUwMjE4NTg2OSwianRpIjoiMGY2YzIyN2ItODU5Ni00NmUwLWE4M2UtOTMwM2IyNGZlY2RkIn0.3XGfDLWrzyKjKx1ZKuH9PO8H-ydHeEvA1DjhZqRIkVc", "client_assertion_type": "urn:ietf:params:oauth:client-assertion-type:jwt-bearer", "redirect_uri": "http://foo.bar", "grant_type": "authorization_code"}' "https://www.mydigipass.com/oauth/token"
```

The token endpoint now supports [Client Authentication](http://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication) using the `client_assertion` & `client_assertion_type` params.
Pass these new params instead of the `client_id` & `client_secret`

The values should be as such:

Param                 | Value
---                   | ---
client_assertion      | a [JWS](#jws) signed by yourself and verifiable using your own [JWKS endpoint](#jwks-endpoint). The payload for this client_assertion is defined [here](#clien)
client_assertion_type | `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |



```json
{
"account": "the_user_account_uuid",
"access_token": "access_token_for_userinfo_endpoint",
"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjVlMDJlNjMxLTc3ZjMtNGM3Yy1iMzZhLTVhMWVjMTNhNDhmYiJ9.eyJpc3MiOiJodHRwczovL215ZGlnaXBhc3MuY29tIiwic3ViIjoidGhlX3VzZXJfYWNjb3VudF91dWlkIiwiYXVkIjoieW91cl9hcHBsaWNhdGlvbnNfY2xpZW50X2lkIiwiaWF0IjoxNDk5NzU2NDcxLCJleHAiOjE0OTk3NTY3NzF9.YRnhRxrlLK5EGSG2U2L9dG0t04vCuAa4pKT5_RvwZkKb31Hrj17TeT_bzi8ljdgBI5KVksSjZOEf9b9LNeAOam9a0Q2NPWEHnnxOjLnMPaUyxtYS4ZW20A_gfPfJsjAchSYd6v-P6FR8ZgjzdMopLiVO0xfO5sNCBOK-Eg0kQoahNrih9FnmVpg7guy1EZA1JaVKBaLHHmENFYvduXbyhoIjb0NaWGz1I3xJinnbzEHHQ1Kt5vwe2Rn34y-ODC5aELqYMIQwYkwHOUn7GExZu8FUCgoL_ThFLkpirJI96-YUhgB0rUOGEyhACCwpTFwqsoFD7pGFJZgY95FNRfIW2g"
}
```

Now, the token endpoint will also return an `id_token` param containing a [JWS](#jws) with the [following payload](#id_token-payload).

## client_assertion JWS payload

```json
{
  "sub": "client_id",
  "aud": "http://mdp.test:3000/oauth/token",
  "exp": 1502185869,
  "jti": "0f6c227b-8596-46e0-a83e-9303b24fecdd"
}
```

Claim | Description
--- | ---
sub | your client_id
aud | `https://www.mydigipass.com/oauth/token`
exp | The expiration time on or after which the client_assertion will be rejected expressed as a [NumericDate](#terminology)
jti | a unique id that will invalidate any other further reuse of this client_assertion


## id_token JWS payload

```json
{
"iss": "https://mydigipass.com",
"sub": "the_user_account_uuid",
"aud": "your_applications_client_id",
"iat": 1499756471,
"exp": 1499756771
}
```

Claim     | Description
--------- | -----------
iat       | The time at which the id_token was issued
exp       | The expiration time on or after which the id_token will be rejected expressed as a [NumericDate](#terminology)

# User Data Endpoint

## GET /oauth/user_data

```sh
curl -X GET -H 'Accept: application/jwt' -H 'Authorization: Bearer access_token_for_userinfo_endpoint' 'https://www.mydigipass.com/oauth/user_data'
```

```txt
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjVlMDJlNjMxLTc3ZjMtNGM3Yy1iMzZhLTVhMWVjMTNhNDhmYiJ9.eyJpc3MiOiJodHRwczovL215ZGlnaXBhc3MuY29tIiwic3ViIjoidGhlX3VzZXJfYWNjb3VudF91dWlkIiwiYXVkIjoieW91cl9hcHBsaWNhdGlvbnNfY2xpZW50X2lkIiwiaWF0IjoxNDk5NzU2NDcxLCJleHAiOjE0OTk3NTY3NzF9.YRnhRxrlLK5EGSG2U2L9dG0t04vCuAa4pKT5_RvwZkKb31Hrj17TeT_bzi8ljdgBI5KVksSjZOEf9b9LNeAOam9a0Q2NPWEHnnxOjLnMPaUyxtYS4ZW20A_gfPfJsjAchSYd6v-P6FR8ZgjzdMopLiVO0xfO5sNCBOK-Eg0kQoahNrih9FnmVpg7guy1EZA1JaVKBaLHHmENFYvduXbyhoIjb0NaWGz1I3xJinnbzEHHQ1Kt5vwe2Rn34y-ODC5aELqYMIQwYkwHOUn7GExZu8FUCgoL_ThFLkpirJI96-YUhgB0rUOGEyhACCwpTFwqsoFD7pGFJZgY95FNRfIW2g
```

It's now possible to request the user_data as a [JWS](#jws).

Just set the `Accept` header of the request to "[application/jwt](https://www.iana.org/assignments/media-types/application/jwt)".

# Eid Data Endpoint

## GET /oauth/eid_data

```sh
curl -X GET -H 'Accept: application/jwt' -H 'Authorization: Bearer access_token_for_userinfo_endpoint' 'https://www.mydigipass.com/oauth/eid_data'
```

```txt
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjVlMDJlNjMxLTc3ZjMtNGM3Yy1iMzZhLTVhMWVjMTNhNDhmYiJ9.eyJpc3MiOiJodHRwczovL215ZGlnaXBhc3MuY29tIiwic3ViIjoidGhlX3VzZXJfYWNjb3VudF91dWlkIiwiYXVkIjoieW91cl9hcHBsaWNhdGlvbnNfY2xpZW50X2lkIiwiaWF0IjoxNDk5NzU2NDcxLCJleHAiOjE0OTk3NTY3NzF9.YRnhRxrlLK5EGSG2U2L9dG0t04vCuAa4pKT5_RvwZkKb31Hrj17TeT_bzi8ljdgBI5KVksSjZOEf9b9LNeAOam9a0Q2NPWEHnnxOjLnMPaUyxtYS4ZW20A_gfPfJsjAchSYd6v-P6FR8ZgjzdMopLiVO0xfO5sNCBOK-Eg0kQoahNrih9FnmVpg7guy1EZA1JaVKBaLHHmENFYvduXbyhoIjb0NaWGz1I3xJinnbzEHHQ1Kt5vwe2Rn34y-ODC5aELqYMIQwYkwHOUn7GExZu8FUCgoL_ThFLkpirJI96-YUhgB0rUOGEyhACCwpTFwqsoFD7pGFJZgY95FNRfIW2g
```

It's now possible to request the user_data as a [JWS](#jws).

Just set the `Accept` header of the request to "[application/jwt](https://www.iana.org/assignments/media-types/application/jwt)".

# Eid Photo Endpoint

The photo is always sent as a `type/image` and can't be fetched as a [JWS](#jws)

# Terminology

Term        | Description
---         | ---
NumericDate | A JSON numeric value representing the number of seconds from 1970-01-01T00:00:00Z UTC until the specified UTC date/time,


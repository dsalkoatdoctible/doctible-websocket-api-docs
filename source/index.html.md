---
title: API Reference

language_tabs: # must be one of https://github.com/rouge-ruby/rouge/wiki/List-of-supported-languages-and-lexers
  - javascript

toc_footers:
  - <a href='developer@doctible.com'>Contact us</a>
  - <a href='https://github.com/slatedocs/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true

code_clipboard: true

meta:
  - name: description
    content: Documentation for the Doctible Websocket API
---

# Introduction

The purpose of this API is to provide a simple way to stream UI events related to Patient Communicator product.

# Authentication

> To authorize, please generate JWT token, this could be the same token you use for the SSO but with different `jti` claim to avoid collision:

```c#
     IJwtAlgorithm algorithm = new HMACSHA256Algorithm();
    // learn more about JWT at https://jwt.io/
    string jwt = new JwtBuilder()
    .WithSecret(ssoSecret)
    .WithAlgorithm(algorithm)
    .AddClaim("uniqueId", uniqueId)
    .AddClaim("jti", nonce) // unique identifier for the token
    .AddClaim("iat", DateTimeOffset.Now.ToUnixTimeSeconds()) // issued at timestamp
    .AddClaim("exp", DateTimeOffset.Now.ToUnixTimeSeconds() + ExpiresIn) // token expiration timestamp
    .Encode();
```

> Make sure to replace ssoSecret with correct secret for location you are trying to connect to.

You will pass this JWT token in query string as `jwt` parameter. Also, you need to provide your partner code in query string as `partner` parameter.

# Include Doctible Websocket API client

```html
  <script src="https://doctible-cdn.s3-us-west-2.amazonaws.com/dist/doctible_client.min.js"></script>
```

## Establish connection

```javascript
    // staging
    const client = new DoctibleWebsocketClient('ws://ws.fairbill.me/cable?jwt=<JWT>&partner=<PARTNER>');
```

```javascript
    // production
    const client = new DoctibleWebsocketClient('ws://ws.doctible.com/cable?jwt=<JWT>&partner=<PARTNER>');
```

## Subscribe for updates 

```javascript
    client.connect(function() {
      const subscription = client.subscribe({
        connected() {
          console.log('Connected to PartnerChannel');
        },
        received(data) {
          console.log('Received data:', data); // { type: "unread_count", count: 1 }
        }
      });
    });
```

Currently, only `unread_count` event is supported. This event will be triggered when unread count changes or after initial connection.
The purpose of this event is to notify you about unread messages count in the Patient Communicator product.

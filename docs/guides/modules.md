---
title: "uPort Transports"
category: "guides"
type: "content"
---

## <a name="transport-guide"></a> uPort Transports Guide

`uport-transports` is a loosely coupled collection of functions called transports and utility functions used to set up communication channels between an application and a uPort client. Transports are simply functions that consume request messages and additional transport params, then they send these strings to a uPort client. Some the tranports will also manage receiving a response to a given request. Many of these function can be combined to create transports specific to your use case and enviroment. You can then use these transports in [uport-connect](https://github.com/uport-project/uport-connect) or use them in combination with message creation in [uport-credentials](https://github.com/uport-project/uport-credentials). If you are looking for quick start integration you will likely be better served by using the default tranports used in [uport-connect](https://github.com/uport-project/uport-connect).

 At this time there are three primary transports for handling requests:

- **QR Codes:** Messages are sent in a QR code to the mobile app client. You can use our default modal and flow here or configure your own QR codes. You can use our messaging server `Chasqui` to receive responses our have response returned to your own server.

- **URL Passing:** When a uPort client and app are on the same mobile device, requests and responses can be passed through URLs. Messages are sent in a URL and request are returned in a URL which is parsed and returned.

- **Push Notifications:** Messages are encrypted and sent to a uPort client through a push notification, using a push notification service provided by uPort.

There are two primary transports for handling response, otherwise you may just receive responses at a callback on your own server:

- **URL Passing:** Response is passed through a URL and parsed. Helper functions for parsing and change listeners are provided.

- **Message Server:** Response is relayed and fetched through a message server. You can run your own message server or you can use Chasqui by default, a message server service provided by uPort.

Beside the primary transports provided there is a number of smaller composable functions available to build your own transports for different needs. As we (and the community) build more transports for differing communication channels and differing uPort clients they can be added to `uport-transports`.

### <a name="quick-start"></a> Quick Start

Install through npm:

```shell
npm install uport-transports
```
Import specific modules. You will primarily use tranport. Message and crypto include utility functions for handling, parsing, encrypting, and decrypting messages for transports.

```javascript
import { transport, message, crypto } from 'uport-transports'
```
To send a request in our default QR code modal:

```javascript
const request = `eyJ0eXAiOiJKV1QiLCJhbG...`
const transportQR = transport.qr.send()
transportQR(request)
```

To send a request in our default QR code modal and use the message server transport and chasqui (the message server service provided by uPort) to get the response. This transport combines the QR send transport along with the message server tranport which handles responses. This assumes that chasqui was set as a callback in the request token. You can get a chasqui callback with utility function `transport.messageServer.genCallback()`

```javascript
const request = `eyJ0eXAiOiJKV1QiLCJhbG...`
const transportQRChasqui = transport.qr.chasquiSend()
transportQRChasqui(request).then(response => {
  // response to request returned here
})
```

To send a request in push notification. You can get a pushToken and pubEncKey for a user by requesting push notification permissions in a selective disclosure request. If the user accepts, these two values can be found in the response returned. You can handle the respones as you want and specify or you can combine this with the message server tranport to handle the response.

```javascript
const request = `eyJ0eXAiOiJKV1QiLCJhbG...`
const pushTransport = transport.push.send(pushToken, pubEncKey)
pushTransport(request)
```
To send the the request through a url when on the same mobile device as uPort app, whether from a mobile browser or a mobile application. Tranport adds necessary params for sending and handling response and then opens request url.

```javascript
const request = `eyJ0eXAiOiJKV1QiLCJhbG...`
const urlTransport = transport.url.send()
urlTransport(request)
```
To the get a response from a url:

```javascript
const response = tranport.url.getResponse()
```

Or listen for url response:
```javascript
tranport.url.onResponse().then(response => {
  ...
})
```

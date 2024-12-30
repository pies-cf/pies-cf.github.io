# Tunnels

## This service has been discontinued.
This page contains the docs to the old tunnels.

#### Why?
There was a few reasons I discontinued and removed http tunnels

1. Websockets: The tunnels did not support websockets due to the way they where coded.
2. The whole program was not made well. It used socket.io websockets, along with express.js backend which simply did not tunnel some infomation that should be tunneled. Instead of sending the raw request it read the headers, body, etc, and change it into a object which would be sent. Then on the server it would read the object that does not contain the original request and respond. This is also why websockets did not work.

#### What is *.tunnel.pies.cf used for now?
I have made a custom tcp tunnel software which is now hosted on `*.tunnel.pies.cf`\
Unfortunately this service is private. Maybe it will be public in the future.

#### The rest of this page is the docs to the old tunnels.
---

Protocal Infomation:
- [v0](./protocal/v0)
- [v1](./protocal/v1)

Last updated for protocal `v1`

**Table of contents**
- [Tunnels](#tunnels)
  - [This service has been discontinued.](#this-service-has-been-discontinued)
      - [Why?](#why)
      - [What is \*.tunnel.pies.cf used for now?](#what-is-tunnelpiescf-used-for-now)
      - [The rest of this page is the docs to the old tunnels.](#the-rest-of-this-page-is-the-docs-to-the-old-tunnels)
  - [What is this page about?](#what-is-this-page-about)
  - [What is a http tunnel?](#what-is-a-http-tunnel)
  - [How our tunnels work](#how-our-tunnels-work)
  - [Concept of these tunnels.](#concept-of-these-tunnels)
  - [What is the protocal?](#what-is-the-protocal)
  - [Connectcing to the socket](#connectcing-to-the-socket)
  - [Authenticating your tunnel](#authenticating-your-tunnel)
  - [Listening to a request](#listening-to-a-request)
  - [Responding to a request](#responding-to-a-request)
- [Other Info](#other-info)

## What is this page about?
This page contains information about how the **http tunnels** on [pies.cf](https://pies.cf) work.

Everything on this page is coded in `node.js / javascript`

Please note that only the tunnel client is open source and the server is closed source.

## What is a http tunnel?
For a example you have a website on `localhost:3000` and you want to share this with someone else that is not on your network.\
To do this you would have to port forward but with a tunnel you use a seperate software to make it accessable through `exampe.tunnel.pies.cf`\

## How our tunnels work

The program will connect to a socket and whenever a request is made to `example.tunnel.pies.cf` it will send all the information about that request through the socket to the client. The client will make a HTTP Request locally to the local-url and respond with it's data back.

This is the way the [default client](https://github.com/hi12167pies/pietunnel-client) work. You may implement this behaviour differently.

## Concept of these tunnels.

When you make a request to our servers if we use a socket to request your computer then you don't need to port forward.\
When you first open the socket you send a auth token which lets the server know which tunnel to use, and who owns it.\
After checking the token you will either get `auth_reject` with a reason or `auth_accept` with the current tunnel url.\
Then after this, the server will pass http requests and http responses through the socket.

## What is the protocal?

The protocal is a number letting the server know what version the client is on, if none is defined it defaults to `v0`, the first version and all packets sent to and back must use v0 protocal syntax. If it is v1 you must use v1 protocal syntax and commincuate in that way, etc, etc... older protocals may be removed later on.

## Connectcing to the socket
The tunnels system communicates through a socket. We use `socket.io`

The endpoint is: `https://pies.cf/__pies.cf/websocket/public/tunnel/`

To connect using `socket.io-client` (`npm install socket.io-client`):

```js
// require the socket.io library
const io = require("socket.io-client")

// create a socket
const socket = io("https://pies.cf/", {
  path: "/__pies.cf/websocket/public/tunnel/"
})
```

## Authenticating your tunnel
After connecting to the socket you will need to authenticate the socket with a tunnel.

- Get your authentication token from [the tunnels page](https://pies.cf/panel/tunnels)

You will now need to send this token on the socket as well as the protocal version.

First we need to listen to connecting to the socket, then send our token and the protocal version.

> The protocal version indicates what packets should look like and how they are sent a recieved.\
> The newest one will ussaly be the best as it might have more things or more support.
```js
socket.on("connect", () => {
  socket.emit("auth_token", [
    "PUT_YOUR_TOKEN_HERE", // Auth token
    1 // Protocal version 1
  ])
})
```

You may also want to know if the authentication failed or not.

```js
socket.on("auth_reject", (reason) => {
  // reason: A string of why the request was rejected.
  console.log(`Authentication rejected with reason ${reason}`)
})

socket.on("auth_accept", (data) => {
  const url = data[0]       // This is the url the website is now on.
  const protocal = data[1]  // This is the protocal that the server and client are using to comminicate
  console.log(`Authentication accepted with URL ${url} with protocal ${protocal}`)
})
```

## Listening to a request

When a request is made to `example.tunnel.pies.cf` it will send the following data through the socket.

It will look something like this:
```js
{
  id: 'ZEhWdWJtVnM6Ok1UWTNOamM0TnpVeU9EWTFOamhqWW1OaE1ESm1NR001TWpSbE56UmlNVGxrTkRZNU9UY3lOREJqWVRNMg==',
  method: 'GET',
  headers: {
    host: 'example.tunnel.pies.cf',
    connection: 'keep-alive',
    'cache-control': 'max-age=0',
    'sec-ch-ua': '"Chromium";v="110", "Not A(Brand";v="24", "Brave";v="110"',
    'sec-ch-ua-mobile': '?0',
    'sec-ch-ua-platform': '"Windows"',
    'upgrade-insecure-requests': '1',
    'user-agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/110.0.0.0 Safari/537.36',
    accept: 'text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8',
    'sec-gpc': '1',
    'accept-language': 'en-GB,en;q=0.7',
    'sec-fetch-site': 'none',
    'sec-fetch-mode': 'navigate',
    'sec-fetch-user': '?1',
    'sec-fetch-dest': 'document',
    'accept-encoding': 'gzip, deflate, br'
  },
  url: '/',
  body: {}
}
```

- `id` **This is a unique ID that referancees this request. When you send a response you will need this ID**
- `method` **This is the HTTP method sent in the request**
- `headers` **This is a list of headers sent in the request**
- `url` **This url sent in the request**
- `body` **This the body / data sent in the request**

To listen for this data you will need to listen for `web-request`

```js
socket.on("web-request", async (data) => {
  console.log(data)
  // request local ip here
})
```

## Responding to a request

After listening to a request you will need to respond to it so the final data can be sent back.

You will send a object like this:

```ts
{
  id: string,
  headers: object,
  data: string,
  status: number
}
```

- `id` **This is the id sent in the request.**
- `headers` **This is a object with the keys and values of the headers to respond with**
- `data` **This is a string of the data to send back to the client. Must be encoded in base64**
- `status` **A HTTP status code to be sent with the final response**

To respond:

```js
// Replace with your data.
// EG. Use axios to request the server then use it's response and send here.
// Check the offical github for an example. https://github.com/hi12167pies/pietunnel-client
socket.emit("web-response", {
  id: data.id,
  headers: {},
  data: Buffer.from("Data to respond with").toString("base64"),
  status: 200
})
```

# Other Info

Message packet:
```js
// If the website ever needs to send a message, eg. "Tunnels are currently down" this is the packet sent
socket.on("message", (message) => {
  console.log(message)
})
```
# Express HTTP2 Template
A express template to easily work with tests

## What is Express ?
express is a minimal and flexible Node.js web application framework that provides a robust set of features for web and mobile applications.

## What is HTTP2 ?
HTTP/2 (originally named HTTP/2.0) is a major revision of the HTTP network protocol used by the World Wide Web. It was derived from the earlier experimental SPDY protocol, originally developed by Google.

## Benefits
* Low overhead in parsing data — a critical value proposition in HTTP/2 vs HTTP1.
* Less prone to errors.
* Lighter network footprint.
* Effective network resource utilization.
* Eliminating security concerns associated with the textual nature of HTTP1.x such as response splitting attacks.
* Enables other capabilities of the HTTP/2 including compression, multiplexing, prioritization, flow control and effective handling of TLS.
* Compact representation of commands for easier processing and implementation.
* Efficient and robust in terms of processing of data between client and server.
* Reduced network latency and improved throughput.

## Node Libraries
* [express] (https://expressjs.com/)
* [spdy] (https://www.npmjs.com/package/spdy)

## How does it work ?
This project creates a REST endpoint returning a Json response. In order were built the following resources:

1. Certificates
```bash
# It is necessary to import your certificates on the files **server.crt** and **server.key**
# or
# generate them

openssl genrsa -des3 -passout pass:xpto -out server.pass.key 2048
openssl rsa -passin pass:xpto -in server.pass.key -out server.key
openssl x509 -req -sha256 -days 365 -in server.csr -signkey server.key -out server.crt
```

2. An Endpoint (__/api/health/router.js__) and a controller (__/api/health/controller.js__)
```javascript
/*
  Controller.js
*/

const _health = (req, res) => {
  return res.send(JSON.stringify({ status: 'OK' }));
};

const getController = () => ({
  health: (req, res, next) => _health(req, res, next)
});


module.exports.getController = getController;
```
```javascript
/*
  Router.js
*/
const express = require('express');
const controller = require('./controller');

const PATH = '/health';

const router = express.Router();

router.get('/', controller.getController().health);

module.exports = { path: PATH, router };
```

## Running
This project uses Docker, so it can be executed through the following command:
```bash
docker-compose up
```

After the endpoint will be accessible on:

__GET - https://localhost:3000/health__

## Note
If you are generating and signing your own certificate, web browsers will detect that your certificate it is not safe, and you will need to force the web browser to call you API. However it is possible to test it using **cURL**

```bash
curl https://localhost:3000/health/ -kiv

# *   Trying ::1...
# * TCP_NODELAY set
# * Connected to localhost (::1) port 3000 (#0)
# * ALPN, offering h2
# * ALPN, offering http/1.1
# * successfully set certificate verify locations:
# *   CAfile: /etc/pki/tls/certs/ca-bundle.crt
#   CApath: none
# * TLSv1.3 (OUT), TLS handshake, Client hello (1):
# * TLSv1.3 (IN), TLS handshake, Server hello (2):
# * TLSv1.2 (IN), TLS handshake, Certificate (11):
# * TLSv1.2 (IN), TLS handshake, Server key exchange (12):
# * TLSv1.2 (IN), TLS handshake, Server finished (14):
# * TLSv1.2 (OUT), TLS handshake, Client key exchange (16):
# * TLSv1.2 (OUT), TLS change cipher, Change cipher spec (1):
# * TLSv1.2 (OUT), TLS handshake, Finished (20):
# * TLSv1.2 (IN), TLS handshake, Finished (20):
# * SSL connection using TLSv1.2 / ECDHE-RSA-AES128-GCM-SHA256
# * ALPN, server accepted to use h2
# * Server certificate:
# *  subject: C=BR; ST=Sao Paulo; L=Itu; O=Test; OU=Test; CN=Test; emailAddress=test@test.com
# *  start date: Jun 28 14:23:23 2019 GMT
# *  expire date: Jun 27 14:23:23 2020 GMT
# *  issuer: C=BR; ST=Sao Paulo; L=Itu; O=Test; OU=Test; CN=Test; emailAddress=test@test.com
# *  SSL certificate verify result: self signed certificate (18), continuing anyway.
# * Using HTTP2, server supports multi-use
# * Connection state changed (HTTP/2 confirmed)
# * Copying HTTP/2 data in stream buffer to connection buffer after upgrade: len=0
# * Using Stream ID: 1 (easy handle 0x558dd1d4f590)
# > GET /health/ HTTP/2
# > Host: localhost:3000
# > User-Agent: curl/7.61.1
# > Accept: */*
# >
# * Connection state changed (MAX_CONCURRENT_STREAMS == 4294967295)!
# < HTTP/2 200
# HTTP/2 200
# < x-powered-by: Express
# x-powered-by: Express
# < access-control-allow-origin: *
# access-control-allow-origin: *
# < content-type: application/json; charset=utf-8
# content-type: application/json; charset=utf-8
# < content-length: 15
# content-length: 15
# < etag: W/"f-v/Y1JusChTxrQUzPtNAKycooOTA"
# etag: W/"f-v/Y1JusChTxrQUzPtNAKycooOTA"

# <
# * Connection #0 to host localhost left intact
# {"status":"OK"}%
```

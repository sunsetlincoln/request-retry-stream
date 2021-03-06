# request-retry-stream 

[![npm version](https://badge.fury.io/js/request-retry-stream.svg)](https://badge.fury.io/js/request-retry-stream) [![Build Status](https://travis-ci.org/debitoor/request-retry-stream.svg?branch=master)](https://travis-ci.org/debitoor/request-retry-stream) [![Dependency Status](https://david-dm.org/debitoor/request-retry-stream.svg)](https://david-dm.org/debitoor/request-retry-stream) [![devDependency Status](https://david-dm.org/debitoor/request-retry-stream/dev-status.svg)](https://david-dm.org/debitoor/request-retry-stream#info=devDependencies) [![Coverage Status](https://coveralls.io/repos/github/debitoor/request-retry-stream/badge.svg?branch=master)](https://coveralls.io/github/debitoor/request-retry-stream?branch=master)

	npm install request-retry-stream

Request wrapper with retries, supports streaming. Takes the same options as the 
[request](https://github.com/request/request#readme) module.

NOTE: only GET http requests are supported when streaming.

There is support for POST, PUT, DELETE and PATCH retrying using callbacks but not streaming
(be careful with retrying POST it's not idempotent)

## Non-2XX http statusCodes

Non-2XX http statusCodes are returned as errors with
the information needed for debugging.

If you want to pass non-2XX error-codes through when streaming use the option
`passThrough: true`

## Simple callback usage

```javascript
var rrs = require('request-retry-stream');
rrs.get({url: 'http://google.com', timeout: 5000}, function(err, resp){
	// handle err and resp. Any response that does not have http status code 2XX is an error here
});

```

## Example of error returned

The error returned in case of non 2XX, has all the options passed to rrs, 
as well as a `statusCode`, `message`, `stack`, `attemptsDone` and the `body` returned 
for the request.

```js
{
    message: 'Error in request statusCode: 400',
    stack: '...[STACKTRACE]...',
    statusCode: 400,
    url: 'http://example.com',
    attemptsDone: 1,
    method: 'POST'
    body: '...[BODY RETURNED FROM REQUEST]...'
    timeout: 5000
}
```

## Simple stream usage in express middleware with pump

```javascript
var rrs = require('request-retry-stream');
var pump = require('pump');

function(req, res, next){
	pump(rrs.get('http://google.com', {timeout: 5000}), res, next);
}
```

## Usage in express middleware with pump

```javascript
var rrs = require('request-retry-stream');
var pump = require('pump');

function(req, res, next){
	var stream = rrs.get({
			url: 'http://google.com',
			attempts: 3, //default
			delay: 500, //default
			timeout: 2000,
			logFunction: console.warn // optional, if you want to be notified about retry
		});	
	pump(stream, res, next);
}

```


## License

[MIT](http://opensource.org/licenses/MIT)

- [rext](#)
	- [Quick Import](#quick-import)
	- [IE 9- Support](#ie-9--support)
	- [API](#api)
		- [XMLHttpRequest](#xmlhttprequest)
		- [JSONP](#jsonp)
		- [Promise Object Returned](#promise-object-returned)
	- [Options](#options)
	- [Case Matrix](#case-matrix)
	- [Take a Look at headers['Content-Type']](#take-a-look-at-headerscontent-type)
	- [Take a Look at responseType](#take-a-look-at-responsetype)
	- [License](#license)

<h1 align="center">rext</h1>

![gzip size](http://img.badgesize.io/https://raw.githubusercontent.com/shenfe/rext/master/dist/rext.min.js?compression=gzip)
<a href="https://www.npmjs.com/package/rexter"><img src="https://img.shields.io/npm/v/rexter.svg"></a>
![license](https://img.shields.io/npm/l/rexter.svg)

A lightweight (< 5kb gzipped) and **Promise-supported** HTTP request library, for all browsers (i.e. even cross-domain requests in IE 6 are possible).

| <img src="https://raw.githubusercontent.com/godban/browsers-support-badges/master/src/images/edge.png" alt="IE / Edge" width="16px" height="16px" /> IE / Edge | <img src="https://raw.githubusercontent.com/godban/browsers-support-badges/master/src/images/chrome.png" alt="Chrome" width="16px" height="16px" /> Chrome | <img src="https://raw.githubusercontent.com/godban/browsers-support-badges/master/src/images/firefox.png" alt="Firefox" width="16px" height="16px" /> Firefox | <img src="https://raw.githubusercontent.com/godban/browsers-support-badges/master/src/images/safari.png" alt="Safari" width="16px" height="16px" /> Safari |
| :---: | :---: | :---: | :---: |
| 6+ ✔ | All ✔ | All ✔ | All ✔ |

## Quick Import

```bash
$ npm install --save rexter
```

```js
var rext = require('rexter');
```

## IE 9- Support

If IE9- browsers are required to **send cross-domain requests with user credentials**, define the invoker-hostname whitelist in `dist/iframe-agent.html` and put it at the root path of the target origin.

For example:

Define the invoker-hostname whitelist in `iframe-agent.html`:

> Replace
> ```js
> [/* Define a whitelist of host names here, e.g. '.invoker.com'. */]
> ```
> with
> ```js
> ['.my-domain.com', '.my-domain1.com']
> ```

Additianally, before `rext` is imported, the JSON polyfill (e.g. [JSON-js](https://github.com/douglascrockford/JSON-js)) shall be executed.

⚠️ `rext` does not include full polyfills in itself.

## API

As simple as `rext(options)`.

Besides, the format of jQuery ajax's option object is also allowed:

```js
{
    type: 'post',
    url: '/path/to/api',
    data: {},
    contentType: 'application/json',
    dataType: 'json',
    xhrFields: {
        withCredentials: true
    },
    success: function () { /**/ },
    error: function () { /**/ },
    complete: function () { /**/ }
}
```

### XMLHttpRequest

The `success`, `error`, `always` callbacks are allowed both a chain call and defined in the option object.

```js
rext({
    url: '/path/to/resource',
    data: { /**/ },
    promise: false
}).success((data, response) => {
    /**/
}).error((data, response) => {
    /**/
}).always((data, response) => {
    /**/
});
```

### JSONP

The callback function is allowed either as the second parameter behind the option object or defined in the option object.

```js
rext({
    jsonp: true,
    url: '/path/to/resource',
    data: { /**/ }
}, data => {
    /**/
});
```

### Promise Object Returned

A Promise object is returned by default. Set property `promise` value `false` if you do not want to get a Promise object or you prefer to use `success` and `error` methods actually.

```js
rext({
    url: '/path/to/resource',
    data: { /**/ }
}).then(data => {
    /**/
}).catch(err => {
    /**/
});
```

👏 [shenfe/promises-aplus](https://github.com/shenfe/promises-aplus) is used as a polyfill when the browser does not support Promise.

## Options

Instructions of the option object:

| Property | Type | Value |
| :---: | :---: | :--- |
| `type` | `{Undefined\|String}` | 'get' (default), 'post'. |
| `url` | `{String}` | The resource url string. |
| `data` | `{Undefined\|Object}` | The data to send. Object recommended. |
| `withCredentials` | `{Undefined\|Boolean}` | undefined (false, as default), true. The `withCredentials` property of the request. Whether to send user credentials with the request to another origin or not. An `xhrFields` object with `withCredentials` property of value `true` is accepted as well. |
| `agent` | `{Undefined\|Boolean}` | undefined (false, as default), true. Whether to fall back to the iframe agent directly when the request is cross-domain and the browser is IE 9-. |
| `agentPageUrl` | `{Undefined\|String}` | undefined (\`${targetOrigin}/iframe-agent.html\`, as default), a url string. Specify the url of the iframe agent page if it's not in the root path of the target origin. |
| `responseType` (or `dataType`) | `{Undefined\|String}` | 'text' (default), 'json', .etc. Similar to the `dataType` option in jQuery ajax. A simple trial of JSON parsing would be conducted upon the response data besides the MIME type. See below for more. |
| `headers` | `{Undefined\|Object}` | The request headers object. Usually used to define the `Content-Type` property (similar to the `contentType` option in jQuery ajax), of which 'application/x-www-form-urlencoded' is the default value. See below for more. |
| `contentType` | `{Undefined\|String}` | The same as `headers['Content-Type']`. |
| `jsonp` | `{Undefined\|Boolean}` | undefined (false, as default), true. The same as setting `responseType` (or `dataType`) `jsonp`. |
| `promise` | `{Undefined\|Boolean\|Function}` | undefined, false, true, or a Promise object constructor. Whether to return a Promise object. Set it `false` if not for a Promise object. |
| `simple` | `{Undefined\|Boolean}` | undefined (false, as default), true. Whether not to force an X-Requested-With header in an XHR. |

## Case Matrix

All the cases of browser requests.

| Cross-Domain | With-Credentials | Web Browser | Approach | Restriction | Security |
| :---: | :---: | :---: | :---: | :--- | :--- |
| no | - | IE 6- | ActiveXObject | Almost the same API as the XMLHttpRequest Object. | - |
| no | - | IE 7-9 | XMLHttpRequest | [The XMLHttpRequest Object](https://www.w3.org/TR/2006/WD-XMLHttpRequest-20060405/). | - |
| yes | no | IE 8-9 | XDomainRequest | [XDomainRequest - Restrictions, Limitations and Workarounds](https://blogs.msdn.microsoft.com/ieinternals/2010/05/13/xdomainrequest-restrictions-limitations-and-workarounds/) | - |
| no | - | IE 10-11, non-IE | XMLHttpRequest (Level 2) | [XMLHttpRequest Level 2](https://xhr.spec.whatwg.org/). IE 10-11 do not support value `json` as XHR's `responseType`, but it doesn't matter. | - |
| yes | - | IE 10-11, non-IE | XMLHttpRequest (Level 2) | Server responses should include the `Access-Control-Allow-Origin` HTTP response header with value `*`, or the exact origin of the calling page. | - |
| yes | yes | IE 10-11, non-IE | XMLHttpRequest (Level 2) | Server responses should include the `Access-Control-Allow-Origin` HTTP response header with the exact origin of the calling page, and the `Access-Control-Allow-Credentials` HTTP response header with value `true`. | - |
| - | - | - | JSONP | - | [Security concerns](https://en.wikipedia.org/wiki/JSONP#Security_concerns) |
| - | - | - | iframe agent | Be put in a specific place of the target origin. | A whitelist of visitor origins is required. |

## Take a Look at `headers['Content-Type']`

The MIME type of data to **send**, like the `contentType` in jQuery ajax.

| Value | Effect |
| :---: | :--- |
| `application/x-www-form-urlencoded` | The default, recommended. Almost the same as the url query string. |
| `multipart/form-data` | [HTML 4 specification](https://www.w3.org/TR/html401/interact/forms.html#h-17.13.4). This allows entire files to be included in the data. Use this when the form includes any `<input type="file">` element. In this case, `Content-Type` would be ignored because the browser set this header with a boundary generated by itself. |
| `text/plain` | [HTML 5 specification](https://www.w3.org/TR/html5/forms.html#text/plain-encoding-algorithm). Not recommended. Do not use it unless for debugging. |
| `application/json` | **Personally** not recommended for common POST requests. Make sure you really need to post complex data with user credentials. Additionally, for CORS requests, setting the content type to anything other than `application/x-www-form-urlencoded`, `multipart/form-data`, or `text/plain` will trigger the browser to send a preflight OPTIONS request to the server. |

## Take a Look at `responseType`

The alias of the expected MIME type of data to **receive**, similar to the `dataType` in jQuery ajax. This option should affect the request header `Accept` and how to treat the response data, relating the response header `Content-Type`. However, **a simple trial of JSON parsing** would be conducted then regardless of the type of response data.

According to the [specification](https://xhr.spec.whatwg.org/#the-responsetype-attribute) of `responseType`, the value can be:

| Value | Description |
| :---: | :--- |
| text | Default. |
| json | - |
| blob | If to receive files. |
| arraybuffer | If to receive files. |
| document | Seldom. |

## License

[MIT](http://opensource.org/licenses/MIT)

Copyright © 2017-present, [shenfe](https://github.com/shenfe)

# It's AJAX

All over again. Includes support for xmlHttpRequest, JSONP, CORS, and CommonJS Promises A.

It is also isomorphic allowing you to `require('reqwest')` in `Node.js` through the peer dependency [xhr2](https://github.com/pwnall/node-xhr2), albeit the original intent of this library is for the browser. For a more thorough solution for Node.js, see [mikeal/request](https://github.com/request/request).

## API

``` js
reqwest('path/to/html', function (resp) {
  qwery('#content').html(resp)
})

reqwest({
    url: 'path/to/html'
  , method: 'post'
  , data: { foo: 'bar', baz: 100 }
  , success: function (resp) {
      qwery('#content').html(resp)
    }
})

reqwest({
    url: 'path/to/html'
  , method: 'get'
  , data: [ { name: 'foo', value: 'bar' }, { name: 'baz', value: 100 } ]
  , success: function (resp) {
      qwery('#content').html(resp)
    }
})

reqwest({
    url: 'path/to/json'
  , type: 'json'
  , method: 'post'
  , error: function (err) { }
  , success: function (resp) {
      qwery('#content').html(resp.content)
    }
})

reqwest({
    url: 'path/to/json'
  , type: 'json'
  , method: 'post'
  , contentType: 'application/json'
  , headers: {
      'X-My-Custom-Header': 'SomethingImportant'
    }
  , error: function (err) { }
  , success: function (resp) {
      qwery('#content').html(resp.content)
    }
})

// Uses XMLHttpRequest2 credentialled requests (cookies, HTTP basic auth) if supported
reqwest({
    url: 'path/to/json'
  , type: 'json'
  , method: 'post'
  , contentType: 'application/json'
  , crossOrigin: true
  , withCredentials: true
  , error: function (err) { }
  , success: function (resp) {
      qwery('#content').html(resp.content)
    }
})

reqwest({
    url: 'path/to/data.jsonp?callback=?'
  , type: 'jsonp'
  , success: function (resp) {
      qwery('#content').html(resp.content)
    }
})

reqwest({
    url: 'path/to/data.jsonp?foo=bar'
  , type: 'jsonp'
  , jsonpCallback: 'foo'
  , jsonpCallbackName: 'bar'
  , success: function (resp) {
      qwery('#content').html(resp.content)
    }
})

reqwest({
    url: 'path/to/data.jsonp?foo=bar'
  , type: 'jsonp'
  , jsonpCallback: 'foo'
  , success: function (resp) {
      qwery('#content').html(resp.content)
    }
  , complete: function (resp) {
      qwery('#hide-this').hide()
    }
})
```

## Promises

``` js
reqwest({
    url: 'path/to/data.jsonp?foo=bar'
  , type: 'jsonp'
  , jsonpCallback: 'foo'
})
  .then(function (resp) {
    qwery('#content').html(resp.content)
  }, function (err, msg) {
    qwery('#errors').html(msg)
  })
  .always(function (resp) {
    qwery('#hide-this').hide()
  })
```

``` js
reqwest({
    url: 'path/to/data.jsonp?foo=bar'
  , type: 'jsonp'
  , jsonpCallback: 'foo'
})
  .then(function (resp) {
    qwery('#content').html(resp.content)
  })
  .fail(function (err, msg) {
    qwery('#errors').html(msg)
  })
  .always(function (resp) {
    qwery('#hide-this').hide()
  })
```

``` js
var r = reqwest({
    url: 'path/to/data.jsonp?foo=bar'
  , type: 'jsonp'
  , jsonpCallback: 'foo'
  , success: function () {
      setTimeout(function () {
        r
          .then(function (resp) {
            qwery('#content').html(resp.content)
          }, function (err) { })
          .always(function (resp) {
             qwery('#hide-this').hide()
          })
      }, 15)
    }
})
```

## Options

  * `url` a fully qualified uri
  * `method` http method (default: `GET`)
  * `headers` http headers (default: `{}`)
  * `data` entity body for `PATCH`, `POST` and `PUT` requests. Must be a query `String` or `JSON` object
  * `type` a string enum. `html`, `xml`, `json`, or `jsonp`. Default is inferred by resource extension. Eg: `.json` will set `type` to `json`. `.xml` to `xml` etc.
  * `contentType` sets the `Content-Type` of the request. Eg: `application/json`
  * `crossOrigin` for cross-origin requests for browsers that support this feature.
  * `success` A function called when the request successfully completes
  * `error` A function called when the request fails.
  * `complete` A function called whether the request is a success or failure. Always called when complete.
  * `jsonpCallback` Specify the callback function name for a `JSONP` request. This value will be used instead of the random (but recommended) name automatically generated by reqwest.

## Predefined options

Use `.ajaxSetup` to set up predefined values for all options, like headers.

``` js
reqwest.ajaxSetup({
  headers: {
    'X-CSRF-Token': document.querySelector('[name="csrf-token"]').content
  }
});
```

## Security

If you are *still* requiring support for IE6/IE7, consider including [JSON3](https://bestiejs.github.io/json3/) in your project. Or simply do the following

``` html
<script>
(function () {
  if (!window.JSON) {
    document.write('<scr' + 'ipt src="http://cdnjs.cloudflare.com/ajax/libs/json3/3.3.2/json3.min.js"><\/scr' + 'ipt>')
  }
}());
</script>
```


## Contributing

``` sh
$ git clone git://github.com/ded/reqwest.git reqwest
$ cd !$
$ npm install
```

Please keep your local edits to `src/reqwest.js`.
The base `./reqwest.js` and `./reqwest.min.js` will be built upon releases.

## Running Tests

``` sh
make test
```

## Browser support

  * IE6+
  * Chrome 1+
  * Safari 3+
  * Firefox 1+
  * Opera

## Ender Support
Reqwest can be used as an [Ender](http://enderjs.com) module. Add it to your existing build as such:

    $ ender add reqwest

Use it as such:

``` js
$.ajax({ ... })
```

Serialize things:

``` js
$(form).serialize() // returns query string -> x=y&...
$(form).serialize({type:'array'}) // returns array name/value pairs -> [ { name: x, value: y}, ... ]
$(form).serialize({type:'map'}) // returns an object representation -> { x: y, ... }
$(form).serializeArray()
$.toQueryString({
    foo: 'bar'
  , baz: 'thunk'
}) // returns query string -> foo=bar&baz=thunk
```

Or, get a bit fancy:

``` js
$('#myform input[name=myradios]').serialize({type:'map'})['myradios'] // get the selected value
$('input[type=text],#specialthing').serialize() // turn any arbitrary set of form elements into a query string
```

## ajaxSetup
Use the `request.ajaxSetup` to predefine a data filter on all requests. See the example below that demonstrates JSON hijacking prevention:

``` js
$.ajaxSetup({
  dataFilter: function (response, type) {
    if (type == 'json') return response.substring('])}while(1);</x>'.length)
    else return response
  }
})
```

## RequireJs and Jam
Reqwest can also be used with RequireJs and can be installed via jam

```
jam install reqwest
```

```js
define(function(require){
  var reqwest = require('reqwest')
});
```

## spm
Reqwest can also be installed via spm [![](http://spmjs.io/badge/reqwest)](http://spmjs.io/package/reqwest)

```
spm install reqwest
```

## jQuery and Zepto Compatibility
There are some differences between the *Reqwest way* and the
*jQuery/Zepto way*.

### method ###
jQuery/Zepto use `type` to specify the request method while Reqwest uses
`method` and reserves `type` for the response data type.

### dataType ###
When using jQuery/Zepto you use the `dataType` option to specify the type
of data to expect from the server, Reqwest uses `type`. jQuery also can
also take a space-separated list of data types to specify the request,
response and response-conversion types but Reqwest uses the `type`
parameter to infer the response type and leaves conversion up to you.

### JSONP ###
Reqwest also takes optional `jsonpCallback` and `jsonpCallbackName`
options to specify the callback query-string key and the callback function
name respectively while jQuery uses `jsonp` and `jsonpCallback` for
these same options.


But fear not! If you must work the jQuery/Zepto way then Reqwest has
a wrapper that will remap these options for you:

```js
reqwest.compat({
    url: 'path/to/data.jsonp?foo=bar'
  , dataType: 'jsonp'
  , jsonp: 'foo'
  , jsonpCallback: 'bar'
  , success: function (resp) {
      qwery('#content').html(resp.content)
    }
})

// or from Ender:

$.ajax.compat({
  ...
})
```

If you want to install jQuery/Zepto compatibility mode as the default
then simply place this snippet at the top of your code:

```js
$.ajax.compat && $.ender({ ajax: $.ajax.compat });
```


**Happy Ajaxing!**

<h3 id='res.cookie'>res.cookie(name, value [, options])</h3>

Sets cookie `name` to `value`.  The `value` parameter may be a string or object converted to JSON.

The `options` parameter is an object that can have the following properties.

| Property    | Type |  Description                                                             |
|-------------|-------------------------------------------------------------------------|
| `domain`    | String | Domain name for the cookie. Defaults to the domain name of the app.
| `encode`    | Function | A synchronous function used for cookie value encoding. Defaults to `encodeURIComponent`.
| `expires`   | Date | Expiry date of the cookie in GMT. If not specified or set to 0, creates a session cookie.
| `httpOnly`  | Boolean | Flags the cookie to be accessible only by the web server.
| `maxAge`    | Number | Convenient option for setting the expiry time relative to the current time in milliseconds.
| `path`      | String | Path for the cookie. Defaults to "/".
| `secure`    | Boolean | Marks the cookie to be used with HTTPS only.
| `signed`    | Boolean | Indicates if the cookie should be signed.
| `sameSite`  | Boolean or String | Value of the "SameSite" **Set-Cookie** attribute. More information at [https://tools.ietf.org/html/draft-ietf-httpbis-cookie-same-site-00#section-4.1.1](https://tools.ietf.org/html/draft-ietf-httpbis-cookie-same-site-00#section-4.1.1).

<div class="doc-box doc-notice" markdown="1">
All `res.cookie()` does is set the HTTP `Set-Cookie` header with the options provided.
Any option not specified defaults to the value stated in [RFC 6265](http://tools.ietf.org/html/rfc6265).
</div>

For example:

```js
res.cookie('name', 'tobi', { domain: '.example.com', path: '/admin', secure: true })
res.cookie('rememberme', '1', { expires: new Date(Date.now() + 900000), httpOnly: true })
```

The `encode` option allows you to choose the function used for cookie value encoding.
Does not support asynchronous functions.

Example use case: You need to set a domain-wide cookie for another site in your organization.
This other site (not under your administrative control) does not use URI-encoded cookie values.

```js
// Default encoding
res.cookie('some_cross_domain_cookie', 'http://mysubdomain.example.com', { domain: 'example.com' })
// Result: 'some_cross_domain_cookie=http%3A%2F%2Fmysubdomain.example.com; Domain=example.com; Path=/'

// Custom encoding
res.cookie('some_cross_domain_cookie', 'http://mysubdomain.example.com', { domain: 'example.com', encode: String })
// Result: 'some_cross_domain_cookie=http://mysubdomain.example.com; Domain=example.com; Path=/;'
```

The `maxAge` option is a convenience option for setting "expires" relative to the current time in milliseconds.
The following is equivalent to the second example above.

```js
res.cookie('rememberme', '1', { maxAge: 900000, httpOnly: true })
```

You can pass an object as the `value` parameter; it is then serialized as JSON and parsed by `bodyParser()` middleware.

```js
res.cookie('cart', { items: [1, 2, 3] })
res.cookie('cart', { items: [1, 2, 3] }, { maxAge: 900000 })
```

When using [cookie-parser](https://www.npmjs.com/package/cookie-parser) middleware, this method also
supports signed cookies. Simply include the `signed` option set to `true`.
Then, `res.cookie()` will use the secret passed to `cookieParser(secret)` to sign the value.

```js
res.cookie('name', 'tobi', { signed: true })
```

Later, you may access this value through the [req.signedCookies](#req.signedCookies) object.

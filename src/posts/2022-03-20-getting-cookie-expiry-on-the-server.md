---
layout: post
title: Getting a cookie's expiry value on a server
slug: getting-a-cookies-expiry-value-on-a-server
tags: ['node', 'cookie']
---

Browsers handle cookie expiry so they don't pass the cookie's expiry value to the server. You have to make some adjustments if you want to get the cookie's expiry value on the server.

There are two methods:

- You can create a cookie with a JSON value
- You can use another cookie to signify the expiry

<!-- more -->

## Creating a cookie with a JSON value

You can create a cookie with a JSON value. It looks like this:

```js
const cookieValue = JSON.stringify({
  value: 'hello world',
  expiry: Date.now() + 3600 * 1000
})

res.cookie('myCookie', cookieValue)
```

If you use Express, you can indicate to [cookie-parser](https://www.npmjs.com/package/cookie-parser) that a cookie is a JSON cookie by prepending the value with a `j:`. Cookie-parser will automatically decode the JSON cookie and turn it back into an object.

```js
// Setting a JSON cookie for cookie-parser
const cookieValue = JSON.stringify({
  value: 'hello world',
  expiry: Date.now() + 3600 * 1000
})

res.cookie('myCookie', `j: ${cookieValue}`)
```

```js
// Reading the JSON cookie
import cookieParser from 'cookie-parser'

app.use(cookieParser())

app.get('/', (req, res) => {
  const { myCookie } = req.cookies

  if (myCookie.expiry < Date.now()) {
    // Do something
  }
})
```

Of course, if you want the browser to remove the cookie automatically when it expires, you can still set the `maxAge` property.

```js
res.cookie('myCookie', 'j:' + cookieValue, { maxAge: 3600 * 1000 })
```

## Creating another cookie to store the expiry

You can create another cookie to store the expiry value. Here's what it looks like (including the `maxAge` property).

```js
res.cookie('myCookie', 'hello world', { maxAge: 3600 * 1000 })
res.cookie('myCookieExpiry', Date.now() + 3600 * 1000 { maxAge: 3600 * 1000 })
```

You'd be able to check the cookie expiry value in the server like this:

```js
import cookieParser from 'cookie-parser'
app.use(cookieParser())

app.get('/', (req, res) => {
  const { myCookie, myCookieExpiry } = req.cookies

  if (myCookieExpiry < Date.now()) {
    // Do something
  }
})
```

That's it!

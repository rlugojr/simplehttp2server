`simplehttp2server` serves the current directory on an HTTP/2.0 capable server. This server is for development purposes only. `simplehttp2server` takes a JSON config that allows you to configure headers, redirects and URL rewrites in a lightweight JSON fromat.

The format is partially compatible with [Firebase’s JSON config]. Please see [disclaimer](#firebase-disclaimer) below.

# Installation
## Binaries
`simplehttp2server` is `go get`-able:

```
$ go get github.com/GoogleChrome/simplehttp2server
```

Precompiled binaries can be found in the [release section](https://github.com/GoogleChrome/simplehttp2server/releases).

## Brew
You can also install `simplehttp2server` using brew if you are on macOS:

```
$ brew tap GoogleChrome/simplehttp2server https://github.com/GoogleChrome/simplehttp2server
$ brew install simplehttp2server
```

## Docker
If you have Docker set up, you can serve the current directory via `simplehttp2server` using the following command:

```
$ docker run -p 5000:5000 -v $PWD:/data surma/simplehttp2server [-config firebase.json]
```

# Config

`simplehttp2server` can be configured with the `-config` flag and a JSON config file. This way you can add custom headers, rewrite rules and redirects. It is partially compatible with [Firebase’s JSON config].

All `source` fields take the [Extglob] syntax.

## Redirects

```js
{
  "redirects": [
    {
      "source": "/shortlinks/a",
      "destination": "https://google.com",
      "type": 301
    }
  ]
}
```

## Rewrites

Rewrites are useful for SPAs, where all paths return `index.html` and the routing is taking care of in the app itself. Rewrites are only applied when the original target file does not exist.

```js
{
  "rewrites": [
    {
      "source": "/app/**",
      "destination": "/index.html"
    }
  ]
}
```

## Headers

```js
{
  "headers": [
    {
      "source": "/**.html",
      "headers": [
        {
          "key": "Cache-Control",
          "value": "max-age=3600"
        }
      ]
    },
    {
      "source": "/index.html",
      "headers": [
        {
          "key": "Cache-Control",
          "value": "no-cache"
        },
        {
          "key": "Link",
          "value": "</header.jpg>; rel=preload; as=image, </app.js>; rel=preload; as=script"
        }
      ]
    }
  ]
}
```

For details see the [Firebase’s documentation][Firebase’s JSON config].

## Firebase Disclaimer

I haven’t tested if the behavior of `simplehttp2server` _always_ matches the live server of Firebase, and some options (like `trailingSlash` and `cleanUrls`) are completely missing. Please open an issue if you find a discrepancy! The support is not offically endorsed by Firebase (yet 😜), so don’t rely on it!

## HTTP/2 PUSH

Any `Link` headers with `rel=preload` will be translated to a HTTP/2 PUSH, [as is common practice on static hosting platforms and CDNs](https://w3c.github.io/preload/#server-push-http-2). See the [example](#headers) above.

# License

Apache 2.

[Extglob]: https://www.npmjs.com/package/extglob
[Firebase’s JSON config]: https://firebase.google.com/docs/hosting/full-config

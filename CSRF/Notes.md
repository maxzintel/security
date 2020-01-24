# Cross-Site Request Forgery
  * Writing code on your site to make requests to another site.
  * Cookies/Basic Auth credz are passed along with requests.
  * Great reason to align with REST convention.
  * Ex:
  ```html
  <form id="money" method="post" action="http://localhost:3000/transfers">
    <input type="hidden" name="accountFrom" value="2"/>
    <input type="hidden" name="accountTo" value="1"/>
    <input type="hidden" name="amount" value="100"/>
  </form>

  <script type="text/javascript">
    document.getElementById('money').submit();
  </script>
  ```
  * Ex2:
  ```html
  <html>
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width">
    <title>JSBin</title>
  </head>
  <body>
    This is XSS
    <img src="http://localhost:3000/transfers/perform?accountFrom=2&accountTo=1&amount=1">
  </body>
  </html>
  ```
  * GET requests can be embedded in things that make requests by default, like iframes and css background images.
    * Wouldn't even have to do a POST request that requires a user to be logged in.

### CSRF Tokens:
* Who is vulnerable to CSRF?
  * Anyone using basic/cookie authentication. Except 'client-side cookies'
    * Using cookies doesn't require the ability to read them.
    * Instead, use an alternative like `localStorage` or `sessionStorage`.
* Defend against it with CSRF tokens!
  * Change the token with each request in an unpredictable way.
  * Key Idea: The token is a value not in the cookie, which something with access to the fram (your JS app) can see/use.
    * Basically, 2FA. 1) Something that proves you are authenticated. 2) Something proving you're sending the request from an appropriate place.
    * For server-rendered apps, use meta tags.
    * Could also encrypt the cookie such that only the server can interpret it.
  * Validate Request Origin
    * Modern browsers send an `Origin` header, which cannot be altered by client-side code, with each request.
    * In cases where there is no `Origin` header, there's almost always a `Referer` header.
    * If behind a proxy, you can get some information from the `Host` and `X-Forwarded-Host` headers.
      * Ex: Heroku is always behind a Squid proxy.
  * Use CORS (Cross-Origin Resource Sharing)
    * Client sends a pre-flight `options` request to give the server a chance to indicate what is allowed. The server responds with the allowed origin (https://maxzintel.dev), request methods (PUT POST GET OPTIONS), allowed headers, and preflight age maximum. The _actual_ request that was intended to be sent then follows, using those preflight settings.
* Install CSURF.
  * Token generation and verification.
  * Follow the docs for setup, obviously.




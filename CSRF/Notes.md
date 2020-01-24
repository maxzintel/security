# Cross-Site Request Forgery
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

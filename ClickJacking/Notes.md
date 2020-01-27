# ClickJacking
* Given an example site, equihax.com.
  * A clickjacker would purchase a domain like... `equihax.com-securebank.cc` where it looks like equihax.com, but really that bit is just a subdomain. The real domain is the `com-securebank`.
  * An iframe of the real site is overlaid on top of ours. Opacity makes the sketchy bit invisible.
* UI Redress Attack.
* Can be used to capture keystrokes as well.

### Attack Example:
* Use the `transfer funds` form in the example app. It has a glaring issue which allows it to be pre-populated with queryParams.
* We can use jsbin.com to create a landing page that uses the clickjacking technique to trick a user into unintentionally performing an action (i.e. transfer funds based on web query params we pre-populate and hide beneath our landing page)
  * Combining this with cross-site scripting can be extremely powerful.

### Defense!
* In Modern browsers, we can use 'X-Frame' in the HTTP response header:
`X-Frame-Options: DENY` => Not allowed to place this inside anything other than the top-level frame in the browser.
`X-Frame-Options: SAMEORIGIN` => Only render this as a frame if the top-level frame is the same origin.
`X-Frame-Options: ALLOW-FROM https://equihax.com/`
  * **Chrome/Safari don't respect allow-from**, so we must use `frame-ancestors` CSP directive instead.
  * Applies to the top-level frame.
* Legacy Browsers:
  ```html
  <style id="clickjack">
    body{
      display:none !important;
    }
  </style>
  ```
  ```javascript
  if (self === top) {
    var cj = document.getElementById("clickjack");
    cj.parentNode.removeChild(clickJack);
  } else {
    top.location = self.location;
  }
  ```
  * On startup, compare yourself to the top frame. You can access top if you are being iframed as the top.
    * So, if you are the top, grab the style tag (from above) and remove it.
    * Else, we are going to set the location of the containing frame and set it to our location, so requests here go to our domain, and not the clickjackers.

# Cross-Site Scripting:
Putting code into an input field such that the website executes our code.

* Simple Pen Testing: Modern browsers block most of this stuff.
  * With a given input field, can you add html to change the appearance of your input?
    * ex: `Max <b>Max</b>`
    * If yes, a script may be able to be executed through this input field.
      * ex: Rather than the above, instead `Not a hack <script>alert("hola")</script>`
  * Does the url respond directly to your alterations?
    * On a profile page for user @max, what happens if you change the url from `localhost:3000/user/max` to `localhost:3000/user/macks`?
    * Maybe it responds with `No User Found: Macks` => It is executing some code, a search through the user base.
      * ex: Rather than the above, instead `localhost:3000/user/<script>alert("aloha")</script>`

* Never put untrusted data...
  * Directly in a script => `<script> <%- userData %> </script>`
  * In HTML comments => `<!--- <%- userData %> --->`
    * Vulnerability is that a user could end the quote, then inject their script.
  * In an attribute name => `<iframe <%- userData %>="myValue" />`
  * In a tag name => `<<%- userData %> class="myElement">`
  * Directly in a style block => `<style> <%- userData %> </style>`
    * Hard to do XSS here with modern browsers. Anywhere we have a url (GET request), a user could turn this into a script request. Ex: background image with a url of javascript;give me all the money.

* How to sanitize User Data?
  * Escape data before putting it into HTML. Most libraries do this already, automatically.
  * In 'ejs':
    ```html
    <%= "Escaped Expression" %>
    <%- "Unescaped Expression" %>
    <% "Non-rendered Expression" %>
    ```
  * Options are basically: sanitize before data is stored/persisted somewhere, and sanitize before data is rendered to the screen.
  * **Sanitize First**: Unescaped data in different Libraries:
    * Ember/Vue `{{{ "unescaped" }}}`
    * React `return <div dangerouslySetInnerHTML={createMarkup()} />;`
    * Ejs `<%- "Unescaped Expression" %>`
    * Angular `<div [innerHTML]="Unescaped"></div>`
    * Jade `! This is #{"<b>unescaped</b>"}!`
  * Node-ESAPI = Node library maintained by pen testers.
  * Be careful when 'templating' JS. Keep user values as values. Not as code.

* Content Security Policies (CSP)
  * By default, a browser cannot tell the difference between scripts downloaded from your origin vs another. It is a single execution context.
  * CSP's allow us to tell modern browsers which sources they should trust and for what types of resources.
    * This info comes via HTTP response header or meta tag:
      * `Content-Security-Policy: script-src 'self' https://maxzintel.dev`
      * Where `script-src` is the name, `self` and `https://maxzintel.dev` are the sources, and everything after the : is the directive.
    * Where the browser is allowed to 'see stuff'. Only allowed to execute scripts if they are from assets.maxzintel.dev, google analytics, and github.
    * Can also specify things like `font-src`.
    * Only the first `script-src` is respected.
  * Many different types of CSP Directives.
    * child-src = child execution contexts (frames, workers)
    * connect-src = whitelist of what we can connect to (fetch, websocket, eventsource)
    * form-action = where you can `<form>` submit to
    * etc...
  * Sources:
    * `none`, `self`=current origin, `unsafe-inline`=allows inline JS and CSS, `unsafe-eval()`=allows eval()
  * Mitigating `unsafe-inline`
    * Script tags embedded in HTML is the most common method of XSS today, thus banning it mitigates risk considerably.
    * Use cryptography, nonces generated on page-load, and changing unpredictably.
      ```html
      <script nonce=ajshdfljkashdfjlasdhflkjashdf>
      alert('This is inline for some reason');
      </script>

      Content-Security-Policy: script-src 'nonce-ajshdfljkashdfjlasdhflkjashdf'
      ```
    * Can also add checksums:
      ```html
      <script>alert('yadda yadda yadda')</script>

      Content-Security-Policy: script-src 'sha256-qzn...asdfasdf='
      ```
      * Script is permitted via the sha tag. Generate via browser itself.
  * Popular CSP: `HelmetJS`.
    * Middleware for Connect. Will work with Express.
    * Note: anything with `app.use` is generally middleware.

* When inspecting sites:
  * View => Source is showing you what came over the wire. Dev Tools are trying to render things in a more readable way.

* Mitigating Malicious Attachments:
  * Be restrictive about file types that can be uploaded.
  * For images, compress them. Anything that compresses an image will drop non-visible data.
  * Before adding new attachment types, research! Acrobat PDF's can execute JS!!


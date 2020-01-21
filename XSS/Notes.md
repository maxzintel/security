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

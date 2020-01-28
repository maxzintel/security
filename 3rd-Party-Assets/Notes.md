# Third Party Assets
### Last category we will cover on the client-side.
* Any resource, client-side, that we pull from but are not hosting.
  * ex:
  ```html
  <script src="http://asdas.cloudflare.com/asdasd">stuff</script>
    * Use subresource integrity attributes on `<script></script>` and `<link/>` tags.
      * A hash of the contents of a file. If that hash changes, the browser will refuse to load it.
    * Dont pass credentials in these tags.
  ```
  ```json
  // Like in a package.json
  "express": "^4.1"
  ```
  * And anything that, via html, deploys its own script tag.
* Version Changes to Third Party Assets are a vulnerability!
  * The people who write your dependencies make mistakes too.
  * Recommendations:
    * Reproducable builds with a lock file, like `package-lock.json`.
    * Use LTS versions => Care less about being on the bleeding edge of asset feature sets.
      * Let the bleeding edge folks figure out the vulnerabilities.
    * Support bug bounties in important open-source projects.
    * Tests that assert only expected requests are sent out.
    * snyk.io
* Vendor Tags are a vulnerability!
  * Can also be updated independently from your deployments.
  * Avoid adding scripts that add more scripts, like above.
  * When 'fail secure' is desired, add our own SRI (Sub Resource Integrity) to the script tags.
  * Ask that vendors VERSION scripts so we have control over when new code lands and the SRI doesn't break.

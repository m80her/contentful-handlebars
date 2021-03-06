# contentful-handlebars
## Quick Contentful & Handlebars setup

Having the need to evaluate [Contentful](https://www.contentful.com) as a headless CMS I struggled to find any examples of how to quickly and simply display data from their API on a webpage.

I'd had some exposure to [Handlesbars.js](https://handlebarsjs.com) before but only when set up by someone else, so I was keen to see if I could use it to complete my goal:

> _"No Webpack, no Browserify, just plain and simple get some stuff on a page for rapid prototyping."_

I got so far with the [Contentful Javascript SDK docs](https://www.contentful.com/developers/docs/javascript/tutorials/using-js-cda-sdk/), but I needed a bit more searching and some trial and error to get over the line.

## Let's break down the code

From CDNs we've got some jQuery, and the pre-built versions of Handlebars and Contentful SDK (not recommended for production).

```html
<script src="//code.jquery.com/jquery-3.3.1.slim.min.js"></script>
<script src="//cdnjs.cloudflare.com/ajax/libs/handlebars.js/4.0.11/handlebars.min.js"></script>
<script src="//cdn.jsdelivr.net/npm/contentful@latest/dist/contentful.browser.min.js"></script>
```
      

### The Contentful bit

First up we need to shake hands with the Contentful API:

```javascript
var client = contentful.createClient({
  space: '<space_id>',
  accessToken: '<access_token>'
});
``` 
      

Then we get the data, returning `entries.json`:

```javascript
var renderTemplates = (templateId, target) => {
  client.getEntries();
}
``` 
      

### The Handlebars bit

At this point we can browse the data structure and see what things are called, so it's a good time to build out a little Handlebars template using basic [Handlebars expressions](https://handlebarsjs.com/expressions.html) as a test that will loop over, for example, `items` and display, for example, all the `fields.title`.

```html
<script id="templateTitles" type="text/template">
  {{#items}}
      <li>{{fields.title}}</li>
  {{/items}}
</script>
```
      

Once we've got the template in place, in the `renderTemplates` function we reference the content of the template:

```javascript
var templateTitles = $(templateId).html();
```
      

Then compile the template in the Javascript (again, not recommended for production) and tell it which HTML element to append the compiled output to:

```javascript
var payload = Handlebars.compile(templateTitles)(entries);
$(target).append(payload);
```
      

The whole function with invocation looks like this:

```javascript
var renderTemplates = (templateId, target) => {
  client.getEntries()
    .then(function(entries) {
      var templateTitles = $(templateId).html();
      var payload = Handlebars.compile(templateTitles)(entries);
      $(target).append(payload);
    });
}
renderTemplates('#templateTitles', '#titles');
```

## Conclusion

Quite an interesting little project, and while not anything you'd use in production, it does enable you to quickly display your Contentful datasets in HTML so you can fine-tune your content model in Contentful.

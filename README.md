# contentful-handlebars
## Quick Contentful & Handlebars setup

Having the need to evaluate [Contentful](https://www.contentful.com) as a headless CMS I struggled to find any examples of how to quickly and simply display data from their API on a webpage.

I'd had some exposure to [Handlesbars.js](https://handlebarsjs.com) before but only when set up by someone else, so I was keen to see if I could use it to complete my goal:

> _"No Webpack, no Browserify, just plain and simple get some stuff on a page for rapid prototyping."_

I got so far with the [Contentful Javascript SDK docs](https://www.contentful.com/developers/docs/javascript/tutorials/using-js-cda-sdk/), but I needed a bit more searching and some trial and error to get over the line.

## Let's break down the code

From CDNs we've got some jQuery, and the pre-built versions of Handlebars and Contentful SDK (not recommended for production).

        `
        <script src="//code.jquery.com/jquery-3.3.1.slim.min.js"></script>
        <script src="//cdnjs.cloudflare.com/ajax/libs/handlebars.js/4.0.11/handlebars.min.js"></script>
        <script src="//cdn.jsdelivr.net/npm/contentful@latest/dist/contentful.browser.min.js"></script>
        ` 
      

### The Contefulful bit

First up we need to shake hands with the Contentful API:

        `var client = contentful.createClient({
              space: '<space_id>',
              accessToken: '<access_token>'
            });` 
      

Then we get the data, returning `entires.json`:

        `var renderTemplates = (templateId, target) => {
              client.getEntries();
            }` 
      

### The Handlebars bit

At this point we can browse the data structure and see what things are called, so it's a good time to build out a little Handlebars template using basic [Handlebars expressions](https://handlebarsjs.com/expressions.html) as a test that will loop over, for exmpale, `items` and display, for exmaple, all the `fields.title`.

        `<script id="template" type="text/template">
            {{#items}}
                <li>{{fields.title}}</li>
            {{/items}}
          </script>` 
      

Once we've got the template in place, in the `renderTemplates` function we reference the content of the template:

        `var template = $(templateId).html();` 
      

Then compile the template in the Javascript (again, not recommended for production) and tell it which HTML element to append the compiled output to:

        `var payload = Handlebars.compile(template)(entries);
          $(target).append(payload);` 
      

The whole function with invocation looks like this:

        `var renderTemplates = (templateId, target) => {
            client.getEntries()
              .then(function(entries) {
                var template = $(templateId).html();
                var payload = Handlebars.compile(template)(entries);
                $(target).append(payload);
              });
          }
          renderTemplates('#template', '#titles');` 

## Conclusion

Quite an interesting little project, and while not anything you'd use in production, it does enable you to quickly display your Contentful datasets in HTML so you can fine-tune your content model in Contentful.

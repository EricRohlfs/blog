# Changing my mind on the 'is' attribute for custom elements

I am starting to favor the 'is' attribute over custom tag names lately.

When I first started building web components, the app was built top down and custom tag names worked great and felt comfortable to me given my xml background.

But when I started integrating components into legacy systems and wanted to have configurable custom element names, I'v found that the 'is=my-component' is cleaner.

The other benefit is, often I get to drop an element, because the component was originally a div and my custom element was a wrapper or a way to attach JavaScript.

Which looks cleaner in a template.

```
const MY_COMPONENT = 'my-component'

const myTemplate = `<${MY_COMPONENT}> My content...</MY_COMPONENT>`
or 

const myTemplate = `<div is='MY_COMPONENT'>  My Content</div>`
```

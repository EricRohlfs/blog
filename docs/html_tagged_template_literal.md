# Template Literal Sanitization with Named or Tagged Template Literals 
This article describes the high level goals and usage of an custom function 'html' that sanitizes JavaScript template literals.

## Goals
* Secure templates (no xss or vulnerabilities)
* Improved developer experience
* Allows developers to do work before painting to the DOM like attaching events to internal elements before painting to the DOM.
* Only use .innerHTML in one place in the entire code base. Have a linter plug in enforce this with the one exception being this function.


The 'html' and ['createElement'](https://ericrohlfs.github.io/blog/custom_createElement.html) functions are a strong foundation for working with custom elements with minimal amount of helper code. This article discusses my 'html' tagged template literal function.

https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals

Breakdown of the 'html' in templateLit()
``` javascript
  export class myComponent extends HTMLElement {
    
    connectedCallback(){
      const frag = this.templateLit()
      const submit = frag.queryselector('button')
      submit.addeventListener('click', submitEventHandler)
      this.append(frag); // append to the DOM after you have event handlers and other work complete.
    }
    
    templateLit(){
      retrun html`<div>${user_input}`
     }
    
  }
```

Because of various reporting reasons I'm not going to post my html function, but if you follow the link to MDN and do the following things you can build your own.

## Main Features of my html function

* remove unsafe characters - this is pretty basic see [stackOverFlow](https://stackoverflow.com/questions/1637275/simple-html-sanitizer-in-javascript) the snarkdown implementation looks close
* return a document fragment - document fragment to prevent any unnecessary html wrappers.

Before you return you can do extra work like:

* stripping out any script tags
* ensuring all links stay on your site
* remove any attributes that start with 'on' before they are added to the page.


## How to create a document fragment 

* make a div or a table see known issue below
* div.innerHTML = sanitized string
* make a document fragment
* frag.prepend(...tempDiv.childNodes)

### Known issue

Table related elements are different, instead of using a div to add the sanitized string, I use the 'table' element. 

For tables I export a slightly different function 'htmlTable' vs 'html'.  Everything is the same, except a table element is created vs. a div. 

### Character clean up
I've done both the regex way to clean up data and I have used createTextNode. CreateTextNode is a bit more code, and does the same thing.

# Disadvantage of this approach

Cannot nest html via html`<div>${childHtmlString}</div>`.

This hasn't been an issue for me. I have several work arounds. Either a separate component, or a separate template in the same component.

### Workaround As separate component 

``` html`<div><child-component></child-component></div> ```

### Workaround as separate template literal and append where needed before painting to the DOM
``` javascript
  const frag = this.templateLit()
  const childLocation = frag.querySelectort('something')
  // treat is just like the parent and append it where it is needed.
  childLocation.append(this.templateLitSomeChildHTML());
```


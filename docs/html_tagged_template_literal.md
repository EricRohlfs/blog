# Template Literal Sanitization with Named or Tagged Template Literals 

The 'html' and ['createElement'](https://ericrohlfs.github.io/blog/custom_createElement.html) functions are a strong foundation for working with custom elements with minimal amount of helper code.

https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals

Breakdown of the 'html' in templateLit()
```
  export class myComponent extends HTMLElement{
    
    connectedCallback(){
      this.append(this.templateLit());
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


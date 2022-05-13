# Blended template building strategies in ES6+

## Background Info (you can skip) 

ES6+ is the term I use for all the new features in JavaScript to support native customElements without using any 3rd party libraries. This capability was introduced in in ECMAScript 6 (in 2015). But also includes all the new features released every year, hence the '+'. 

Now that Internet Explorer is obsolete, native custom elements are now feasible. 

I use ES6+ components because I pay a heavy tax for using 3rd party libraries: paperwork, continual updates and releases, breaking changes, etc.


## Main Strategies

My primary native ES6+ template strategies (so far) are: 

- template literals (additive, substractive)
- document.createElement or my custom createElement() function
- template element (using shadowdom and not using shadowdom)

These strategies are not unique to ES6+ native custom elements. I've seen these same patterns used in vue and react. Especially used in popular 3rd party frameworks used in the vue and react ecosystem. 




## Template literals

For one off components I can often get away with just using template literals.  I've even built simple one off tables primarily using this approach, but I would not recommend it.

I use template literals for forms and static content. 

```
this.innerHTML = `<div>${this.user.name}</div><div>${this.user.email}`

// Not the best use case for additive and subtractive, going for readability vs. practicality

// additive example - add optional elements
if(this.user.phone){
  const phoneEle = createElement('div')
  phoneEle.textContent = this.user.phone
}

// subtractive strategy - include the expected happy day items in the main template,
//                        and remove the element under less common circumstances
if(this.user.email === ''){
  const emailEle = Array.from(this.children).find(ele => ele.textContent === this.user.email)
  this.removeChild(emailEle)
}

```



## document.createElement

The counterparts to document.createElement are:

- vue has 'h('div',{class:'btn'})'
- react has createElement().

I often use createElement for data driven components like tables or components that assemble other components together (like a shady and non-shady version of a component.)

Technically` I don't call document.createElement in my components anymore.  
I have my own createElement method that does a few nice things like:

- adds the 'is' attribute to the element after it is created
- automatically detects boolean attributes and constructs them properly for use with this.hasAttribute('foo')
- does not overwrite any css classes that may have been added by the constructor

```
// shadowdom
export const MyComponentShady extends HTMLDivElement {
  constructor(){
    super()
    this.attachShadow({mode: 'open'});
    const style = createElement('style', {is:'my-component-style'})
    const component = createElement('div', {is:'my-component'})
    this.shadowRoot.append(style, component)
  }
}

// No shadowdom
export const MyComponentNotShady extends HTMLDivElement {
  constructor(){
    super()
  }
  connectedCallback(){
   const style = createElement('style', {is:'my-component-style'})
   document.head.append(style)

   const component = createElement('div', {is:'my-component'})
   this.append(component)
  }
}

```

## Template Element/Tag

I primarily use the <template><div>hello</div></template> on reusable components. Not needed for one off components like forms and such.

This allows the user to inject html into my components. 

There are plenty of examples on the internet using templates with and without shadowdom, so I'll skip that.

See [Changing my mind on the template tag](template-tag-vs-template-literals.md) for one of my approaches for using templates without shadowdom. 
I have a new TemplateRender class that I now use that I want to do a write up on.  It behaves much like a lightweight version of slots.

Psudeo code for my new TemplateRender class, you can probably figure out the implementation from the usage.
```
<div is='my-custom-element'>
  <template name="header">
    <h1>Hello</h1>
  </template>
  <template name="footer">
    <div><a href="five-o-eight.html>508 Compliance</a></div>
  </template>
</div>

export const MyCustomElement extends HTMLDiv{
  constructor(){
    super()
    // TemplateRenderer(scope, templateSelector, renderDestinationSelector)
    this.headerTemplate = new TemplateRenderer(this, ':scope template[name=header]', ':scope div[role=heading]')
    this.headerTemplate.backup()
    this.footerTemplate = new TemplateRenderer(this, ':scope tempalet[name=footer]', ':scope footer')
    this.footerTemplate.backup()
    this.innerHTML = '' // don't have to do this, but can now that the templates are backed up.
  }
  
  connectedCallback(){
    this.innerHTML = '<div><div role='heading'></div><footer></footer>'
    this.headerTemplate.render() // inserts the header content to the DOM
    this.footerTemplate.render() // inserts the footer content to the DOM
  }

```


## Blended Strategies

*The first example in this article is somewhat of a blended strategy. I did use createElement() to add the phone number.*

I recently I created a re-usable, configurable toaster.  Configurable means data-driven, and using template literals alone did not make sense.  

The toaster followed a type of factory pattern.

- standard - nothing special, just set a color and a message, if button-text then shows a button
- success - color green, with a checkMark icon
- error - color red, with a X icon


Examples of calling the toaster
```
  toaster({type:standard, color:'purple', message:'hello'})
  // or 
  toaster({type:success})
  // or
  toaster({type:standard, color:'blue', textColor:'white', message:'Call your mom on mothers day', button-text:'ok'})

```

Had I used createElement for everything in this component, it would be very hard to read and reason about. So instead I used template literals where it made sense.

I did the subtractive and additive methods to drive the HTML for certain configurations.

I mainly used additive for features that are not needed when the component is first added to the DOM. For example, if the same notification is added twice, I don't add the same message twice, instead I show a badge with a counter.  The badge usses the additive method to add the html to the component.







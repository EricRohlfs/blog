# Blended template building strategies

- template literals
- document.createElement
- template element


## Template literals

For one off components I can often get away with just using template literals.  I've even built simple one off tables primarily using this approach, but I would not recommend it.

I use template literals for forms and static content. 

```
this.innerHTML = `<div>${this.user.name}</div><div>${this.user.email}`
```

## document.createElement

I often use createElement for data driven components like tables or components that assemble other components together.
An example of a component that assembles other components could be when I make a shadowdom and non-shadowdom version of a component.

Technically I don't call document.createElement in my components anymore.  
I have my own createElement method that does a few nice things like adding the 'is' attribute to the element after it is created.
Automatically detects boolean attributes and constructs them properly for use with this.hasAttribute('foo').
Does not overwrite any css that may have been added by the constructor 

```
export const MyComponentShady extends HTMLDivElement {
  constructor(){
    super()
    this.attachShadow({mode: 'open'});
    const style = createElement('style', {is:'my-component-style'})
    const component = createElement('div', {is:'my-component'})
    this.shadowRoot.append(style, component)
  }
}

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




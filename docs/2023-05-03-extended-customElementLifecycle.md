# Additional Custom Element Lifecycle

## Built-in lifecycle.

* constructor
* connectedCallback
* attributeChangedCallback
* disconnectedCallback

## Extended Lifecycle

### connectedCallback
(Still working on more creative names)

The outline below is more of a thought process than named callbacks or hooks.

* pre-template-lit
* post-template-lit
* pre-paint
* post-paint

Example
```
...
connectedCallback(){
  
}

templateLit(){
   // html tagged template returns a document fragment that is not added to the DOM yet.
   return html`<div>
      <div><span>${this.user.firstName} ${this.user.lastName}</span></div>
      <div><button>Close</div>
    
     </div>`
}
```

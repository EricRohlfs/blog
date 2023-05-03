# Additional Custom Element Lifecycle

## Built-in lifecycle.

* constructor
* connectedCallback
* attributeChangedCallback
* disconnectedCallback

## Extended Lifecycle

### Extended Lifecycle in connectedCallback
(Still working on more creative names)

The outline below is more of a thought process than named callbacks or hooks.

* pre-template-lit
* post-template-lit or pre-paint (samething)
* post-paint - rarely if ever gets used. But here for conceptual purposes.

#### pre-template-lit
Get things ready so you can turn the template into a document fragment.

#### pre-paint
You have a document fragment, now attach events and get everything ready so when you attach to the DOM it is ready for the user, a script, or other elements to interact with. Other elements maybe using mutationObserver to wait for the paint then do work. Adding html to the DOM before things are ready can cause issues.

#### post-paint
I can't think of anything that would go here. This is more conceptual, other components might interact with your component post paint via using mutation observers or maybe thrown events. Note: I have not had the issue of other components interacting with my components too early using this approach. I do NOT recommend throwing an event that your component is painted on the screen. If everything is setup before paint, throwing events is just extra work or maybe a sign your architecture needs more work.

Example 1
```
...
connectedCallback(){
  // pre-template-lit
  
  this.user = {
    firstName:'Jane',
    lastName:'Doe'
   }
  const frag = this.templateLit()
  
  // pre-paint
  
  this.abortController = new AbortController() // this needs to be in the connectedCallback, do not create in the constructor.
  this.button = frag.querySelector(':scope button')
  this.button.addEventListener('click',()=>{ // do something interesting
  },signal:this.abortController.signal)
  
  this.append(frag)
  
  // post-paint - nothing to do in this example
}

templateLit(){
   // html tagged template returns a document fragment that is not added to the DOM yet.
   return html`<div>
      <div>${this.user.firstName} ${this.user.lastName}</div>
      <div><button>Ok</div>
     </div>`
}

loadingTemplateLit(){
  return html`<div>Loading</div>`
}
```


Example 2 - multiple lifecycles in the connectedCallback
```
...

connectedCallback(){
  // pre-template-lit 
  const loadingFrag = this.loadingTemplateLit();
  // pre-paint 
  this.append(loadingFrag);
  // post-paint
  
  // pre-template-lit
  this.user = await this.getUserInfo();//fetch - could be long running so we show loading screen.
  const frag = this.templateLit()
  // pre-paint
  this.abortController = new AbortController()
  this.button = frag.querySelector(':scope button')
  this.button.addEventListener('click',()=>{ // do something interesting
  },signal:this.abortController.signal)
  
  this.append(frag) // overwrites the loading templatelit
  
  // post-paint - still nothing to do here.
}

templateLit(){
   // html tagged template returns a document fragment that is not added to the DOM yet.
   return html`<div>
      <div>${this.user.firstName} ${this.user.lastName}</div>
      <div><button>Ok</div>
     </div>`
}

loadingTemplateLit(){
  return html`<div>Loading</div>`
}
```

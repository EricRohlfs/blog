# Additional Custom Element Lifecycle
(Work in progress. Want to add notes for constructor and others. Add details to Updates. Outline how component creation makes a difference: createElement vs. tag-on-dom or tag-in-template.)

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
* post-template-lit or pre-paint (same thing)
* post-paint - rarely if ever gets used. But here for conceptual purposes.
* updates - technically not part of the connectedCallback, update functions are called after connectedCallback is finished. These are custom functions that modify elements in the template, or could even just re-render the whole template.

#### pre-template-lit
Get things ready so you can turn the template into a document fragment.

#### pre-paint
You have a document fragment, now attach events and get everything ready so when the fragment is attached to the DOM it is ready for the user or script or other customElements to interact with. Other elements maybe using mutationObserver to wait for the paint then do work. Adding html to the DOM before things are ready can cause issues.

#### post-paint
I can't think of anything that would go here. This is more conceptual, other components might interact with the component post paint via using mutation observers or maybe thrown events. Note: I have not had the issue of other components interacting with my components too early using this approach. I do NOT recommend throwing an event like 'is-painted'. If everything is setup before paint, throwing events is just extra work or maybe a sign your architecture needs more work.

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

disconnectedCallback(){
  this.abortController.abort('disconnected')
  this.user = null // can optionally clear out any large data sets. This is not large and I probbaly wouldn't bother cleaning it up in a real component.
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

disconnectedCallback(){
  this.abortController.abort('disconnected')
  this.user = null // can optionally clear out any large data sets. This is not large and I probbaly wouldn't bother cleaning it up in a real component.
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

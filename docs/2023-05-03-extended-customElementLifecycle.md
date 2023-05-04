# Additional Custom Element Lifecycle
(Work in progress. Want to add notes for constructor and others. Add details to Updates. Outline how component creation makes a difference: createElement vs. tag-in-template. Also map to vue or react lifecycles. )

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
* pre-paint (or could be thought of as post-template-lit, but pre-paint rolls off the tongue better.)
* post-paint - rarely if ever gets used. But here for conceptual purposes.
* updates - (see the reactivity section later in this article) technically not part of the connectedCallback, update functions are called after connectedCallback is finished. These are custom functions that modify elements in the template, or could even just re-render the whole template.

I might later get rid of post paint and go straight to updates, even though they are not part of the connectedCallback.

#### pre-template-lit
Get things ready so you can turn the template into a document fragment.

#### pre-paint
You have a document fragment, now attach events and get everything ready so when the fragment is attached to the DOM it is ready for the user or script or other customElements to interact with. Other elements maybe using mutationObserver to wait for the paint then do work. Adding html to the DOM before things are ready can cause issues.

#### post-paint
I can't think of anything that would go here. This is more conceptual, other components might interact with the component post paint via using mutation observers or maybe thrown events. Note: I have not had the issue of other components interacting with my components too early using this approach. I do NOT recommend throwing an event like 'is-painted'. If everything is setup before paint, throwing events is just extra work or maybe a sign your architecture needs more work.

Note: all examples assume they are wrapped in a class that creates a custom element.  I may come back and add that in at a future time.

Example 1
```
export class UserDetailsEle Extends HTMLDivElement{
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
  this.button.addEventListener('click',()=>{ // do something interesting },signal:this.abortController.signal)
  
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
}
```

Example 1 being used in another component
```
import {createElement} from '....somewhere'
// see my blog on my custom createElement function //todo: put a link here
 const userDetails = createElement({
    tagName:'div',
    attributes:{
      is:'user-details'
    }
    properties:{
       user:{ 
        firstName:'Jane',
        lastName:'Doe'
    }
 })

this.append(userDetails)
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
  this.button.addEventListener('click',()=>{ // do something interesting },signal:this.abortController.signal)
  
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

## Reactivity 
When adopting this approach, I found that I do not need reactivity as much as I thought I did.

Reactivity happens either:

* by calling an update function in your component 
* modifying an attribute - usually done in JavaScript too

### Reactivity via a update function

```

connectedCallback(){ 
 ... 
  const mainFrag = this.mainTemplateLit()
  // strive to only query once
  this.userDetailsContainer = mainFrag.querySelector('.user-details')
  userDetialsContainer.append(this.userDetailsLit())
  this.append(mainFrag)
}

/*
* I repaint the child DOM unless I think there will be
*  events attached to it by outer elements.
*  Input elements and other from elements I might not re-paint. 
*  I would instead just update the values in the elements.
*/
updateUserDetails(firstName, lastName){
  this.user.firstName = firstName
  this.user.lastName = lastName
  this.userDetailsContainer.replaceChildren()
  this.userDetailsContainer.append(this.userDetailsLit())
}

mainTemplateLit(){
  return html`<div>
    <h1>Hello</h1>
    <div class='user-details'></div>
  </div>`
}


userDetailsLit(){
  return html`<span>${this.user.firstName} ${this.user.lastName}</span>`
}

```


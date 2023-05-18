# Additional Custom Element Lifecycle
(Work in progress. Want to add notes for constructor and others. Add details to Updates. Outline how component creation makes a difference: createElement vs. tag-in-template. Also map to vue or react lifecycles. )

## Built-in lifecycle.
For a recap here is the built in lifecycle for ES6 custom elements

* constructor
* connectedCallback
* attributeChangedCallback
* disconnectedCallback

### Extended Lifecycle in connectedCallback


Example 1
```
export class UserDetailsEle extends HTMLDivElement{
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

The outline below is more of a thought process than named callbacks or hooks.

* pre-template-lit or pre-lit (easier to say)
* pre-paint (or could be thought of as post-template-lit, but pre-paint rolls off the tongue better.)
* post-paint - rarely if ever gets used. But here for conceptual purposes.
* updates - (see the reactivity section later in this article) technically not part of the connectedCallback, update functions are called sometime later by an event or some action; long after connectedCallback is finished. These are custom functions that modify elements in the template, or could even just re-render the whole template.

I might later get rid of post paint and go straight to updates, even though updates are not part of the connectedCallback.

#### pre-template-lit
Get things ready to turn the template into a document fragment.

#### pre-paint
The template is now a document fragment. Before painting, attach events and do any other work so when the fragment is appended/painted to the live-DOM it is ready for the user/script/ or other customElements to interact with it. Other elements maybe using mutationObserver to wait for the paint then do work. The whole point is to avoid adding html to the live-DOM too early and then trying to compensate.

#### post-paint
I can't think of anything that would go here. This is more conceptual, other components might interact with the component post paint via using mutation observers or maybe thrown events. Note: I have not had the issue of other components interacting with my components too early using this approach. I do NOT recommend throwing an event like 'is-painted'. If everything is setup before paint, throwing events is just extra work or maybe a sign your architecture needs more work.

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
When adopting this approach, I found that I do not need or use reactivity most of the time. When using vue,react,angular, since reactivity is there and cheap, it can easily be over used.

For the sake of this article, reactivity happens after the element is painted on the DOM, and things need to change.

Reactivity happens either:

* by calling an update function in your component 
* modifying an attribute - nothing special here just follow the attributeChangedCallback as per the ES6 spec. Recommend calling an update function from attributeChangedCallback so both of these strategies are supported.

Note on modifying an attribute: The value of the attribute route is low. Chances are you are modifying the attribute using JavaScript. Modifying an attribute is about the same amount of code as just calling an update function. But there is a big difference between using attributes to set values before an element is rendered to the page and after. Using attributes to set initial values does not need to use attributeChangedCallback.

### Reactivity via a update function

(Technically not reactivity or 'data driven reactivity', but achieves the same goal, making changes to the DOM. In a true reactive system we would change the data and the UI would update.)

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

Reactive component being used
```
import {createElement} from '....somewhere'
// see my blog on my custom createElement function //todo: put a link here
const userDetails = createElement({ // same as the first usage example in this article})
this.append(userDetails)
// something interesting happens, maybe an event

   userDetails.update('Jane', 'Doe') // or this could be designed to take a user object.

```

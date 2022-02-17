# Belly Button Attributes a convention for component reactivity in web components

*I'll regret this name later, but it stuck in my head as a good enough mnemonic device to explain the concept, so sticking with it for now.*  

*Also, this is not about buttons, but html attributes.*

*Note: There are a few goals in the HTML and ES spec for managing external and internal state using aria-attributes and other things, but for now I would like something simpler.*

I'm proposing what I call belly-button-attributes.

Belly button attributes are reactive attributes. They are either in or out, but not both.

The convention to prefix or suffix with in/out is team preference, but using the word prefix in the rest of the document.

For elements that are one way in or out, but not reactive, avoid using the 'in' or 'out' prefix. 

The general presumption is the component is using attributeChangedCallback to react to changes.

```
  <my-component in-some-var="foo" out-some-var="bar"></my-component>
```

or
Lets say you have a custom element that creates a drop down list, and encapsulates it's own logic including the fetch to get the data to populate the drop down.
And you want to expose the selected index as an attribute for easy observability (instead of publishing an event, which would work too without using attributes.)
And you want to also allow the initial value to be set when the custom element is first created by a parent element.
```
   const user = { salutation:'Mr.', fname:'John', lname:'doe'}
   this.innerHTML = safeHTML`<form> 
                          <select is="salutations-dropdown" selected-value-in="${user.salutation}" name="salutation"></select>
                          <input type="text" value="${user.fname}" name="fname"/>
                          <input type="text" value="${user.lname} name="lname"/>
                     </form>`
```

The same component rendered on the DOM
```
  <select is="salutations-dropdown" selected-value-in="Mr." selected-value-out="Mr.">....Options <select>
```

Attributes prefixed with 'in' are for parent or other external components to set.

Attributes prefixed with 'out' are set by the component for either internal use or external notification.

There is no real technology under the hood that helps with reactivity by using this convention, but it should help in development and prevent some complexitiy issues than can be cause when 
a single attribute is used for external use and or for internal use. Sometimes context and who is setting the value matters.

One use case is when the component has a default or initial state or value (that is reactive) but in some cases, we want an outside component to set the initial value.  

A good example is a drop-down list where internally the default is an empty string, but our form has the real initial value, it could use this pattern to set the in-selected-index and the component can use the out-selected-index to report the real value, this prevents a conflict when a single attribute tries to manage both internal and external in a reactive example that may have complex timing issues.


```
  //Notice the button-id's, they are not reactive, but used in the template, don't prefix them.
  <my-component in-state="" out-state="" submit-btn-id="submit001" cancel-btn-id="cancel001"></my-component>
```

Typed from memory, so YMMV if you copy and paste.
Also, I highly recommend using getters and setters for attributes vs using this.getAttribute(...) all over the place.

```
export MyComponent extends HTMLElement{
  static get observedAttributes() { return ['in-state', 'out-state']; }
  ...
  attributeChangedCallback(name, oldVal, newVal){
    // try to keep this clean if you need to also deal with other attributes
    if(name=== 'in-state' || name=== 'out-state') this.stateMgr(name, oldVal, newVal)
    
  }
  
  //*
  * Nothing really stops another component from setting an 'out' variable, 
  *      this is just a convention to help reduce downstream complexity when knowing who set the state matters.
  */
  stateMgr(name, oldVal, newVal){
    if(name === 'in-state'){
      // restrict in-state allowed values
      const allowed = ['loading', 'error']
      if(!allowed){ throw new Error('the only states allowed to be set are loading and error')
      ...
    }
    if(name === 'out-state'){
      if(newVal ==='completed'){
        ...
      }
      if(newVal === 'loading'){
      
      }
    }
  }
  
  
  doSomethnig(){ //like get data
    if .... this.setAttribute('out-state','error')
    .... //other work
    this.setAttribute('out-state', 'ready')
    
  }
  
  template(){
    return `
      <form>
          ..... html form here
          <button id="${this.getAttribute('cancel-btn-id')}"></button>
          <button id="${this.getAttribute('submit-btn-id')}" type="sumbit"></button>
      
      </form>
    `
  }
  
  
}

```

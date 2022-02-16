# Belly Button Attributes a convention for component reactivity

I'll regret this name later, but it stuck in my head as a good enough mnemonic device to explain the concept, so sticking with it for now.  

Note: There are a few goals in the HTML and ES spec for managing external and internal state using aria-attributes and other things, but for now I would like something simpler.

I'm proposing what I call belly-button-attributes.

belly-button-attributes are reactive attributes. They are either in or out, but not both.

For elements that are one way in or out, but not reactive, avoid using the 'in' or 'out' prefix. 

The general presumption is the component is using attributeChangedCallback to react to changes.

```
  <my-component in-some-var="foo" out-some-var="bar"></my-component>
```

Attributes prefixed with 'in' are for parent or other external components to set.

Attributes prefixedf with 'out' are set by the component for either internal use or external notification.

There is no real technology under the hood that helps with reactivity by using this convention, but it should help in development and prevent some complexitiy issues than can be cause when 
a single attribute is used for external use and or for internal use. Sometimes context and who is setting the value matters.


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

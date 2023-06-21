# Slots and textContent

Learned somethnig new today. I'm not a big shadowDom user.  But found this interesting behavior.

I have not explored this in detail. Unsure if this works with named slots.

```
  const myBtn = document.createElement('my-btn');
  myBtn.textContent = 'helloWorld' // this will render inside a slot without any work
```

```
  export class myButton extends HTMLElement {
    constructor(){
      super();
      const shadowRoot = this.attachShadow({mode: 'open'});
    }
    connectedCallback(){
      // helloWorld with display within the slot
      const frag = html`<div class='wrap'><button><slot></slot></button></div>`
      this.shadowroot.append(frag);
    }
  }
  customElements.define('my-btn, myButton)
```

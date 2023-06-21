# Slots and textContent

Learned somethnig new today. I'm not a big shadowDom user.  But found this interesting behavior.

```
  const myBtn = document.createElement('my-btn');
  myBtn.textContent = helloWorld // this will render inside a slot without any work
```

```
  export class myButton extends HTMLElement {
    constructor(){
      super();
      const shadowRoot = this.attachShadow({mode: 'open'});
    }
    connectedCallback(){
      const frag = html`<div class='wrap'><button><slot></slot></button></div>`
      this.shadowroot.append(frag);
    }
  }
  customElements.define('my-btn, myButton)
```

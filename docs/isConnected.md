# isConnected use in customElements 

I haven't had the need to use isConnected in my work. I was reviewing some code that did use it. 

I realized I didn't know if it was called before or after the connectedCallback. 

I did a quick test in Chrome 110?

```
customElements.define('my-test',class extends HTMLElement{

  connectedCallback(){
    console.log(this.isConnected); // true
  }
});
```


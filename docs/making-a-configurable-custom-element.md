# Making a configurable custom element

Overcomes the issues with not being able to pass in data to the constructor.

If I get time I'll put up a real world example.

```
  //myCustomElement.js
  export function makeMyCustomElement(cfg){
    const myCustomElement = class extends HTMLElement{
      ... all the usual stuff, but can use the cfg
    }
    
    return myCustomElement
  }
```



```
// index.html

  <script type="module">
    import {makeMyCustomElement} from './myCustameElement.js'
    const cfg = {}
    const myCustomElement = makeMyCustomElement(cfg);
    customElement.define('my-custom-ele', myCustomElement)
    
    const x = new myCustomElement(); // or any of the other ways to new up a custom element
    document.body.append(x);
  </script>

```


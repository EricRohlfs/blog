First design decision for custom elements, to extend an existing element, or create a new custom tag.

Below is the code I plan to use for making a youtube video on the topic.  After the video is made, I'll update the link here.

The elements we plan to use.  One is a custom tag the other extends an existing element.

```
import { createElement } from "utils/createElement"

export class HelloWorld extends HTMLElement {
  connectedCallback() {
    this.innerText = 'Hello World'
  }
}

export class HelloUniverse extends HTMLSpanElement {
  connectedCallback() {
    this.innerText = 'Hello Universe'
  }
}
```

```
export class View01 extends HTMLElement {
  connectedCallback(){
    this.innerHTML = `
        <hello-world></hello-world>
        <span is="hello-universe"></span>
    `
  }
}

export class View02 extends HTMLElement {
  connectedCallback(){
   //uses the new keyword
   const hw = new HelloWorld()
   hw.style.color = "red"
   this.appendChild(hw)
   const hu = new HelloUniverse()
   this.appendChild(hu)
  }
}

export class View03 extends HTMLElement {
  connectedCallback(){
    const hw = document.createElement('hello-world')
    hw.style.color = 'green'
    this.appendChild(hw)
    const hu = document.createElement('span', {is:'hello-universe'})
    this.appendChild(hu)
  }
}

export class View04 extends HTMLElement{
  constructor(){
    super()
  }
  connectedCallback() {
    const hw = createElement('hello-world',  {
      style: 'color:purple;'
    })
    this.appendChild(hw)
    const hu = createElement('span', {
      is:'hello-universe',
      style:'color: brown'
    })
    this.appendChild(hu)
  }
}

export { createElement }
export function defineAll() {
  customElements.define('hello-world', HelloWorld)
  customElements.define('hello-universe', HelloUniverse, { extends:'span' })
  customElements.define('view-01', View01)
  customElements.define('view-02', View02)
  customElements.define('view-03', View03)
  customElements.define('view-04', View04)
}


```

The html
```
<html>
  <head>
      <style>
        view-01, view-02, view-03, view-04 {
          display: block;
        }
        hello-world {
          display: inline;
        }
        span[is=hello-universe]::before {
          content: " ";
          white-space: pre;
        }
      </style>
      <script type="importmap">
      {
        "imports": {
          "utils/createElement" : "/dist/utils/createElement.js"
        }
      }
    </script>
    <script type="module">
      import {defineAll} from '/dist/components/helloWorld.js'
      defineAll()
    </script>
  </head>
  <body>
    <view-01></view-01>
    <view-02></view-02>
    <view-03></view-03>
    <view-04></view-04>
  </body>
</html>

```

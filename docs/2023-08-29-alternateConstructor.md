# Alternate Constructor Pattern for customElements

It is generally accepted that the constructor for customElements does not have any parameters.
This way we can initialize the component in JavaScript or as HTML.

But what if we don't care about initializing the customElement in HTML. We will only do it in JS because we have 
to pass data to it for it to work.

No constructor arguments parameters - nothing interesting here, just setting a baseline.
```
export class HelloWorld extends HTMLElement{
  constructor(){
    super();
    this.textContent = 'Hello World'
  }
}
customElements.define('hello-world',HelloWorld)

// example usage in JavaScript
const hello = new HelloWorld()
document.append(hello)

// example usage in the html
<body> <hello-world></hello-world>
```

With Constructor Arguments
```
export class HelloWorld extends HTMLElement{
  constructor(msg){
    super();
    this.textContent = msg
  }
}
// Yes! this will work
const hello = new HelloWorld('Hello Universe')
document.append(hello)

// in html this will not work
```

The only issue I have with this approach, right now, is if the spect evolves, will
 

# Alternate Constructor Pattern for customElements

It is generally accepted that the constructor for customElements does not have any parameters.
This way we can initialize the component in JavaScript or as HTML.

But what if we don't care about initializing the customElement in HTML and only plan to initialize in JavaScript?

## No constructor arguments parameters - nothing interesting here, just setting a baseline.
```
export class HelloWorld extends HTMLElement {
  // unnecessary constructor, but here for clarity.
  constructor(){
    super();
  }
  connectedCallback(){
    this.textContent = this.getAttribute('msg')
  }
}
customElements.define('hello-world',HelloWorld)

// example usage in JavaScript
const hello = new HelloWorld()
document.append(hello)

// example usage in the html
<body> <hello-world msg="Hello World"></hello-world>
```

## Exmaple With Constructor Arguments
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

The only issue I have with this approach, right now, is if the spec evolves, will it cause issues?

Right now using constructor args doesn't feel right. I'm sure the more I use this approach, the more comfortable I'll be with it.

## Similar Approach, best of both worlds
Another approach that is a bit more verbose is to create a function that behaves much the same way but still uses older approaches.

```
// uses the traditional component from the first example, but provides the same benefits.
export const createHelloWorld(msg){
  const hello = new HelloWorld()
  hello.setAttribute('msg',msg)
  return hello
}
```

## Required and Optional Arguments 

Back to the constructor with argumnets example, we colud handle one of the other issues with components, what is required and what is optional.

Communicating required and optional arguments is not trivial in customElements. There are a few specs that try to generate metadata to help. Intellisense is nicer that hunting through documents.

```
export type requiredArgs = {
  msg:string
}
export type optionalArgs = {
  color?:string
}

export class HelloWorld extends HTMLElement {
  constructor(required:requiredArgs, optional:optionalArgs ={}){
    super();
    this.textContent = required.msg
    if(optional?.color){
      this.style.color = color // don't set color this way, just a simple example.
    }
  }
}

const required = { msg: 'Hello World' }
const optional = { color: 'green' }
const hello = new HelloWorld(required, optional)
document.append(hello)

```

I exaggerated having two arguments in this example. In a real component, with typescript, I would have one argument and let typescript manage the required and optional arguments. 
 
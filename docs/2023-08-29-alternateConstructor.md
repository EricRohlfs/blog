# Using Constructor Arguments in customElements

It is generally accepted that customElement constructors do not accept parameters.
This makes since since HTML is a markupg language and not a programming language. HTML does not have a concept of a constructor. This is the main reason why we don't use constructor arguments. But, nothing says we can't. There are benefits to the approach. The primarny benefit is it feels more natural in JavaScript. The whole idea of the constructor is to gather the dependencies needed to do work. Passing data via the constructor makes sense and can help with other issues like what is required for the component to work, or order of operations.  

Note: I am not talking about reactivity. Reactivity defined as; if the data changes after the component is materialized or rendered, the change is reflected in the rendered html.

Decision Tree: 
1. Do you need to use JavaScript to set data or attributes to create the component?


1A. Yes: Then this is a valid option.


1B. No: Then your mileage may varry depending on what you are planning to do with your component.

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
document.body.append(hello)

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
document.body.append(hello)

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

Back to the constructor with argumnets example. 
Another issue with customElements is communicating required and optional arguments. This is not trivial. There are a few specs that try to generate metadata to help. I find them hard to read, when they are autogenerated.

Intellisense is more developer friendly and we can get that with Typescript and constructor arguments.

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
document.body.append(hello)

```

I exaggerated having two arguments in this example. In a real component, with typescript, I would have one argument and let typescript manage the required and optional arguments. 

## Real World Usage Note
In the examples above, I simply appended the component to the document body.

In a component architure, I would have a placeholder element, usually a div, that would be queried and appended to.

```
// Host component that will use hello-world.

export class MyHostComponent extends HTMLElement{

  connectedCallback(){
    // black magic in my html function described in another post but returns document fragment from a template literal
    const frag = html`<div><h1>Welcome</h1> <div placeholder-hello-world></div></div>
    const helloWorldPlaceholder = frag.querySelector(':scope div[placeholder-hello-world])
    const helloWorld = new HelloWorld({msg:'Hello World'})
    helloWorldPlaceholder.append(helloWorld)
    
  }

}

```
### Note on querySelector strategy: 
Attributes: I'm using a boolean attribute. Querying attributes is slower, BUT in this example it won't make a difference. Why? We are not querying the whole DOM and we have a very shallow tree. If this component was a top level component with a deep tree underneath, then I would use classes.

Classes: Classes are faster, but if using a framework like tailwind where there might be 20 class attributes, that can be hard to read and manage.

ID's: If I knew there would only ever be one hello-world-placeholder one on the page, then I might use id if I needed to increase performance and was having a readability issue with too many classes. Otherwise, I would not recommend using an id. As per the spec, id's are supposed to be unique to the document.

## Other Interesting Facts  

In this approach, you will have to new up the component like you would any other class. document.createElement(tagname, options) will not work.

You still need to define the element in the custom element registry. customElements.define(tagname,implementation, options)
 

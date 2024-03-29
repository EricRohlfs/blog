# Custom createElement

see also [CED](customElementDefinition.md)

The goal is to simplify development and gracefully handle annoying edge cases.
AND
To use connical names.  For example, use 'tagName' vs 'name'. TagName is in the spec, and when you read for the property on the element, it's the same language.
For this to work, after the element is created, we obviously do not want to add that property. So the internals will need to have a list of properties not to add.

## Features

* security - do not allow creation of the script tag.
* security - do not allow setting innerHTML as a property
* security - do not allow attributes starting with 'on'
* security - do not allow off page a hrefs (you might need to relax this in certain situations 
* attributes - class support several different types like 'show' or an array ['show', 'green']
* automatically handle boolean attributes
* automatically handle boolean aria attributes - the logic is not the same
* automatically add attributes
* automatically add properties
* have a way to not add some properites
* automatically add properites and convert dash-case to camelCase
* automatically add the is attribute to the element 
* automatically call the correct constructor when using the is attribute
* a way to have flags to turn the features on or off
* Can pass an argumnet into the constructor under certain conditions.

I've tried several methods and the cleanest is to pass in on object, having argumnets get too weird given all the potential flags

Example: basic element - not a custom element
```
const myDiv = createElement({
  tagName:'div',
  attributes: {
    class:'large'
  }
 });
 // <div class="large"></div>
```

Example: same as above but class as an array
```
const color = 'gray'
const myDiv = createElement({
  tagName:'div',
  attributes: {
    class:['large', color]
  }
 });
 // <div class="large gray"></div>
```

Example: setting the text of an element with typescript support

Allows setting any valid and 'secure' element property. innerHTML is not considered secure, so it cannot be set.

```
const myDiv = createElement<div>({
  tagName:'div',
  textContent: 'Hello World'
 });
 // <div>Hello World</div>
```

Example: basic custom element using 'is' where custom element extends HTMLDivElement vs. HTMLElement
```
const myDiv = createElement({
  tagName:'div',
  attributes: {
    is:'my-modal',
    class:'modal large primary',
    show: true
  }
 });
 // <div show="true" class="modal large primary"></div>
```

Example: custom element with typescript support
```
import {MyModal} from './myModal.js'
const myDiv = createElement<MyModal>({
  tagName:'div',
  attributes: {
    is:'my-modal',
    class:'modal large primary',
    show: true
  }
 });
 // <div show="true" class="modal large primary"></div>
 console.info(myDiv.show) // does not throw typescript error.
```

Example: properties -similar to props but do not get bound as attributes like in other frameworks. 
Custom element inherits HTMLElement - no 'is' syntax required.
Custom element has a property of user.
```
import {UserDetails} from './userDetails.js'
const userDetails = createElement<UserDetails>({
  tagName:'user-details',
  properties:{
    user: {
      firstName: 'Jane',
      lastName: 'Doe',
      'user-id': '12345'
    }
  }
 });

 console.info(myDiv.user.userId) // user-id becomes userId
 console.info(myDiv.user.firstName) // returns 'Jane'
```

Example: defineProperties 
[see MDN for details](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperties)

Use Cases:
  * Freeze a value so it cannot be changed
  * Stash some data in an element that is not part of the formal definition and you need that data to be enumerable - rarely happens but have had to do it a few times.

```
import {UserDetails} from './userDetails.js'
const userDetailsCED = { // CED is component element description
  tagName:'user-details',
  defineProperties:{
    userId:{
      value:'12345',
      writable: false,
      enumerable: true
    }
  }
 });

const router = this.closest('router') // find a parent component router element
router.change('#/user/12345', userDetailsCED)

// the router would have code something like this
export class Router{
  ...
  change(route, ced){
    const component = createElement(ced)
    ... // do work to insert the component
  }
 }
```

Example: Older example with several extra use cases

```

// for the unsafe string, instead you should have a tagged template literal that sanitizes and returns a documnetFragment

const unsafeString = `<span>${somethingUnsafe}</span>`
const myDiv = createElement({
  tagName:'div',
  textContent:'I am text',
  innerHtml = unsafeString
  ['foo-bar']:'hello', // adds property to the elemnt as fooBar
  attributes:{
    show:'true',
    off:'false',
    is:'my-fancy-component',
    ['aria-busy']:'true', // or can be a javaScript boolean of true
    class: ['show','green'], // or could be a string
    ['bar-bar']:'i am an attribute', // since attribute the attribute is bar-bar and if flag to add attributes as props, the prop will be camel case
  }
 });
 
 myDiv.textContent // 'I am text'
 myDiv.innerHTML // nothing, it is ignored, it does not escape the html.  For this type of work create a secure tagged template literal function that returns a document fragment then you can just append.
 myDiv.fooBar // hello
 myDiv.getAttribute('aria-busy') // 'true'
 myDiv.hasAttribute('show') // true
 myDiv.hasAttribute('off') // false
 myDiv.getAttribute('is') // 'my-fancy-component'
 
```

## An incomplete example of tagged template literal
```
import {html} from 'my-project-essentials'; // you will have to make your own. I have one, but can't share it.
const userSuppliedInput = html`<span>${somethingUnsafe}</span>`
myDiv.appendChild(userSuppliedInput);

```

## Update calling a constructor. This has limitations, but it useful.
Update 2023-09-06 - I have recently updated my createElement to also support a calling a constructor with one argument. I'll post more info later but the quick is 

```
// Notice the constructor accepts an argument. This works as long as you plan to only create the element is JavaScript.
// Normaly custom elements do not accept argumnets in the constructor. But it can be done. Can be a nice alternative to the more traditional methods when passing around complex objects/props/properties. 
class HelloMsg extends HTMLElement{
  constructor(arg){
    super()
    this.msg = arg.msg
  }
  connectedCallback(){
    this.textContent = this.msg
  }
}
customElements.define('hello-msg', HelloMsg)

const myEle = createElement({
  ctor: HelloMsg,
  ctorArg: { msg:'Hello World'}
})

document.body.append(myEle)
```

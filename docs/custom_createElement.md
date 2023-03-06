# Custom createElement

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

I've tried several methods and the cleanest is to pass in on object, having argumnets get too weird given all the potential flags

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

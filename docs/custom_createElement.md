# Custom createElement

The goal is to simplify development and gracefully handle annoying edge cases

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
const myDiv = createElement({
  tagName:'div',
  ['foo-bar']:'hello', // adds property to the elemnt as fooBar
  attributes:{
    is:'my-fancy-component',
    ['aria-busy']:'true', // or can be a javaScript boolean of true
    class: ['show','green'], // or could be a string
    ['bar-bar']:'i am an attribute', // since attribute the attribute is bar-bar and if flag to add attributes as props, the prop will be camel case
  }
 });
 
 
 
```

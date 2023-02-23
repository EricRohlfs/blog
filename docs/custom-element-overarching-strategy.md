# Custom Element Design Strategy

The most heritical and important statement is __'update the largest amount HTML you can easily get away with'__ 


- Learn the fundimentals
- For templates and reactivity update the largest amount HTML you can easily get away with. Limiting factors are usually things like transitions or loops.
- Start with getting the template on the page, then work on reactivity.

## Fundimentals
- customElements.define
- constructor
- connectedCallback
- attributeChangedCallback (observedAttributes)
- disconnectedCallback
- secure template literals (via tagged template literals)
- addEventListener
- custom element that extends HTMLFormElement for input and managing input
- formData especially - ```this.user = Object.fromEntries(new FormData(this).entries());```
- use boolean attributes
- custom Events
- mutationObserver

## Extras 
- how to pass primitive data between components (usually attributes and optionally observedAttributes)
- how to pass complex object data between components (not attributes, usually query element and call a method, or could use some sort of store/broker to do the work potentially use proxy objects)
- using events to manage transitionEnd events vs. listening for clicks.
- proxy objects
- building reusable sortable table vs. one off tables (this is hard work)

## Building an actual app
- importmap or rollup
- typescript with rollup or importmap
- security XSS and XSRF
- router - just mapping a url to a component and potentially passing some data via route changes
- unit testing (Karma is recommended, but there is a trick when using typescript.)

## Naming things

DOM is the main page. Technically any document or document fragment can have a DOM, but unless specificed, this is the live page.

- render - actually adds the thing to the DOM
- create - creates the element, but does not add the item to the DOM 
- lit - anything that does template literal work, might return an element or a string just depends on the work being done. Could be thought of as a specialized create function since it usually returns and element 
- updateDOM - (I might change my mind on this later). The difference from render is this does not delete the DOM, it should mainly change attributes and text. It should not try to create elements. (I better be careful going down this road. I might end up with the vue lifecycle.)

examples - renderTBodyEle(), createTBodyEle(), templateLit(data)

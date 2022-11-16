# Custom Element Design Strategy

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
- creating custom Events

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
- 

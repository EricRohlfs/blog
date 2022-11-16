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
- formData especially - this.user = Object.fromEntries(new FormData(this).entries());
- 

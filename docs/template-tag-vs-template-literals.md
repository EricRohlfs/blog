# Template tag vs. template literals

My first production release app with 100% web components was all template literals and createElement(...).

I had very little use for the template tag. Or so I thought.

The app I'm working on now has some Quasar components and they use the template tag quite a bit.

I've found that I like it (for somethings).  I originally did not use the template tag because template literals were more effective at inserting data.

The biggest benefit of the template tag, that other tags can't do, is the browser does not try to render it or invoke the connectedCallback on any of the contents. (I tried to replicate this 
in the browser by making my own template element, and I could not find a way to do it. Why? I was just curious.)

```
const user = {
  firstName: Eric,
  lastName: R
}

const template = `<div> ${user.firstName}</div>`
```

My template strategy now.
This way I can create a re-usable component, like a dialog, but I don't need a virtual DOM or other fancy parser. 
I can create a template that has a component that I can configure. 

```
// something.html or inside another template

<my-dialog>
  <template>
    <!-- this way I can inject or configure my component in the html, if the component is to be used in multiple different projects -->
    <my-users-component api-url="/api/v1/users"></my-users-component>
  </template>
</my-dialog>
```

```
// my-users-component.js

constructor(){
  super()
  const temp = this.querySelector('template') // or could get first child
  this.template = temp.clone(true) // copy because we are going to erase later
}

connectedCallback(){
  this.innerHTML = this.template.content.clone(true)
}

```

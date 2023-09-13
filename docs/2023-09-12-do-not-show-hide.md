# Do not show/hide in templates when ...

tldr: In templates do not show or hide when the state will never change. Instead delete the never-to-be-used elements before the fragment is added to the DOM.

If you know that the element will never be visible, just remove it. This way the DOM is lighter. Which is important for components that are used in table like structures, 
where hundreds of copies are on the DOM at the same time.


```
// bad practice
connectedCallback(){
  const frag = html`
      <button class="${this.choice !== 'chicken'? 'hidden':'' }">Chicken</button>
      <button class="${this.choice !== 'steak'? 'hidden':'' }">Steak</button>
  `
}

// better practice: just remove the element
connectedCallback(){
  const frag = html`
    <div>Chicken</div>
    <div>Steak</div>
  `
  if(this.choice === 'steak'){
    frag.firstChild.remove()
  }else{
    frag.lastChild.remove()
  }
}
```



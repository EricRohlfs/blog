# Create Element is annoying

tldr; Makeing an new element using document.createElement('div',{is:'hello-world'}) does not add the is attribute to the DOM!  That is a separate step.

Wouldn't it make more sense to to just added the attribute! Maybe there is some side effect in the low level code or some other reason why it can't do it, but it should.

Most of the html created in my custom elements uses safe template literals. But I had a special case where I needed to create an element manually, then another class was going to query 
the DOM for it based on the is="hello-world" attribute, but no luck!  I had to manually add the is attribute to the DOM.

Luckily I didn't loose too much time tracking this one down.

```
  const hw = document.createElement('div', {is:'hello-world})
  hw.setAttribute('is','hello-world')
  document.body.appendChild(hw)
  
  // now other components can query the is attribute
```

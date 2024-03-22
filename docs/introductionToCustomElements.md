# Introduction to Custom Elements

Custom Element support is possible now that Internet Explorer is officially end of life.

This article shows the basic syntax to get started with custom elements.

## Hello World Example
```
// helloWorld.js
// bare bones implementation
class HelloWorld extends HTMLElement{
  connectedCallback(){
    this.textContent = "Hello World"
  }
}

customElements.define('hello-world', HelloWorld)

// index.html
<html>
  <head>
    <script type="module">
      import './helloWorld.js'
    </script>
  </head>
  <body>
     <hello-world></hello-world>
  </body>
</html>

// In the browser, should see 'Hello World'

```

## Hello World Explained
```
// helloWorld.js
// bare bones implementation
1. Must extend HTMLElement or a built-in element like HTMLFormElement. Note: built-in's use customElements.define('my-form', MyFormClass, {extends:'form'})
2. Usually ConnectedCallback does all the work and and called when the element is addded to the DOM.
3. Security is talked about in other blog posts on my site. I'll try to link to them in the future.
class HelloWorld extends HTMLElement{
  connectedCallback(){
    this.textContent = "Hello World"
  }
}

4. Associate a new tag name to the class so the browser knows what to do. Skipping this step will throw an error.
customElements.define('hello-world', HelloWorld)

5. Import the component into the page. type="module" is important.
// index.html
<html>
  <head>
    <script type="module">
      import './helloWorld.js'
    </script>
  </head>
  <body>
     <hello-world></hello-world>
  </body>
</html>
```

## Reactivity
The easiest form of reactivity is to just re-render the component. This is much faster that most people expect. If you would like a library to help with following an htmx like workflow, checkout TimberFrameJs 
https://github.com/timberframejs/timberframejs


## Built-in Very Basic Reactivity 

Don't get too excited here. This is a good place to start, but most of the time you will use more complex reactivity strategies.

I'll try to post links to other strategies here.

Google 'attributeChangedCallback' for more information.

```
class HelloWorld extends HTMLElement{
  connectedCallback(){
    this.textContent = "Hello World"
  }

  static get observedAttributes() { return ['msg']; }
  attributeChangedCallback(name, oldValue, newValue) {
    if(name === 'msg'){
      this.textContent = newValue;
    }
  }
}

// index.html
<html>
  <head>
    <script type="module">
      import './helloWorld.js'
    </script>
  </head>
  <body>
     
    <input type="text"/>
    <hello-world msg=""></hello-world>
    <script>
      // Behavior: The message should update as the user types in the text box.
      const input = document.querySelector('input')
      const helloWorldEle = document.querySelector('hello-world')
      input.addEventListener('input',()=>{
        helloWorldEle.setAttribute('msg', input.value )
      }
    </script>
  </body>
</html>

```





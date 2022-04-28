# Issue with ES6 custom elements with a template tag and vue loader

First, I'm not 100% sure if the issue is with vue loader, or vue-cli or someother vue plug-in in the stack.

The issue was, I have a custom element that has a child template 

```
 //something.vue 
 
 <template>
    <div>
      <div> ... bunch of vue stuff</div>
      <my-component>
        <!-- this worked in non-vue environment, but not in the vue-cli app  -->
        <template>
            <my-other-component></my-other-component>
        </template>
      </my-component>
    </div>
    
 </template>

```

The issue was the template under my-component quit working.  Somethnig that manages templates in vue, a vue plug-in, or a build plug-in broke it.

My solution was to create a wrapper component so that the template tag would only render on the DOM.

```
  // my-component-loader.js
  export class MyComponentLoader extends HTMLElement{
    ....
    connectedCallback(){
      this.innerHTML= `
        <my-component>
          <template>
              <my-other-component></my-other-component>
          </template>
        </my-component>
      `}}
  customElements.define('my-component-loader', MyComponentLoader)
  
 //something.vue 
 <template>
    <div>
      <div> ... bunch of vue stuff</div>
      <!-- this bypasses the loader that was trynig to hijack my template -->
      <my-component-loader></my-component-loader>
    </div>
 </template>
  
```



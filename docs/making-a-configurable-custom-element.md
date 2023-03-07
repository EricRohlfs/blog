# Making a configurable custom element

Overcomes the issues with not being able to pass in data to the constructor.

This pattern is best used for one off components that are self contained after configured and registered in the customElement registry. 
For example I have an SVG sprite custom element that needs to know the url for the sprites on app initialization. 

```
// app.ts

const svgSpriteCtor = createSvgSpriteCtor({url:'/public/images/svgsprites.svg'});
customElement.define('svg-sprite', svgSpriteCtor);

// somepage.html

<div><svg-sprite sprite='checkmark'></svg-sprite>

```

```
  //createSvgSprite.ts
  export function createSvgSpriteCtor(cfg){
    const svgSprite = class extends HTMLElement{
      ... all the usual stuff, but can use the cfg
    }
    
    return svgSprite
  }
```



Update 2023-03-07

I recently had to work on a button component that uses shadowDom so it can be ported to several different apps without experiencing css side effects.

The pattern described above was tempting to use, but felt excessive for an app to have to register hundreds of buttons AND you still need to query for them to attach click event handlers.

In this case it was easier to just use a function to new up the element and not use custom elements at all, but still use shadowdom.

```

// in use

const openBtn = createShadyBtn({textContent:'ok'})
openBtn.addEventListener('click',()=>{});

// the shared code

// this isn't the real template, just enough to pass along the idea
function templateLit(props){
return html`
  <style> .container{ color:${props.color}</style>
  <span class='container'><button>${props.textContent}</button></span>
`
const defaultProps ={
  color:'black',
  textContent:''
}
export function createShadyBtn(props){
  const propsAll = {...defaultProps, ...props} // prevent errors and save time
  const host createElement({tagName:'span'});
  const shadow = host..attachShadow({ mode: 'open' });
  const templateDocFrag = templateLit(props)
  shadow.appendChild(templateDocFrag)
  return host;
}

```

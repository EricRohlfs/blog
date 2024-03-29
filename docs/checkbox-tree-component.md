# Building a checkbox tree component

(This is specifically about making the actual checkbox that is used in a checkbox-tree component, which has way more code that I thought it would take! )

The native &lt; input type="checkbox"/ &gt; did not work for me.

My tree component has the ability to check a parent and then cascade that value to all the children. AND... have the ability to undo if it was a mistake.

My issue was I wanted to get the state of the checkbox before it was changed. I couldn't make that happen.  
Simply reversing the boolean checked doesn't work becasue there are three states:

- checked
- unchecked
- indeterminate 

Instead of using the native input element.  I created a new component that uses svg's for the three states listed above, and support disabled by graying out the background of the svg's.

State management is much easier with my custom control, because a parent item can control the click event, vs. just being a reciever of a click event with the change already taken place.  (There may be a way to do this by reading a property in the input change event, but I was already annoyed with the checked and indeterminate properties so I worte my own.)

I originally wanted to participate in formEvents, but given the nature of a big textbox tree (think a bunch of nested ul and li items), it does not need to participate in form events. But if I did need to do it, I could make an invisible child element that this class could control the state.)

Here is the public inteface. 

```
  export interface iCheckboxIcon extends HTMLElement {
    get state():checkboxStates
    set state(val:checkboxStates)
    get disabled():boolean
    set disabled(val:boolean)
  }
```

In my implementation, I'm use observedAttributes and attributeChangedCallback to add reactivity to it.
The code below is not complete. I'm sure there are parts missing, but you should be able to figure all that out.

```
// typescript magic so we can treat the following as a type without using enums.
// Special thanks to David for showing me this trick.
  export const states = {
    unchecked: 'unchecked',
    checked: 'checked',
    indeterminate: 'indeterminate'
  } as const;
  type ObjectValues<T> = T[keyof T];
  export type checkboxStates = ObjectValues<typeof states>


export class iconCheckbox extends HTMLElement {
  static get observedAttributes(){ return ['disabled', 'state'];
  
  constructor(){
    ...
  }
    
  attributeChangedCallback(name){
    // since disabled is all handleded via CSS I don't need to do anything with that here.
    if(name === state){
      this.changeIcon()
    }
  }
  changeIcon(){
    ...
  }

  get state(){
    return this.getAttribute('state') as checkBox;
  }
  set state(val:checkboxStates){
    this.setAttribute('state',val);
  }
 
  get disabled(){
    return this.hasAttribute('disabled')
  }
  set disabled(val:boolean){
    if(val === true){
      this.setAttribute('disabled','')
      return
    }
    this.removeAttribute('disabled');
  }
  
  
```

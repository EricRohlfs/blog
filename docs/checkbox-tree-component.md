# Building a checkbox tree component

The native <input type="checkbox"/> did not work for me.

My tree component has the ability to check a parent and then cascade that value to all the children. AND... have the ability to undo if it was a mistake.

My issue was I wanted to get the state of the checkbox before it was changed. I couldn't make that happen.  
Simply reversing the boolean checked doesn't work becasue there are three states:

- checked
- unchecked
- indeterminate 

Instead of using the native input element.  I created a new component that uses svg's for the three states listed above, and support disabled by graying out the background of the svg's.

The new component is much easier to controll the state with because I can control when to make the change in the UI or overall state.

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
The code below is not complete. Missing a constructor, icons and change icon implementaion, but you should be able to figure all that out.

```
// typescript magic so we can treat the following as a type without using enums.
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
  
  
```

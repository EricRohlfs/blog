# AngularJs or Vue migration to ES6

This is a general guide with some rules of thumb for where to start in a migration.

I'll be adding new entries as I work through the migration of an app.

## ng-show or ng-hide

I once tried to make my own ng-show ng-hide. I found if I just made a template literal function and wired up to a source type of event, that worked much better.

Imagine a 'view' or 'page' with a few ng-shows for a form that roughly equate to

1. loading
2. loaded
3. success
4. error

If I made corresponding functions that returned the html for these, usually using template literals, then using either events or even an attribute to manage the ui-state, it 
all worked pretty well.

I would copy and paste the ng-show html into the function as a template literal, convert the angular model properties to template literal attributes it just worked.

If using attributeChangedCallback for managing a ui-state="loading" attribute. Then the attribute changed callback can stitch it all together.

```

export class myTodoListForm extends HTMLFormElement(){
 // this is not fully functioning

  attributeChangedCallback(name, oldval, newval){
    if(name === 'ui-state'){
      // can move this to a uiStateMgr function to prevent nesting and keep this function a bit cleaner
      if(newVal === 'loading'){
        this.innerHTML = this.loadingTemplateLit()
        // wire up any events associated with this html
      }
      if(newVal === 'loaded'){
        this.innerHTML = this.showFormTemplateLit();
        // wire up any events associated wtih this html
      }
    }
  }

  loadingTemplateLit(){
    // html is our taggedTemplate security function. You can make your own, see MDN for named or tagged template literals
    return html`<div>Loading</div>`
  }
  
  showFormTemplateLit(){
    // put form html below
    return html``
  }
  
 }

```

# Connected Callback General Pattern

This is a general pattern and does not have to always be used.

The idea is to wireup everything like eventlisteners and such before you add the templateLit to the document DOM or page.

```
  // I did not run this code, so there may be syntax errors.
  export class userForm extend HTMLFormElement {
  
    connectedCallback(){
      const lit = this.templateLit()
      // this.append(lit) // NO, don't add it here, the idea of the general pattern is to append at the end.
      const btn = lit.querySelector('button')
      btn.addEventListener('click',(evt)=>{
          // do what you need to do to save to db
      }}
      
      // all the work is done before the inner elements/html is added to the DOM.
      this.append(lit)
    }
  
    templateLit(): DocumentFragment{
      // my html tagged template literal returns a document fragment vs. a string.  
      return html`
      <label for="username">User Name</label>
      <input name="username" type="text" minlength="5" maxlength="50"/>
       // todo: add more form here
       <button>Save</button>
      `
    }
  }

```

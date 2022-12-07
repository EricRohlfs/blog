# Cleaning up events on disconnectedCallback

I use to use the bound method. Where you have the function, then a named property that binds this to the function. Then you have a named property to delet in the disconnected callback.
This approach works but has a bit too much ceremony and verbosity for my liking. 

Today on StackOverflow I saw an example using the abort controller.  
I've used the abort controller before but never thought to use it for cleaning up events on disconnected callback.

This approach works great with ananoymous functions and I really like the separation of concerns.

fyi, I did not run the following code.

```
  export class userEditDialog extends HTMLDialogElement{
  
    constructor(){
      this.closeDialogAborter = new AbortController();
      this.addEventListener('close', ()=>{
        // ... do some stuff here every time the dialog closes
      },
      {signal: this.closeDialogAborter.signal})
      
      this.tableClickAborter = new AbourtController();
      const watchedTable = this.parentElement.parentElement.querySelector('table'); // pretend we have a table in a grandparent element tree
      watchedTable.addEventListener('click', (e)=>{ ...}, {signal: this.tableClickAborter});
    }
  
    disconnectedCallback(){
      this.closeDialogAborter.abort();
      this.tableClickAborter.abort();
    }
  }

```

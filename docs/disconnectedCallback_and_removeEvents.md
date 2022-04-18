# Cleaning up events in disconnectedCallback

## Backstory
In my first major native web components project was not a SPA. It was mainly forms with a shared header, footer, modals, etc. 
If I had a memory leak, no big deal, even if a user failed at filling out a form 50 different times, it would not 'leak' enough to matter.

But now I am working on a SPA and making re-usable components where a user might sit on the app all day long without refreshing the browser.
Cleanup matters.

## Issues with removeEventListener 

JavaScript removeEventListener is not as straight forward as one would initially think. Read the MDN docs for more details.
But in short, removeEventListener has to try to find and match the right event to remove. This task becomes easier if you tell it which event to remove.



The code below will not work because add .bind(this) creates a new function instance, and the two are not the same.
https://stackoverflow.com/questions/11565471/removing-event-listener-which-was-added-with-bind

```
  
  connectedCallback(){
    this.innerHTML = '<button>Close</button>
    this.closeButton = this.querySelector(':scope button')
    this.closeButton.addEventListener('click', this.closeHandler.bind(this),{once:true})
  }
  
  disconnectedCallback(){
    this.closeButton.removeEventListener('click', this.closeHandler.bind(this), {once:true})
  }

  closeHandler(e:PointerEvent){
    this.classList.add('hidden')
  }

```


One simple solution:
Make a property to hold a reference to the bound object.

https://stackoverflow.com/questions/54921027/remove-event-listener-that-has-been-added-using-bindthis


```
  
  connectedCallback(){
    this.innerHTML = '<button>Close</button>
    this.closeButton = this.querySelector(':scope button')
    this.closeHandlerBound = this.closeHandler.bind(this) // the fix
    this.closeButton.addEventListener('click', this.closeHandlerBound,{once:true})
  }
  
  disconnectedCallback(){
    this.closeButton.removeEventListener('click', this.closeHandlerbound)
  }

  closeHandler(e:PointerEvent){
    this.classList.add('hidden')
  }

```




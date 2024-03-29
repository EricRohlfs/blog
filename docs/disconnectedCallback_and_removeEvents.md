# Cleaning up events in disconnectedCallback

Older article, still good but see [Cleaning up events on disconnectedCallback](cleaningUpEvents.md)

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
    this.closeButton.addEventListener('click', this.closeHandlerBound, {once:true})
  }
  
  disconnectedCallback(){
    this.closeButton.removeEventListener('click', this.closeHandlerbound)
  }

  closeHandler(e:PointerEvent){
    this.classList.add('hidden')
  }

```

## Ensuring this strategy works

Check to see if the event count is stays the same using the Memory tab in Chrome dev tools.

Before: 20 events
Events: 22 events
After: 20 events

Safe to say this solution works

Before events are added

![image](https://user-images.githubusercontent.com/697757/163839013-bfdd8c26-e2ca-423f-b5eb-fe1740e9504b.png)

After events are added

![image](https://user-images.githubusercontent.com/697757/163839036-8ae2d0e1-1eaa-48aa-b0d0-732ab9b2e522.png)

After events are removed

![image](https://user-images.githubusercontent.com/697757/163839114-b4bd357a-11cd-4558-84ef-865d5955b077.png)





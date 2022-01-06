# Experiences rewriting AngularJs with ES6 Custom Elements
For the past year I have been upgrading AngularJs apps and for some of them I have been replacing angularJs with with ES6 customElements.
The catch for one of the upgades was to try to do it without using any 3rd party components (except for CSS was ok.)  
I have to pay a huge paperwork penalty when using 3rd party OSS.  

(Note: The original app was written in Bootstrap 3 and I couldn't use any of the old bootstrap.js due to security vulnerabilities, so I did get some experience writing a few of those components as ES6 components.
I had a good time reverse engineering that behavior, mabye I'll write about that later too.  When I compared my code to what is in Bootstrap5, the new .js stuff, it looked pretty close, I was surprised.)

When I first started I tried to replace commonly used AngularJs features with corresponding features, but found out that in many cases they were not needed.

## ng-show and ng-hide (didn't need it)
Take ng-show and ng-hide, they were widely used in just about every angularJs app ever written.  I created a simple relpacement for these called zz-show and zz-hide, 
but found out that I did not need them.  Take a simple form for example where you first show a 'loading' spinner, then the form, then 'thank you' upon submit.  
Instead of showing and hiding these I simply over-wrote them with my template literals. See the roughed out code below. Obviously not everthing can be solved with this approach.
The other main approaches I used when I needed reactivity are discussed later.

Older AngularJs in the HTML

```
  <div ng-show="$model.isLoading">...</div>
  <div ng-hide="$model.isLoading || $model.isSubmitted">...</div>
  <div ng-show="$model.isSubmitted">...</div>
```

Newer ES6 with Template Literal

```
connectedCallback(){
  this.innerHTML = this.loadingTemplate()
  const user = this.getData()
  this.innerHtml = this.formTemplate
  //wire up submit handler and show thank you on submit.
}

const loadingTemplate = () => '<div>Loading</div>'
const formTemplate = (user) => `<div> <input type="text" name="fname" value="${htmlEncode(user.fname)}"></input> </div>`
const thankYouTemplate = () => '<div>Thank You</div>'

```



## Reactivity

My main approaches for reactivitiy were

- attributeChangedCallback - for tracking things within my component
- mutation observer - when I had to reacted to Elements that were not part of my component

More on this later, 

### attributeChangedCallback 
I mostly used this to react to internal changes, and I would mainly track a 'state' attribute on the component.
I found it very nice that I could change the attribute right in the browser and see the changes. My state concept is not turing complete and does not extend well for code re-use.
But it is simple, ease to write and debug.  I found that extending it, well it started to look like vuex or redux, and it just lost the simplicity that I was looking for.
see my SalutationDropDown code below for the main idea. I found if my attributeChangedCallback was getting too big, that it might be time to break up the code into smaller components
and that is how I ended up having drop-down's each being their own component. 


## Drop Down Lists (html select)

I found that making data driven drop down lists their own components that relied on a shared base class had several advantages.
1. My form controllers were easier to read and could focus on the form submit logic
2. I could better handle the state of the html select, especially for chained drop downs, where choices change based on previous selections.
3. I could re-use previously defined selects, like really re-use.

Older AngualrJs html but the JavaScript still had to get the data in the controller or service or somewhere
```
<select ng-options="item as item.label for item in items track by item.id" ng-model="selected"></select>

```

ES6 Isolated Custom element in use in a form
```
const formTemplate = (user) => `<form>... <salutation-drop-down initial-value="${user.salutationId}"></salutation-drop-down>`
```
The Salutation Custom Element, where I have the shared code in my HTMLSelectV2 class (I found that I needed to version my base class so I just added V2 so I wouldn't have to update all my code at once.
The code for the base class is not shown, maybe I'll share the main ideas some other time. But it basically has watchers for attributes that are commonly used like my initial-value, or the quasi simple state manager.

```
 export class SalutationDropDown extends HTMLSelectV2 {
  
  attributeChangedCallback(name, newValue, oldValue){
    if(name === 'state'){
      if(newValue === 'loading'){
        this.loadingTemplate() //where the css for this is defined in the base class.
      }
      if(newValue === 'loaded'){
        ...
      }
    }
    if(name === 'selected'){
      // can do something if the selected value changes
    }
  }
 
  connectedCallback(){
    // load the data and change states as the data loads
    // do anything else you need to do here to make this a standalone component
  }
 }

// inherit from the real html select element, then you get all the benefits of what is already in the class
export class HTMLSelectV2 extends HTMLSelect {

}

```


## 

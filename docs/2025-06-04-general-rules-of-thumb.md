# 2025 ES6 Custom Element General Ruls of Thumb

These 'rules' are starting places, I follow them and when they don't make sense or do not work for the problem at hand, I move to more complex strategies.

I've found these rules hold true most componets I've needed to build. I'm mainly building business web applications; web forms, tables, etc. 


## Reactivity/State Managment

Don't do it. Let the live DOM hold the state or be the source of truth. (Obviously the database, when loading something up for the first time. I don't hit the database unnecessarily either.)

For reactivity, just re-render the whole component or the whole tree of components. Less code and easier to read. You'll thank me later.

Use this line of code to extract data from a from ```const users = Object.fromEntries(new FormData(formEle).entries());```

If you hit a point where this does not work, supported by data and performance metrics, then do targeted updates, or something else.

I've written about a few different strategies on this site. I'll try to update this page later.


## Passing data to components

For passing HTML from a parent component to a child component, use templates and slots. (write your own slot-renderer to copy down slots if not using shadow-dom, or use shadow dom when appropriate.)

For passing JSON or JavaScript objects to components...

- pass via the constructor (yes you can do that, just like in regular programming.)
- pass via a function, or functions.
- Attributes are ok for primitive data types, and have their place, but if you are already in JS just call a function.

 ## Shadow Dom

 Don't use it unless ... you want to use slots and it makes sense to use slots AND it is an end of the line component aka no further child components in the mix.

 If you are using shadown dom just for CSS purposes, you may want to rethink your css strategy, or just use it at the highest level you can to isolate a parent css.

## Extracting data from a form element

My favorite line of code to extract all the data from a form. 

 ```js
const users = Object.fromEntries(new FormData(formEle).entries());
```

## Security

Always sanitize your template literals.

Here is an older sanitizer, but I now fixed the issue around having to have a different function for table elements.

(The fix is to put the fragment into a template instead of a div, then call template.content add that to a fragment.)

https://github.com/timberframejs/timberframejs/blob/main/src/utils/taggedTemplateLit/index.ts

 

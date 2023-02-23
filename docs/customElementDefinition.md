# Custom Element Definition CED Typescript interface

Goal make things easier without recreating react,vue,angular,solid,svelte.

I have a writeup in here somewhere about building my own createElemeent function. This is an extension of that and I may have even already wrote about this.

```
createElement(tagName,attributes, options?)

export type CED = {
 tagName:string
 attrs:object //I actully have a specialzed type, but for brevity lets go with object
 props:object
 children:Array<HTMLElement>| Array<CED> //not sure if this is the correct typescript but you get the idea
 options: createElementOptions
}

export type createElementOptions = {
  booleanAttributes?: boolean
  onlyHashLinks?:boolean
  noGlobalEventHandlers?:boolean
}
```

Now we have a nice basis for making configurable components like routers or sortable tables.

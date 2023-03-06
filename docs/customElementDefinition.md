# Custom Element Definition CED Typescript interface

Goal make things easier without recreating react,vue,angular,solid,svelte.

I have a writeup in here somewhere about building my own createElemeent function. This is an extension of that and I may have even already wrote about this.

It is a good idea to have a typescript file that has all the built in html props you allow. And a typescript type for the attributes that has the allowed aria types

```
createElement(tagName,attributes, options?)

export type CED = {
 tagName:string
 attributes:object //I actully have a specialzed type, but for brevity lets go with object
 props:object
 options: createElementOptions
}

export type createElementOptions = {
  booleanAttributes?: boolean
  onlyHashLinks?:boolean
  noGlobalEventHandlers?:boolean
}
```

Now we have a nice basis for making configurable components like routers or sortable tables.


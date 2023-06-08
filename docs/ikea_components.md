# IKEA components

These components help solve the problem of unnecessary passthough attributes.

I call them IKEA components, because some assembly is required.

We need an auto-complete combobox type of component.

We could build an all-in-wonder, as in wonder why I want to do that much work for such a little reward.
I guess these can be fine in frameworks like vue.js. But I want to make basic comodity functionality that other teams can re-use and bring their own 
css frameworks and structures to. All-in-wonders do not make that easy.
``` html
  // the list of attributes could go on and on
  <my-combo-box input-classes="" button-classes="" ul-classes="" li-classes="" input-required ></my-combo-box>
```

or we could make an IKEA component
Yes, there is some copy pasta there, but last time I checked copy/past took 5 seconds and now we have the freedom
to work with our html in a manner we are familiar with.
``` html
 <div>
   <div class="group">
     <input is="my-combo-box" required min-length="2" class="combobox" />
     <button class="combobox icon"/>
   </div>
   <!-- since this is data driven we can pass classes and other attributes for the 'ul' via JavaScript using the CED or component element definition structure see other posts on this blog site for more info.-->
   <ul class="combobox></ul>
 </div>
 
 // notice all the JavaScript is in the input element.
 // The input element will coordinate everything, but expects this basic structure. It could even reach up and check that the basic parts are there.
 // While this may seem to violate some programming sacred cows, 
 // that is the trade off for the flexibility of leveragving each components built in functionality and not have to write a bunch of unnecessary pass through code. 
```


# Why not templates?
This approach may make sense if making shadowDom components, but I'm not a big fan right now. Especially for form elements/components, its just more extra work
without a reward.
Without-shadow-dom and using a slot-renderer or template renderer, then the issue with templates
is they don't really exist till they are on the DOM, causing issues if you want to grab it or add eventListeners.

``` html
  <my-combo-box>
    <template>
      <input required min-length="2" class="combobox"/>
    </template>
  </my-combo-box>
```

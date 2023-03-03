# Configuring children components 

Here are a few choices when configuring children components

## HTML Attributes 
Before connected callback or rendering the template, this is fairly easy. Get the attributes, render them in the template.

After connected callback, then we have to make them reactive, easy enough, but does require a bit more code and use attributeChangedCallback and observedAttributes

## A render function

Either already constructed or even after connectedCallback has been executed. Doesn't matter.

Don't put any code in the connectedCallback.

Require the parent to call the render function and pass in the properties.

Properties we have a few choices. Flat add them to the class or have a props property

If we flat map them, we can have getters and setters 


## createElement + properites

Use create element, bind or add the properties. Then let connected callback to the work or call the render function

Properties we have a few choices. Flat add them to the class or have a props property

If we flat map them, we can have getters and setters do any cleanup or validation.  We could do getters and setters on a props variable too, but it would not have access to the parent.

## Extra

Use proxy objects on a this.props object to do all the getter and setter work, but the the use needs to know to use a props object this.props.foo vs. something like this.foo.
Using proxies to get/set attributes can be a very helpful tool.

In the end just depends on what you are trying to do.

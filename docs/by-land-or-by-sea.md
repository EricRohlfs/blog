# By Land or by Sea

(This is a rough draft of some thoughts that need a better way to explain when building web components.
 This might be a dig on trying to take proxy objects too far and ignoring the other tools at your disposal.
 Or about data driven web vs. using the web for the web and trying to be semantic about it.
 )

When starting with native web components, there is a temptation to try to mimic and re-build the framework that you know instead of using native web components for what they are.  One of the sticking points is when it comes to what most call reactivity.  Teams usually want to have a one size fits all.  


We all want things to be easy and rational.  It sounds logical to have one way to do everything.  
In web-components and web development at large we work in a few domains HTML, JavaScript, CSS.

Since HTML and CSS are document focused, much of the action happens in JavaScript.
But JavaScript is a behind the scenes technology and it manipulates HTML and CSS.

When I build components, I'm taking the path of least resistance (for me) to build the component.
Sometimes I store and react to 'data' in attributes and sometimes I store it in so-called 'DOM properties'.

When building web-components that need to interact with each other, there is a temptation to use only JavaScipt and alot of fancy code to automagically make the components work together.
A one size fits all solution, either by land or by sea.  Where the thought of using a simple mutationObserver to watch the DOM is not even considered.

The roadblock for building a one size fits all JavaScript object only solution is so much information is needed that a totally different paradigm is needed.
Think compiler based JSX like how solidJs does things. But when you go through the code, it has vast 'rules' around every html element, it's attributes and how to interact with it.

SolidJs is great, but if you are trying to create simple component that everyone on your dev team can follow, the one size fits all solution becomes very complex and will no longer 
be something devs can follow.

My point of this article is, the web is the web. If the goal is to keep things simple, we can't be dogmatic in our solutions.

I don't have time right now for an example, and I hope to cleanup these thoughts with examples in the future.
In short there is a movement to use aria attributes for state management.  
These semantics are known and standardized and can be used, but may require some DOM type work like mutation observers for other components to react to them using JavaScript.
CSS can also react to attribute changes. If a teams strategy is do only react to things via proxy objects, then things will become more complex, not less complex.


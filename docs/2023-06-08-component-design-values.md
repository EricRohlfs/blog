# Component Design Values

As every web developer knows there are always exceptions to a rule.
Instead of making a bunch of rules and exceptions here are the things I strive for when building components.

* Security
* Reduce Cognitive Complexity or improve the readability of the code (same thing)
* Fewer paints to live DOM
* Testable


I'm sure there are more, but I usually don't know I have a preferance till I see it violated.

# Strategies

My 'essential' helper libraries to achieve these goals are below. They help with all of the design values listed above. But some of the details are implied.

* [html tagged template lit sanitizer](./html_tagged_template_literal.md)
* [createElement](./custom_createElement.md)

## Template Literals and Components 

HTMLTemplateElement and Template Literals are not the same thing. For more see [difference between templates and template literals](./template-tag-vs-template-literals.md)

I recently reviewed some code that used template literals, but did not have any variables. 
Data was filled in after the component was rendered to the DOM. 
This approach 

* did not improve readablity 
* had more paints to the live DOM than was necessary 
* required 'extra' coordination with the 'calling' parent component via events, which resulted in quite a bit more boiler plate code between components.



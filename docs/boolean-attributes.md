# Boolean Attributes

https://developer.mozilla.org/en-US/docs/Glossary/Boolean/HTML

According to the spec 
```
// incorrect and this breaks using hasAttribute
//  ele.hasAttribute(foo) is true regradless of the vaule inside
<div foo='true'></div>

// correct - can use hasAttribute('foo') to determine flow control logic
<div foo></foo>
```

Except when dealing with aria attributes 

```
// correct
<div aria-busy='true'></div>

```



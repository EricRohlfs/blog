# Chrome 133 has breaking change when using :scope in a querySelector where the context is a document fragment

[Chrome Bug 394825121](https://issues.chromium.org/issues/394825121)

In Chrome 133, querySelector against a document fragment with :scope selector returns null, where it used to return the element that matched the query.

Removing the :scope selector fixes the issue, but all the applications in production using this selector have to be redeployed.

The `:scope` selector works when the context is an element.


```
<html>
<script type="module">
  const frag = new DocumentFragment()
  const html = `<div><span>hello</span></div>`
  const div = document.createElement('div');
  div.innerHTML = html
  frag.append(div);
  const works = frag.querySelector("span");

  // !!!! :scope breaks the query in Chrome 133. Does not break in previous versions.
  const breaks = frag.querySelector(":scope span")

  const cloned =  breaks.cloneNode()
  document.body.append(cloned)

</script>

<body>
  <div class="src">Hello World</div>
  <div class="target"></div>
</body>

</html>
```

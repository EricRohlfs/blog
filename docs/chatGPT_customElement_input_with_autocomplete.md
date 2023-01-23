I've been using chatGPT to 'stub' out common custom elements and I'm surprised that each time I ask the question in a different way I get different results.
They all look good. Sometimes the language is 'options' like the example below, other times it uses the language of 'suggestions'.

Sometimes I use the langague of vanillaJs, or JavaScript without using 'custom elements'. Other times I use custom elements.  I do find that you get what you ask for.
If it is vanilla, it is vanilla that may need some minor tweaks for making a custom element.  

One example, generated the code for using the keyboard to select 'options' like up-arrow, down-arrow using keyup event.  It was a pleasant detail to add.

My main correction to this answer would be to put a builder function around it, so I can pass in callback functions for setting 
new data to the 'options' and for setting the input via a callback. See https://github.com/EricRohlfs/blog/blob/main/docs/making-a-configurable-custom-element.md


My Question to ChatGPT
```
create ES6 customElement for input element with autocomplete
```

```
class AutocompleteInput extends HTMLInputElement {
  constructor() {
    super();
    this.options = [];
    this.list = document.createElement('ul');
    this.list.classList.add('autocomplete-list');
    this.parentNode.appendChild(this.list);
    this.addEventListener('input', this.handleInput.bind(this));
    this.list.addEventListener('click', this.handleClick.bind(this));
  }

  handleInput(event) {
    let input = event.target.value;
    this.list.innerHTML = '';
    for (let option of this.options) {
      if (option.startsWith(input)) {
        let li = document.createElement('li');
        li.textContent = option;
        this.list.appendChild(li);
      }
    }
  }

  handleClick(event) {
    let selected = event.target.textContent;
    this.value = selected;
    this.list.innerHTML = '';
  }

  set options(options) {
    this._options = options;
  }

  get options() {
    return this._options;
  }
}

customElements.define('autocomplete-input', AutocompleteInput, {extends: 'input'});

```

I've been using chatGPT to 'stub' out common custom elements and I'm surprised that each time I ask the question in a different way I get different results.
They all look good. Sometimes the language is 'options' like the example below, other times it uses the language of 'suggestions'.

Sometimes I use the langague of vanillaJs, or JavaScript without using 'custom elements'. Other times I use custom elements.  I do find that you get what you ask for.
If it is vanilla, it is vanilla that may need some minor tweaks for making a custom element.  

One example, generated the code for using the keyboard to select 'options' like up-arrow, down-arrow using keyup event.  It was a pleasant detail to add.

My main correction to this answer would be to put a builder function around it, so I can pass in callback functions for setting 
new data to the 'options' and for setting the input via a callback. See [making a configurable custom element](making-a-configurable-custom-element.md)

My Question to ChatGPT
```
create ES6 customElement for input element with autocomplete
```

Update: use this.list.replaceChildren() instead of this.list.innerHTML = ''.

```
// unedited output from chatGPT 2023-01-23

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


update 2023-01-25

I was giving a quick demo today and asked the same question (exactly the same).
This was the incredible response.

Notice how this time it filled in the callback functions and added blur and also added the ability to pass the data in via attributes. The attributes are not reactive, but I'm sure with some prompt engineering they can be added.

Now I don't agree with the decision to use serialized options as an attribute, but that is a minor detail I can correct.

```
class AutocompleteInput extends HTMLInputElement {
  constructor() {
    super();

    this.options = [];
    this.list = document.createElement("ul");
    this.list.classList.add("autocomplete-list");
    this.hidden = true;

    this.addEventListener("input", this.handleInput.bind(this));
    this.addEventListener("focus", this.handleFocus.bind(this));
    this.addEventListener("blur", this.handleBlur.bind(this));
    this.list.addEventListener("mousedown", this.handleSelect.bind(this));
  }

  handleInput(event) {
    let value = event.target.value;
    if (!value) {
      this.hidden = true;
      this.list.innerHTML = "";
      return;
    }

    let filteredOptions = this.options.filter((option) => option.startsWith(value));
    this.list.innerHTML = "";
    filteredOptions.forEach((option) => {
      let optionElement = document.createElement("li");
      optionElement.innerHTML = option;
      this.list.appendChild(optionElement);
    });
    this.hidden = false;
  }

  handleFocus(event) {
    this.handleInput(event);
    document.body.appendChild(this.list);
  }

  handleBlur(event) {
    this.hidden = true;
    document.body.removeChild(this.list);
  }

  handleSelect(event) {
    let value = event.target.innerHTML;
    this.value = value;
    this.hidden = true;
    this.list.innerHTML = "";
    document.body.removeChild(this.list);
  }

  set options(options) {
    this._options = options;
  }

  get options() {
    return this._options;
  }

  set hidden(hidden) {
    this._hidden = hidden;
    if (hidden) {
      this.list.style.display = "none";
    } else {
      this.list.style.display = "block";
    }
  }

  get hidden() {
    return this._hidden;
  }
}

customElements.define("autocomplete-input", AutocompleteInput, { extends: "input" });
```

```
  <input is="autocomplete-input" options='["option1","option2","option3"]'>
```

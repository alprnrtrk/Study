## Custom Elements
----
### Implementing a custom element :
#### Built in Element
```javascript
class WordCount extends HTMLParagraphElement {
  constructor() {
    super();
  }
  // Element functionality written in here
}
```

#### Custom Element
```javascript
class PopupInfo extends HTMLElement {
  constructor() {
    super();
  }
  // Element functionality written in here
}
```

- Always call `super()` first in `constructor()`

#### Lifecycle Callbacks
```javascript
// Create a class for the element
class MyCustomElement extends HTMLElement {
  static observedAttributes = ["color", "size"];

  constructor() {
    // Always call super first in constructor
    super();
  }

  connectedCallback() {
    console.log("Custom element added to page.");
  }

  disconnectedCallback() {
    console.log("Custom element removed from page.");
  }

  adoptedCallback() {
    console.log("Custom element moved to new page.");
  }

  attributeChangedCallback(name, oldValue, newValue) {
    console.log(`Attribute ${name} has changed.`);
  }
}

customElements.define("my-custom-element", MyCustomElement);
```


#### Register Custom Elements
- you can register youre custom element with `customElements.define()`
- arguents :
	- `name`
	- `constructor`
	- `options` (only for built in elements)

##### Syntax :
```javascript 
customElements.define(name, constructor, options);
```

##### Built in example :
```javascript 
customElements.define("word-count", WordCount, { extends: "p" });
```

##### custom example :
```javascript 
customElements.define("popup-info", PopupInfo);
```

#### Using Custom Elements
##### Using built in element
```html 
	<p is="world-count"></p>
```
##### Using custom element
```html 
	<popup-info>
		  <!-- content of the element -->
	</popup-info>
```

#### Responding to attribute changes
-  Define attributes with `observedAttributes`. It's an array of string for attributes.
-  Listen `attributeChangedCallback` callback
-  Passed arguments :
	- `nameOfAttribute`
	- `oldValue`
	- `newValue`
##### Example:
```javascript
// Create a class for the element
class MyCustomElement extends HTMLElement {
  static observedAttributes = ["size"];

  constructor() {
    super();
  }

  attributeChangedCallback(name, oldValue, newValue) {
    console.log(
      `Attribute ${name} has changed from ${oldValue} to ${newValue}.`,
    );
  }
}

customElements.define("my-custom-element", MyCustomElement);
```

``` html
<my-custom-element size="100"></my-custom-element>
```

#### Custom States
- You can store data inside of an custom element or built-in element with states, and use pseudo-class selection for them 
- use  `_internals` for defining states 
- then use `.states`
- You can add custom state via `this._internals.states.add("Alduin")`
- You can remove custom state via `this._internals.states.remove("Alduin")`
- You can get custom state via `this._internals.states.has("Alduin")`

##### Example: 
``` javascript
class MyCustomElement extends HTMLElement {
  constructor() {
    super();
    this._internals = this.attachInternals();
  }

  get collapsed() {
    return this._internals.states.has("hidden");
  }

  set collapsed(flag) {
    if (flag) {
      // Existence of identifier corresponds to "true"
      this._internals.states.add("hidden");
    } else {
      // Absence of identifier corresponds to "false"
      this._internals.states.delete("hidden");
    }
  }
}

// Register the custom element
customElements.define("my-custom-element", MyCustomElement);
```

``` css
my-custom-element {
  border: dashed red;
}
my-custom-element:state(hidden) {
  border: none;
}
```

#### Real Life Example

``` javascript
// Create a class for the element
class PopupInfo extends HTMLElement {
  constructor() {
    // Always call super first in constructor
    super();
  }

  connectedCallback() {
    // Create a shadow root
    const shadow = this.attachShadow({ mode: "open" });

    // Create spans
    const wrapper = document.createElement("span");
    wrapper.setAttribute("class", "wrapper");

    const icon = document.createElement("span");
    icon.setAttribute("class", "icon");
    icon.setAttribute("tabindex", 0);

    const info = document.createElement("span");
    info.setAttribute("class", "info");

    // Take attribute content and put it inside the info span
    const text = this.getAttribute("data-text");
    info.textContent = text;

    // Insert icon
    let imgUrl;
    if (this.hasAttribute("img")) {
      imgUrl = this.getAttribute("img");
    } else {
      imgUrl = "img/default.png";
    }

    const img = document.createElement("img");
    img.src = imgUrl;
    icon.appendChild(img);

    // Create some CSS to apply to the shadow dom
    const style = document.createElement("style");
    console.log(style.isConnected);

    style.textContent = `
      .wrapper {
        position: relative;
      }

      .info {
        font-size: 0.8rem;
        width: 200px;
        display: inline-block;
        border: 1px solid black;
        padding: 10px;
        background: white;
        border-radius: 10px;
        opacity: 0;
        transition: 0.6s all;
        position: absolute;
        bottom: 20px;
        left: 10px;
        z-index: 3;
      }

      img {
        width: 1.2rem;
      }

      .icon:hover + .info, .icon:focus + .info {
        opacity: 1;
      }
    `;

    // Attach the created elements to the shadow dom
    shadow.appendChild(style);
    console.log(style.isConnected);
    shadow.appendChild(wrapper);
    wrapper.appendChild(icon);
    wrapper.appendChild(info);
  }
}
```

```javascript
customElements.define("popup-info", PopupInfo);
```

```html
<popup-info
  img="img/alt.png"
  data-text="Your card validation code (CVC)
  is an extra security feature — it is the last 3 or 4 numbers on the
  back of your card."></popup-info>
```
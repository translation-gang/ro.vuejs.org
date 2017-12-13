---
title: Clasă și Combinări de Stil
type: guide
order: 6
---

Deseori, avem nevoia de a modifica dinamic clasele CSS și elementele stilurilor inline. Deoarece ambele sunt atribute, putem folosi `v-bind` pentru a le manipula: avem nevoie doar de a calcula un string final cu expresiile noastre. Cu toate acestea, amestecul cu concatenarea șirului este enervant și predispusă la erori. Din acest motiv, Vue oferă îmbunătățiri speciale atunci când `v-bind` este folosit cu `clasă` și `stil`. În plus față de șirurile de caractere, expresiile pot, de asemenea, evalua  obiecte sau tablouri(array).
 

## Legarea Claselor HTML

### Sintaxa Obiect

Putem trece un obiect la `v-bind:class` pentru a comuta dinamic clasele:

``` html
<div v-bind:class="{ active: isActive }"></div>
```

Sintaxa de mai sus înseamnă că prezența clasei `active` va fi determinată de [truthiness](https://developer.mozilla.org/en-US/docs/Glossary/Truthy) a proprietății de date `isActive`.

Puteți avea mai multe clase comutate având mai multe câmpuri în obiect. În plus, directiva `v-bind:class` poate coexista cu atributul simplu `class`. Deci, având în vedere următorul șablon:

``` html
<div class="static"
     v-bind:class="{ active: isActive, 'text-danger': hasError }">
</div>
```

Și următoarele date:

``` js
data: {
  isActive: true,
  hasError: false
}
```

Aceasta va face:

``` html
<div class="static active"></div>
```

Când `isActive` sau `hasError` se modifică, lista de clase va fi actualizată în consecință. De exemplu, dacă `hasError` devine `true`, lista claselor va deveni `"static active text-danger"`.

Obiectul legat nu trebuie să fie în linie:

``` html
<div v-bind:class="classObject"></div>
```
``` js
data: {
  classObject: {
    active: true,
    'text-danger': false
  }
}
```

Acest lucru va da același rezultat. De asemenea, putem lega de o [proprietate computed](computed.html) care returnează un obiect. Acesta este un model comun și puternic:

``` html
<div v-bind:class="classObject"></div>
```
``` js
data: {
  isActive: true,
  error: null
},
computed: {
  classObject: function () {
    return {
      active: this.isActive && !this.error,
      'text-danger': this.error && this.error.type === 'fatal'
    }
  }
}
```

### Sintaxa Array

Putem trece un array la `v-bind:class` pentru a aplica o listă de clase:

``` html
<div v-bind:class="[activeClass, errorClass]"></div>
```
``` js
data: {
  activeClass: 'active',
  errorClass: 'text-danger'
}
```

Ceea ce vom primi:

``` html
<div class="active text-danger"></div>
```

Dacă doriți să comutați și o clasă în listă în mod condiționat, o puteți face cu o expresie ternară:

``` html
<div v-bind:class="[isActive ? activeClass : '', errorClass]"></div>
```

În acest caz `errorClass` se va aplica întotdeauna, dar `activeClass` se va aplica doar atunci când `isActive` este adevărat.

Cu toate acestea, acest lucru poate fi un pic apăsător dacă aveți mai multe clase condiționale. De aceea este posibilă și utilizarea sintaxei obiectului în sintaxa array:

``` html
<div v-bind:class="[{ active: isActive }, errorClass]"></div>
```

### With Components

> This section assumes knowledge of [Vue Components](components.html). Feel free to skip it and come back later.

When you use the `class` attribute on a custom component, those classes will be added to the component's root element. Existing classes on this element will not be overwritten.

For example, if you declare this component:

``` js
Vue.component('my-component', {
  template: '<p class="foo bar">Hi</p>'
})
```

Then add some classes when using it:

``` html
<my-component class="baz boo"></my-component>
```

The rendered HTML will be:

``` html
<p class="foo bar baz boo">Hi</p>
```

The same is true for class bindings:

``` html
<my-component v-bind:class="{ active: isActive }"></my-component>
```

When `isActive` is truthy, the rendered HTML will be:

``` html
<p class="foo bar active">Hi</p>
```

## Binding Inline Styles

### Object Syntax

The object syntax for `v-bind:style` is pretty straightforward - it looks almost like CSS, except it's a JavaScript object. You can use either camelCase or kebab-case (use quotes with kebab-case) for the CSS property names:

``` html
<div v-bind:style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
```
``` js
data: {
  activeColor: 'red',
  fontSize: 30
}
```

It is often a good idea to bind to a style object directly so that the template is cleaner:

``` html
<div v-bind:style="styleObject"></div>
```
``` js
data: {
  styleObject: {
    color: 'red',
    fontSize: '13px'
  }
}
```

Again, the object syntax is often used in conjunction with computed properties that return objects.

### Array Syntax

The array syntax for `v-bind:style` allows you to apply multiple style objects to the same element:

``` html
<div v-bind:style="[baseStyles, overridingStyles]"></div>
```

### Auto-prefixing

When you use a CSS property that requires [vendor prefixes](https://developer.mozilla.org/en-US/docs/Glossary/Vendor_Prefix) in `v-bind:style`, for example `transform`, Vue will automatically detect and add appropriate prefixes to the applied styles.

### Multiple Values

> 2.3.0+

Starting in 2.3.0+ you can provide an array of multiple (prefixed) values to a style property, for example:

``` html
<div v-bind:style="{ display: ['-webkit-box', '-ms-flexbox', 'flex'] }"></div>
```

This will only render the last value in the array which the browser supports. In this example, it will render `display: flex` for browsers that support the unprefixed version of flexbox.

---
title: Directive Personalizate
type: guide
order: 302
---

## Introducere

Pe lângă setul implicit de directive livrate în nucleu (`v-model` și `v-show`), Vue vă permite, de asemenea, să vă înregistrați propriile directive personalizate. Rețineți că în Vue 2.0, forma primară de reutilizare și abstractizare a codului reprezintă componentele - cu toate acestea, pot exista cazuri în care aveți nevoie de a efectua operațiuni la nivel inferior cu DOM, pentru care directivele personalizate pot fi foarte utile. Un exemplu ar fi focalizarea asupra unui element input:

{% raw %}
<div id="simplest-directive-example" class="demo">
  <input v-focus>
</div>
<script>
Vue.directive('focus', {
  inserted: function (el) {
    el.focus()
  }
})
new Vue({
  el: '#simplest-directive-example'
})
</script>
{% endraw %}

Când pagina se încarcă, acel element capătă focalizare (rețineți: autofocusul nu funcționează pe Safari mobil). De fapt, dacă nu ați făcut clic pe altceva de la vizitarea acestei pagini, inputul de mai sus ar trebui să fie focalizat acum. Deci, să construim directiva care realizează acest lucru:

``` js
// Înregistrăm o directivă personalizată globală numită v-focus
Vue.directive('focus', {
  // Când elementul legat este inserat în DOM...
  inserted: function (el) {
    // Focalizarea elementului
    el.focus()
  }
})
```

Dacă doriți să înregistrați o directivă locală, componentele acceptă și opțiunea `directives`:

``` js
directives: {
  focus: {
    // definirea directivei
    inserted: function (el) {
      el.focus()
    }
  }
}
```

Apoi, într-un șablon, puteți folosi noul atribut `v-focus` pe orice element, cum ar fi acesta:

``` html
<input v-focus>
```

## Hook Functions

A directive definition object can provide several hook functions (all optional):

- `bind`: called only once, when the directive is first bound to the element. This is where you can do one-time setup work.

- `inserted`: called when the bound element has been inserted into its parent node (this only guarantees parent node presence, not necessarily in-document).

- `update`: called after the containing component's VNode has updated, __but possibly before its children have updated__. The directive's value may or may not have changed, but you can skip unnecessary updates by comparing the binding's current and old values (see below on hook arguments).

- `componentUpdated`: called after the containing component's VNode __and the VNodes of its children__ have updated.

- `unbind`: called only once, when the directive is unbound from the element.

We'll explore the arguments passed into these hooks (i.e. `el`, `binding`, `vnode`, and `oldVnode`) in the next section.

## Directive Hook Arguments

Directive hooks are passed these arguments:

- **el**: The element the directive is bound to. This can be used to directly manipulate the DOM.
- **binding**: An object containing the following properties.
  - **name**: The name of the directive, without the `v-` prefix.
  - **value**: The value passed to the directive. For example in `v-my-directive="1 + 1"`, the value would be `2`.
  - **oldValue**: The previous value, only available in `update` and `componentUpdated`. It is available whether or not the value has changed.
  - **expression**: The expression of the binding as a string. For example in `v-my-directive="1 + 1"`, the expression would be `"1 + 1"`.
  - **arg**: The argument passed to the directive, if any. For example in `v-my-directive:foo`, the arg would be `"foo"`.
  - **modifiers**: An object containing modifiers, if any. For example in `v-my-directive.foo.bar`, the modifiers object would be `{ foo: true, bar: true }`.
- **vnode**: The virtual node produced by Vue's compiler. See the [VNode API](../api/#VNode-Interface) for full details.
- **oldVnode**: The previous virtual node, only available in the `update` and `componentUpdated` hooks.

<p class="tip">Apart from `el`, you should treat these arguments as read-only and never modify them. If you need to share information across hooks, it is recommended to do so through element's [dataset](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/dataset).</p>

An example of a custom directive using some of these properties:

``` html
<div id="hook-arguments-example" v-demo:foo.a.b="message"></div>
```

``` js
Vue.directive('demo', {
  bind: function (el, binding, vnode) {
    var s = JSON.stringify
    el.innerHTML =
      'name: '       + s(binding.name) + '<br>' +
      'value: '      + s(binding.value) + '<br>' +
      'expression: ' + s(binding.expression) + '<br>' +
      'argument: '   + s(binding.arg) + '<br>' +
      'modifiers: '  + s(binding.modifiers) + '<br>' +
      'vnode keys: ' + Object.keys(vnode).join(', ')
  }
})

new Vue({
  el: '#hook-arguments-example',
  data: {
    message: 'hello!'
  }
})
```

{% raw %}
<div id="hook-arguments-example" v-demo:foo.a.b="message" class="demo"></div>
<script>
Vue.directive('demo', {
  bind: function (el, binding, vnode) {
    var s = JSON.stringify
    el.innerHTML =
      'name: '       + s(binding.name) + '<br>' +
      'value: '      + s(binding.value) + '<br>' +
      'expression: ' + s(binding.expression) + '<br>' +
      'argument: '   + s(binding.arg) + '<br>' +
      'modifiers: '  + s(binding.modifiers) + '<br>' +
      'vnode keys: ' + Object.keys(vnode).join(', ')
  }
})
new Vue({
  el: '#hook-arguments-example',
  data: {
    message: 'hello!'
  }
})
</script>
{% endraw %}

## Function Shorthand

In many cases, you may want the same behavior on `bind` and `update`, but don't care about the other hooks. For example:

``` js
Vue.directive('color-swatch', function (el, binding) {
  el.style.backgroundColor = binding.value
})
```

## Object Literals

If your directive needs multiple values, you can also pass in a JavaScript object literal. Remember, directives can take any valid JavaScript expression.

``` html
<div v-demo="{ color: 'white', text: 'hello!' }"></div>
```

``` js
Vue.directive('demo', function (el, binding) {
  console.log(binding.value.color) // => "white"
  console.log(binding.value.text)  // => "hello!"
})
```

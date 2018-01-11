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

Când pagina se încarcă, acel element capătă focalizare (rețineți: `autofocus` nu funcționează pe Safari mobil). De fapt, dacă nu ați făcut clic pe altceva de la vizitarea acestei pagini, inputul de mai sus ar trebui să fie focalizat acum. Deci, să construim directiva care realizează acest lucru:

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

## Funcții de Declanșare (Hook-uri)

Un obiect de definire a directivelor poate oferi mai multe hook-uri (toate opționale):

- `bind`: apelată o singură dată, când directiva este prima dată legată de element. Aici puteți efectua o singură operație de configurare.

- `inserted`: apelată atunci când elementul legat a fost inserat în nodul părinte (acest lucru garantează doar prezența în nodul părinte, nu neapărat în document).

- `update`: apelată după ce VNode-ul componentei-container este actualizat, __dar eventual înainte ca elementele derivate să fie actualizate__. Valoarea directivei se poate schimba până în acest moment sau poate nu, dar puteți sări peste actualizările inutile prin compararea valorilor curente și vechi ale legăturii (priviți mai jos despre argumentele hook-urilor).

- `componentUpdated`: apelată după actualizarea atât a VNodei componentei-container __cât și a VNode-lor derivatelor sale__.

- `unbind`: apelată o singură dată, când directiva se dezleagă de la element.

Vom examina argumentele transmise în aceste hook-uri (și anume `el`, `binding`, `vnode` și `oldVnode`) în secțiunea următoare.

## Argumentele Hook-urilor

Următorii parametri sunt transmiși în hook-uri:

- **el**: Elementul de care directiva este legată. Acesta poate fi folosit pentru a manipula direct DOM.
- **binding**: Un obiect care conține următoarele proprietăți.
  - **name**: Numele directivei, fără prefixul `v-`.
  - **value**: Valoarea transmisă directivei. De exemplu, în `v-my-directive="1 + 1"`, valoarea ar fi `2`.
  - **oldValue**: Valoarea anterioară, disponibilă numai în `update` și `componentUpdated`. Este disponibilă chiar dacă valoarea nu a fost modificată.
  - **expression**: Expresia legării ca șir de caractere. De exemplu, în `v-my-directive="1 + 1"`, aceasta va fi `"1 + 1"`.
  - **arg**: Argumentul transmis directivei, dacă există. De exemplu, în `v-my-directive:foo`, arg va fi `"foo"`.
  - **modifiers**: Un obiect care conține modificatori, dacă există. De exemplu, în `v-my-directive.foo.bar`, obiectul cu modificatori ar fi `{ foo: true, bar: true }`.
- **vnode**: Nodul virtual produs de compilatorul Vue. Analizați [VNode API](../api/#VNode-Interface) pentru detalii complete.
- **oldVnode**: Nodul virtual anterior, disponibil numai în hook-urile `update` și `componentUpdated`.

<p class="tip">În afară de `el`, ar trebui să tratați aceste argumente ca fiind doar pentru citire și să nu le modificați niciodată. Dacă aveți nevoie să partajați informația între hook-uri, se recomandă să faceți acest lucru prin [dataset](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/dataset).</p>

Urmează un exemplu de directivă personalizată care utilizează unele dintre aceste proprietăți:

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

## Forma Prescurtată

În multe cazuri, este posibil să doriți același comportament penreu `bind` și `update`, dar să nu vă fie importante celelalte hook-uri. De exemplu:

``` js
Vue.directive('color-swatch', function (el, binding) {
  el.style.backgroundColor = binding.value
})
```

## Transmiterea Obiectului de Date în Directivă

Dacă directiva dvs. are nevoie de mai multe valori, puteți transmite și un obiect JavaScript. Rețineți că directivele pot lua orice expresie JavaScript validă.

``` html
<div v-demo="{ color: 'white', text: 'hello!' }"></div>
```

``` js
Vue.directive('demo', function (el, binding) {
  console.log(binding.value.color) // => "white"
  console.log(binding.value.text)  // => "hello!"
})
```

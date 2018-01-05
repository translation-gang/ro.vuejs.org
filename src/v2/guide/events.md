---
title: Manipularea Evenimentelor
type: guide
order: 9
---

## Ascultătorii de Evenimente

Noi putem folosi directiva `v-on` pentru a asculta evenimentele DOM-ului și a rula JavaScript atunci când acestea sunt declanșate.

De exemplu:

``` html
<div id="example-1">
  <button v-on:click="counter += 1">Add 1</button>
  <p> Butonul de mai sus a fost apăsat de {{ counter }} ori.</p>
</div>
```
``` js
var example1 = new Vue({
  el: '#example-1',
  data: {
    counter: 0
  }
})
```

Rezultat:

{% raw %}
<div id="example-1" class="demo">
  <button v-on:click="counter += 1">Adaugă 1</button>
  <p>Butonul de mai sus a fost apăsat de {{ counter }} ori.</p>
</div>
<script>
var example1 = new Vue({
  el: '#example-1',
  data: {
    counter: 0
  }
})
</script>
{% endraw %}

## Metoda de Gestionare a Evenimentelor

Logica procesării evenimentelor poate fi destul de complexă, astfel încât nu este întotdeauna posibilă lăsarea întregului cod JavaScript în valoarea atributului v-on. În acest caz, `v-on` poate accepta numele metodei pe care doriți să o apelați.

De examplu:

``` html
<div id="example-2">
  <!-- `greet`  este numele metodei is the name of a method definite mai jos -->
  <button v-on:click="greet">Greet</button>
</div>
```

``` js
var example2 = new Vue({
  el: '#example-2',
  data: {
    name: 'Vue.js'
  },
  // definiți metodele în obiectul `methods`
  methods: {
    greet: function (event) {
      // `this` din interior metodelor indică instanța Vue
      alert('Hello ' + this.name + '!')
      // `event` este evenimentul DOM-ului nativ
      if (event) {
        alert(event.target.tagName)
      }
    }
  }
})

// puteți invoca și metode în JavaScript
example2.greet() // => 'Salut Vue.js!'
```

Rezultat:

{% raw %}
<div id="example-2" class="demo">
  <button v-on:click="greet">Greet</button>
</div>
<script>
var example2 = new Vue({
  el: '#example-2',
  data: {
    name: 'Vue.js'
  },
  methods: {
    greet: function (event) {
      alert('Salut ' + this.name + '!')
      if (event) {
        alert(event.target.tagName)
      }
    }
  }
})
</script>
{% endraw %}

## Metode și Manipulatoare-Inline

În loc să se lege direct de un nume de metodă, putem folosi și metode într-o instrucțiune JavaScript inline:

``` html
<div id="example-3">
  <button v-on:click="say('Salut')">Spune Salut</button>
  <button v-on:click="say('Ce')">Spune Ce</button>
</div>
```
``` js
new Vue({
  el: '#example-3',
  methods: {
    say: function (message) {
      alert(message)
    }
  }
})
```

Rezultat:
{% raw %}
<div id="example-3" class="demo">
  <button v-on:click="say('Salut')">Spune Salut</button>
  <button v-on:click="say('Ce')">Spune Ce</button>
</div>
<script>
new Vue({
  el: '#example-3',
  methods: {
    say: function (message) {
      alert(message)
    }
  }
})
</script>
{% endraw %}

Uneori trebuie să accesăm evenimentul DOM original într-un manipulant de declarații inline. Puteți să o treceți într-o metodă utilizând variabila specială `$event`:

``` html
<button v-on:click="warn('Formularul încă nu poate fi trimis.', $event)">
  Submit
</button>
```

``` js
// ...
methods: {
  warn: function (message, event) {
    // acum avem acces la evenimentul nativ
    if (event) event.preventDefault()
    alert(message)
  }
}
```

## Modificatorii de Evenimente

Este o necesitate foarte frecventă de a apela `event.preventDefault()` sau `event.stopPropagation()` în cadrul procesatorilor de evenimente. Deși putem face acest lucru cu ușurință în interiorul metodelor, ar fi mai bine dacă metodele pot fi pur și simplu legate de logica datelor, mai degrabă decât să se ocupe de detaliile evenimentului DOM.

Pentru a rezolva această problemă, Vue oferă **modificatorii de evenimente** pentru `v-on`. Amintiți-vă că modificatorii sunt directive postfixe direcționate cu un punct.

- `.stop`
- `.prevent`
- `.capture`
- `.self`
- `.once`

``` html
<!-- evenimentul de click nu se va mai afișa -->
<a v-on:click.stop="doThis"></a>

<!-- evenimentul submit nu va mai reîncărca pagina -->
<form v-on:submit.prevent="onSubmit"></form>

<!-- modificatorii pot fi combinați în lanțuri -->
<a v-on:click.stop.prevent="doThat"></a>

<!-- doar modificatorul -->
<form v-on:submit.prevent></form>

<!-- utilizați modul capture atunci când adăugați ascultătorul de evenimente -->
<!-- adică un eveniment care vizează un element interior este tratat aici înainte de a fi manipulat de acel element -->
<div v-on:click.capture="doThis">...</div>

<!-- numai un manipulant de declanșare dacă event.target este elementul în sine -->
<!-- adică nu dintr-un element derivat -->
<div v-on:click.self="doThat">...</div>
```

<p class="tip">Ordinul contează atunci când se utilizează modificatorii, deoarece codul relevant este generat în aceeași ordine. Prin urmare, folosirea lui `@click.prevent.self` va împiedica **toate click-urile** în timp ce `@click.self.prevent` va împiedica doar click-urile pe elementul în sine..</p>

> Nou în 2.1.4+

``` html
<!--  evenimentul click va fi declanșat cel mult o dată -->
<a v-on:click.once="doThis"></a>
```

Spre deosebire de ceilalți modificatori, care sunt exclusivi pentru evenimentele DOM-ului nativ, modificatorul `.once` poate fi de asemenea utilizat în [evenimentele component](components.html#Using-v-on-with-Custom-Events). Dacă nu ați citit încă despre componente, nu vă faceți griji în acest moment.

## Modificatorii de tip Cheie

Când ascultați evenimentele de la tastatură, trebuie să verificați frecvent codurile cheie comune. Vue permite, de asemenea, adăugarea de modificatori cheie pentru `v-on` atunci când ascultați evenimentele-cheie:

``` html
<!-- apelați numai vm.submit() când codul cheie este 13 -->
<input v-on:keyup.13="submit">
```

Reținerea în memorie a tuturor codurilor cheie este un dificilă, astfel Vue oferă pseudonime pentru cele mai frecvent utilizate chei:

``` html
<!-- la fel ca mai sus -->
<input v-on:keyup.enter="submit">

<!-- lucrează și pentru shorthand -->
<input @keyup.enter="submit">
```

Iată lista completă a pseudonimelor modificatoare:

- `.enter`
- `.tab`
- `.delete` (captează atât cheile "Ștergere", cât și pe cele "Backspace")
- `.esc`
- `.space`
- `.up`
- `.down`
- `.left`
- `.right`

Puteți, de asemenea, să [definiți pseudonime a cheilor modificatoare personalizate](../api/#keyCodes) prin obiectul global `config.keyCodes`:

``` js
// activați v-on: keyup.f1
Vue.config.keyCodes.f1 = 112
```

### Modificatorii Cheie Automatizați

> Nou în 2.5.0+

De asemenea, puteți folosi direct orice nume cheie expuse prin [`KeyboardEvent.key`](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/key/Key_Values) ca modificatori prin conversia lor în kebab-case:

``` html
<input @keyup.page-down="onPageDown">
```

În exemplul de mai sus, manipulatorul va fi apelat numai dacă `$event.key === 'PageDown'`.

<p class="tip">Câteva taste (`.esc` și toate tastele săgeată) au valori incoerente `cheie` în IE9, pseudonimele lor construite ar trebui să fie preferate dacă aveți nevoie de suport pentru IE9.</p>

## System Modifier Keys

> New in 2.1.0

You can use the following modifiers to trigger mouse or keyboard event listeners only when the corresponding modifier key is pressed:

- `.ctrl`
- `.alt`
- `.shift`
- `.meta`

> Note: On Macintosh keyboards, meta is the command key (⌘). On Windows keyboards, meta is the windows key (⊞). On Sun Microsystems keyboards, meta is marked as a solid diamond (◆). On certain keyboards, specifically MIT and Lisp machine keyboards and successors, such as the Knight keyboard, space-cadet keyboard, meta is labeled “META”. On Symbolics keyboards, meta is labeled “META” or “Meta”.

For example:

```html
<!-- Alt + C -->
<input @keyup.alt.67="clear">

<!-- Ctrl + Click -->
<div @click.ctrl="doSomething">Do something</div>
```

<p class="tip">Note that modifier keys are different from regular keys and when used with `keyup` events, they have to be pressed when the event is emitted. In other words, `keyup.ctrl` will only trigger if you release a key while holding down `ctrl`. It won't trigger if you release the `ctrl` key alone.</p>

### `.exact` Modifier

> New in 2.5.0

The `.exact` modifier should be used in combination with other system modifiers to indicate that the exact combination of modifiers must be pressed for the handler to fire.

``` html
<!-- this will fire even if Alt or Shift is also pressed -->
<button @click.ctrl="onClick">A</button>

<!-- this will only fire when only Ctrl is pressed -->
<button @click.ctrl.exact="onCtrlClick">A</button>
```

### Mouse Button Modifiers

> New in 2.2.0+

- `.left`
- `.right`
- `.middle`

These modifiers restrict the handler to events triggered by a specific mouse button.

## Why Listeners in HTML?

You might be concerned that this whole event listening approach violates the good old rules about "separation of concerns". Rest assured - since all Vue handler functions and expressions are strictly bound to the ViewModel that's handling the current view, it won't cause any maintenance difficulty. In fact, there are several benefits in using `v-on`:

1. It's easier to locate the handler function implementations within your JS code by skimming the HTML template.

2. Since you don't have to manually attach event listeners in JS, your ViewModel code can be pure logic and DOM-free. This makes it easier to test.

3. When a ViewModel is destroyed, all event listeners are automatically removed. You don't need to worry about cleaning it up yourself.

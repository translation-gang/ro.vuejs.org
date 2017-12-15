---
title: Randarea condiționată
type: guide
order: 7
---

## `v-if`

În șabloanele de tip string, de exemplu, Handlebars, am putea scrie un bloc condițional astfel:

``` html
<!-- Șablon Handlebars -->
{{#if ok}}
  <h1>Yes</h1>
{{/if}}
```

În Vue, folosim directiva `v-if` pentru a obține aceleași rezultate:

``` html
<h1 v-if="ok">Yes</h1>
```

Este, de asemenea, posibil să adăugați "un alt bloc" cu `v-else`:

``` html
<h1 v-if="ok">Yes</h1>
<h1 v-else>No</h1>
```

### Grupuri condiționate cu `v-if` pe `<template>`

Deoarece `v-if` este o directivă, aceasta va trebui atașată la un singur element. Dar dacă vrem să comutăm mai multe elemente? În acest caz putem folosi `v-if` pe un element `<template>`, care servește ca un wrap invizibil. Rezultatul final randat nu va include elementul `<template>`.

``` html
<template v-if="ok">
  <h1>Titlu</h1>
  <p>Paragraful 1</p>
  <p>Paragraful 2</p>
</template>
```

### `v-else`

Puteți folosi directiva `v-else` pentru a indica un "alt bloc" pentru `v-if`:

``` html
<div v-if="Math.random() > 0.5">
  Acum mă vezi
</div>
<div v-else>
  Acum nu
</div>
```

Un element `v-else` trebuie să urmeze imediat după un element v-if sau un `v-else-if` element - altfel acesta nu va fi recunoscut.

### `v-else-if`

> Adăugat în versiunea 2.1.0+

Modelul `v-else-if`, așa cum sugerează și numele, servește drept "alt bloc if" pentru `v-if`. De asemenea, poate fi legat de mai multe ori:

```html
<div v-if="type === 'A'">
  A
</div>
<div v-else-if="type === 'B'">
  B
</div>
<div v-else-if="type === 'C'">
  C
</div>
<div v-else>
  Nu A/B/C
</div>
```

Similar cu `v-else`, un element `v-else-if` trebuie să urmeze imediat după un element `v-if` sau `v-else-if`.

### Controlul elementelor reutilizabile cu `key`

Vue încearcă să facă elementele cât mai eficient posibil, adesea reutilizându-le în loc de randare de la zero. Pe lângă faptul că aceasta ajuta la rapiditatea Vue-ului, acest lucru poate avea și alte avantaje utile. De exemplu, dacă permiteți utilizatorilor să comute între mai multe tipuri de login:

``` html
<template v-if="loginType === 'username'">
  <label>Numele Utilizatorului</label>
  <input placeholder="Enter your username">
</template>
<template v-else>
  <label>Email</label>
  <input placeholder="Enter your email address">
</template>
```

Apoi, schimbarea `loginType` în codul de mai sus nu va șterge ce a introdus deja utilizatorul. Deoarece ambele șabloane utilizează aceleași elemente, `<input>` nu este înlocuit - doar `placeholder`.

Verificați-l pentru dvs. prin introducerea unui text în intrare, apoi apăsând butonul de comutare:

{% raw %}
<div id="no-key-example" class="demo">
  <div>
    <template v-if="loginType === 'username'">
      <label>Numele Utilizatorului</label>
      <input placeholder="Enter your username">
    </template>
    <template v-else>
      <label>Email</label>
      <input placeholder="Enter your email address">
    </template>
  </div>
  <button @click="toggleLoginType">Comutați tipul de conectare</button>
</div>
<script>
new Vue({
  el: '#no-key-example',
  data: {
    loginType: 'username'
  },
  methods: {
    toggleLoginType: function () {
      return this.loginType = this.loginType === 'username' ? 'email' : 'username'
    }
  }
})
</script>
{% endraw %}

Acest lucru nu se recomandă întotdeauna, așa că Vue vă oferă o cale de a spune: "Aceste două elemente sunt complet separate - nu le re-utilizați". Adăugați un atribut `key` cu valori unice:

``` html
<template v-if="loginType === 'username'">
  <label>Numele Utilizatorului</label>
  <input placeholder="Enter your username" key="username-input">
</template>
<template v-else>
  <label>Email</label>
  <input placeholder="Enter your email address" key="email-input">
</template>
```

Acum, aceste intrări vor fi redate de la zero de fiecare dată când comutați. Încearcă și singur:

{% raw %}
<div id="key-example" class="demo">
  <div>
    <template v-if="loginType === 'username'">
      <label>Numele Utilizatorului</label>
      <input placeholder="Enter your username" key="username-input">
    </template>
    <template v-else>
      <label>Email</label>
      <input placeholder="Enter your email address" key="email-input">
    </template>
  </div>
  <button @click="toggleLoginType">Comutați tipul de conectare</button>
</div>
<script>
new Vue({
  el: '#key-example',
  data: {
    loginType: 'username'
  },
  methods: {
    toggleLoginType: function () {
      return this.loginType = this.loginType === 'username' ? 'email' : 'username'
    }
  }
})
</script>
{% endraw %}

Rețineți că elementele `<label>` sunt încă reutilizate eficient, deoarece nu au atribute `key`.

## `v-show`

Another option for conditionally displaying an element is the `v-show` directive. The usage is largely the same:

``` html
<h1 v-show="ok">Hello!</h1>
```

The difference is that an element with `v-show` will always be rendered and remain in the DOM; `v-show` only toggles the `display` CSS property of the element.

<p class="tip">Note that `v-show` doesn't support the `<template>` element, nor does it work with `v-else`.</p>

## `v-if` vs `v-show`

`v-if` is "real" conditional rendering because it ensures that event listeners and child components inside the conditional block are properly destroyed and re-created during toggles.

`v-if` is also **lazy**: if the condition is false on initial render, it will not do anything - the conditional block won't be rendered until the condition becomes true for the first time.

In comparison, `v-show` is much simpler - the element is always rendered regardless of initial condition, with CSS-based toggling.

Generally speaking, `v-if` has higher toggle costs while `v-show` has higher initial render costs. So prefer `v-show` if you need to toggle something very often, and prefer `v-if` if the condition is unlikely to change at runtime.

## `v-if` with `v-for`

When used together with `v-if`, `v-for` has a higher priority than `v-if`. See the <a href="../guide/list.html#V-for-and-v-if">list rendering guide</a> for details.

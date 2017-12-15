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

O altă opțiune pentru afișarea condiționată a unui element este directiva `v-show`. Utilizarea este în mare parte aceeași:

``` html
<h1 v-show="ok">Salut!</h1>
```

Diferența este că un element cu `v-show` va fi întotdeauna randat și va rămâne în DOM; `v-show` comută doar proprietatea CSS `display` a elementului.

<p class="tip">Rețineți că `v-show` nu acceptă elementul `<template>`, nici nu funcționează cu `v-else`.</p>

## `v-if` vs `v-show`

`v-if` este randare condiționată "reală", deoarece asigură că ascultătorii de evenimente și componentele derivate din interiorul blocului condițional sunt distruse și recreate în timpul comutării.

`v-if` este de asemenea **leneș**: dacă condiția este falsă la redarea inițială, nu va face nimic - blocul condiționat nu va fi randat până când condiția nu va deveni reală.

În comparație, `v-show` este cu mult mai simplu - elementul este întotdeauna randat indiferent de starea inițială, cu comutarea pe bază de CSS.

Vorbind la general, `v-if` are costuri mai mari de comutare, în timp ce `v-show` are costuri inițiale mai mari de randare. Așadar, preferați `v-show` dacă trebuie să comutați ceva foarte des, și preferați `v-if` în cazul în care condiția este puțin probabil să se schimbe în timpul de execuție.

## `v-if` cu `v-for`

Când este folosit împreună cu `v-if`, `v-for` are o prioritate mai mare decât `v-if`. Consultați <a href="../guide/list.html#V-for-and-v-if"> ghidul de randare a listei </a> pentru detalii.
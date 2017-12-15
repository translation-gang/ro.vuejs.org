---
title: Mixin-uri
type: guide
order: 301
---

## Principiile Fundamentale 

Mixin-urile reprezintă o modalitate flexibilă de distribuire a funcționalităților reutilizabile pentru componentele Vue. Un obiect mixin poate conține orice opțiuni ale componentei. Când o componentă folosește un mixin, toate opțiunile mixin-ului vor fi "amestecate" cu opțiunile proprii ale componentei.

Exemplu:

``` js
// definim un obiect mixin
var myMixin = {
  created: function () {
    this.hello()
  },
  methods: {
    hello: function () {
      console.log('hello from mixin!')
    }
  }
}

// definim o componentă care folosește mixin-ul dat
var Component = Vue.extend({
  mixins: [myMixin]
})

var component = new Component() // => "Salut din mixin!"
```

## Fuziunea Opțiunilor

Când un mixin și componenta în sine conțin opțiuni ce se suprapun, acestea vor fi "fuzionate" folosind strategii adecvate. De exemplu, funcțiile de tip hook cu același nume sunt îmbinate într-o matrice, astfel încât toate acestea să fie apelate. În plus, hook-urile mixin-ului vor fi apelate **înainte** de hook-urile componentei:

``` js
var mixin = {
  created: function () {
    console.log('mixin hook called')
  }
}

new Vue({
  mixins: [mixin],
  created: function () {
    console.log('component hook called')
  }
})

// => "hook-ul mixinului a fost apelat"
// => "hook-ul componentei a fost apelat"
```

Opțiunile care așteaptă valorile obiectului, de exemplu `methods`, `components` și `directives`, vor fi îmbinate în același obiect. Opțiunile componentei vor avea prioritate când există conflicte între cheile acestor obiecte:

``` js
var mixin = {
  methods: {
    foo: function () {
      console.log('foo')
    },
    conflicting: function () {
      console.log('din mixin')
    }
  }
}

var vm = new Vue({
  mixins: [mixin],
  methods: {
    bar: function () {
      console.log('bar')
    },
    conflicting: function () {
      console.log('din componentă')
    }
  }
})

vm.foo() // => "foo"
vm.bar() // => "bar"
vm.conflicting() // => "din componentă"
```

Rețineți că aceleași strategii de fuziune sunt utilizate în `Vue.extend()`.

## Mixin Global

De asemenea, puteți aplica mixin-ul la nivel global. Utilizați cu prudență! Odată ce aplicați un mixin la nivel global, acesta va afecta **fiecare** instanță Vue creată ulterior. Atunci când este folosit corect, acesta poate fi utilizat pentru a injecta logica de procesare pentru opțiunile personalizate:

``` js
// injectăm un handler pentru opțiunea personalizată `myOption`
Vue.mixin({
  created: function () {
    var myOption = this.$options.myOption
    if (myOption) {
      console.log(myOption)
    }
  }
})

new Vue({
  myOption: 'hello!'
})
// => "hello!"
```

<p class="tip">Utilizați mixin-uri globale rar și atent, deoarece acestea afectează fiecare instanță creată de Vue, inclusiv componentele terțe. În majoritatea cazurilor, ar trebui să le utilizați numai pentru manipularea opțiunilor personalizate, așa cum sa demonstrat în exemplul de mai sus. Este, de asemenea, o idee bună să le înregistrați ca [Plugin-uri](plugins.html) pentru a evita aplicarea duplicată.</p>

## Custom Option Merge Strategies

When custom options are merged, they use the default strategy which overwrites the existing value. If you want a custom option to be merged using custom logic, you need to attach a function to `Vue.config.optionMergeStrategies`:

``` js
Vue.config.optionMergeStrategies.myOption = function (toVal, fromVal) {
  // return mergedVal
}
```

For most object-based options, you can use the same strategy used by `methods`:

``` js
var strategies = Vue.config.optionMergeStrategies
strategies.myOption = strategies.methods
```

A more advanced example can be found on [Vuex](https://github.com/vuejs/vuex)'s 1.x merging strategy:

``` js
const merge = Vue.config.optionMergeStrategies.computed
Vue.config.optionMergeStrategies.vuex = function (toVal, fromVal) {
  if (!toVal) return fromVal
  if (!fromVal) return toVal
  return {
    getters: merge(toVal.getters, fromVal.getters),
    state: merge(toVal.state, fromVal.state),
    actions: merge(toVal.actions, fromVal.actions)
  }
}
```

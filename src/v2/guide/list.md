---
title: Randarea Listei
type: guide
order: 8
---

## Afișarea unui Array de elemente cu `v-for`

Putem folosi directiva `v-for` pentru a crea o listă de elemente bazate pe un array. Direcția `v-for` necesită o sintaxă specială sub forma unui `item in items`, unde `items` este sursa de date a array-ului și `item` este un **alias** pentru elementul din array fiind repetat pe:

``` html
<ul id="example-1">
  <li v-for="item in items">
    {{ item.message }}
  </li>
</ul>
```

``` js
var example1 = new Vue({
  el: '#example-1',
  data: {
    items: [
      { message: 'Foo' },
      { message: 'Bar' }
    ]
  }
})
```

Rezultat:

{% raw %}
<ul id="example-1" class="demo">
  <li v-for="item in items">
    {{item.message}}
  </li>
</ul>
<script>
var example1 = new Vue({
  el: '#example-1',
  data: {
    items: [
      { message: 'Foo' },
      { message: 'Bar' }
    ]
  },
  watch: {
    items: function () {
      smoothScroll.animateScroll(document.querySelector('#example-1'))
    }
  }
})
</script>
{% endraw %}

În interiorul blocurilor `v-for` avem acces deplin la proprietățile domeniului părinte. `v-for` sprijină, de asemenea, un al doilea argument opțional pentru indexul elementului curent.

``` html
<ul id="example-2">
  <li v-for="(item, index) in items">
    {{ parentMessage }} - {{ index }} - {{ item.message }}
  </li>
</ul>
```

``` js
var example2 = new Vue({
  el: '#example-2',
  data: {
    parentMessage: 'Parent',
    items: [
      { message: 'Foo' },
      { message: 'Bar' }
    ]
  }
})
```

Rezultat:

{% raw%}
<ul id="example-2" class="demo">
  <li v-for="(item, index) in items">
    {{ parentMessage }} - {{ index }} - {{ item.message }}
  </li>
</ul>
<script>
var example2 = new Vue({
  el: '#example-2',
  data: {
    parentMessage: 'Parent',
    items: [
      { message: 'Foo' },
      { message: 'Bar' }
    ]
  },
  watch: {
    items: function () {
      smoothScroll.animateScroll(document.querySelector('#example-2'))
    }
  }
})
</script>
{% endraw %}

De asemenea, puteți folosi `of` ca delimitator în loc de `in`, astfel încât să fie mai aproape de sintaxa JavaScript pentru iteratori:

``` html
<div v-for="item of items"></div>
```

## `v-for` cu un Obiect

De asemenea, puteți folosi `v-for` pentru a itera prin proprietățile unui obiect.

``` html
<ul id="v-for-object" class="demo">
  <li v-for="value in object">
    {{ value }}
  </li>
</ul>
```

``` js
new Vue({
  el: '#v-for-object',
  data: {
    object: {
      firstName: 'John',
      lastName: 'Doe',
      age: 30
    }
  }
})
```

Rezultat:

{% raw %}
<ul id="v-for-object" class="demo">
  <li v-for="value in object">
    {{ value }}
  </li>
</ul>
<script>
new Vue({
  el: '#v-for-object',
  data: {
    object: {
      firstName: 'John',
      lastName: 'Doe',
      age: 30
    }
  }
})
</script>
{% endraw %}

De asemenea, puteți oferi un al doilea argument pentru primirea unei chei:

``` html
<div v-for="(value, key) in object">
  {{ key }}: {{ value }}
</div>
```

{% raw %}
<div id="v-for-object-value-key" class="demo">
  <div v-for="(value, key) in object">
    {{ key }}: {{ value }}
  </div>
</div>
<script>
new Vue({
  el: '#v-for-object-value-key',
  data: {
    object: {
      firstName: 'John',
      lastName: 'Doe',
      age: 30
    }
  }
})
</script>
{% endraw %}

Și al treilea pentru index:

``` html
<div v-for="(value, key, index) in object">
  {{ index }}. {{ key }}: {{ value }}
</div>
```

{% raw %}
<div id="v-for-object-value-key-index" class="demo">
  <div v-for="(value, key, index) in object">
    {{ index }}. {{ key }}: {{ value }}
  </div>
</div>
<script>
new Vue({
  el: '#v-for-object-value-key-index',
  data: {
    object: {
      firstName: 'John',
      lastName: 'Doe',
      age: 30
    }
  }
})
</script>
{% endraw %}

<p class="tip">Atunci când iterăm peste un obiect, ordinea se bazează pe ordinul de enumerare a cheiei în `Object.keys ()`, care **nu** este garantat a fi consecvent în implementările motorului JavaScript.</p>

## `key`

Când Vue actualizează o listă de elemente redate cu `v-for`, implicit folosește o strategie "in-place patch". Dacă ordinea elementelor de date s-a schimbat, în loc să mutați elementele DOM pentru a se potrivi cu ordinea articolelor, Vue va face patch pentru fiecare element în loc și va asigura că reflectă ceea ce ar trebui să fie redat la indexul respectiv. Acest lucru este similar cu comportamentul `track-by="$index"` în Vue 1.x.

Acest mod implicit este eficient, dar numai potrivit **când lista de rendare nu se bazează pe starea componentă a elementului derivat sau pe starea DOM temporară (de exemplu, forma de intrare pentru valori)**.

Pentru a da Vue-ului un indiciu, astfel încât să poată urmări identitatea fiecărui nod, reutilizând și reordonând elementele existente, trebuie să oferiți un atribut unic `key` pentru fiecare element. O valoare ideală pentru `key` ar fi id-ul unic al fiecărui element. Acest atribut special este strict echivalent cu `track-by` în 1.x, dar funcționează ca un atribut, deci trebuie să folosiți `v-bind` pentru a le lega de valorile dinamice (folosind shorthand aici):

``` html
<div v-for="item in items" :key="item.id">
  <!-- content -->
</div>
```

Se recomandă să furnizați (o cheie)`key` cu `v-for` ori de câte ori este posibil, cu excepția cazului în care conținutul DOM-ului iterabil este simplu sau dacă vă bazați în mod intenționat pe comportamentul implicit pentru câștigurile de performanță.

Deoarece este un mecanism generic pentru Vue de a identifica nodurile, `key` are și alte utilizări care nu sunt legate în mod specific cu `v-for`, așa cum vom vedea mai târziu în ghid.

## Array-ul Schimbă Detectarea

### Metode de Mutație

Vue împachetează o metodă de mutație observată a array-ului, astfel încât aceasta va declanșa actualizări de vizualizare. Metodele împachetate sunt:

- `push()`
- `pop()`
- `shift()`
- `unshift()`
- `splice()`
- `sort()`
- `reverse()`

Puteți deschide consola și puteți juca cu array-ului exemplelor anterioare, prin apelarea metodelor de mutație. De exemplu: `example1.items.push({ message: 'Baz' })`.

### Înlocuirea unui Array

Metodele de mutație, după cum sugerează și numele, mută array-ul original ]n cel care este chemat. În comparație, există, de asemenea, metode non-mutație, de ex. `filter()`, `concat()` și `slice()`, care nu modifică array-ul original, dar **întoarce întotdeauna un șir nou**. Când lucrați cu metode non-mutație, puteți înlocui array-ul vechi cu cel nou:

``` js
example1.items = example1.items.filter(function (item) {
  return item.message.match(/Foo/)
})
```

S-ar putea crede că acest lucru va face ca Vue să arunce DOM-ul existent și să redea întreaga listă - din fericire, nu este cazul. Vue implementează unele euristici inteligente pentru a maximiliza reutilizarea elementului DOM, înlocuind astfel un array cu un alt array care conține obiecte suprapuse, este o operație foarte eficientă.

### Avertismente

Datorită limitărilor din JavaScript, Vue **nu poate** detecta următoarele modificări la un arrray:

1. Când setați direct un element cu indexul, de ex. `vm.items[indexOfItem] = newValue`
2. Când modificați lungimea array-ului, de ex. `vm.items.length = newLength`

Pentru a depăși avertismentul 1, ambele dintre ele vor realiza aceleași lucruri ca și `vm.items[indexOfItem] = newValue`, dar vor declanșa actualizări de stare în sistemul de reactivitate:

``` js
// Vue.set
Vue.set(example1.items, indexOfItem, newValue)
```
``` js
// Array.prototype.splice
example1.items.splice(indexOfItem, 1, newValue)
```

Pentru a face față avertismentului 2, puteți folosi `splice`:

``` js
example1.items.splice(newLength)
```

## Avertismente de Schimbare a Obiectelor

Din nou datorită limitelor JavaScript-ului modern, **Vue nu poate detecta adăugarea sau ștergerea de proprietăți**. De exemplu:

``` js
var vm = new Vue({
  data: {
    a: 1
  }
})
// `vm.a` este acum reactiv

vm.b = 2
// `vm.b` nu este reactiv
```

Vue nu permite adăugarea dinamică a unor noi proprietăți reactive la nivel de rădăcină, la o instanță deja creată. Cu toate acestea, este posibil să adăugați proprietăți reactive unui obiect imbricat utilizând metoda `Vue.set(object, key, value)`. De exemplu, fiind:

``` js
var vm = new Vue({
  data: {
    userProfile: {
      name: 'Anika'
    }
  }
})
```

Puteți să adăugați o nouă proprietate `age` la obiectul imbricat `userProfile` cu:

``` js
Vue.set(vm.userProfile, 'age', 27)
```

De asemenea, puteți utiliza metoda instanței `vm.$set`, care este un alias pentru `Vue.set` global:

``` js
this.$set(this.userProfile, 'age', 27)
```

Uneori este posibil să doriți să atribuiți un număr de proprietăți noi unui obiect existent, de exemplu folosind `Object.assign()` sau `_.extend()`. În astfel de cazuri, ar trebui să creați un obiect proaspăt cu proprietăți din ambele obiecte. Deci, în loc de:

``` js
Object.assign(this.userProfile, {
  age: 27,
  favoriteColor: 'Vue Green'
})
```

Puteți adăuga proprietăți reactive noi cu:

``` js
this.userProfile = Object.assign({}, this.userProfile, {
  age: 27,
  favoriteColor: 'Vue Green'
})
```

## Afișarea Rezultatelor Filtrate/Sortate

Uneori, dorim să afișăm o versiune filtrată sau sortată a unui array, fără a muta sau a reseta datele originale. În acest caz, puteți crea o proprietate computed care returnează array-ul filtrat sau sortat.

De exemplu:

``` html
<li v-for="n in evenNumbers">{{ n }}</li>
```

``` js
data: {
  numbers: [ 1, 2, 3, 4, 5 ]
},
computed: {
  evenNumbers: function () {
    return this.numbers.filter(function (number) {
      return number % 2 === 0
    })
  }
}
```

În situațiile în care proprietățile computed nu sunt fezabile (de exemplu, în interiorul ciclurilor imbricate `v-for`), puteți utiliza o metodă:

``` html
<li v-for="n in even(numbers)">{{ n }}</li>
```

``` js
data: {
  numbers: [ 1, 2, 3, 4, 5 ]
},
methods: {
  even: function (numbers) {
    return numbers.filter(function (number) {
      return number % 2 === 0
    })
  }
}
```

## `v-for` cu un Interval

`v-for` poate lua de asemenea un număr întreg. În acest caz, se va repeta șablonul de mai multe ori.

``` html
<div>
  <span v-for="n in 10">{{ n }} </span>
</div>
```

Rezultat:

{% raw %}
<div id="range" class="demo">
  <span v-for="n in 10">{{ n }} </span>
</div>
<script>
  new Vue({ el: '#range' })
</script>
{% endraw %}

## `v-for` pe un `<template>`

Similar cu șablonul `v-if`, puteți folosi și o etichetă `<template>` cu `v-for` pentru a reda un bloc de elemente multiple. De exemplu:

``` html
<ul>
  <template v-for="item in items">
    <li>{{ item.msg }}</li>
    <li class="divider"></li>
  </template>
</ul>
```

## `v-for` cu `v-if`

Când există pe același nod, `v-for` are o prioritate mai mare decât `v-if`. Asta inseamna ca `v-if` va fi rulat pe fiecare iteratie a ciclului separat. Acest lucru poate fi util atunci când doriți să redați noduri doar pentru _unele_ elemente, cum ar fi:

``` html
<li v-for="todo in todos" v-if="!todo.isComplete">
  {{ todo }}
</li>
```

Cele de mai sus fac doar todos care nu sunt complete.

Dacă în schimb, intenția dvs. este să ignorați condiția execuției a ciclului, puteți plasa elementul `v-if` pe un element de înfășurare (sau [`<template>`](conditional.html#Conditional-Groups-with-v-if-on-lt-template-gt)). De exemplu:

``` html
<ul v-if="todos.length">
  <li v-for="todo in todos">
    {{ todo }}
  </li>
</ul>
<p v-else>Nu au mai rămas todos!</p>
```

## `v-for` cu o Componentă

> Această secțiune presupune cunoașterea [Componentelor](components.html). Simțiți-vă liber să săriți și să reveniți mai târziu.

Puteți folosi direct `v-for` pe o componentă personalizată, ca orice element normal:

``` html
<my-component v-for="item in items" :key="item.id"></my-component>
```

> În versiunea 2.2.0+, atunci când se folosește `v-for` cu o componentă, este acum necesară o cheie[`key`](list.html#key).

Cu toate acestea, acest lucru nu va transfera automat niciun fel de date componentei, deoarece componentele au domenii izolate. Pentru a transfera datele iterate pe o componentă, trebuie să utilizați în mod explicit parametrii de intrare:

``` html
<my-component
  v-for="(item, index) in items"
  v-bind:item="item"
  v-bind:index="index"
  v-bind:key="item.id"
></my-component>
```

Motivul pentru care `item`-ul nu este transmis automat componentei se datorează faptului că ar face ca componenta să fie codificată cu logica operației `v-for`. Și dacă sursa de date este specificată explicit, componenta poate fi reutilizată în alte situații.

Aici este un exemplu complet a unei liste simple de tip todo:

``` html
<div id="todo-list-example">
  <input
    v-model="newTodoText"
    v-on:keyup.enter="addNewTodo"
    placeholder="Adaugă un todo"
  >
  <ul>
    <li
      is="todo-item"
      v-for="(todo, index) in todos"
      v-bind:key="todo.id"
      v-bind:title="todo.title"
      v-on:remove="todos.splice(index, 1)"
    ></li>
  </ul>
</div>
```

<p class="tip">Rețineți atributul `is="todo-item"`. Acesta este necesar în șabloanele DOM-ului, deoarece numai un element `<li>` este valabil în interiorul unui `<ul>`. El face același lucru cu `<todo-item>`, dar funcționează în jurul unei erori potențiale de parsing a browserului. Consultați [Caracteristicile Parsingului  Șabloanelor ale DOM-ului](components.html#DOM-Template-Parsing-Caveats) pentru a afla mai multe.</p>

``` js
Vue.component('todo-item', {
  template: '\
    <li>\
      {{ title }}\
      <button v-on:click="$emit(\'remove\')">X</button>\
    </li>\
  ',
  props: ['title']
})

new Vue({
  el: '#todo-list-example',
  data: {
    newTodoText: '',
    todos: [
      {
        id: 1,
        title: 'De spălat vesela',
      },
      {
        id: 2,
        title: 'De aruncat gunoiul',
      },
      {
        id: 3,
        title: 'De tăiat gazonul'
      }
    ],
    nextTodoId: 4
  },
  methods: {
    addNewTodo: function () {
      this.todos.push({
        id: this.nextTodoId++,
        title: this.newTodoText
      })
      this.newTodoText = ''
    }
  }
})
```

{% raw %}
<div id="todo-list-example" class="demo">
  <input
    v-model="newTodoText"
    v-on:keyup.enter="addNewTodo"
    placeholder="Adaugă un todo"
  >
  <ul>
    <li
      is="todo-item"
      v-for="(todo, index) in todos"
      v-bind:key="todo.id"
      v-bind:title="todo.title"
      v-on:remove="todos.splice(index, 1)"
    ></li>
  </ul>
</div>
<script>
Vue.component('todo-item', {
  template: '\
    <li>\
      {{ title }}\
      <button v-on:click="$emit(\'remove\')">X</button>\
    </li>\
  ',
  props: ['title']
})

new Vue({
  el: '#todo-list-example',
  data: {
    newTodoText: '',
    todos: [
      {
        id: 1,
        title: 'De spălat vesela',
      },
      {
        id: 2,
        title: 'De aruncat gunoiul',
      },
      {
        id: 3,
        title: 'De tăiat gazonul'
      }
    ],
    nextTodoId: 4
  },
  methods: {
    addNewTodo: function () {
      this.todos.push({
        id: this.nextTodoId++,
        title: this.newTodoText
      })
      this.newTodoText = ''
    }
  }
})
</script>
{% endraw %}

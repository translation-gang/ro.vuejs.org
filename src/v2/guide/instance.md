---
title: Instanța Vue
type: guide
order: 3
---

## Crearea unei instanțe Vue

Fiecare aplicație Vue începe prin crearea unei noi **instanțe Vue** cu funcția `Vue`:

``` js
var vm = new Vue({
  // opțiuni
})
```

Deși nu este strict asociat cu [pattern-ul MVVM](https://en.wikipedia.org/wiki/Model_View_ViewModel), designul Vue a fost inspirat parțial de acesta. Ca convenție, adesea folosim variabila `vm` (scurtă pentru ViewModel) pentru referire la instanța noastră Vue.

Când creați o instanță Vue, treceți într-un **obiect de opțiuni**. Majoritatea acestui ghid descrie modul în care puteți utiliza aceste opțiuni pentru a crea comportamentul dorit. Pentru referință, puteți de asemenea să răsfoiți lista completă a opțiunilor din [referința API](../api/#Options-Data).

O aplicație Vue constă dintr-o **instanță root Vue** creată cu `new Vue`, opțional organizată într-un arbore de componente reutilizabile. De exemplu, arborele de componente al aplicației todo ar putea arăta astfel:

```
Root Instance
|- TodoList
   |- TodoItem
      |- DeleteTodoButton
      |- EditTodoButton
   |- TodoListFooter
      |- ClearTodosButton
      |- TodoListStatistics
```

Vom vorbi despre [componenta sistemului](components.html) detaliat mai târziu. Pentru moment, trebuie să știți doar că toate componentele Vue sunt și instanțe Vue, acceptând astfel același obiect de opțiuni (cu excepția câtorva opțiuni root-specifice).

## Date și metode

Atunci când este creată o instanță Vue, ea adaugă toate proprietățile găsite în obiectul `data` la **sistemul de reactivitate** Vue. Atunci când valorile acelor proprietăți se vor schimba, vizualizarea va "reacționa", actualizându-se pentru a se potrivi cu noile valori.

``` js
// Obiectul nostru de date
var data = { a: 1 }

// Obiectul este adăugat într-o instanța Vue
var vm = new Vue({
  data: data
})

// Acestea fac referire la același obiect!
vm.a === data.a // => true

// Setarea proprietății pe instanță
// afectează de asemenea datele originale
vm.a = 2
data.a // => 2

// ... și invers
data.a = 3
vm.a // => 3
```
Când aceste date se modifică, vizualizarea va face re-render. Trebuie notat faptul că proprietățile din `data` sunt numai **reactive**, dacă au existat atunci când instanța a fost creată. Aceasta înseamnă că dacă adăugați o proprietate nouă, cum ar fi:

``` js
vm.b = 'hi'
```
Apoi modificările la `b` nu vor declanșa nici o actualizare a vizualizărilor. Dacă știți că veți avea nevoie de o proprietate mai târziu, dar va începe fiind goală sau inexistentă, va trebui să setați o valoare inițială. De exemplu:

``` js
data: {
  newTodoText: '',
  visitCount: 0,
  hideCompletedTodos: false,
  todos: [],
  error: null
}
```

În plus față de proprietățile de date, instanțele Vue expun o serie de proprietăți și metode de instanțe utile. Acestea sunt prefixate cu `$` pentru a le diferenția de proprietățile definite de utilizator. De exemplu:

``` js
var data = { a: 1 }
var vm = new Vue({
  el: '#example',
  data: data
})

vm.$data === data // => true
vm.$el === document.getElementById('example') // => true

// $ceasul este o metodă instanță
vm.$watch('a', function (newValue, oldValue) {
  // Acest callback va fi apelat când `vm.a` se va modifica
})
```

În viitor, puteți consulta [referința API](../api/#Instance-Properties) pentru o listă completă de proprietăți și metode ale instanței.

## Instance Lifecycle Hooks

Each Vue instance goes through a series of initialization steps when it's created - for example, it needs to set up data observation, compile the template, mount the instance to the DOM, and update the DOM when data changes. Along the way, it also runs functions called **lifecycle hooks**, giving users the opportunity to add their own code at specific stages.

For example, the [`created`](../api/#created) hook can be used to run code after an instance is created:

``` js
new Vue({
  data: {
    a: 1
  },
  created: function () {
    // `this` points to the vm instance
    console.log('a is: ' + this.a)
  }
})
// => "a is: 1"
```

There are also other hooks which will be called at different stages of the instance's lifecycle, such as [`mounted`](../api/#mounted), [`updated`](../api/#updated), and [`destroyed`](../api/#destroyed). All lifecycle hooks are called with their `this` context pointing to the Vue instance invoking it.

<p class="tip">Don't use [arrow functions](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Functions/Arrow_functions) on an options property or callback, such as `created: () => console.log(this.a)` or `vm.$watch('a', newValue => this.myMethod())`. Since arrow functions are bound to the parent context, `this` will not be the Vue instance as you'd expect, often resulting in errors such as `Uncaught TypeError: Cannot read property of undefined` or `Uncaught TypeError: this.myMethod is not a function`.</p>


## Lifecycle Diagram

Below is a diagram for the instance lifecycle. You don't need to fully understand everything going on right now, but as you learn and build more, it will be a useful reference.

![The Vue Instance Lifecycle](/images/lifecycle.png)

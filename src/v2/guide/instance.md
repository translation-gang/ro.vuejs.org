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

## Data and Methods

When a Vue instance is created, it adds all the properties found in its `data` object to Vue's **reactivity system**. When the values of those properties change, the view will "react", updating to match the new values.

``` js
// Our data object
var data = { a: 1 }

// The object is added to a Vue instance
var vm = new Vue({
  data: data
})

// These reference the same object!
vm.a === data.a // => true

// Setting the property on the instance
// also affects the original data
vm.a = 2
data.a // => 2

// ... and vice-versa
data.a = 3
vm.a // => 3
```

When this data changes, the view will re-render. It should be noted that properties in `data` are only **reactive** if they existed when the instance was created. That means if you add a new property, like:

``` js
vm.b = 'hi'
```

Then changes to `b` will not trigger any view updates. If you know you'll need a property later, but it starts out empty or non-existent, you'll need to set some initial value. For example:

``` js
data: {
  newTodoText: '',
  visitCount: 0,
  hideCompletedTodos: false,
  todos: [],
  error: null
}
```

In addition to data properties, Vue instances expose a number of useful instance properties and methods. These are prefixed with `$` to differentiate them from user-defined properties. For example:

``` js
var data = { a: 1 }
var vm = new Vue({
  el: '#example',
  data: data
})

vm.$data === data // => true
vm.$el === document.getElementById('example') // => true

// $watch is an instance method
vm.$watch('a', function (newValue, oldValue) {
  // This callback will be called when `vm.a` changes
})
```

In the future, you can consult the [API reference](../api/#Instance-Properties) for a full list of instance properties and methods.

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

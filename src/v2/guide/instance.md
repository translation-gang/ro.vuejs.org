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

## Hook-urile ciclului de viață al instanței

Fiecare instanță Vue trece printr-o serie de pași de inițializare atunci când este creată - de exemplu, trebuie să configureze observația datelor, să compileze șablonul, să monteze instanța în DOM și să actualizeze DOM-ul atunci când se schimbă datele. În același timp, rulează și funcții numite **hook-uri ale ciclului de viață**, oferind utilizatorilor posibilitatea de a adăuga propriul cod la anumite etape.

De exemplu, hook-ul [`created`](../api/#created) poate fi folosit pentru a rula codul după crearea unei instanțe: 

``` js
new Vue({
  data: {
    a: 1
  },
  created: function () {
    // `this` indică instanța vm
    console.log('a is: ' + this.a)
  }
})
// => "a este: 1"
```

Există, de asemenea, alte hook-uri care vor fi chemate în diferite etape ale ciclului de viață al instanței, cum ar fi [`mounted`](../api/#mounted), [`updated`](../api/#updated) și [`destroyed`](../api/#destroyed). Toate hook-urile ale ciclurilor de viață sunt chemate cu contextul `this` care indică instanța Vue care o invocă.


<p class="tip">Nu utilizați [funcțiile arrow](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Functions/Arrow_functions) pe proprietatea de opțiuni sau pe callback, cum ar fi `created: () => console.log(this.a)` sau `vm.$watch('a', newValue => this.myMethod())`. Deoarece funcțiile arrow sunt legate de contextul părinte, `this` nu va fi instanța Vue așa cum v-ați așteptat, adesea rezultând erori precum `Uncaught TypeError: Cannot read property of undefined` sau `Uncaught TypeError: this.myMethod is not a function`.</p>

## Lifecycle Diagram

Below is a diagram for the instance lifecycle. You don't need to fully understand everything going on right now, but as you learn and build more, it will be a useful reference.

![The Vue Instance Lifecycle](/images/lifecycle.png)

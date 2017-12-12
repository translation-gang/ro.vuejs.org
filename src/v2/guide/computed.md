---
title: Proprietățile Computed și Observatorii
type: guide
order: 5
---

## Proprietățile Computed 

Expresiile în șablon sunt foarte convenabile, dar sunt destinate operațiilor simple. Punerea prea multă logică în șabloanele dvs. le poate face umflate și greu de întreținut. De exemplu:

``` html
<div id="example">
  {{ message.split('').reverse().join('') }}
</div>
```

În acest moment, șablonul nu mai este simplu și declarativ. Trebuie să te uiți la acesta o secundă înainte de a realiza că afișează mesajul în sens invers. Problema este agravată atunci când doriți să includeți mesajul inversat în șablonul dvs. de mai multe ori.

De aceea, pentru orice logică complexă, ar trebui să utilizați o **proprietate computed**.

### Exemplul de Bază

``` html
<div id="example">
  <p>Mesajul Original: "{{ message }}"</p>
  <p>Mesajul Inversat: "{{ reversedMessage }}"</p>
</div>
```

``` js
var vm = new Vue({
  el: '#example',
  data: {
    message: 'Salut'
  },
  computed: {
    // un computed getter
    reversedMessage: function () {
      // `this` indică instanța vm
      return this.message.split('').reverse().join('')
    }
  }
})
```

Resultat:

{% raw %}
<div id="example" class="demo">
  <p>Mesajul Original: "{{ message }}"</p>
  <p>Mesajul Inversat: "{{ reversedMessage }}"</p>
</div>
<script>
var vm = new Vue({
  el: '#example',
  data: {
    message: 'Salut'
  },
  computed: {
    reversedMessage: function () {
      return this.message.split('').reverse().join('')
    }
  }
})
</script>
{% endraw %}

Aici am declarat o proprietate computed `reversedMessage`. Funcția pe care am furnizat-o va fi folosită ca funcția getter pentru proprietatea `vm.reversedMessage`:

``` js
console.log(vm.reversedMessage) // => 'tulaS'
vm.message = 'Goodbye'
console.log(vm.reversedMessage) // => 'ap aP'
```

Puteți deschide consola și puteți juca singur cu exemplul vm. Valoarea `vm.reversedMessage` este întotdeauna dependentă de valoarea `vm.message`.

Puteți lega datele de proprietățile computed în șabloane, la fel ca o proprietate normală. Vue este conștient de faptul că `vm.reversedMessage` depinde de `vm.message`, deci va actualiza orice legare care depinde de `vm.reversedMessage` când se va modifica `vm.message`. Cea mai bună parte este faptul că am creat relația de dependență declarativă: funcția getter computed nu are efecte secundare, ceea ce face mai ușoară testarea și înțelegerea.

### Caching-ul Computed vs Metode

Se poate observa că putem obține același rezultat prin invocarea unei metode în expresie:

``` html
<p>Mesaj Inversat: "{{ reverseMessage() }}"</p>
```

``` js
// în component
methods: {
  reverseMessage: function () {
    return this.message.split('').reverse().join('')
  }
}
```

În loc de o proprietate computed, putem defini aceeași funcție ca metodă. Pentru rezultatul final, cele două abordări sunt exact aceleași. Cu toate acestea, diferența este că **proprietățile computed sunt stocate în cache-ul bazat pe dependențele lor.** O proprietate computed va reevalua numai când unele dintre dependențele sale s-au schimbat. Aceasta înseamnă că atâta timp cât mesajul nu s-a schimbat, accesul multiplu la proprietatea computed `reversedMessage` va readuce imediat rezultatul calculat anterior fără a mai fi nevoie să executați din nou funcția.

Aceasta înseamnă, de asemenea, că următoarea proprietate computed nu se va actualiza niciodată, deoarece `Date.now()` nu este o dependență reactivă:

``` js
computed: {
  now: function () {
    return Date.now()
  }
}
```

În comparație, o invocare a metodei va executa **întotdeauna** funcția ori de câte ori se face o redare.

De ce avem nevoie de caching? Imaginați-vă că avem o proprietate computed scumpă **A**, care necesită ciclu printr-un tablou array uriaș și face o mulțime de calcule. Apoi, putem avea alte proprietăți calculate care la rândul lor depind de **A**. Fără caching, am fi executat **A** de mai multe ori decât este necesar! În cazurile în care nu doriți caching, utilizați o metodă în schimb.

### Computed vs Watched Property

Vue does provide a more generic way to observe and react to data changes on a Vue instance: **watch properties**. When you have some data that needs to change based on some other data, it is tempting to overuse `watch` - especially if you are coming from an AngularJS background. However, it is often a better idea to use a computed property rather than an imperative `watch` callback. Consider this example:

``` html
<div id="demo">{{ fullName }}</div>
```

``` js
var vm = new Vue({
  el: '#demo',
  data: {
    firstName: 'Foo',
    lastName: 'Bar',
    fullName: 'Foo Bar'
  },
  watch: {
    firstName: function (val) {
      this.fullName = val + ' ' + this.lastName
    },
    lastName: function (val) {
      this.fullName = this.firstName + ' ' + val
    }
  }
})
```

The above code is imperative and repetitive. Compare it with a computed property version:

``` js
var vm = new Vue({
  el: '#demo',
  data: {
    firstName: 'Foo',
    lastName: 'Bar'
  },
  computed: {
    fullName: function () {
      return this.firstName + ' ' + this.lastName
    }
  }
})
```

Much better, isn't it?

### Computed Setter

Computed properties are by default getter-only, but you can also provide a setter when you need it:

``` js
// ...
computed: {
  fullName: {
    // getter
    get: function () {
      return this.firstName + ' ' + this.lastName
    },
    // setter
    set: function (newValue) {
      var names = newValue.split(' ')
      this.firstName = names[0]
      this.lastName = names[names.length - 1]
    }
  }
}
// ...
```

Now when you run `vm.fullName = 'John Doe'`, the setter will be invoked and `vm.firstName` and `vm.lastName` will be updated accordingly.

## Watchers

While computed properties are more appropriate in most cases, there are times when a custom watcher is necessary. That's why Vue provides a more generic way to react to data changes through the `watch` option. This is most useful when you want to perform asynchronous or expensive operations in response to changing data.

For example:

``` html
<div id="watch-example">
  <p>
    Ask a yes/no question:
    <input v-model="question">
  </p>
  <p>{{ answer }}</p>
</div>
```

``` html
<!-- Since there is already a rich ecosystem of ajax libraries    -->
<!-- and collections of general-purpose utility methods, Vue core -->
<!-- is able to remain small by not reinventing them. This also   -->
<!-- gives you the freedom to use what you're familiar with. -->
<script src="https://cdn.jsdelivr.net/npm/axios@0.12.0/dist/axios.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/lodash@4.13.1/lodash.min.js"></script>
<script>
var watchExampleVM = new Vue({
  el: '#watch-example',
  data: {
    question: '',
    answer: 'I cannot give you an answer until you ask a question!'
  },
  watch: {
    // whenever question changes, this function will run
    question: function (newQuestion) {
      this.answer = 'Waiting for you to stop typing...'
      this.getAnswer()
    }
  },
  methods: {
    // _.debounce is a function provided by lodash to limit how
    // often a particularly expensive operation can be run.
    // In this case, we want to limit how often we access
    // yesno.wtf/api, waiting until the user has completely
    // finished typing before making the ajax request. To learn
    // more about the _.debounce function (and its cousin
    // _.throttle), visit: https://lodash.com/docs#debounce
    getAnswer: _.debounce(
      function () {
        if (this.question.indexOf('?') === -1) {
          this.answer = 'Questions usually contain a question mark. ;-)'
          return
        }
        this.answer = 'Thinking...'
        var vm = this
        axios.get('https://yesno.wtf/api')
          .then(function (response) {
            vm.answer = _.capitalize(response.data.answer)
          })
          .catch(function (error) {
            vm.answer = 'Error! Could not reach the API. ' + error
          })
      },
      // This is the number of milliseconds we wait for the
      // user to stop typing.
      500
    )
  }
})
</script>
```

Result:

{% raw %}
<div id="watch-example" class="demo">
  <p>
    Ask a yes/no question:
    <input v-model="question">
  </p>
  <p>{{ answer }}</p>
</div>
<script src="https://cdn.jsdelivr.net/npm/axios@0.12.0/dist/axios.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/lodash@4.13.1/lodash.min.js"></script>
<script>
var watchExampleVM = new Vue({
  el: '#watch-example',
  data: {
    question: '',
    answer: 'I cannot give you an answer until you ask a question!'
  },
  watch: {
    question: function (newQuestion) {
      this.answer = 'Waiting for you to stop typing...'
      this.getAnswer()
    }
  },
  methods: {
    getAnswer: _.debounce(
      function () {
        var vm = this
        if (this.question.indexOf('?') === -1) {
          vm.answer = 'Questions usually contain a question mark. ;-)'
          return
        }
        vm.answer = 'Thinking...'
        axios.get('https://yesno.wtf/api')
          .then(function (response) {
            vm.answer = _.capitalize(response.data.answer)
          })
          .catch(function (error) {
            vm.answer = 'Error! Could not reach the API. ' + error
          })
      },
      500
    )
  }
})
</script>
{% endraw %}

In this case, using the `watch` option allows us to perform an asynchronous operation (accessing an API), limit how often we perform that operation, and set intermediary states until we get a final answer. None of that would be possible with a computed property.

In addition to the `watch` option, you can also use the imperative [vm.$watch API](../api/#vm-watch).

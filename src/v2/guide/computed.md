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

Rezultat:

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

### Computed vs Proprietatea de Observare

Vue oferă o modalitate mai generică de a observa și de a reacționa la schimbările de date de pe o instanță Vue: **proprietățile de observare**. Când aveți date care trebuie să se schimbe pe baza altor date, este tentant să utilizați `watch` - mai ales dacă veniți dintr-un fundal AngularJS. Cu toate acestea, este adesea o idee mai bună de a utiliza o proprietate computed, mai degrabă decât un apel imperativ `watch`. Luați în considerație acest exemplu:

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

Codul de mai sus este imperativ și repetat. Comparați-l cu o versiune de proprietate computed:

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

Mult mai bine, nu-i așa?

### Computed Setter

Proprietățile computed sunt, în mod implicit, numai getter-ul, dar puteți oferi și un setter atunci când aveți nevoie de el:

``` js
// ...
computed: {
  fullName: {
    // getter-ul
    get: function () {
      return this.firstName + ' ' + this.lastName
    },
    // setter-ul
    set: function (newValue) {
      var names = newValue.split(' ')
      this.firstName = names[0]
      this.lastName = names[names.length - 1]
    }
  }
}
// ...
```

Acum, când executați `vm.fullName = 'John Doe'`, setter-ul va fi invocat și `vm.firstName` și `vm.lastName` vor fi actualizate corespunzător.

## Observatorii

Deși proprietățile computed sunt mai potrivite în majoritatea cazurilor, există momente când este necesar un observator personalizat. De aceea, Vue oferă o modalitate mai generică de a reacționa la schimbările de date prin intermediul opțiunii `watch`. Acest lucru este foarte util atunci când doriți să efectuați operații asincrone sau costisitoare ca răspuns la modificarea datelor.

De exemplu:

``` html
<div id="watch-example">
  <p>
    Scriți o întrebare de tip da/nu:
    <input v-model="question">
  </p>
  <p>{{ answer }}</p>
</div>
```

``` html
<!-- Deoarece există deja un ecosistem bogat de biblioteci ajax    -->
<!-- și colecții de metode de utilitate generală, nucleul Vue este -->
<!-- capabil să rămână mic, prin faptul că nu le reinventează. Acesta -->
<!-- de asemenea vă oferă libertatea de a folosi ceea ce cunoașteți. -->
<script src="https://cdn.jsdelivr.net/npm/axios@0.12.0/dist/axios.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/lodash@4.13.1/lodash.min.js"></script>
<script>
var watchExampleVM = new Vue({
  el: '#watch-example',
  data: {
    question: '',
    answer: 'Nu vă pot da un răspuns până când nu puneți o întrebare!'
  },
  watch: {
    // ori de câte ori se schimbă întrebarea, această funcție va funcționa
    question: function (newQuestion) {
      this.answer = 'Vă aștept să încetați să scrieți...'
      this.getAnswer()
    }
  },
  methods: {
    // _.debounce este o funcție oferită de lodash pentru a limita cum
     // deseori se poate executa o operație deosebit de costisitoare.
     // În acest caz, dorim să limităm cât de des accesăm
     // yesno.wtf/api, așteptând până când utilizatorul a terminat complet
     //  să tasteze, înainte de a efectua solicitarea ajax. Pentru a invata
     // mai multe despre funcția _.debounce (și vărul ei
     // _.throttle), vizitați: https://lodash.com/docs#debounce
    getAnswer: _.debounce(
      function () {
        if (this.question.indexOf('?') === -1) {
          this.answer = 'Întrebările, de obicei, conțin un semn de întrebare. ;-)'
          return
        }
        this.answer = 'Gândire...'
        var vm = this
        axios.get('https://yesno.wtf/api')
          .then(function (response) {
            vm.answer = _.capitalize(response.data.answer)
          })
          .catch(function (error) {
            vm.answer = 'Eroare! Nu s-a putut ajunge la API. ' + error
          })
      },
      // Aceste este numărul de milisecunde în care noi 
      // așteptăm ca utilizatorul să înceteze să scrie
      500
    )
  }
})
</script>
```

Rezultat:

{% raw %}
<div id="watch-example" class="demo">
  <p>
    Scriți o întrebare de tip da/nu:
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
    answer: 'Nu vă pot da un răspuns până când nu puneți o întrebare!'
  },
  watch: {
    question: function (newQuestion) {
      this.answer = 'Vă aștept să încetați să scrieți...'
      this.getAnswer()
    }
  },
  methods: {
    getAnswer: _.debounce(
      function () {
        var vm = this
        if (this.question.indexOf('?') === -1) {
          vm.answer = 'Întrebările, de obicei, conțin un semn de întrebare. ;-)'
          return
        }
        vm.answer = 'Thinking...'
        axios.get('https://yesno.wtf/api')
          .then(function (response) {
            vm.answer = _.capitalize(response.data.answer)
          })
          .catch(function (error) {
            vm.answer = 'Eroare! Nu s-a putut ajunge la API. ' + error
          })
      },
      500
    )
  }
})
</script>
{% endraw %}

În acest caz, folosirea opțiunii `watch` ne permite să efectuăm o operație asincronă (accesând un API), să limităm cât de des se efectuează acea operație și să se stabilească stări intermediare până când primim un răspuns final. Nici una dintre acestea nu ar fi posibilă cu o proprietate component.

În plus față de opțiunea `watch`, puteți folosi și imperativul [vm.$watch API](../api/#vm-watch).

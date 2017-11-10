---
title: Introducere
type: guide
order: 2
---

## Ce este Vue.js?

Vue (pronunțat /vjuː/, ca **view**) este un **framework progresiv** pentru construirea interfețelor de utilizator. Spre deosebire de alte framework-uri monolitice, Vue este conceput pentru o implementare treptată. Biblioteca centrală se concentrează numai asupra stratului de vizualizare și este ușor de preluat și integrat cu alte biblioteci sau proiecte existente. Pe de o altă parte, Vue este, de asemenea, perfect capabil să alimenteze aplicații sofisticate cu o singură pagină, atunci când sunt utilizate în combinație cu [unelte moderne](single-file-components.html) și [biblioteci de suport](https://github.com/vuejs/awesome-vue#components--libraries).

Dacă sunteți un dezvoltator frontend cu experiență și doriți să știți cum se compară Vue cu alte biblioteci/framework-uri, consultați [Comparația cu alte Framework-uri](comparison.html).

## Să începem

<p class="tip">Ghidul oficial presupune că deja sunteți familiarizați cu HTML, CSS și JavaScript la un nivel de bază. Dacă sunteți începător în dezvoltarea frontend-ului, s-ar putea să nu fie cea mai bună ideie începerea imediată a studierii - reveniți după cunoașterea noțiunilor de bază! Experiența de lucru cu alte cadre poate ajuta, dar nu este obligatorie.</p>

Cea mai ușoară modalitate de a încerca Vue.js este utilizarea [exemplului JSFiddle Hello World](https://jsfiddle.net/chrisvfritz/50wL7mdz/). Pur și simplu deschideți-l în altă filă și urmați-l pe măsură ce treceți prin câteva exemple de bază. Sau puteți <a href="https://gist.githubusercontent.com/chrisvfritz/7f8d7d63000b48493c336e48b3db3e52/raw/ed60c4e5d5c6fec48b0921edaed0cb60be30e87c/index.html" target="_blank" download="index.html">crea un fișier<code>index.html</code></a> și includeți Vue cu :

``` html
<script src="https://unpkg.com/vue"></script>
```

Pagina [Instalarea](installation.html) oferă mai multe opțiuni de instalare a Vue. 
Notă: Nu **recomandăm** ca începătorii să înceapă cu `vue-cli`, mai ales dacă nu sunteți încă familiarizați cu instrumentele de construire bazate pe Node.js.

## Rendering Declarativ

În fișierul Vue.js este disponibil un sistem care ne permite să afișăm în mod automat datele din DOM, folosind o simplă sintaxă de șablon:

``` html
<div id="app">
  {{ message }}
</div>
```
``` js
var app = new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue!'
  }
})
```
{% raw %}
<div id="app" class="demo">
  {{ message }}
</div>
<script>
var app = new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue!'
  }
})
</script>
{% endraw %}

Am creat deja prima noastră aplicație Vue! Acest lucru pare destul de similar cu redarea unui șablon de șir, dar Vue a făcut o mulțime de lucruri "sub capotă". Datele și DOM-ul sunt acum legate și totul acum este **reactiv**. De unde stim? Deschideți consola JavaScript a browserului dvs. (chiar acum, în această pagină) și setați `app.message` cu o valoare diferită. Ar trebui să vedeți în exemplul de mai sus datele actualizate în consecință.

În plus față de interpolarea textului, putem lega atribute de element precum:

``` html
<div id="app-2">
  <span v-bind:title="message">
   Plasați mouse-ul peste mine pentru câteva secunde
    pentru a vedea titlul meu legat dinamic!
  </span>
</div>
```
``` js
var app2 = new Vue({
  el: '#app-2',
  data: {
    message: 'You loaded this page on ' + new Date().toLocaleString()
  }
})
```
{% raw %}
<div id="app-2" class="demo">
  <span v-bind:title="message">
    Plasați mouse-ul peste mine pentru câteva secunde pentru a vedea titlul meu legat dinamic!
  </span>
</div>
<script>
var app2 = new Vue({
  el: '#app-2',
  data: {
    message: 'Dvs ați încărcat această pagină pe ' + new Date().toLocaleString()
  }
})
</script>
{% endraw %}

Aici întâlnim ceva nou. Atributul `v-bind` pe care îl vedeți se numește **directivă**. Directivele sunt prefixate cu `v-` pentru a indica faptul că acestea sunt atribute speciale furnizate de Vue și, după cum probabil ați ghicit, aplică un comportament reactiv special la DOM rendat. Aici se spune că "mențineți atributul `title` al acestui element actualizat cu proprietatea `message` din instanța Vue."

Dacă deschideți din nou consolă JavaScript și introduceți `app2.message = 'un mesaj nou'`, veți vedea încă o dată că HTML-ul legat - în acest caz de atributul `title` - a fost actualizat.

## Condiții și Cicluri

De asemenea, este ușor să comutați prezența unui element:


``` html
<div id="app-3">
  <span v-if="seen">Acum mă vezi</span>
</div>
```

``` js
var app3 = new Vue({
  el: '#app-3',
  data: {
    seen: true
  }
})
```

{% raw %}
<div id="app-3" class="demo">
  <span v-if="seen">Acum mă vezi</span>
</div>
<script>
var app3 = new Vue({
  el: '#app-3',
  data: {
    seen: true
  }
})
</script>
{% endraw %}

Mergeți mai departe și introduceți `app3.seen = false` în consolă. Ar trebui să  dispără mesajul.

Acest exemplu demonstrează că putem lega datele nu doar de text și atribute, ci și de **structura** DOM-ului. În plus, Vue oferă și un sistem de efect puternic de tranziție care poate aplica automat [efecte de tranziție](tranitions.html) când elementele sunt inserate/actualizate/eliminate de Vue.

Există și alte directive numeroase, fiecare având propria sa funcționalitate. De exemplu, directiva `v-for` poate fi utilizată pentru a afișa o listă de elemente utilizând datele dintr-un tablou(Array):

``` html
<div id="app-4">
  <ol>
    <li v-for="todo in todos">
      {{ todo.text }}
    </li>
  </ol>
</div>
```
``` js
var app4 = new Vue({
  el: '#app-4',
  data: {
    todos: [
      { text: 'Să studiez JavaScript' },
      { text: 'Să studiez Vue' },
      { text: 'Să creez ceva nou și interesant' }
    ]
  }
})
```
{% raw %}
<div id="app-4" class="demo">
  <ol>
    <li v-for="todo in todos">
      {{ todo.text }}
    </li>
  </ol>
</div>
<script>
var app4 = new Vue({
  el: '#app-4',
  data: {
    todos: [
      { text: 'Să studiez JavaScript' },
      { text: 'Să studiez Vue' },
      { text: 'Să creez ceva nou și interesant' }
    ]
  }
})
</script>
{% endraw %}

În consola, introduceți `app4.todos.push({ text: 'Element nou' })`. Ar trebui să vedeți un element nou atașat în  listă.

## Lucru cu datele introduse de utilizator

Pentru a permite utilizatorilor să interacționeze cu aplicația dvs., putem folosi directiva `v-on` pentru a atașa ascultătorii de evenimente care invocă metode în instanțele noastre Vue:

``` html
<div id="app-5">
  <p>{{ message }}</p>
  <button v-on:click="reverseMessage">Inversează Mesajul</button>
</div>
```
``` js
var app5 = new Vue({
  el: '#app-5',
  data: {
    message: 'Hello Vue.js!'
  },
  methods: {
    reverseMessage: function () {
      this.message = this.message.split('').reverse().join('')
    }
  }
})
```
{% raw %}
<div id="app-5" class="demo">
  <p>{{ message }}</p>
  <button v-on:click="reverseMessage">Inversează Mesajul</button>
</div>
<script>
var app5 = new Vue({
  el: '#app-5',
  data: {
    message: 'Hello Vue.js!'
  },
  methods: {
    reverseMessage: function () {
      this.message = this.message.split('').reverse().join('')
    }
  }
})
</script>
{% endraw %}

Notați că în această metodă actualizăm starea aplicației noastre fără să atingem DOM-ul - toate manipulările DOM sunt tratate de Vue, iar codul pe care îl scrieți este axat pe logica extinsă.

Vue oferă, de asemenea, directiva `v-model` care face legătura bidirecțională între forma input și starea aplicației:

``` html
<div id="app-6">
  <p>{{ message }}</p>
  <input v-model="message">
</div>
```
``` js
var app6 = new Vue({
  el: '#app-6',
  data: {
    message: 'Hello Vue!'
  }
})
```
{% raw %}
<div id="app-6" class="demo">
  <p>{{ message }}</p>
  <input v-model="message">
</div>
<script>
var app6 = new Vue({
  el: '#app-6',
  data: {
    message: 'Hello Vue!'
  }
})
</script>
{% endraw %}

## Compoziție cu Componente

Sistemul component este un alt concept important în Vue, deoarece este o abstracție care ne permite să construim aplicații de scară largă, compuse din componente mici, autonome și adesea reutilizabile. Dacă ne gândim la asta, aproape orice tip de interfață de aplicație poate fi extrasă într-un arbore de componente:

![Arbore de Componente](/images/components.png)

În Vue, o componentă este în esență o instanță Vue cu opțiuni predefinite. Înregistrarea unei componente în Vue este simplă:

``` js
// Definiți o componentă nouă numită todo-item
Vue.component('todo-item', {
  template: '<li>This is a todo</li>'
})
```

Acum puteți să o puneți în șablonul altui component:

``` html
<ol>
  <!-- Creați o instanță a componentei todo-item -->
  <todo-item></todo-item>
</ol>
```

Dar acest lucru ar face același text pentru fiecare todo, care nu este super interesant. Ar trebui să putem transmite datele din domeniul părintelui în componentele copil. Să modificăm definiția componentei pentru a accepta o [proprietate](components.html#Props):

``` js
Vue.component('todo-item', {
  // Componenta todo-item acceptă acum o
  // "prop", care este ca un atribut personalizat.
  // Această proprietate este numită todo.
  props: ['todo'],
  template: '<li>{{ todo.text }}</li>'
})
```

Acum putem trece todo-ul în fiecare componentă repetată utilizând `v-bind`:

``` html
<div id="app-7">
  <ol>
    <!--
      Acum oferim fiecărui component cu todo obiect care-l
       reprezintă, astfel încât conținutul său poate fi dinamic.
       De asemenea, trebuie să furnizăm fiecărei componente o "cheie",
       care va fi explicată mai târziu.
    -->
    <todo-item
      v-for="item in groceryList"
      v-bind:todo="item"
      v-bind:key="item.id">
    </todo-item>
  </ol>
</div>
```
``` js
Vue.component('todo-item', {
  props: ['todo'],
  template: '<li>{{ todo.text }}</li>'
})

var app7 = new Vue({
  el: '#app-7',
  data: {
    groceryList: [
      { id: 0, text: 'Legume' },
      { id: 1, text: 'Cașcaval' },
      { id: 2, text: 'Alt produs alimentar' }
    ]
  }
})
```
{% raw %}
<div id="app-7" class="demo">
  <ol>
    <todo-item v-for="item in groceryList" v-bind:todo="item" :key="item.id"></todo-item>
  </ol>
</div>
<script>
Vue.component('todo-item', {
  props: ['todo'],
  template: '<li>{{ todo.text }}</li>'
})
var app7 = new Vue({
  el: '#app-7',
  data: {
    groceryList: [
      { id: 0, text: 'Legume' },
      { id: 1, text: 'Cașcaval' },
      { id: 2, text: 'Alt produs alimentar' }
    ]
  }
})
</script>
{% endraw %}

Acesta este un exemplu controversat, dar am reușit să separăm aplicația noastră în două unități mai mici, iar copilul este în mod rezonabil decuplat de părinte prin intermediul interfeței parametrilor de intrare. Acum putem îmbunătăți componenta `<todo-item>` cu un șablon și o logică mai complexă, fără a afecta aplicația părinte.

Într-o aplicație mare, este necesar să împărțiți întreaga aplicație în componente, pentru ca dezvoltarea să poată fi gestionată. Vom vorbi mult mai mult despre componente [mai târziu în ghid](components.html), dar aici este un exemplu (imaginar) despre cum ar putea arăta șablonul unei aplicații cu componente:

``` html
<div id="app">
  <app-nav></app-nav>
  <app-view>
    <app-sidebar></app-sidebar>
    <app-content></app-content>
  </app-view>
</div>
```

### Relation to Custom Elements

You may have noticed that Vue components are very similar to **Custom Elements**, which are part of the [Web Components Spec](https://www.w3.org/wiki/WebComponents/). That's because Vue's component syntax is loosely modeled after the spec. For example, Vue components implement the [Slot API](https://github.com/w3c/webcomponents/blob/gh-pages/proposals/Slots-Proposal.md) and the `is` special attribute. However, there are a few key differences:

1. The Web Components Spec is still in draft status, and is not natively implemented in every browser. In comparison, Vue components don't require any polyfills and work consistently in all supported browsers (IE9 and above). When needed, Vue components can also be wrapped inside a native custom element.

2. Vue components provide important features that are not available in plain custom elements, most notably cross-component data flow, custom event communication and build tool integrations.

## Ready for More?

We've briefly introduced the most basic features of Vue.js core - the rest of this guide will cover them and other advanced features with much finer details, so make sure to read through it all!

---
title: Animația Listelor și a Vizibilității
type: guide
order: 201
---

## Prezentare generală

Vue oferă o mare varietate de metode pentru a aplica efecte de animație când elementele sunt inserate, actualizate sau eliminate din DOM. Acesta include instrumente pentru:

- aplicarea automată a claselor pentru tranziții și animare CSS
- integrarea bibliotecilor terțe pentru animație CSS, precum Animate.css
- folosirea JavaScript pentru manipularea directă a DOM-ului în timpul hook-urilor de tranziție
- integrarea bibliotecilor JavaScript terțe de animație, cum ar fi Velocity.js

Pe această pagină ne vom referi doar la animația apariției, dispariției elementelor și la tranziția listelor, dar puteți analiza următoarea secțiune pentru [tranzițiile stărilor](transitioning-state.html).

## Tranziția Elementelor/Componentelor unice

Vue oferă un component învelitoare `transition`, care permite adăugarea tranzițiilor de apariție/dispariție pentru orice element sau componentă în următoarele contexte:

- Rendarea condiționată (folosind `v-if`)
- Afișarea condiționată (folosind `v-show`)
- Componente dinamice
- Elementele rădăcină ale unei componente

Astfel arată un exemplu simplu în acțiune:

``` html
<div id="demo">
  <button v-on:click="show = !show">
    Comutare
  </button>
  <transition name="fade">
    <p v-if="show">hello</p>
  </transition>
</div>
```

``` js
new Vue({
  el: '#demo',
  data: {
    show: true
  }
})
```

``` css
.fade-enter-active, .fade-leave-active {
  transition: opacity .5s
}
.fade-enter, .fade-leave-to /* .fade-leave-active până la versiunea 2.1.8 */ {
  opacity: 0
}
```

{% raw %}
<div id="demo">
  <button v-on:click="show = !show">
    Comutare
  </button>
  <transition name="demo-transition">
    <p v-if="show">hello</p>
  </transition>
</div>
<script>
new Vue({
  el: '#demo',
  data: {
    show: true
  }
})
</script>
<style>
.demo-transition-enter-active, .demo-transition-leave-active {
  transition: opacity .5s
}
.demo-transition-enter, .demo-transition-leave-to {
  opacity: 0
}
</style>
{% endraw %}

Atunci când un element înfășurat într-o componentă `transition` este inserat sau eliminat, se petrec următoarele:

1. Vue va căuta automat dacă elementul țintă are tranziții CSS sau animații aplicate. În caz că detectează, vor fi adăugate/eliminate clasele de tranziție CSS în momente corespunzătoare.

2. Dacă componenta de tranziție oferă [hook-uri JavaScript](#JavaScript-Hooks), atunci hook-urile date vor fi apelate la momente corespunzătoare.

3. În cazul în care nu sunt detectate tranziții/animații CSS și nu sunt prezente hook-uri JavaScript, operațiile DOM pentru inserare și/sau eliminare vor fi executate imediat pe următorul frame (Notă: acesta este un frame de animație al browserului, diferit de conceptul Vue de `nextTick`).

### Clasele de Tranziție

Există șase clase utilizate pentru tranzițiile de apariție/dispariție.

1. `v-enter`: Reprezintă începutul animației de apariție a elementului. Această clasă se adaugă înainte de inserarea elementului și se elimină în următorul frame după inserare.

2. `v-enter-active`: Reprezintă starea activă a animației de apariție a elementului. Aplicată pe toată faza de animație a apariției elementului. Este adăugată înaintea inserării elementului și se elimină atunci când tranziția/animația se termină. Această clasă poate fi utilizată pentru a defini durata (duration), întârzierea (delay) și curba de relaxare (easing curve) pentru animația de apariție.

3. `v-enter-to`: **Disponibil doar în versiunile 2.1.8+.** Reprezintă sfârșitul animației de apariție. Este adăugată în frame-ul ce urmează după inserarea elementului (în același moment cu eliminarea `v-enter`), clasa este eliminată la terminarea tranziției/animației.

4. `v-leave`: Reprezintă începutul animației de dispariție a elementului. Clasa se adaugă imediat după startul animației de dispariție și se elmină în următorul frame.

5. `v-leave-active`: Reprezintă starea activă a animației de dispariție. Este aplicată pentru întreaga perioadă de dispariție a elementului. Se adaugă imediat după începutul tranziției de dispariție, clasa se elimină la terminarea tranziției/animației. Această clasă poate fi utilizată pentru a defini durata (duration), întârzierea (delay) și curba de relaxare (easing curve) pentru animația de dispariție.

6. `v-leave-to`: **Disponibil doar în versiunile 2.1.8+.** Reprezintă sfârșitul animației de dispariție. Este adăugată în frame-ul ce urmează după eliminarea elementului (în același moment cu eliminarea `v-leave`), clasa este eliminată la terminarea tranziției/animației.

![Transition Diagram](/images/transition.png)

Fiecare dintre aceste clase va fi prefixată cu numele tranziției. Aici prefixul `v-` este implicit când folosiți un element `<transition>` fără nume. De exemplu, dacă o să utilizți `<transition name="my-transition">` , atunci în loc de clasa `v-enter` se va folosi `my-transition-enter`.

`v-enter-active` și `v-leave-active` vă oferă posibilitatea de a specifica diferite curbe de relaxare (easing curves) pentru tranzițiile de apariție/dispariție a elementului, exemplul folosirii acestora va urma în următoarea secțiune.

### Tranziții CSS

Una dintre cele mai comune tipuri de animație utilizează tranziții CSS. Iată un exemplu:

``` html
<div id="example-1">
  <button @click="show = !show">
    Comutare rendare
  </button>
  <transition name="slide-fade">
    <p v-if="show">hello</p>
  </transition>
</div>
```

``` js
new Vue({
  el: '#example-1',
  data: {
    show: true
  }
})
```

``` css
/* Animațiile de apariție și dispariție pot folosi    */
/* diferite setări de durată și temporizare (timing). */
.slide-fade-enter-active {
  transition: all .3s ease;
}
.slide-fade-leave-active {
  transition: all .8s cubic-bezier(1.0, 0.5, 0.8, 1.0);
}
.slide-fade-enter, .slide-fade-leave-to
/* .slide-fade-leave-active mai jos de versiunea 2.1.8 */ {
  transform: translateX(10px);
  opacity: 0;
}
```

{% raw %}
<div id="example-1" class="demo">
  <button @click="show = !show">
    Comutare rendare
  </button>
  <transition name="slide-fade">
    <p v-if="show">hello</p>
  </transition>
</div>
<script>
new Vue({
  el: '#example-1',
  data: {
    show: true
  }
})
</script>
<style>
.slide-fade-enter-active {
  transition: all .3s ease;
}
.slide-fade-leave-active {
  transition: all .8s cubic-bezier(1.0, 0.5, 0.8, 1.0);
}
.slide-fade-enter, .slide-fade-leave-to {
  transform: translateX(10px);
  opacity: 0;
}
</style>
{% endraw %}

### Animații CSS

Animațiile CSS sunt aplicate în același mod ca și tranzițiile CSS, diferența fiind că `v-enter` nu este eliminat imediat după inserarea elementului, ci la petrecerea acțiunii `animationend` (on animationend event).

Iată un exemplu în care pentru concisitate vor fi omise prefixele CSS legate de diferite browsere:

``` html
<div id="example-2">
  <button @click="show = !show">Comutare prezentare</button>
  <transition name="bounce">
    <p v-if="show">Privește la mine!</p>
  </transition>
</div>
```

``` js
new Vue({
  el: '#example-2',
  data: {
    show: true
  }
})
```

``` css
.bounce-enter-active {
  animation: bounce-in .5s;
}
.bounce-leave-active {
  animation: bounce-in .5s reverse;
}
@keyframes bounce-in {
  0% {
    transform: scale(0);
  }
  50% {
    transform: scale(1.5);
  }
  100% {
    transform: scale(1);
  }
}
```

{% raw %}
<div id="example-2" class="demo">
  <button @click="show = !show">Comutare prezentare</button>
  <transition name="bounce">
    <p v-show="show">Privește la mine!</p>
  </transition>
</div>

<style>
  .bounce-enter-active {
    -webkit-animation: bounce-in .5s;
    animation: bounce-in .5s;
  }
  .bounce-leave-active {
    -webkit-animation: bounce-in .5s reverse;
    animation: bounce-in .5s reverse;
  }
  @keyframes bounce-in {
    0% {
      -webkit-transform: scale(0);
      transform: scale(0);
    }
    50% {
      -webkit-transform: scale(1.5);
      transform: scale(1.5);
    }
    100% {
      -webkit-transform: scale(1);
      transform: scale(1);
    }
  }
  @-webkit-keyframes bounce-in {
    0% {
      -webkit-transform: scale(0);
      transform: scale(0);
    }
    50% {
      -webkit-transform: scale(1.5);
      transform: scale(1.5);
    }
    100% {
      -webkit-transform: scale(1);
      transform: scale(1);
    }
  }
</style>
<script>
new Vue({
  el: '#example-2',
  data: {
    show: true
  }
})
</script>
{% endraw %}

### Clase de Tranziție Personalizate

De asemenea, puteți specifica clase de tranziție personalizate folosind următoarele atribute:

- `enter-class`
- `enter-active-class`
- `enter-to-class` (2.1.8+)
- `leave-class`
- `leave-active-class`
- `leave-to-class` (2.1.8+)

Acestea vor înlocui numele claselor convenționale. Acest lucru este util în special atunci când doriți să combinați sistemul de tranziție Vue cu o bibliotecă de animație CSS existentă, cum ar fi [Animate.css](https://daneden.github.io/animate.css/).

Iată un exemplu:

``` html
<link href="https://cdn.jsdelivr.net/npm/animate.css@3.5.1" rel="stylesheet" type="text/css">

<div id="example-3">
  <button @click="show = !show">
    Comutare rendare
  </button>
  <transition
    name="custom-classes-transition"
    enter-active-class="animated tada"
    leave-active-class="animated bounceOutRight"
  >
    <p v-if="show">hello</p>
  </transition>
</div>
```

``` js
new Vue({
  el: '#example-3',
  data: {
    show: true
  }
})
```

{% raw %}
<link href="https://cdn.jsdelivr.net/npm/animate.css@3.5.1" rel="stylesheet" type="text/css">
<div id="example-3" class="demo">
  <button @click="show = !show">
    Comutare rendare
  </button>
  <transition
    name="custom-classes-transition"
    enter-active-class="animated tada"
    leave-active-class="animated bounceOutRight"
  >
    <p v-if="show">hello</p>
  </transition>
</div>
<script>
new Vue({
  el: '#example-3',
  data: {
    show: true
  }
})
</script>
{% endraw %}

### Folosirea Tranzițiilor și Animațiilor împreună

Pentru a ști când s-a încheiat o tranziție Vue trebuie să stabilească abonați la acțiuni (event listeners). În funcție de regulile CSS utilizate acțiunea petrecută poate fi `transitionend` sau `animationend`. Dacă folosiți doar una din metode, Vue poate detecta automat tipul corect.

Cu toate acestea, în unele cazuri, puteți dori să aveți ambele aplicate pe aceleași element, de exemplu având o animație CSS declanșată de Vue, împreună cu un efect de tranziție CSS pe hover. În aceste cazuri, va trebui să declarați în mod explicit tipul de care doriți Vue să aibă grijă într-un atribut `type`, cu valoarea `animation` sau `transition`.

### Specificarea Duratei Tranziției

> Nou în 2.2.0+

În cele mai multe cazuri, Vue detectează automat când tranziția este terminată. În mod implicit, Vue așteaptă prima acțiune (event) `transitionend` sau `animationend` pe elementul de tranziție de bază. Totuși, acest lucru nu este întotdeauna dorit - de exemplu, este posibil să avem o secvență de tranziții regizată în care unele elemente interioare au o tranziție întârziată sau o durată de tranziție mai lungă decât elementul de tranziție de bază.

În astfel de cazuri, puteți specifica o durată de tranziție explicită (în milisecunde) folosind proprietatea `duration` pe componenta `<transition>` :

``` html
<transition :duration="1000">...</transition>
```

De asemenea, puteți specifica valori separate pentru durata de apariție și de dispariție:

``` html
<transition :duration="{ enter: 500, leave: 800 }">...</transition>
```

### Hook-uri JavaScript

De asemenea, puteți defini hook-uri JavaScript în atribute:

``` html
<transition
  v-on:before-enter="beforeEnter"
  v-on:enter="enter"
  v-on:after-enter="afterEnter"
  v-on:enter-cancelled="enterCancelled"

  v-on:before-leave="beforeLeave"
  v-on:leave="leave"
  v-on:after-leave="afterLeave"
  v-on:leave-cancelled="leaveCancelled"
>
  <!-- ... -->
</transition>
```

``` js
// ...
methods: {
  // --------
  // APARIȚIE
  // --------

  beforeEnter: function (el) {
    // ...
  },
  // callback-ul done este opțional atunci când
  // este utilizat în combinație cu CSS
  enter: function (el, done) {
    // ...
    done()
  },
  afterEnter: function (el) {
    // ...
  },
  enterCancelled: function (el) {
    // ...
  },

  // --------
  // DISPARIȚIE
  // --------

  beforeLeave: function (el) {
    // ...
  },
  // callback-ul done este opțional atunci când
  // este utilizat în combinație cu CSS
  leave: function (el, done) {
    // ...
    done()
  },
  afterLeave: function (el) {
    // ...
  },
  // leaveCancelled este disponibil numai cu v-show
  leaveCancelled: function (el) {
    // ...
  }
}
```

Aceste hook-uri pot fi utilizate fie singure, fie în combinație cu tranziții/animații CSS.

<p class="tip">Când utilizați doar tranziții JavaScript, **callback-urile `done` sunt necesare pentru hook-urile `enter` și `leave`**. În caz contrar, ele vor fi chemate în mod sincron și tranziția se va termina imediat.</p>

<p class="tip">De asemenea, este o idee bună să adăugați în mod explicit `v-bind:css="false"` pentru tranziții ce utilizează doar JavaScript, astfel încât Vue să poată sări peste detectarea CSS. Acest lucru totodată previne ca regulile CSS să interfereze accidental cu tranziția.</p>

Acum să analizăm un exemplu. Iată o tranziție JavaScript folosind Velocity.js:

``` html
<!--
Velocity funcționează aproape la fel ca jQuery.animate
și este o opțiune excelentă pentru animațiile JavaScript
-->
<script src="https://cdnjs.cloudflare.com/ajax/libs/velocity/1.2.3/velocity.min.js"></script>

<div id="example-4">
  <button @click="show = !show">
    Comutare
  </button>
  <transition
    v-on:before-enter="beforeEnter"
    v-on:enter="enter"
    v-on:leave="leave"
    v-bind:css="false"
  >
    <p v-if="show">
      Demo
    </p>
  </transition>
</div>
```

``` js
new Vue({
  el: '#example-4',
  data: {
    show: false
  },
  methods: {
    beforeEnter: function (el) {
      el.style.opacity = 0
    },
    enter: function (el, done) {
      Velocity(el, { opacity: 1, fontSize: '1.4em' }, { duration: 300 })
      Velocity(el, { fontSize: '1em' }, { complete: done })
    },
    leave: function (el, done) {
      Velocity(el, { translateX: '15px', rotateZ: '50deg' }, { duration: 600 })
      Velocity(el, { rotateZ: '100deg' }, { loop: 2 })
      Velocity(el, {
        rotateZ: '45deg',
        translateY: '30px',
        translateX: '30px',
        opacity: 0
      }, { complete: done })
    }
  }
})
```

{% raw %}
<div id="example-4" class="demo">
  <button @click="show = !show">
    Comutare
  </button>
  <transition
    v-on:before-enter="beforeEnter"
    v-on:enter="enter"
    v-on:leave="leave"
  >
    <p v-if="show">
      Demo
    </p>
  </transition>
</div>
<script src="https://cdnjs.cloudflare.com/ajax/libs/velocity/1.2.3/velocity.min.js"></script>
<script>
new Vue({
  el: '#example-4',
  data: {
    show: false
  },
  methods: {
    beforeEnter: function (el) {
      el.style.opacity = 0
      el.style.transformOrigin = 'left'
    },
    enter: function (el, done) {
      Velocity(el, { opacity: 1, fontSize: '1.4em' }, { duration: 300 })
      Velocity(el, { fontSize: '1em' }, { complete: done })
    },
    leave: function (el, done) {
      Velocity(el, { translateX: '15px', rotateZ: '50deg' }, { duration: 600 })
      Velocity(el, { rotateZ: '100deg' }, { loop: 2 })
      Velocity(el, {
        rotateZ: '45deg',
        translateY: '30px',
        translateX: '30px',
        opacity: 0
      }, { complete: done })
    }
  }
})
</script>
{% endraw %}

## Tranziții la Rendarea Inițială

Dacă doriți să aplicați o tranziție la rendarea inițială a unui nod, puteți adăuga atributul `appear` :

``` html
<transition appear>
  <!-- ... -->
</transition>
```

În mod implicit, se vor folosi tranzițiile specificate pentru apariția și dispariția elementului. Însă dacă doriți, puteți specifica clase personalizate CSS:

``` html
<transition
  appear
  appear-class="custom-appear-class"
  appear-to-class="custom-appear-to-class" (2.1.8+)
  appear-active-class="custom-appear-active-class"
>
  <!-- ... -->
</transition>
```

și hook-uri JavaScript personalizate:

``` html
<transition
  appear
  v-on:before-appear="customBeforeAppearHook"
  v-on:appear="customAppearHook"
  v-on:after-appear="customAfterAppearHook"
  v-on:appear-cancelled="customAppearCancelledHook"
>
  <!-- ... -->
</transition>
```

## Tranziția între elemente

We discuss [transitioning between components](#Transitioning-Between-Components) later, but you can also transition between raw elements using `v-if`/`v-else`. One of the most common two-element transitions is between a list container and a message describing an empty list:
Vom discuta [tranziția între componente](#Transitioning-Between-Components) în detaliu mai târziu, dar acum putem analiza tranziția intre elemente folosind `v-if`/`v-else`. Unul dintre cazurile cele mai frecvente este trecerea de la containerul listei la mesajul că lista este goală:

``` html
<transition>
  <table v-if="items.length > 0">
    <!-- ... -->
  </table>
  <p v-else>Ne pare rău, nu a fost gasit nici-un element.</p>
</transition>
```

Această metodă funcționează bine, dar totuși există o avertizare:

<p class="tip">Atunci când comutați între elementele care au **același nume de tag**, trebuie de indicat pentru Vue că elementele sunt distincte oferindu-le valori unice prin intermediul atributului `key` . În caz contrar, compilatorul Vue va înlocui doar conținutul elementului pentru eficiență. Chiar și atunci când nu este necesar din punct de vedere tehnic, **se consideră o bună practică înfășurarea mai multor elemente în cadrul unei componente `<transition>` .**</p>

De exemplu:

``` html
<transition>
  <button v-if="isEditing" key="save">
    Salvare
  </button>
  <button v-else key="edit">
    Editare
  </button>
</transition>
```

În aceste cazuri, puteți folosi și atributul `key` pentru a aplica tranziția între diferite stări ale aceluiași element. În loc de a folosi `v-if` și `v-else`, exemplul de mai sus poate fi rescris în modul următor:

``` html
<transition>
  <button v-bind:key="isEditing">
    {{ isEditing ? 'Salvare' : 'Editare' }}
  </button>
</transition>
```

Practic este posibilă aplicarea tranziției între orice număr de elemente, fie prin utilizarea mai multor `v-if`-uri, fie prin legarea unui singur element de o proprietate dinamică. De exemplu:

``` html
<transition>
  <button v-if="docState === 'saved'" key="saved">
    Editare
  </button>
  <button v-if="docState === 'edited'" key="edited">
    Salvare
  </button>
  <button v-if="docState === 'editing'" key="editing">
    Anulare
  </button>
</transition>
```

Care ar putea fi scris și în modul următor:

``` html
<transition>
  <button v-bind:key="docState">
    {{ buttonMessage }}
  </button>
</transition>
```

``` js
// ...
computed: {
  buttonMessage: function () {
    switch (this.docState) {
      case 'saved': return 'Editare'
      case 'edited': return 'Salvare'
      case 'editing': return 'Anulare'
    }
  }
}
```

### Moduri de tranziție

Totuși, există o problemă. Încercați să faceți click pe butonul de mai jos:

{% raw %}
<div id="no-mode-demo" class="demo">
  <transition name="no-mode-fade">
    <button v-if="on" key="on" @click="on = false">
      on
    </button>
    <button v-else key="off" @click="on = true">
      off
    </button>
  </transition>
</div>
<script>
new Vue({
  el: '#no-mode-demo',
  data: {
    on: false
  }
})
</script>
<style>
.no-mode-fade-enter-active, .no-mode-fade-leave-active {
  transition: opacity .5s
}
.no-mode-fade-enter, .no-mode-fade-leave-active {
  opacity: 0
}
</style>
{% endraw %}

În timpul tranziției de la butonul "on" la butonul "off", se afișează simultan ambele butoane - un buton în procesul dispariției, iar celălalt în procesul apariției. Acesta este comportamentul implicit al componentei `<transition>` - apariția și dispariția elementului se întâmplă simultan.

Uneori acestea funcționează excelent, precum în cazul când elementele de tranziție au poziționare absolută unul peste altul:

{% raw %}
<div id="no-mode-absolute-demo" class="demo">
  <div class="no-mode-absolute-demo-wrapper">
    <transition name="no-mode-absolute-fade">
      <button v-if="on" key="on" @click="on = false">
        on
      </button>
      <button v-else key="off" @click="on = true">
        off
      </button>
    </transition>
  </div>
</div>
<script>
new Vue({
  el: '#no-mode-absolute-demo',
  data: {
    on: false
  }
})
</script>
<style>
.no-mode-absolute-demo-wrapper {
  position: relative;
  height: 18px;
}
.no-mode-absolute-demo-wrapper button {
  position: absolute;
}
.no-mode-absolute-fade-enter-active, .no-mode-absolute-fade-leave-active {
  transition: opacity .5s;
}
.no-mode-absolute-fade-enter, .no-mode-absolute-fade-leave-active {
  opacity: 0;
}
</style>
{% endraw %}

De asemenea puteți interpreta acțiunile sub forma tranzițiilor de alunecare (slide transitions)

{% raw %}
<div id="no-mode-translate-demo" class="demo">
  <div class="no-mode-translate-demo-wrapper">
    <transition name="no-mode-translate-fade">
      <button v-if="on" key="on" @click="on = false">
        on
      </button>
      <button v-else key="off" @click="on = true">
        off
      </button>
    </transition>
  </div>
</div>
<script>
new Vue({
  el: '#no-mode-translate-demo',
  data: {
    on: false
  }
})
</script>
<style>
.no-mode-translate-demo-wrapper {
  position: relative;
  height: 18px;
}
.no-mode-translate-demo-wrapper button {
  position: absolute;
}
.no-mode-translate-fade-enter-active, .no-mode-translate-fade-leave-active {
  transition: all 1s;
}
.no-mode-translate-fade-enter, .no-mode-translate-fade-leave-active {
  opacity: 0;
}
.no-mode-translate-fade-enter {
  transform: translateX(31px);
}
.no-mode-translate-fade-leave-active {
  transform: translateX(-31px);
}
</style>
{% endraw %}

Tranzițiile simultane de apariție și dispariție nu sunt întotdeauna de dorit, de aceea Vue oferă câteva **moduri de tranziție** alternative:

- `in-out`: Mai întâi se efectuează tranziția de apariție a elementului nou și doar după tranziția de dispariție a elementului vechi.

- `out-in`: Mai întâi se efectuează tranziția de dispariție a elementului curent și după finalizare tranziția de apariție a elementului nou.

Acum, să actualizăm tranziția pentru butoanele on/off folosind modul `out-in`:

``` html
<transition name="fade" mode="out-in">
  <!-- ... butoanele ... -->
</transition>
```

{% raw %}
<div id="with-mode-demo" class="demo">
  <transition name="with-mode-fade" mode="out-in">
    <button v-if="on" key="on" @click="on = false">
      on
    </button>
    <button v-else key="off" @click="on = true">
      off
    </button>
  </transition>
</div>
<script>
new Vue({
  el: '#with-mode-demo',
  data: {
    on: false
  }
})
</script>
<style>
.with-mode-fade-enter-active, .with-mode-fade-leave-active {
  transition: opacity .5s
}
.with-mode-fade-enter, .with-mode-fade-leave-active {
  opacity: 0
}
</style>
{% endraw %}

Adăugând un atribut, am fixat această tranziție originală fără a fi nevoie să adăugăm stiluri speciale.

Modul `in-out` nu este folosit la fel de des, dar uneori poate fi util pentru un efect de tranziție un pic diferit. Să încercăm să îl combinăm cu tranziția (slide-fade) la care am lucrat mai devreme:

{% raw %}
<div id="in-out-translate-demo" class="demo">
  <div class="in-out-translate-demo-wrapper">
    <transition name="in-out-translate-fade" mode="in-out">
      <button v-if="on" key="on" @click="on = false">
        on
      </button>
      <button v-else key="off" @click="on = true">
        off
      </button>
    </transition>
  </div>
</div>
<script>
new Vue({
  el: '#in-out-translate-demo',
  data: {
    on: false
  }
})
</script>
<style>
.in-out-translate-demo-wrapper {
  position: relative;
  height: 18px;
}
.in-out-translate-demo-wrapper button {
  position: absolute;
}
.in-out-translate-fade-enter-active, .in-out-translate-fade-leave-active {
  transition: all .5s;
}
.in-out-translate-fade-enter, .in-out-translate-fade-leave-active {
  opacity: 0;
}
.in-out-translate-fade-enter {
  transform: translateX(31px);
}
.in-out-translate-fade-leave-active {
  transform: translateX(-31px);
}
</style>
{% endraw %}

Destul de mișto, nu?

## Tranziția între componente

Tranziția între componente este chiar mai simplă - nici măcar nu avem nevoie de atributul `key`. În schimb, înfășurăm [componenta dinamică](components.html#Dynamic-Components):

``` html
<transition name="component-fade" mode="out-in">
  <component v-bind:is="view"></component>
</transition>
```

``` js
new Vue({
  el: '#transition-components-demo',
  data: {
    view: 'v-a'
  },
  components: {
    'v-a': {
      template: '<div>Componenta A</div>'
    },
    'v-b': {
      template: '<div>Componenta B</div>'
    }
  }
})
```

``` css
.component-fade-enter-active, .component-fade-leave-active {
  transition: opacity .3s ease;
}
.component-fade-enter, .component-fade-leave-to
/* .component-fade-leave-active mai jos de versiunea 2.1.8 */ {
  opacity: 0;
}
```

{% raw %}
<div id="transition-components-demo" class="demo">
  <input v-model="view" type="radio" value="v-a" id="a" name="view"><label for="a">A</label>
  <input v-model="view" type="radio" value="v-b" id="b" name="view"><label for="b">B</label>
  <transition name="component-fade" mode="out-in">
    <component v-bind:is="view"></component>
  </transition>
</div>
<style>
.component-fade-enter-active, .component-fade-leave-active {
  transition: opacity .3s ease;
}
.component-fade-enter, .component-fade-leave-to {
  opacity: 0;
}
</style>
<script>
new Vue({
  el: '#transition-components-demo',
  data: {
    view: 'v-a'
  },
  components: {
    'v-a': {
      template: '<div>Componenta A</div>'
    },
    'v-b': {
      template: '<div>Componenta B</div>'
    }
  }
})
</script>
{% endraw %}

## Tranzițiile listei

Deocamdată, am reușit să cercetăm următoarele tranziții:

- Elemente individuale
- Elemente multiple în care doar 1 este rendat la un moment dat

Și cum rămâne cu situația, când avem o întreagă listă de elemente pe care am dori să le afișăm simultan, de exemplu folosind `v-for`? În acest caz, vom utiliza componenta `<transition-group>`. Înainte de a analiza un exemplu, totuși, există câteva lucruri importante pe care ar fi bine să le cunoaștem despre această componentă:

- Spre deosebire de `<transition>`, rendează un element real: un `<span>` în mod implicit. Puteți schimba elementul care se va renda folosind atributul `tag`.
- Elementele din interiorul `<transition-group>` **întotdeauna necesită** indicarea valorii unice pentru atributul `key`.

### Tranzițiile apariției/dispariției elementelor unei listei

Acum să analizăm un exemplu, tranziția apariției și dispariției folosind aceleași clase CSS pe care le-am utilizat anterior:

``` html
<div id="list-demo">
  <button v-on:click="add">Adaugă</button>
  <button v-on:click="remove">Elimină</button>
  <transition-group name="list" tag="p">
    <span v-for="item in items" v-bind:key="item" class="list-item">
      {{ item }}
    </span>
  </transition-group>
</div>
```

``` js
new Vue({
  el: '#list-demo',
  data: {
    items: [1,2,3,4,5,6,7,8,9],
    nextNum: 10
  },
  methods: {
    randomIndex: function () {
      return Math.floor(Math.random() * this.items.length)
    },
    add: function () {
      this.items.splice(this.randomIndex(), 0, this.nextNum++)
    },
    remove: function () {
      this.items.splice(this.randomIndex(), 1)
    },
  }
})
```

``` css
.list-item {
  display: inline-block;
  margin-right: 10px;
}
.list-enter-active, .list-leave-active {
  transition: all 1s;
}
.list-enter, .list-leave-to /* .list-leave-active mai jos de versiunea 2.1.8 */ {
  opacity: 0;
  transform: translateY(30px);
}
```

{% raw %}
<div id="list-demo" class="demo">
  <button v-on:click="add">Adaugă</button>
  <button v-on:click="remove">Elimină</button>
  <transition-group name="list" tag="p">
    <span v-for="item in items" :key="item" class="list-item">
      {{ item }}
    </span>
  </transition-group>
</div>
<script>
new Vue({
  el: '#list-demo',
  data: {
    items: [1,2,3,4,5,6,7,8,9],
    nextNum: 10
  },
  methods: {
    randomIndex: function () {
      return Math.floor(Math.random() * this.items.length)
    },
    add: function () {
      this.items.splice(this.randomIndex(), 0, this.nextNum++)
    },
    remove: function () {
      this.items.splice(this.randomIndex(), 1)
    },
  }
})
</script>
<style>
.list-item {
  display: inline-block;
  margin-right: 10px;
}
.list-enter-active, .list-leave-active {
  transition: all 1s;
}
.list-enter, .list-leave-to {
  opacity: 0;
  transform: translateY(30px);
}
</style>
{% endraw %}

Totuși există o problemă cu acest exemplu. Când adăugați sau eliminați un element, cele din jurul acestuia se fixează instantaneu în noul loc fără tranziție lină. O vom rezolva mai târziu.

### List Move Transitions

The `<transition-group>` component has another trick up its sleeve. It can not only animate entering and leaving, but also changes in position. The only new concept you need to know to use this feature is the addition of **the `v-move` class**, which is added when items are changing positions. Like the other classes, its prefix will match the value of a provided `name` attribute and you can also manually specify a class with the `move-class` attribute.

This class is mostly useful for specifying the transition timing and easing curve, as you'll see below:

``` html
<script src="https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.14.1/lodash.min.js"></script>

<div id="flip-list-demo" class="demo">
  <button v-on:click="shuffle">Shuffle</button>
  <transition-group name="flip-list" tag="ul">
    <li v-for="item in items" v-bind:key="item">
      {{ item }}
    </li>
  </transition-group>
</div>
```

``` js
new Vue({
  el: '#flip-list-demo',
  data: {
    items: [1,2,3,4,5,6,7,8,9]
  },
  methods: {
    shuffle: function () {
      this.items = _.shuffle(this.items)
    }
  }
})
```

``` css
.flip-list-move {
  transition: transform 1s;
}
```

{% raw %}
<script src="https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.14.1/lodash.min.js"></script>
<div id="flip-list-demo" class="demo">
  <button v-on:click="shuffle">Shuffle</button>
  <transition-group name="flip-list" tag="ul">
    <li v-for="item in items" :key="item">
      {{ item }}
    </li>
  </transition-group>
</div>
<script>
new Vue({
  el: '#flip-list-demo',
  data: {
    items: [1,2,3,4,5,6,7,8,9]
  },
  methods: {
    shuffle: function () {
      this.items = _.shuffle(this.items)
    }
  }
})
</script>
<style>
.flip-list-move {
  transition: transform 1s;
}
</style>
{% endraw %}

This might seem like magic, but under the hood, Vue is using an animation technique called [FLIP](https://aerotwist.com/blog/flip-your-animations/) to smoothly transition elements from their old position to their new position using transforms.

We can combine this technique with our previous implementation to animate every possible change to our list!

``` html
<script src="https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.14.1/lodash.min.js"></script>

<div id="list-complete-demo" class="demo">
  <button v-on:click="shuffle">Shuffle</button>
  <button v-on:click="add">Add</button>
  <button v-on:click="remove">Remove</button>
  <transition-group name="list-complete" tag="p">
    <span
      v-for="item in items"
      v-bind:key="item"
      class="list-complete-item"
    >
      {{ item }}
    </span>
  </transition-group>
</div>
```

``` js
new Vue({
  el: '#list-complete-demo',
  data: {
    items: [1,2,3,4,5,6,7,8,9],
    nextNum: 10
  },
  methods: {
    randomIndex: function () {
      return Math.floor(Math.random() * this.items.length)
    },
    add: function () {
      this.items.splice(this.randomIndex(), 0, this.nextNum++)
    },
    remove: function () {
      this.items.splice(this.randomIndex(), 1)
    },
    shuffle: function () {
      this.items = _.shuffle(this.items)
    }
  }
})
```

``` css
.list-complete-item {
  transition: all 1s;
  display: inline-block;
  margin-right: 10px;
}
.list-complete-enter, .list-complete-leave-to
/* .list-complete-leave-active below version 2.1.8 */ {
  opacity: 0;
  transform: translateY(30px);
}
.list-complete-leave-active {
  position: absolute;
}
```

{% raw %}
<script src="https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.14.1/lodash.min.js"></script>
<div id="list-complete-demo" class="demo">
  <button v-on:click="shuffle">Shuffle</button>
  <button v-on:click="add">Add</button>
  <button v-on:click="remove">Remove</button>
  <transition-group name="list-complete" tag="p">
    <span v-for="item in items" :key="item" class="list-complete-item">
      {{ item }}
    </span>
  </transition-group>
</div>
<script>
new Vue({
  el: '#list-complete-demo',
  data: {
    items: [1,2,3,4,5,6,7,8,9],
    nextNum: 10
  },
  methods: {
    randomIndex: function () {
      return Math.floor(Math.random() * this.items.length)
    },
    add: function () {
      this.items.splice(this.randomIndex(), 0, this.nextNum++)
    },
    remove: function () {
      this.items.splice(this.randomIndex(), 1)
    },
    shuffle: function () {
      this.items = _.shuffle(this.items)
    }
  }
})
</script>
<style>
.list-complete-item {
  transition: all 1s;
  display: inline-block;
  margin-right: 10px;
}
.list-complete-enter, .list-complete-leave-to {
  opacity: 0;
  transform: translateY(30px);
}
.list-complete-leave-active {
  position: absolute;
}
</style>
{% endraw %}

<p class="tip">One important note is that these FLIP transitions do not work with elements set to `display: inline`. As an alternative, you can use `display: inline-block` or place elements in a flex context.</p>

These FLIP animations are also not limited to a single axis. Items in a multidimensional grid can be [transitioned too](https://jsfiddle.net/chrisvfritz/sLrhk1bc/):

{% raw %}
<div id="sudoku-demo" class="demo">
  <strong>Lazy Sudoku</strong>
  <p>Keep hitting the shuffle button until you win.</p>
  <button @click="shuffle">
    Shuffle
  </button>
  <transition-group name="cell" tag="div" class="sudoku-container">
    <div v-for="cell in cells" :key="cell.id" class="cell">
      {{ cell.number }}
    </div>
  </transition-group>
</div>
<script>
new Vue({
  el: '#sudoku-demo',
  data: {
    cells: Array.apply(null, { length: 81 })
      .map(function (_, index) {
        return {
          id: index,
          number: index % 9 + 1
        }
      })
  },
  methods: {
    shuffle: function () {
      this.cells = _.shuffle(this.cells)
    }
  }
})
</script>
<style>
.sudoku-container {
  display: flex;
  flex-wrap: wrap;
  width: 238px;
  margin-top: 10px;
}
.cell {
  display: flex;
  justify-content: space-around;
  align-items: center;
  width: 25px;
  height: 25px;
  border: 1px solid #aaa;
  margin-right: -1px;
  margin-bottom: -1px;
}
.cell:nth-child(3n) {
  margin-right: 0;
}
.cell:nth-child(27n) {
  margin-bottom: 0;
}
.cell-move {
  transition: transform 1s;
}
</style>
{% endraw %}

### Staggering List Transitions

By communicating with JavaScript transitions through data attributes, it's also possible to stagger transitions in a list:

``` html
<script src="https://cdnjs.cloudflare.com/ajax/libs/velocity/1.2.3/velocity.min.js"></script>

<div id="staggered-list-demo">
  <input v-model="query">
  <transition-group
    name="staggered-fade"
    tag="ul"
    v-bind:css="false"
    v-on:before-enter="beforeEnter"
    v-on:enter="enter"
    v-on:leave="leave"
  >
    <li
      v-for="(item, index) in computedList"
      v-bind:key="item.msg"
      v-bind:data-index="index"
    >{{ item.msg }}</li>
  </transition-group>
</div>
```

``` js
new Vue({
  el: '#staggered-list-demo',
  data: {
    query: '',
    list: [
      { msg: 'Bruce Lee' },
      { msg: 'Jackie Chan' },
      { msg: 'Chuck Norris' },
      { msg: 'Jet Li' },
      { msg: 'Kung Fury' }
    ]
  },
  computed: {
    computedList: function () {
      var vm = this
      return this.list.filter(function (item) {
        return item.msg.toLowerCase().indexOf(vm.query.toLowerCase()) !== -1
      })
    }
  },
  methods: {
    beforeEnter: function (el) {
      el.style.opacity = 0
      el.style.height = 0
    },
    enter: function (el, done) {
      var delay = el.dataset.index * 150
      setTimeout(function () {
        Velocity(
          el,
          { opacity: 1, height: '1.6em' },
          { complete: done }
        )
      }, delay)
    },
    leave: function (el, done) {
      var delay = el.dataset.index * 150
      setTimeout(function () {
        Velocity(
          el,
          { opacity: 0, height: 0 },
          { complete: done }
        )
      }, delay)
    }
  }
})
```

{% raw %}
<script src="https://cdnjs.cloudflare.com/ajax/libs/velocity/1.2.3/velocity.min.js"></script>
<div id="example-5" class="demo">
  <input v-model="query">
  <transition-group
    name="staggered-fade"
    tag="ul"
    v-bind:css="false"
    v-on:before-enter="beforeEnter"
    v-on:enter="enter"
    v-on:leave="leave"
  >
    <li
      v-for="(item, index) in computedList"
      v-bind:key="item.msg"
      v-bind:data-index="index"
    >{{ item.msg }}</li>
  </transition-group>
</div>
<script>
new Vue({
  el: '#example-5',
  data: {
    query: '',
    list: [
      { msg: 'Bruce Lee' },
      { msg: 'Jackie Chan' },
      { msg: 'Chuck Norris' },
      { msg: 'Jet Li' },
      { msg: 'Kung Fury' }
    ]
  },
  computed: {
    computedList: function () {
      var vm = this
      return this.list.filter(function (item) {
        return item.msg.toLowerCase().indexOf(vm.query.toLowerCase()) !== -1
      })
    }
  },
  methods: {
    beforeEnter: function (el) {
      el.style.opacity = 0
      el.style.height = 0
    },
    enter: function (el, done) {
      var delay = el.dataset.index * 150
      setTimeout(function () {
        Velocity(
          el,
          { opacity: 1, height: '1.6em' },
          { complete: done }
        )
      }, delay)
    },
    leave: function (el, done) {
      var delay = el.dataset.index * 150
      setTimeout(function () {
        Velocity(
          el,
          { opacity: 0, height: 0 },
          { complete: done }
        )
      }, delay)
    }
  }
})
</script>
{% endraw %}

## Reusable Transitions

Transitions can be reused through Vue's component system. To create a reusable transition, all you have to do is place a `<transition>` or `<transition-group>` component at the root, then pass any children into the transition component.

Here's an example using a template component:

``` js
Vue.component('my-special-transition', {
  template: '\
    <transition\
      name="very-special-transition"\
      mode="out-in"\
      v-on:before-enter="beforeEnter"\
      v-on:after-enter="afterEnter"\
    >\
      <slot></slot>\
    </transition>\
  ',
  methods: {
    beforeEnter: function (el) {
      // ...
    },
    afterEnter: function (el) {
      // ...
    }
  }
})
```

And functional components are especially well-suited to this task:

``` js
Vue.component('my-special-transition', {
  functional: true,
  render: function (createElement, context) {
    var data = {
      props: {
        name: 'very-special-transition',
        mode: 'out-in'
      },
      on: {
        beforeEnter: function (el) {
          // ...
        },
        afterEnter: function (el) {
          // ...
        }
      }
    }
    return createElement('transition', data, context.children)
  }
})
```

## Dynamic Transitions

Yes, even transitions in Vue are data-driven! The most basic example of a dynamic transition binds the `name` attribute to a dynamic property.

```html
<transition v-bind:name="transitionName">
  <!-- ... -->
</transition>
```

This can be useful when you've defined CSS transitions/animations using Vue's transition class conventions and want to switch between them.

Really though, any transition attribute can be dynamically bound. And it's not only attributes. Since event hooks are methods, they have access to any data in the context. That means depending on the state of your component, your JavaScript transitions can behave differently.

``` html
<script src="https://cdnjs.cloudflare.com/ajax/libs/velocity/1.2.3/velocity.min.js"></script>

<div id="dynamic-fade-demo" class="demo">
  Fade In: <input type="range" v-model="fadeInDuration" min="0" v-bind:max="maxFadeDuration">
  Fade Out: <input type="range" v-model="fadeOutDuration" min="0" v-bind:max="maxFadeDuration">
  <transition
    v-bind:css="false"
    v-on:before-enter="beforeEnter"
    v-on:enter="enter"
    v-on:leave="leave"
  >
    <p v-if="show">hello</p>
  </transition>
  <button
    v-if="stop"
    v-on:click="stop = false; show = false"
  >Start animating</button>
  <button
    v-else
    v-on:click="stop = true"
  >Stop it!</button>
</div>
```

``` js
new Vue({
  el: '#dynamic-fade-demo',
  data: {
    show: true,
    fadeInDuration: 1000,
    fadeOutDuration: 1000,
    maxFadeDuration: 1500,
    stop: true
  },
  mounted: function () {
    this.show = false
  },
  methods: {
    beforeEnter: function (el) {
      el.style.opacity = 0
    },
    enter: function (el, done) {
      var vm = this
      Velocity(el,
        { opacity: 1 },
        {
          duration: this.fadeInDuration,
          complete: function () {
            done()
            if (!vm.stop) vm.show = false
          }
        }
      )
    },
    leave: function (el, done) {
      var vm = this
      Velocity(el,
        { opacity: 0 },
        {
          duration: this.fadeOutDuration,
          complete: function () {
            done()
            vm.show = true
          }
        }
      )
    }
  }
})
```

{% raw %}
<script src="https://cdnjs.cloudflare.com/ajax/libs/velocity/1.2.3/velocity.min.js"></script>
<div id="dynamic-fade-demo" class="demo">
  Fade In: <input type="range" v-model="fadeInDuration" min="0" v-bind:max="maxFadeDuration">
  Fade Out: <input type="range" v-model="fadeOutDuration" min="0" v-bind:max="maxFadeDuration">
  <transition
    v-bind:css="false"
    v-on:before-enter="beforeEnter"
    v-on:enter="enter"
    v-on:leave="leave"
  >
    <p v-if="show">hello</p>
  </transition>
  <button
    v-if="stop"
    v-on:click="stop = false; show = false"
  >Start animating</button>
  <button
    v-else
    v-on:click="stop = true"
  >Stop it!</button>
</div>
<script>
new Vue({
  el: '#dynamic-fade-demo',
  data: {
    show: true,
    fadeInDuration: 1000,
    fadeOutDuration: 1000,
    maxFadeDuration: 1500,
    stop: true
  },
  mounted: function () {
    this.show = false
  },
  methods: {
    beforeEnter: function (el) {
      el.style.opacity = 0
    },
    enter: function (el, done) {
      var vm = this
      Velocity(el,
        { opacity: 1 },
        {
          duration: this.fadeInDuration,
          complete: function () {
            done()
            if (!vm.stop) vm.show = false
          }
        }
      )
    },
    leave: function (el, done) {
      var vm = this
      Velocity(el,
        { opacity: 0 },
        {
          duration: this.fadeOutDuration,
          complete: function () {
            done()
            vm.show = true
          }
        }
      )
    }
  }
})
</script>
{% endraw %}

Finally, the ultimate way of creating dynamic transitions is through components that accept props to change the nature of the transition(s) to be used. It may sound cheesy, but the only limit really is your imagination.


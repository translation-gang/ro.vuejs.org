---
title: Sintaxa Șablonului
type: guide
order: 4
---

Vue.js utilizează o sintaxă de șablon bazată pe HTML, care vă permite să legați declarativ DOM-ul oferit de datele de bază ale instanței Vue. Toate șabloanele Vue.js sunt valabile în HTML, care pot fi analizate de browsere-le compatibile cu spec și cu parseri HTML.

Sub capotă, Vue compilează șabloanele în funcțiile de redare Virtual DOM. Combinat cu sistemul de reactivitate, Vue este capabil să descopere în mod inteligent cantitatea minimă de componente pentru a redimensiona și aplica cantitatea minimă de manipulări DOM atunci când se modifică starea aplicației.

Dacă sunteți familiarizați cu conceptele DOM virtuale și preferați puterea primară a JavaScript, puteți, de asemenea, să [scrieți în mod direct funcțiile de redare](render-function.html) în locul șabloanelor, cu suport opțional JSX.

## Interpolări

### Text

Forma cea mai elementară de legare a datelor este interpolarea textului folosind sintaxa "Mustache" (acoladă dublă);

``` html
<span>Message: {{ msg }}</span>
```

Eticheta pentru mustață va fi înlocuită cu valoarea proprietății `msg` de pe obiectul corespunzător. De asemenea, acesta va fi actualizat de fiecare dată când proprietatea `msg` se modifică.

De asemenea, puteți efectua interpolări de o singură dată care nu se actualizează la schimbarea datelor utilizând directiva [v-once](../api/#v-once), dar rețineți că aceasta va afecta și orice legare de același nod:

``` html
<span v-once>Aceasta nu se va schimba niciodată: {{ msg }}</span>
```

### HTML-ul Crud

Mustața dublă interpretează datele ca text simplu, nu HTML. Pentru a utiliza HTML-ul real, va trebui să utilizați directiva `v-html`:

``` html
<div v-html="rawHtml"></div>
```

Conținutul acestui "div" va fi înlocuit cu valoarea proprietății `rawHtml`, interpretată ca HTML simplu - legările de date sunt ignorate. Rețineți că nu puteți utiliza v-html pentru a compune șablonul parțial, deoarece Vue nu este un motor de șablon bazat pe șir. În schimb, componentele sunt preferate ca unitate fundamentală pentru reutilizarea și compoziția UI.


<p class="tip"> Redarea dinamică a conținutului HTML arbitrar pe site-ul dvs. poate fi foarte periculoasă deoarece poate duce ușor la [vulnerabilități XSS](https://en.wikipedia.org/wiki/Cross-site_scripting). Utilizați numai interpolarea HTML pe un conținut de încredere și **niciodată** pe conținutul furnizat de utilizator. </p>

### Atribute

Mustațele nu pot fi utilizate în interiorul atributelor HTML, ele folosesc directiva [v-bind](../api/#v-bind):


``` html
<div v-bind:id="dynamicId"></div>
```
De asemenea, funcționează pentru atributele de tip boolean - atributul va fi eliminat dacă condiția este falsă:

``` html
<button v-bind:disabled="isButtonDisabled">Buton</button>
```

### Utilizarea expresiilor JavaScript

Până acum am legat date doar de cheile cu proprietăți simple în șabloanele noastre. Dar Vue.js suportă de fapt puterea completă a expresiilor JavaScript în interiorul tuturor legărilor de date:

``` html
{{ number + 1 }}

{{ ok ? 'YES' : 'NO' }}

{{ message.split('').reverse().join('') }}

<div v-bind:id="'list-' + id"></div>
```

Aceste expresii vor fi evaluate ca JavaScript în domeniul de date al proprietarului instanței Vue. O restricție este că fiecare legare poate conține numai **o singură expresie**, astfel încât următorul lucru **NU** va funcționa:

``` html
<!-- aceasta este o declarație, nu o expresie: -->
{{ var a = 1 }}

<!-- controlul fluxului nu va funcționa niciodată, utilizați expresii ternare -->
{{ if (ok) { return message } }}
```

<p class="tip">Exemplele de șabloane sunt sandboxed și au acces doar la o listă limitată(whitelist) de informații globale, cum ar fi `Math` și `Date`. Nu trebuie să încercați să accesați obiecte globale ale utilizatorului în expresiile de șabloane.</p>

## Directive

Directivele sunt atribute speciale cu prefixul `v-`. Directivele valorilor atributelor se așteaptă să fie **o singură expresie JavaScript** (cu excepția pentru v-for, care va fi discutată mai târziu). Misiunea unei directive este de a aplica reactiv efecte secundare la DOM când se modifică valoarea expresiei sale. Să examinăm exemplul pe care l-am văzut în introducere:

``` html
<p v-if="seen">Acum mă vezi?</p>
```

Aici, directiva `v-if` va elimina/introduce elementul `<p>` bazat pe corectitudinea valorii expresiei `seen`.

### Argumente

Unele directive pot lua un "argument", notat cu două puncte(:) după numele directivei. De exemplu, directiva `v-bind` este folosită pentru a actualiza reactiv un atribut HTML:

``` html
<a v-bind:href="url"></a>
```

Aici `href` este argumentul, care spune directivei v-bind să lege atributul `href` al elementului la valoarea expresiei `url`.

Un alt exemplu este directiva `v-on`, care asculta evenimentele DOM-ului:

``` html
<a v-on:click="doSomething">
```

Aici argumentul este numele acțiunii care va fi ascultat. Vom vorbi mai multe și despre manipularea acțiunilor.

### Modificatorii

Modificatorii sunt postfixe speciale denotate printr-un punct, ceea ce indică faptul că o directivă ar trebui să fie legată într-un mod special. De exemplu, modificatorul `.prevent` declară directiva `v-on` pentru a apela `event.preventDefault()` la evenimentul declanșat:

``` html
<form v-on:submit.prevent="onSubmit"></form>
```
Veți vedea mai multe exemple de modificatori mai târziu, [pentru v-on](events.html#Event-Modifiers) și [pentru `v-model`](forms.html#Modifiers), atunci când vom explora aceste caracteristici.

## Prescurtări

Prefixul `v-` reprezintă o indicație vizuală pentru identificarea atributelor specifice Vue în șabloanele dvs. Acest lucru este util atunci când utilizați Vue.js pentru a aplica un comportament dinamic la unele marcări existente, dar puteți simți mai multe pentru unele directive utilizate frecvent. În același timp, necesitatea prefixului `v-` devine mai puțin importantă atunci când construiți un [SPA](https://en.wikipedia.org/wiki/Single-page_application) unde Vue.js gestionează fiecare șablon. Prin urmare, Vue.js oferă stenograme speciale pentru două dintre cele mai des utilizate directive, `v-bind` și `v-on`:

### Prescurtarea `v-bind`

``` html
<!-- sintaxa completă -->
<a v-bind:href="url"></a>

<!-- prescurtarea -->
<a :href="url"></a>
```

### Prescurtarea `v-on`

``` html
<!-- sintaxa completă -->
<a v-on:click="doSomething"></a>

<!-- prescurtarea -->
<a @click="doSomething"></a>
```

Ele pot arăta un pic diferit de normalul HTML, dar `:` și `@` sunt caractere valabile pentru numele atributelor și toate browserele acceptate de Vue.js pot să o parseze corect. În plus, ele nu apar în marcajul final randat. Sintaxa prescurtată este total opțională, dar probabil că veți aprecia atunci când veți afla mai multe despre utilizarea acesteia mai târziu.

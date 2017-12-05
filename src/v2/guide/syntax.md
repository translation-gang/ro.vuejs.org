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

## Directives

Directives are special attributes with the `v-` prefix. Directive attribute values are expected to be **a single JavaScript expression** (with the exception for `v-for`, which will be discussed later). A directive's job is to reactively apply side effects to the DOM when the value of its expression changes. Let's review the example we saw in the introduction:

``` html
<p v-if="seen">Now you see me</p>
```

Here, the `v-if` directive would remove/insert the `<p>` element based on the truthiness of the value of the expression `seen`.

### Arguments

Some directives can take an "argument", denoted by a colon after the directive name. For example, the `v-bind` directive is used to reactively update an HTML attribute:

``` html
<a v-bind:href="url"></a>
```

Here `href` is the argument, which tells the `v-bind` directive to bind the element's `href` attribute to the value of the expression `url`.

Another example is the `v-on` directive, which listens to DOM events:

``` html
<a v-on:click="doSomething">
```

Here the argument is the event name to listen to. We will talk about event handling in more detail too.

### Modifiers

Modifiers are special postfixes denoted by a dot, which indicate that a directive should be bound in some special way. For example, the `.prevent` modifier tells the `v-on` directive to call `event.preventDefault()` on the triggered event:

``` html
<form v-on:submit.prevent="onSubmit"></form>
```

You'll see other examples of modifiers later, [for `v-on`](events.html#Event-Modifiers) and [for `v-model`](forms.html#Modifiers), when we explore those features.

## Shorthands

The `v-` prefix serves as a visual cue for identifying Vue-specific attributes in your templates. This is useful when you are using Vue.js to apply dynamic behavior to some existing markup, but can feel verbose for some frequently used directives. At the same time, the need for the `v-` prefix becomes less important when you are building an [SPA](https://en.wikipedia.org/wiki/Single-page_application) where Vue.js manages every template. Therefore, Vue.js provides special shorthands for two of the most often used directives, `v-bind` and `v-on`:

### `v-bind` Shorthand

``` html
<!-- full syntax -->
<a v-bind:href="url"></a>

<!-- shorthand -->
<a :href="url"></a>
```

### `v-on` Shorthand

``` html
<!-- full syntax -->
<a v-on:click="doSomething"></a>

<!-- shorthand -->
<a @click="doSomething"></a>
```

They may look a bit different from normal HTML, but `:` and `@` are valid chars for attribute names and all Vue.js supported browsers can parse it correctly. In addition, they do not appear in the final rendered markup. The shorthand syntax is totally optional, but you will likely appreciate it when you learn more about its usage later.

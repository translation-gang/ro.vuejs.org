---
title: Instalarea
type: guide
order: 1
vue_version: 2.5.1
dev_size: "271.12"
min_size: "83.13"
gz_size: "30.33"
ro_gz_size: "21.04"
---

### Nota privind Compatibilitatea

Vue **nu** acceptă IE8 şi mai jos, deoarece utilizează caracteristicile ECMAScript 5 care nu pot fi schimbate în IE8. Cu toate acestea, Vue acceptă toate [browserele compatibile cu ECMAScript 5](http://caniuse.com/#feat=es5).


### Note de Lansare

Informaţiile detaliate despre lansarea versiunilor sunt disponibile pe [GitHub](https://github.com/vuejs/vue/releases).


## Vue Devtools

Atunci când utilizați Vue, vă recomandăm să instalați [Vue Devtools](https://github.com/vuejs/vue-devtools#vue-devtools) în browser-ul dvs., aceasta o să vă permită să inspectați și să depanați aplicațiile dvs. într-o interfață ușor de utilizat.


## Conectarea prin `<script>`

Pur și simplu descărcați fișierul js și conectați-l prin tag-ul `<script>`. `Vue` va fi înregistrat ca variabilă globală.

<p class="tip">Nu utilizați versiunea minimizată în timpul dezvoltării. În caz contrar dvs. nu veți primi avertismente despre greșelile comune!</p>

<div id="downloads">
<a class="button" href="/js/vue.js" download>Versiunea pentru Dezvoltare</a><span class="light info">Cu avertismente și regim de depanare.</span>

<a class="button" href="/js/vue.min.js" download>Versiunea pentru Producție</a><span class="light info">Fără avertismente, {{gz_size}}KB min+gzip</span>
</div>

### CDN

Recomandăm: [https://cdn.jsdelivr.net/npm/vue](https://cdn.jsdelivr.net/npm/vue), aici găsiți cea mai recentă versiune imediat după ce va fi publicată pe **npm**. De asemenea, puteți naviga în sursa package-ului npm la adresa [https://cdn.jsdelivr.net/npm/vue/](https://cdn.jsdelivr.net/npm/vue/).

De asemenea, Vue este disponibil pe [unpkg](https://unpkg.com/vue) și pe [cdnjs](https://cdnjs.cloudflare.com/ajax/libs/vue/{{vue_version}}/vue.js) (sincronizarea cdnjs durează ceva timp, astfel încât cea mai recentă versiune poate să nu fie disponibilă permanent).


## NPM

Recomandăm să utilizați metoda de instalare NPM atunci când creați aplicații de scală largă folosind Vue. Această opțiune funcționează perfect în pereche cu instrumentele de asamblare, cum ar fi [Webpack](https://webpack.js.org/) sau [Browserify](http://browserify.org/). Vue are, de asemenea, instrumente compatibile pentru utilizarea [componentelor cu un singur fișier](single-file-components.html).


``` bash
# ultima versiune stabilă
$ npm install vue
```

## CLI

Vue.js oferă un [CLI oficial](https://github.com/vuejs/vue-cli) pentru crearea rapidă a unei schele pentru aplicațiile ambițioase pe o singură pagină. Șablonurile propuse conțin tot ce este necesar pentru organizarea unei dezvoltări moderne Front-end. Durează doar câteva minute pentru a primi build-urile gata de producție, pregătite și funcționale cu hot-reload și lint-on-save:


``` bash
# instalăm vue-cli
$ npm install --global vue-cli
# creăm un proiect nou utilizând template-ul "webpack"
$ vue init webpack my-project
# instalăm dependențele 
$ cd my-project
$ npm install
$ npm run dev
```

<p class="tip">CLI presupune cunoașterea prealabilă a Node.js și a instrumentelor de ansamblare corespunzătoare. Dacă sunteți începător în Vue sau în instrumente de asamblare front-end, vă sugerăm să treceți prin <a href="./"> ghid </a>, fără folosirea instrumentelor de ansamblare, înainte de a utiliza CLI.


## Explicarea diferitelor build-uri

În  [`dist /` directoriul din pachetul NPM](https://cdn.jsdelivr.net/npm/vue/dist/) veți găsi diverse versiuni ale Vue.js. Iată o prezentare generală a diferenței dintre ele:

| | UMD | CommonJS | ES Module |
| --- | --- | --- | --- |
| **Full** | vue.js | vue.common.js | vue.esm.js |
| **Runtime-only** | vue.runtime.js | vue.runtime.common.js | vue.runtime.esm.js |
| **Full (production)** | vue.min.js | - | - |
| **Runtime-only (production)** | vue.runtime.min.js | - | - |

### Termeni

- **Full**: build-uri care conțin atât compilator, cât și runtime.

- **Compiler**: Cod responsabil de compilarea șablonurilor de tip string în funcții de redare JavaScript.

- **Runtime**: Cod responsabil de crearea instanțelor Vue, de redarea și patching-ul DOM-ului virtual, etc. Practic totul înafara compilatorului.

- **[UMD](https://github.com/umdjs/umd)**: Build-urile UMD pot fi folosite direct în browser printr-o `<script>` etichetă. Fișierul default din jsDelivr CDN din [https://cdn.jsdelivr.net/npm/vue](https://cdn.jsdelivr.net/npm/vue) conține Runtime + Compilatorul UMD build (`vue.js`).

- **[CommonJS](http://wiki.commonjs.org/wiki/Modules/1.1)**: Build-urile comune JS sunt destinate pentru utilizare cu pachete mai vechi, cum ar fi [browserify](http://browserify.org/) sau [webpack 1](https://webpack.github.io). Fișierul default pentru aceste pachete (`pkg.main`) este doar Runtime buildul comun JS (`vue.runtime.common.js`).

- **[ES Module](http://exploringjs.com/es6/ch_modules.html)**: Modulele ES sunt destinate utilizării cu pachete moderne cum ar fi [webpack 2](https://webpack.js.org) sau [rollup](https://rollupjs.org/). Fișierul implicit pentru aceste pachete (`pkg.module`) este construirea modulelor ES numai pentru Runtime (`vue.runtime.esm.js`).

### Runtime + Compilator vs. Runtime-only

Dacă aveți nevoie să compilați șabloane pe client (de exemplu, trecerea unui șir la opțiunea `template` sau montarea într-un element folosindu-l în DOM HTML ca șablon), veți avea nevoie de compilator și prin urmare, construirea completă:

``` js
// aceasta compilatorul cere
new Vue({
  template: '<div>{{ hi }}</div>'
})

// aceasta nu
new Vue({
  render (h) {
    return h('div', this.hi)
  }
})
```
Când se utilizează `vue-loader` sau `vueify`, șabloanele din interiorul fișierelor `* .vue` sunt precompilate în JavaScript la momentul construirii. Nu aveți nevoie de compilatorul din pachetul final și prin urmare, puteți utiliza construirea pentru runtime-only.

Întrucât build-urile runtime-only sunt cu aproximativ 30% mai ușoare decât cele construite complet, ar trebui să le folosiți ori de câte ori este posibil. Dacă totuși doriți să utilizați ansamblul complet, trebuie să configurați aliasul din colector:

#### Webpack

``` js
module.exports = {
  // ...
  resolve: {
    alias: {
      'vue$': 'vue/dist/vue.esm.js' // 'vue/dist/vue.common.js' for webpack 1
    }
  }
}
```

#### Rollup

``` js
const alias = require('rollup-plugin-alias')

rollup({
  // ...
  plugins: [
    alias({
      'vue': 'vue/dist/vue.esm.js'
    })
  ]
})
```

#### Browserify

Adaugați în `package.json` în proiectul dvs.:

``` js
{
  // ...
  "browser": {
    "vue": "vue/dist/vue.common.js"
  }
}
```

### Dezvoltare vs. Modul de Producție

Dezvoltare/modurile de producție sunt greu codificate pentru build-urile UMD: fișierele nefinalizate sunt pentru dezvoltare, iar fișierele miniaturate sunt pentru producție.

CommonJS și build-urile de module ES sunt destinate pentru pachete, prin urmare noi nu le oferim versiuni minifiate. Veți fi responsabil să vă minificați singur pachetul final.

CommonJS și build-urile de module ES, de asemenea, păstrează verificările brute pentru `process.env.NODE_ENV` pentru a determina modul în care ar trebui să ruleze. Ar trebui să utilizați configurațiile corespunzătoare bundler-ului pentru a înlocui aceste variabile de mediu, pentru a controla modul în care funcționa Vue. Înlocuirea procedeului `process.env.NODE_ENV` cu literali de șir permite de asemenea minifierelor, cum ar fi UglifyJS, să renunțe complet la blocurile de cod numai pentru dezvoltare,reducând dimensiunea finală a fișierului.

#### Webpack

Utilizează Webpack [DefinePlugin](https://webpack.js.org/plugins/define-plugin/):

``` js
var webpack = require('webpack')

module.exports = {
  // ...
  plugins: [
    // ...
    new webpack.DefinePlugin({
      'process.env': {
        NODE_ENV: JSON.stringify('production')
      }
    })
  ]
}
```

#### Rollup

Utilizează [rollup-plugin-replace](https://github.com/rollup/rollup-plugin-replace):

``` js
const replace = require('rollup-plugin-replace')

rollup({
  // ...
  plugins: [
    replace({
      'process.env.NODE_ENV': JSON.stringify('production')
    })
  ]
}).then(...)
```

#### Browserify

Aplică o transformare globală [envify](https://github.com/hughsk/envify) în pachetul dvs.

``` bash
NODE_ENV=production browserify -g envify -e main.js | uglifyjs -c -m > build.js
```

De asemenea priviți [Production Deployment Tips](deployment.html).

### CSP environments

Some environments, such as Google Chrome Apps, enforce Content Security Policy (CSP), which prohibits the use of `new Function()` for evaluating expressions. The full build depends on this feature to compile templates, so is unusable in these environments.

On the other hand, the runtime-only build is fully CSP-compliant. When using the runtime-only build with [Webpack + vue-loader](https://github.com/vuejs-templates/webpack-simple) or [Browserify + vueify](https://github.com/vuejs-templates/browserify-simple), your templates will be precompiled into `render` functions which work perfectly in CSP environments.

## Dev Build

**Important**: the built files in GitHub's `/dist` folder are only checked-in during releases. To use Vue from the latest source code on GitHub, you will have to build it yourself!

``` bash
git clone https://github.com/vuejs/vue.git node_modules/vue
cd node_modules/vue
npm install
npm run build
```

## Bower

Only UMD builds are available from Bower.

``` bash
# latest stable
$ bower install vue
```

## AMD Module Loaders

All UMD builds can be used directly as an AMD module.

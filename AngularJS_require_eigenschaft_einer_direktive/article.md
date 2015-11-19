Dass es Direktiven mit eigenen Controllern gibt, dürfte den meisten AngularJS-Entwicklern bekannt sein. Wem dieses Konzept neu ist, kann [hier](https://jsperts.de/blog/ng-ctrl-as-directive) darüber lesen. Was vielleicht nicht allen vertraut ist, ist der vierte Parameter der _link-Funktion_ einer Direktive. In diesem Artikel werden wir sehen, wie wir auf den Controller einer Direktive zugreifen können. Des weiteren werden wir sehen, wie wir Zugriff auf Controller von anderen Direktiven wie z. B. __ng-model__ bekommen können.

Wie schon angedeutet, können wir mit Hilfe des vierten Parameters der _link-Funktion_ Zugriff auf den Controller der Direktive bekommen. Das ist besonders nützlich, wenn wir mit _ControllerAs_ (wie im oben verlinktem Blogartikel beschrieben), einem isoliertem Scope und __bindToController__ arbeiten. Wenn wir nur Interesse an dem Controller der Direktive haben, geht das ganz einfach:

script.js

```javascript
angular.module('directiveTest', [])
.directive('meineDirektive', function() {
    return {
      controller: 'DirCtrl',
      controllerAs: 'dirCtrl',
      bindToController: true,
      scope: {}, // Scope ist isoliert
      link: function(scope, element, attrs, ctrl) {
        console.log(ctrl.name); // 'MeineDirektive'
      }
    };
  })
.controller('DirCtrl', function() {
  this.name = 'MeineDirektive';
});
```

Ein bisschen komplexer wird es, wenn wir Zugriff auf einen anderen oder auf mehrere Controller brauchen. Dafür nutzen wir die __require__-Eigenschaft der Direktive.

### Beispiel: Zugriff auf einen anderen Controller

Ausschnitt aus index.html

```html
<body ng-app="directiveTest">
  <div ng-controller="MainCtrl as mainCtrl">
    <input meine-direktive ng-model="mainCtrl.name" />
  </div>
  <script src="script.js"></script>
</body>
```

script.js

```javascript
angular.module('directiveTest', [])
.directive('meineDirektive', function() {
    return {
      controller: 'DirCtrl',
      controllerAs: 'dirCtrl',
      bindToController: true,
      scope: {}, // Scope ist isoliert
      require: 'ngModel',
      link: function(scope, element, attrs, ctrl) {
        // ctrl ist der Controller der ng-model-Direktive
        console.log(ctrl.$valid); // true
      }
    };
  })
.controller('DirCtrl', function() {
  this.name = 'MeineDirektive';
})
.controller('MainCtrl', function() {
  this.name = 'MainCtrl';
});
```

[Plunker Link für das Beispiel](http://plnkr.co/edit/grM8Ub0MZZ5aa2YIMK02)

Hier haben wir als vierten Parameter der Funktion den Controller der __ng-model__-Direktive statt des Controllers unserer Direktive. Wichtig zu beachten ist, dass __require__ nur mit Controllern von Direktiven funktioniert. Als Wert wird der Name der Direktive erwartet, und als Ergebnis bekommen wir den Controller der Direktive als vierten Parameter der _link-Funktion_. Wir können aber auch Zugriff auf mehrere Controller gleichzeitig bekommen, indem wir ein Array mit Direktivennamen angeben statt des Namens einer einzelnen Direktive.

### Beispiel: Zugriff auf mehrere Controller

Ausschnitt aus index.html

```html
<body ng-app="directiveTest">
  <div ng-controller="MainCtrl as mainCtrl">
    <input meine-direktive ng-model="mainCtrl.name" />
  </div>
  <script src="script.js"></script>
</body>
```

script.js

```javascript
angular.module('directiveTest', [])
.directive('meineDirektive', function() {
    return {
      controller: 'DirCtrl',
      controllerAs: 'dirCtrl',
      bindToController: true,
      scope: {}, // Scope ist isoliert
      require: ['meineDirektive', 'ngModel'],
      link: function(scope, element, attrs, ctrlArray) {
        // ctrlArray[0] ist der Controller unserer Direktive
        console.log(ctrlArray[0].name); // 'MeineDirektive'
        // ctrlArray[1] ist der Controller der ng-model-Direktive
        console.log(ctrlArray[1].$valid); // true
      }
    };
  })
.controller('DirCtrl', function() {
  this.name = 'MeineDirektive';
})
.controller('MainCtrl', function() {
  this.name = 'MainCtrl';
});
```

[Plunker Link für das Beispiel](http://plnkr.co/edit/tED6BEgyrvDNKdukPoF2)

Zum Schluss möchte ich noch erwähnen, dass die Namen der Direktive, die wir bei der __require__-Eigenschaft angeben, auch ein Präfix haben können. Hier noch kurz die verschiedene Präfixe und deren Bedeutung:

* Kein Präfix: Finde den gesuchten Controller auf dem aktuellen Element. Exception wird geworfen, wenn der Controller nicht existiert
* __?__      : Versuche, den Controller auf dem aktuellen Element zu finden. Wenn dieser nicht existiert, ist der vierte Parameter der Funktion __null__
* __^__      : Finde den gesuchten Controller auf dem aktuellen und auf den übergeordneten Elementen. Exception wird geworfen, wenn der Controller nicht existiert
* __^^__     : Finde den gesuchten Controller auf einem übergeordnetem Element. Exception wird geworfen, wenn der Controller nicht existiert
* __?^__     : Wie __^__ aber mit __null__ statt Exception
* __?^^__    : Wie __^^__ aber mit __null__ statt Exception

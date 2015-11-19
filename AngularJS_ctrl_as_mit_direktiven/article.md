Genau wie bei ng-controller und bei einer Route-Definition mittels __ngRoute__ und __UI Router__ steht uns die _ControllerAs_-Schreibweise auch in Direktiven zur Verfügung. Wer mehr über die _ControllerAs_-Schreibweise erfahren möchte, kann [hier (ng-controller mit ControllerAs)](https://jsperts.de/blog/ng-ctrl-as-syntax/) und [hier (ControllerAs mit Route-Definition)](https://jsperts.de/blog/ng-ctrl-as-ng-route) darüber lesen.

### Beispiel: Direktive mit ControllerAs

Ausschnitt aus index.html

```html
<body ng-app="direktiveMitCtrlAs">
  <meine-direktive></meine-direktive>
</body>
```

script.js

```javascript
angular.module('direktiveMitCtrlAs', [])
  .directive('meineDirektive', function() {
    return {
      controller: 'DirCtrl',
      controllerAs: 'dirCtrl',
      template: '<h1>Ich bin eine Direktive</h1><div>Name: {{dirCtrl.name}}</div>'
    };
  })
  .controller('DirCtrl', function() {
    this.name = 'MeineDirektive';
  });
```
[Plunker Link für das Beispiel](http://plnkr.co/edit/QspLywOAwDmvUDwAbjFN)

Im Beispiel haben wir eine Direktive namens "meineDirektive" definiert, und in der index.html-Datei nutzen wir diese Direktive. Wenn jetzt dieser Code in den Browser geladen wird, wird statt __{{dirCtrl.name}}__ der Wert angezeigt, den wir für __this.name__ im Controller angegeben haben. Das ist die einfachste Form, eine Direktive mit _ControllerAs_ zu definieren und zu nutzen. Dies ist auch der erste Schritt zur einer komponentenbasierten Anwendung. In diesem Fall ist eine _Komponente_ eine Direktive mit einer View (__template__ oder __templateUrl__) und einem Controller. So werden auch Komponenten in __AngularJS 2__ definiert, obwohl dort die Schreibweise eine andere ist. Den Controller in der Direktive zu definieren hat den Vorteil, dass man genau sieht, welcher Controller verantwortlich für die View der Direktive ist. Vermutlich wird es auch einfacher, eine solche Direktive in eine AngularJS 2-Komponente umzuschreiben.

Manch einer fragt sich jetzt wahrscheinlich, was passiert, wenn wir mit einem isoliertem Scope arbeiten. Wie kommen wir da an die Daten, ohne einen __$scope__ in den Controller zu injizieren. Zum Glück hat Angular eine Antwort auf diese Frage; sie lautet __bindToController__.

### Beispiel: Direktive mit ControllerAs und isoliertem Scope

Ausschnitt aus index.html

```html
<body ng-app="direktiveMitCtrlAs">
  <div ng-controller="MainCtrl as main">
    <meine-direktive name="main.name"></meine-direktive>
  </div>
</body>
```

script.js

```javascript
angular.module('direktiveMitCtrlAs', [])
  .directive('meineDirektive', function() {
    return {
      controller: 'DirCtrl',
      controllerAs: 'dirCtrl',
      scope: {
        name: '='
      },
      bindToController: true,
      template: '<h1>Ich bin eine Direktive</h1><div>Name: {{dirCtrl.name}}</div><div>Internal Name: {{dirCtrl.internalName}}</div>'
    };
  })
  .controller('MainCtrl', function() {
    this.name = 'MainCtrl';
  })
  .controller('DirCtrl', function() {
    this.internalName = 'MeineDirektive';
  });
```

[Plunker Link für das Beispiel](http://plnkr.co/edit/noAG2kk0zEiDE0t3KNwL)

Mit Hilfe von __bindToController__ haben wir die Eigenschaften des Scope an unseren __dirCtrl__ statt an den Scope gebunden. Somit können wir diese Eigenschaften über den __dirCtrl__-Namespace nutzen, wie wir im Template sehen können. Achtung: Die __name__-Eigenschaft befindet sich nicht mehr im Scope, und somit haben wir auch kein Zugriff auf die Eigenschaft über den __scope__-Parameter der _link-Funktion_. Wer den Zugriff auf diese Eigenschaft braucht, kann den vierten Parameter der _link-Funktion_ nutzen. Sofern die Direktive die __require__-Eigenschaft nicht besitzt, ist der vierte Parameter der Controller der Direktive. Mit Angular 1.4 haben wir noch eine weitere Möglichkeit, externe Eigenschaften an den Controller einer Direktive zu binden und wir können dort _ControllerAs_ auch mit nicht isoliertem Scope nutzen. Aber dies soll in einem anderen Blogartikel behandelt werden.

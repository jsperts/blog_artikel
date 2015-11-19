Die _ControllerAs_-Schreibweise gibt es zwar schon länger (seit AngularJS 1.2.0), hat sich aber in den letzten Monaten zu einer Best practice entwickelt. Es gibt verschiedene Gründe dafür:

* __$scope__-Eigenschaften werden nicht überschrieben bei verschachtelten Controllern/Scopes
* Wir können die Controller als Klassen nutzen mit __this__
* die Schreibweise ist ähnlicher der __AngularJS 2__-Schreibweise für Controller als die klassische Controller-Schreibweise mit __$scope__
* Mit der _controllerAs_-Eigenschaft in Direktiven können wir Komponenten (Direktive + View) implementieren die AngularJS 2- Komponenten ähnlich sind

In diesem Artikel werden wir uns mit der _ControllerAs_-Schreibweise in Kombination mit der __ng-controller__-Direktive beschäftigen. In weiteren Blogartikeln werden wir uns dann _ControllerAs_ mit __ngRoute__ und Direktiven anschauen.

### Klassische Controller-Schreibweise

Ausschnitt aus index.html

```html
<div ng-controller="MainCtrl">
  <span>{{name}}</span>
</div>
```

app.js

```javascript
angular.module('testApp', [])
.controller('MainCtrl', function($scope) {
    $scope.name = 'MyName';
  });
```

In diesem kleinen Beispiel ist es klar, dass __{{name}}__ sich auf __$scope.name__ im __MainCtrl__ bezieht. Was ist aber, wenn wir verschachtelte Scopes/Controller haben? Hier noch ein Beispiel mit verschachtelte Controllern, um die Problematik zu verdeutlichen.

Ausschnitt aus index.html

```html
<div ng-controller="MainCtrl">
  <span>{{name}}</span>
  <div ng-controller="SubCtrl">
    <span>{{name}}</span>
  </div>
</div>
```

app.js

```javascript
angular.module('testApp', [])
.controller('MainCtrl', function($scope) {
    $scope.name = 'MainCtrl';
  })
.controller('SubCtrl', function($scope) {
    $scope.name = 'SubCtrl';
  });
```

Jetzt haben wir zweimal __{{name}}__ im HTML. Welcher Controller definiert den Wert für die zwei Ausdrücke? Diese Frage kann nicht beantwortet werden, ohne im JavaScript-Code nachzuschauen. In unserem Beispiel haben beide Controller die __name__-Eigenschaft im __$scope__, und somit definiert __MainCtrl__ den Wert für den ersten Ausdruck und __SubCtrl__ denjenigen für den zweiten Ausdruck. Wenn wir die _ControllerAs_-Schreibweise nutzen, können wir schon im HTML sehen, welcher Controller den Wert für den jeweiligen Ausdruck definiert, ohne im Code nachschauen zu müssen.

### ControllerAs-Schreibweise

Ausschnitt aus index.html

```html
<div ng-controller="MainCtrl as mainCtrl">
  <span>{{mainCtrl.name}}</span>
</div>
```

app.js

```javascript
angular.module('testApp', [])
.controller('MainCtrl', function() {
  this.name = 'MyName';
});
```

Wenn wir die _ControllerAs_-Schreibweise nutzen, ändern sich 3 Dinge gegenüber der klassische Schreibweise. Im JavaScript-Code nutzen wir jetzt kein __$scope__ mehr. Stattdesen nutzen wir __this__ und definieren dort alle Eigenschaften, die wir sonst im __$scope__ definieren würden. Im HTML haben wir zwei Unterschiede. Als erstes nutzen wir __as__ nach dem Namen des Controllers in der __ng-controller__-Direktive. Mit Hilfe von __as__ definieren wir einen _Namespace_. In unserem Beispiel ist unser Namespace __mainCtrl__. Als zweites nutzen wir unseren Namespace als Präfix für alle _this-Eigenschaften_, also statt __{{name}}__ schreiben wir __{{mainCtrl.name}}__.

Jetzt schauen wir uns nochmal das Beispiel mit den verschachtelten Controllern an, aber diesmal nutzen wir die _ControllerAs_-Schreibweise statt der klassischen Controller-Schreibweise.

```html
<div ng-controller="MainCtrl as mainCtrl">
  <span>{{mainCtrl.name}}</span>
  <div ng-controller="SubCtrl as subCtrl">
    <span>{{mainCtrl.name}}</span>
  </div>
</div>
```

app.js

```javascript
angular.module('testApp', [])
.controller('MainCtrl', function() {
    this.name = 'MainCtrl';
  })
.controller('SubCtrl', function() {
    this.name = 'SubCtrl';
  });
```

Jetzt haben wir für jeden unserer Controller einen eigenen Namespace, __mainCtrl__ für __MainCtrl__ und __subCtrl__ für __SubCtrl__, definiert. Statt __{{name}}__ nutzen wir jetzt __{{_namespace_.name}}__, und es ist schon im HTML klar, dass __MainCtlr__ den Wert für die Ausdrücke definiert, da wir in beiden Fällen das Präfix __mainCtrl__ für den Ausdruck __{{name}}__ benutzt haben. Obwohl der __SubCtrl__ auch eine __name__-Eigenschaft definiert, können wir im HTML genau sagen, ob wir die __name__-Eigenschaft des __MainCtrl__ oder des __SubCtrl__ nutzen möchten. Bei der klassischen Schreibweise ist das nicht so einfach möglich.

Allerdings bringt das _ControllerAs_-Konstrukt nicht nur Vorteile mit sich, sondern auch Nachteile. Erstens müssen wir mehr Code im HTML schreiben, und zweitens müssen wir auf die _this-Bindung_ achten, wenn wir mit Callbacks arbeiten. Aus diesem Grund nutzen viele Entwickler __this__ nicht direkt. Oft sieht man am Anfang des Controllers die Anweisung __var vm = this;__. Dann wird __vm__ benutzt statt __this__, um Eigenschaften und Funktionen zu definieren. Mehr Informationen über die Bindung des _this-Wertes_ finden Sie in unserem Blogartikel [hier](https://jsperts.de/blog/this-binding/).

### ControllerAs mit $scope

Auch wenn wir die _ControllerAs_-Schreibweise nutzen, gibt es Fälle, in denen wir die __$scope__-Variable brauchen, z. B. wenn wir mit Angular-Events arbeiten. Genauso wie das __$scope__-Objekt wird das __this__-Objekt im Controller benutzt, um die View mit dem Modell zu verbinden. Aber das __this__-Objekt hat keine vordefinierte Methoden wie das __$scope__-Objekt. Wenn wir Zugriff auf __$on__, __$broadcast__ und weitere __$scope__-Methoden brauchen, müssen wir den Scope per _Dependency injection_ injizieren. Hier ein Beispiel, wie das funktioniert.

Ausschnitt aus index.html

```html
<div ng-controller="MainCtrl as mainCtrl">
  <span>{{mainCtrl.name}}</span>
</div>
```

app.js

```javascript
angular.module('testApp', [])
.controller('MainCtrl', function($scope) {
  var vm = this;
  vm.name = 'MyName';
  $scope.$on('EventName', function() {
      vm.data = [];
    });
});
```

Hier injizieren wir das __$scope__-Objekt genauso, wie wir es bei der klassischen Schreibweise machen, und weisen den _this-Wert_ der Variable __vm__ zu, um später Probleme bei der _this-Bindung_ zu vermeiden.

Zum Schluss möchte ich noch sagen, dass es sich kaum lohnt, alte Controller umzuschreiben, es ist aber durchaus eine gute Idee, die _ControllerAs_-Schreibweise für neue Controller zu nutzen. Dadurch können wir Fehler vermeiden, und es wird wahrscheinlich einfacher, die _ControllerAs_-Schreibweise nach __AngularJS 2__ zu portieren.

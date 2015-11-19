Als ich das erste Mal mit  _ControllerAs_ (wer möchte kann [hier](http://jsperts.de/blog/ng-ctrl-as-syntax) mehr über die _ControllerAs_-Schreibweise erfahren) und Formularen gearbeitet habe, bin ich auf ein kleines Problem gestoßen. Wie bekomme ich Zugriff auf das Formular in meinem Controller? Das Formular war benannt (das __name__-Attribut des __form__-Tags war gesetzt), aber das Formular war nicht im __this__-Objekt des Controllers. Ich war es gewohnt, mit __$scope__ zu arbeiten, und da wusste ich, dass ich über den Formularnamen Zugriff auf den Formular-Controller bekommen kann, indem ich __$scope.*Formularname*__ benutze. Also habe ich den __$scope__ als Abhängigkeit definiert, um Zugriff auf das Formular zu bekommen. Das sah dann so aus:

Ausschnitt aus index.html

```html
<div ng-controller="MainCtrl as ctrl">
  <form name="myForm">
    <input name="myInput" ng-model="ctrl.name"/>
  </form>
</div>
```

app.js

```javascript
angular.module('testApp', [])
.controller('MainCtrl', function($scope) {
  var vm = this;
  $scope.myForm.$error; // Das $error-Objekt des Formulars

  // Name für ng-model
  vm.name = 'My Name';
});
```

Jetzt hatte ich zwar Zugriff auf das Formular, musste aber das __$scope__-Objekt injizieren. Diese Lösung hat funktioniert, war aber nur mäßig zufriedenstellend. Es musste ja sicherlich auch einen Weg geben, mit Formularen zu arbeiten ohne __$scope__. Nach ein bisschen Tüfteln habe ich die Lösung gefunden. Ich musste dem Formular sagen, dass es zu meinem Controller gehört, und das geht so:

Ausschnitt aus index.html

```html
<div ng-controller="MainCtrl as ctrl">
  <form name="ctrl.myForm">
    <input name="myInput" ng-model="ctrl.name"/>
  </form>
</div>
```

app.js

```javascript
angular.module('testApp', [])
.controller('MainCtrl', function() {
  var vm = this;
  vm.myForm.$error; // Das $error-Objekt des Formulars

  vm.name = 'My Name';
})
```

Die Lösung war denkbar einfach. Ich musste nur __ctrl__ als Präfix für den Formularnamen nutzen, genauso, wie ich es auch beim __ng-model__ getan habe für die __name__-Eigenschaft. Jetzt konnte ich mit meinem Formular arbeiten, ohne den __$scope__ injizieren zu müssen und ohne die _ControllerAs_-Schreibweise aufgeben zu müssen.

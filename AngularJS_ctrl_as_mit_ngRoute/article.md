Letzte Woche habe ich einen Blogartikel über die [_ControllerAs_-Schreibweise](https://jsperts.de/blog/ng-ctrl-as-syntax/) geschrieben. In dem Artikel ging es hauptsächlich um _ControllerAs_ in Kombination mit __ng-controller__. Heute geht es um die _ControllerAs_-Schreibweise in Kombination mit einer Route-Definition. Die Schreibweise wird von dem offiziellen Angular Routing-Modul [ngRoute](https://docs.angularjs.org/api/ngRoute), aber auch von der beliebten Alternative [UI Router](http://angular-ui.github.io/ui-router/site/#/api/ui.router) unterstützt.
Wir werden uns hier auf __ngRoute__ konzentrieren. Die Schreibweise für den __UI Router__ ist analog.

In der Regel definieren wir eine Route, indem wir einen Pfad und ein Konfigurationsobjekt angeben. Der Pfad wir von __ngRoute__ benutzt, um zu entscheiden, welcher Inhalt anzeigt werden soll, wenn der Nutzer zu einer bestimmten URL navigiert. Der Inhalt, sprich das HTML, das angezeigt werden soll, wird im Konfigurationsobjekt angegeben. Wir können es entweder direkt als HTML-String (__template__) oder als Pfad zu einer HTML-Datei (__templateUrl__) angeben. Um das Ganze deutlicher zu machen, hier ein kleines Beispiel.

### Beispiel 1: Route-Definition

Ausschitt aus index.html

```html
<body ng-app="ngRouteCtrlAs">
  <a href="meinPfad">View</a>
  <ng-view>
    <!-- Der Inhalt für die Route -->
  </ng-view>
</body>
```

view.html

```html
<div>Ich bin der Inhalt der View</div>
```

script.js

```javascript
angular.module('ngRouteCtrlAs', ['ngRoute']).config(['$routeProvider' '$locationProvider',
  function($routeProvider, $locationProvider) {
    $routeProvider.when('/', {
        template: '<h1>Hauptseite</h1>'
      }).when('/meinPfad', {
        templateUrl: 'view.html'
      });
    $locationProvider.html5Mode(true);
  }]);
```

In unserem Beispiel haben wir Routen für zwei Pfade definiert. Eine für den Pfad "/" mit View "&lt;h1&gt;Hauptseite&lt;/h1&gt;" (template) und eine für den Pfad "/meinPfad" mit View den Inhalt der view.html-Datei. Jedesmal, wenn der Nutzer nach "/meinPfad" navigiert, wird der Inhalt der __ng-view__-Direktive "Ich bin der Inhalt der View" sein.

In unserem Beispiel haben wir Routen mit Views definiert, aber ohne Controller. In den meisten Fällen ist es sinnvoll, für eine View auch einen Controller zu haben. Wir können, wie unten gezeigt, den Controller mit __ng-controller__ direkt in der View nutzen.

### Beispiel 2: View mit ng-controller

view.html

```html
<div ng-controller="ViewCtrl as viewCtrl">
  Ich bin der Inhalt der View
</div>
```

script.js

```javascript
angular.module('ngRouteCtrlAs', ['ngRoute']).config(['$routeProvider', '$locationProvider',
  function($routeProvider, $locationProvider) {
    $routeProvider.when('/', {
        template: '<h1>Hauptseite</h1>'
      }).when('/meinPfad', {
        templateUrl: 'view.html'
      });
    $locationProvider.html5Mode(true);
  }]).controller('ViewCtrl', function(){});
```

Aber wir können den Controller auch direkt bei der Route-Definition mit angeben. Das hat den Vorteil, dass wir schon bei der Route-Definition sehen können, welcher Controller Herr über eine bestimmte View ist, ohne in der HTML-Datei nachschauen zu müssen; und so sieht das aus:

### Beispiel 3: Controller in der Route-Definition

view.html

```html
<div>Ich bin der Inhalt der View</div>
```

```javascript
angular.module('ngRouteCtrlAs', ['ngRoute']).config(['$routeProvider', '$locationProvider',
  function($routeProvider, $locationProvider) {
    $routeProvider.when('/', {
        template: '<h1>Hauptseite</h1>'
      }).when('/meinPfad', {
        templateUrl: 'view.html'
        controller: 'ViewCtrl'
      });
    $locationProvider.html5Mode(true);
  }]).controller('ViewCtrl', function(){});
```

Unsere zwei Beispiele, 2 und 3, sehen zwar sehr ähnlich aus, sind aber nicht äquivalent. Im ersten Beispiel haben wir die _ControllerAs_-Schreibweise benutzt aber im zweiten Beispiel ist es nicht ganz klar, ob wir mit _ControllerAs_ oder mit der klassische Controller-Schreibweise arbeiten. Tatsächlich arbeiten wir hier mit der klassische Schreibweise. Für die _ControllerAs_-Schreibweise müssen wir noch das __ControllerAs__-Attribut des Konfigurationsobjektes definieren. Und so sieht eine Route-Definition mit der _ControllerAs_-Schreibweise aus:

### Beispiel 4: ControllerAs in der Route-Definition

Ausschnitt aus index.html

```html
<body ng-app="ngRouteCtrlAs">
  <a href="meinPfad">View</a>
  <ng-view>
    <!-- Der Inhalt für die Route -->
  </ng-view>
</body>
```

view.html

```html
<div>Ich bin der Inhalt der View</div>
```

script.js

```javascript
angular.module('ngRouteCtrlAs', ['ngRoute']).config(['$routeProvider' '$locationProvider',
  function($routeProvider, $locationProvider) {
    $routeProvider.when('/', {
        template: '<h1>Hauptseite</h1>'
      }).when('/meinPfad', {
        templateUrl: 'view.html',
        controller: 'ViewCtrl',
        controllerAs: 'viewCtrl
      });
    $locationProvider.html5Mode(true);
  }]).controller('ViewCtrl', function(){});
```

[Link zu Plunker mit Beispiel 4](http://plnkr.co/edit/EXKEA5UTRoo2MCiS1L8G)

Beispiel 4 ist jetzt äquivalent zu Beispiel 2. Der einzige Unterschied ist der Ort, an dem wir den Controller an die View gebunden haben.

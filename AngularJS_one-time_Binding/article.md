Es dürfte mittlerweile bekannt sein, dass AngularJS Performance-Probleme hat, vor allem bei langen Listen mit Datenobjekten, und wenn man dies in Kombination mit __ng-repeat__ nutzt. Der Grund hierfür ist einfach. Alles, was wir in Kombination mit der __ng-bind__ Direktiven oder mit __{{}}__ nutzen, wird von Angular einer sogenannten _$watch-Liste_ hinzugefügt. Bei jedem __$apply()__-Aufruf geht Angular diese Liste durch und überprüft, ob sich etwas in dem Modell geändert hat, und wenn ja, dann wird die Änderung an die View weitergegeben. Dabei spielt es keine Rolle, ob wir __$apply()__ aufrufen oder Angular intern den Aufruf macht. Es ist verständlich, dass, je länger die _$watch-Liste_ ist, desto länger Modelländerungen brauchen, bis sie an die View weitergeleitet werden, und das macht unsere Anwendung träge.

### Beispiel: Ein Lieferservice mit verschiedenen Gerichten

Stellen wir uns vor, wir haben eine Webseite für unseren Lieferservice, bei der Kunden online bestellen können. Die Webseite hat eine Liste von Gerichten, und jedes Gericht hat einen Button, den der Kunde nutzen kann, um das Gericht in seine Bestellung aufzunehmen. So könnte der Controller und das HTML für unsere Seite aussehen.

app.js

```javascript
angular.module('dishes', [])
  .controller('MainCtrl', function($scope) {
    $scope.dishes = []; // Liste mit all unseren Gerichten

    $scope.orderDish = function(dish) {
      dish.inOrder = true;
      // Logic für die Bestellung
    }
  });
```

Ausschnitt aus index.html

```html
<ul>
  <li ng-repeat="dish in dishes">
    <span>Name: {{dish.name}}</span>
    <div>{{dish.inOrder}}
      <button ng-click="orderDish(dish)">Gericht bestellen</button>
    </div>
  </li>
</ul>
```

Für jedes Gericht haben wir zwei _$watches_ (__{{dish.name}}__ und __{{dish.inOrder}}__) und noch ein _$watch_ für das __ng-repeat__. Wenn wir also 30 Gerichte hätten, hätten wir auch 61 _$watch_es. Das für sich alleine ist noch nicht problematisch, aber, wie schon erwähnt, wird Angular alle _$watch_es auf Änderungen überprüfen, sobald __$apply()__ aufgerufen wird. Wenn wir auf den Button klicken, um ein Gericht zu bestellen wird __$apply()__ intern, aufgerufen und Angular wird für alle Modelldaten überprüfen, ob es Änderungen gegeben hat. Was ist aber, wenn wir wissen, dass es für bestimmte Daten keine Änderungen geben kann, wie in unserem Beispiel für den Namen des Gerichts? Hier kommen _One-time bindings_ ins Spiel. Mit deren Hilfe können wir Angular sagen: "Schreib Daten in die View beim Laden, aber danach interessieren uns Änderungen für bestimmte Daten nicht mehr." Indem wir _One-time bindings_ nutzen, können wir Angular anweisen, für bestimmte Daten keine _$watch_es erzeugen, und das macht unsere _$watch-Liste_ kleiner und unsere Anwendung schneller.

### Beispiel mit One-time bindings

Hier haben wir das gleiche Beispiel wie oben, aber diesmal mit _One-time binding_ für den Namen des Gerichts. Unser __app.js__ braucht keine Änderungen, nur die __index.html__-Datei bedarf minimaler Änderungen.

```html
<ul>
  <li ng-repeat="dish in dishes">
    <span>Name: {{::dish.name}}</span>
    <div>{{dish.inOrder}}
      <button ng-click="orderDish(dish)">Gericht bestellen</button>
    </div>
  </li>
</ul>
```

Durch das Hinzufügen von __::__ vor dem __dish.name__ haben wir Angular gesagt, dass es nicht überprüfen soll, ob der Namen des Gerichts sich geändert hat, und somit haben wir die Anzahl der _$watch_es auf 31 reduziert. Überall dort, wo wir mit Daten arbeiten, die sich nach dem initialen Laden nicht mehr ändern, können wir _One-time bindings_ nutzen. In unserem kleinem Beispiel wird die reduzierte Anzahl an _$watch_es kaum einen Unterschied machen, aber je mehr Gerichte wir haben, desto größer ist der Unterschied, d. h. desto langsamer ist unsere Anwendung.

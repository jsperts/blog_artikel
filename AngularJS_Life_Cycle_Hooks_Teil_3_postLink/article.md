<aside style="border: 1px dotted #f37726; padding: 4px; margin-bottom: 20px;">
Dieser Artikel ist der dritte Teil in einer Serie von 5 kleinen Blogartikeln über "life cycle hooks" in Angular 1.5.x.

* [Angular life cycle Hooks. Teil 1: Einführung](https://jsperts.de/blog/angularjs_life_cycle_hooks_teil_1_einfuehrung)
* [Angular life cycle Hooks. Teil 2: $onInit](https://jsperts.de/blog/angularjs_life_cycle_hooks_teil_2_oninit)
* [Angular life cycle Hooks. Teil 3: $postLink](https://jsperts.de/blog/angularjs_life_cycle_hooks_teil_3_postlink)
* Angular life cycle Hooks. Teil 4: $onChanges - Wird demnächst hochgeladen
* Angular life cycle Hooks. Teil 5: $onDestroy - Wird demnächst hochgeladen
</aside>

### $postLink

Nach dem Aufruf des $onInit-Hooks ist der $postLink-Hook an der Reihe.
Der $postLink-Hook ist vergleichbar mit der post-link-Funktion einer Direktive und genau so wie die post-link-Funktion kann dieser benutzt werden, um das DOM zu manipulieren.
Wir müssen also eine Komponente nicht mehr als Direktive definieren, wenn wir den DOM manipulieren möchten.

Der Controller einer Komponente konnte schon immer Zugriff auf das DOM-Element bekommen und zwar über Dependency Injection und __$element__.
Allerdings war es nicht möglich genau zu wissen, wann es sicher war das DOM zu manipulieren.
Wir konnten also nicht wissen, wann Angular mit dem Kompilieren und Link fertig war.
Mit dem $postLink-Hook können wir das DOM der Komponente zum richtigen Zeitpunkt manipulieren, da dieser Hook erst nach dem Kompilieren und Linken der Komponente und Unterkomponenten aufgerufen wird.
Allerdings müssen wir beachten, dass Unterkomponente, die die templateUrl-Eigenschaft nutzten erst nach dem $postLink-Aufruf kompiliert und gelinkt werden, da diese asynchron geladen werden.
Hier ein simples Beispiel.

```javascript
function controllerFn($element) {
  this.$postLink = function() {
    $element.append('<p>Test</p>');
  };
}

var component = {
  template: '<div></div>',
  controller: controllerFn
};

angular.module('app', [])
    .component('myApp', component);
```
[Plunker Link](https://plnkr.co/edit/Kdo0FKLMyNQX0gj1A5mY?p=preview)

### DOM-Manipulation im Controller

Es war schon immer ein Best Practise DOM-Manipulation in Controllers zu vermeiden.
Allerdings gilt dies für Controllers die wir mittels __ng-controller__ definieren.
Der Controller einer Direktive bzw. Komponente darf durchaus das eigene DOM manipulieren und mit Hilfe des $postLink-Hooks können wir auch genau wissen, wann es sicher ist etwas im DOM zu verändern.

### $onInit und $postLink Aufrufreihenfolge

Im Gegensatz zu dem $onInit-Hook, wird der $postLink-Hook von innen nach außen aufgerufen.
Also erst wird der Hook für alle Child-Komponenten aufgerufen und danach für die Überkomponenten.
Hier ein kleines Beispiel mit einer Komponente und einer Unterkomponente, das die Aufrufreihenfolge veranschaulicht.

```javascript
function parentCtrl() {
  this.$onInit = function() {
    console.log('parent init');
  }

  this.$postLink = function() {
    console.log('parent post link');
  }
}

var parentComponent = {
  template: '<child></child>',
  controller: parentCtrl
};

function childCtrl() {
  this.$onInit = function() {
    console.log('child init');
  }

  this.$postLink = function() {
    console.log('child post link');
  }
}

var childComponent = {
  template: '<p>Child</p>',
  controller: childCtrl
};

angular.module('app', [])
    .component('myApp', parentComponent)
    .component('child', childComponent);
```
[Plunker Link](https://plnkr.co/edit/GfKlIG0xDHfaYEPM63WF?p=preview)

Aufrufreihenfolge:

* $onInit im parentCtrl
* $onInit im childCtrl
* $postLink im childCtrl
* $postLink im parentCtrl

### Fazit

Der $postLink-Hook ist der ideale Ort, um DOM-Manipulationen in einer Komponente durchzuführen.
Seit es diesen Hook gibt sind wir nicht mehr gezwungen eine Komponente als Direktive zu implementieren nur, um das DOM zu verändern.


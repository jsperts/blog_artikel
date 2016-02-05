__Update__: 05.02.2016 - Aktualisiert für Angular Version 1.5.0

<del>__Warnung__: Die _component_-Methode, über die wir in diesem Artikel reden, existiert erst ab AngularJS Version 1.5.0.
Diese Version wurde noch nicht veröffentlicht. Für den Artikel nutze ich die Version 1.5.0-rc.0.
Ggf. wird es noch Änderungen an der _component_-Methode geben.
Falls nötig, werde ich nach der Veröffentlichung von Version 1.5.0 den Artikel anpassen.</del>

Komponenten in AngularJS 1.x sind Direktiven mit einem HTML-Template, einem Controller für die Logic und meistens einem isolierten Scope.
Wie den meisten bekannt sein dürfte, kann man in AngularJS Direktiven definieren, indem man die _directive_-Methode eines Angular-Moduls aufruft mit einem String als Namen und einer Factory-Funktion.
Bis jetzt hat man genau diese Methode benutzt, um eine Komponente zu definieren.
Die Version 1.5, von AngularJS bietet dafür eine neue Methode namens _component_ an, mit der wir Komponenten definieren können.
Genauso wie die _directive_-Methode ist auch _component_ eine Methode von Angular-Modulen.
Es ist am einfachsten, anhand eines Beispiels die Unterschiede zwischen der _directive_- und der _component_-Methode zu zeigen.

### Komponente mit .directive(...)

HTML-Template für die Komponente (comp.html)

```html
<input type="number" ng-model="$ctrl.num1"/>
<input type="number" ng-model="$ctrl.num2"/>
<button ng-click="$ctrl.add()" type="button">Add</button>
<p>Result: {{$ctrl.result}}</p>
```

Komponenten-Definition
```javascript
angular.module('testApp', [])
  .directive('numberAdd', function() {
    return {
      controller: function() {
        this.num1 = 0;
        this.num2 = 0;
        this.result = 0;

        this.add = function() {
          this.result = this.num1 + this.num2;
        };
      },
      controllerAs: '$ctrl',
      templateUrl: 'comp.html',
      scope: {}
    };
  });
```

Hier haben wir eine Direktive namens "numberAdd" definiert. Damit aus der Direktive eine Komponente wird, haben wir die Eigenschaften _controller_, _template_ und _scope_ gesetzt.
Um der Angular 2-Syntax näher zu sein, haben wir auch die _controllerAs_-Eigenschaft gesetzt, damit wir __this__ in unserem Controller nutzen können.
Die ControllerAs-Syntax für Direktiven wurde in dem Blogartikel [AngularJS ControllerAs mit Direktiven](https://jsperts.de/blog/ng-ctrl-as-directive/) besprochen.
Übrigens sind Angular 2 Komponenten auch nicht viel mehr als Direktiven mit einem Template (View) und Logic (JavaScript-Klasse).
Es ist sinnvoll, Komponenten einem _ng-controller_ vorzuziehen, vor allem wenn man plant, später Angular 2 zu nutzen.
Mehr Informationen über Angular 2 gibt es in unserem [Angular 2 Kochbuch](https://leanpub.com/angular2kochbuch/read).

### Komponente mit .component(...)

HTML-Template für die Komponente (comp.html)

```html
<input type="number" ng-model="$ctrl.num1"/>
<input type="number" ng-model="$ctrl.num2"/>
<button ng-click="$ctrl.add()" type="button">Add</button>
<p>Result: {{$ctrl.result}}</p>
```

Komponenten-Definition
```javascript
angular.module('testApp', [])
  .component('numberAdd', {
    controller: function() {
      this.num1 = 0;
      this.num2 = 0;
      this.result = 0;

      this.add = function() {
        this.result = this.num1 + this.num2;
      };
    },
    templateUrl: 'comp.html'
  });
```

Wie man sehen kann, ist die Definition einer Komponenten mit Hilfe der _component_-Methode wesentlich einfacher.
Die scope-Eigenschaft muss nicht explizit angegeben werden, da Komponenten standardmäßig einen isolierten Scope haben.
Die controllerAs-Eigenschaft erübrigt sich ebenfalls.
<del>Per Default wird der Name der Komponente als controllerAs-Wert gesetzt; hier "numberAdd", wie wir auch im Template sehen können.
Vermutlich wird sich das in der Zukunft ändern, siehe [#13664](https://github.com/angular/angular.js/issues/13664).</del>
Per Default wird "$ctrl" als controllerAs-Wert gesetzt.
Das sehen wir auch in unserem Template.
Natürlich können wir auch selber ein controllerAs-Werte definieren und den im Template nutzen.
Ein weiterer Unterschied der _component_- gegenüber der _directive_-Methode besteht darin, dass wir bei der _component_-Methode ein Objekt als Komponenten-Definition übergeben und keine Funktion. Auch das macht die Syntax ein bisschen einfacher.
Trotz der Unterschiede ist die _component_-Methode nur ein Wrapper für die _directive_-Methode; intern wird von Angular die _directive_-Methode aufgerufen, um die Komponente zu definieren.

Wichtig zu beachten ist, dass wir Komponenten nur als Tags nutzen können und nicht als Attribute.
Erlaubt:

```html
<number-add></number-add>
```

Nicht erlaubt:

```html
<div number-add></div>
```

Wenn man eine Komponente als Attribut nutzen möchte, muss man diese mit Hilfe der directive-Methode eines Modules definieren.

Weitere Informationen zu der _component_-Methode gibt es in der offiziellen Dokumentation von Angular: [https://code.angularjs.org/1.5.0-rc.0/docs/api/ng/type/angular.Module#component](https://code.angularjs.org/1.5.0-rc.0/docs/api/ng/type/angular.Module#component).

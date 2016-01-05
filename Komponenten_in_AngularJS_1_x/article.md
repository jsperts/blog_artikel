__Warnung__: Die _componente_-Methode über die wir in diesem Artikel reden existiert erst ab AngularJS Version 1.5.0.
Diese Version wurde noch nicht veröffentlicht. Für den Artikel nutze ich die Version 1.5.0-rc.0.
Ggf. wird es noch Änderungen an der _component_-Methode geben.
Falls nötig werde ich nach der Veröffentlichung von 1.5.0 den Artikel anpassen.

Komponenten in AngularJS 1.x sind Direktiven mit einem HTML-Template, ein Controller für die Logic und haben meistens einen isolierten Scope.
Wie den meisten bekannt sein dürfte, kann man in AngularJS Direktiven definieren indem man die _directive_-Methode eines Angular-Moduls aufruft mit einem String als Namen und eine Factory-Funktion.
Bis jetzt hat man genau diese Methode benutzt um eine Komponente zu definieren.
Die nächste Version, Version 1.5, von AngularJS bietet dafür eine neue Methode namens _component_ an, mit derer wir Komponenten definieren können.
Genau so wie die _directive_-Methode, ist auch _component_ eine Methode von Angular-Modulen.
Es ist am einfachsten anhand eines Beispieles die Unterschiede zwischen der _directive_- und der _componet_-Methode zu zeigen.

### Komponent mit .directive(...)

HTML-Template für die Komponente (comp.html)

```html
<input type="number" ng-model="numberAdd.num1"/>
<input type="number" ng-model="numberAdd.num2"/>
<button ng-click="numberAdd.add()" type="button">Add</button>
<p>Result: {{numberAdd.result}}</p>
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
      controllerAs: 'numberAdd',
      templateUrl: 'comp.html',
      scope: {}
    };
  });
```

Hier haben wir eine Direktive namens "numberAdd" definiert. Um wirklich eine Komponente zu haben, haben wir die Eigenschaften _controller_, _template_ und _scope_ gesetzt.
Um näher an die Angular 2 Syntax zu kommen, haben wir auch die _controllerAs_-Eigenschaft gesetzt damit wir __this__ in unserem Controller nutzen können.
Die ControllerAs-Syntax für Direktiven wurde in dem Blogartikel [AngularJS ControllerAs mit Direktiven](https://jsperts.de/blog/ng-ctrl-as-directive/) besprochen.
Übrigens sind Angular 2 Komponenten auch nicht viel mehr als Direktiven mit einem Template (View) und Logic (JavaScript Klasse).
Es ist also eine gute Idee mehr mit Komponenten als mit _ng-controller_ zu arbeiten, vorallem wenn man plant später Angular 2 zu nutzen.
Mehr Informationen über Angular 2 gibt es in unserem [Angular 2 Kochbuch](https://leanpub.com/angular2kochbuch/read).

### Komponent mit .component(...)

HTML-Template für die Komponente (comp.html)

```html
<input type="number" ng-model="numberAdd.num1"/>
<input type="number" ng-model="numberAdd.num2"/>
<button ng-click="numberAdd.add()" type="button">Add</button>
<p>Result: {{numberAdd.result}}</p>
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

Wie man sehen kann ist die Definition einer Komponenten mit Hilfe der _component_-Methode, wesentlich einfacher.
Die scope-Eigenschaft muss nicht explizit angegeben werden da Komponente in diesem Fall automatisch einen isolierten Scope haben.
Die controllerAs-Eigenschaft können wir uns auch sparen.
Per default wird der Name der Komponente als controllerAs-Wert gesetzt.
Hier "numberAdd" wie wir auch im Template sehen können.
Vermutlich wird sich das in der Zukunft ändern, siehe [#13664](https://github.com/angular/angular.js/issues/13664).
Ein weiterer Unterschied der _component_-Methode gegenüber der _directive_-Methode, ist dass wir bei der _component_-Methode eine Objekt als Komponenten-Definition übergeben und keine Funktion. Auch das macht die Syntax ein bisschen einfacher.
Trotz Unterschiede ist die _component_-Methode nur ein Wrapper für die _directive_-Methode. Intern wird von Angular die _directive_-Methode aufgerufen um die Komponente zu definieren.

Weitere Informationen zu der _component_-Methode, gibt es in der offizielle Dokumentation von Angular: [https://code.angularjs.org/1.5.0-rc.0/docs/api/ng/type/angular.Module#component](https://code.angularjs.org/1.5.0-rc.0/docs/api/ng/type/angular.Module#component).

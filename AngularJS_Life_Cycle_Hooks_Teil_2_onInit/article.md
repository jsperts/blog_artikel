<aside style="border: 1px dotted #f37726; padding: 4px; margin-bottom: 20px;">
Dieser Artikel ist der zweiter Teil in einer Serie von 5 kleinen Blogartikeln über "life cycle hooks" in Angular 1.5.x.

* [Angular life cycle Hooks. Teil 1: Einführung](https://jsperts.de/blog/angularjs_life_cycle_hooks_teil_1_einfuehrung)
* [Angular life cycle Hooks. Teil 2: $onInit](https://jsperts.de/blog/angularjs_life_cycle_hooks_teil_2_oninit)
* Angular life cycle Hooks. Teil 3: $postLink - Wird demnächst hochgeladen
* Angular life cycle Hooks. Teil 4: $onChanges - Wird demnächst hochgeladen
* Angular life cycle Hooks. Teil 5: $onDestroy - Wird demnächst hochgeladen
</aside>

### $onInit

Wie schon im ersten Teil der Serie erwähnt, ist der $onInit-Hook der erster Hook der aufgerufen wird.
Dadurch ist dieser der idealer Ort, um initialisierungs Code für die Komponente auszuführen.
Wir können z. B. Standartwerte für das Modell definieren oder Serveranfragen starten, um Daten zu holen. Hier ein kleines Beispiel:

```javascript
function controllerFn() {
  this.$onInit = function() {
    this.data = 'New Data!';
  };
}

var component = {
  template: '<p>{{$ctrl.data}}</p>',
  controller: controllerFn
};

angular.module('app', [])
    .component('myApp', component);
```
[Plunker Link](http://plnkr.co/edit/RWa5HHZLHeMRzvqZXjKM?p=preview)

Natürlich können wir auch initialisierungs Code in der Konstruktorfunktion haben.
Aber die Bindings sprich Daten, die die Parent-Komponente der Child-Komponente übergibt stehen dem Konstruktor nicht zur Verfügung.
Wenn wir also Daten von den Bindings für die Initialisierung brauchen, müssen wir die Initialisierung in dem $onInit-Hook durchführen. Hier ein Beispiel:

```javascript
function parentCtrl() {
  this.number = 2;
}

var parentComponent = {
  template: '<p>Parent</p><child-component num="$ctrl.number"></child-component>',
  controller: parentCtrl
};

function childCtrl() {
  this.$onInit = function() {
    this.timesTwo = this.num * 2;
  };
}

var childComponent = {
  template: '<p>Child: {{$ctrl.timesTwo}}</p>',
  controller: childCtrl,
  bindings: {
    num: '<'
  }
};

angular.module('app', [])
    .component('myApp', parentComponent)
    .component('childComponent', childComponent);
```
[Plunker Link](http://plnkr.co/edit/h6FPsVv7mt0iSZFp5qVN?p=preview)

In diesem Beispiel bekommt die Child-Komponente eine Zahl, die mit zwei multipliziert wird bevor diese angezeigt wird.
Die Zahl wird in der Konstruktorfunktion der Parent-Komponente definiert und mittels [ein-Weg-Datenbindung](https://jsperts.de/blog/angularjs-ein-weg-datenbindung-komponenten/) der Child-Komponente übergeben.
Da wir mit einer Bindung arbeiten, können wir die Multiplikation nicht in der Konstruktorfunktion der Child-Komponente durchführen.
Die num-Eigenschaft ist dort noch nicht definiert.
Wir müssen dafür den $onInit-Hook nutzen.
Allerdings hätten wir in diesem trivialen Beispiel die Multiplikation auch im Template definieren können.

Auch die Controller von Direktiven/Komponenten, die in der require-Eigenschaft referenziert werden, stehen der Konstruktorfunktion nicht zur Verfügung.
Wenn wir also bei der Initialisierung mit so einem Controller interagieren möchten, muss diese Interaktion im $onInit-Hook stattfinden.

### Fazit

Der $onInit-Hook ist der erster Hook der von Angular aufgerufen wird.
Dieser ist hilfreich, wenn wir bei der Initialisierung einer Komponente mit externe Controllers oder mit Bindings interagieren müssen.


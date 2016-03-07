Bis jetzt war es nicht möglich eine ein-Weg-Datenbindung zwischen Angular Komponenten bzw. Direktiven zu definieren.
Wir konnten entweder eine zwei-Weg-Datenbindung mittels __=__ definieren oder Strings mit Hilfe von __@__ von der Parent-Komponente in eine Unterkomponente übergeben.
Das hat sich mit der Version 1.5.0 von Angular geändert.
Jetzt gibt es die Möglichkeit ein-Weg-Datenbindungen mittels __<__ zu definieren.
Wir werden in diesem Blogartikel sehen wie das geht und auf was man achten soll, wenn man ein-Weg-Datenbindungen nutzt.
Wir werden uns auf die bindings-Eigenschaft einer Komponente konzentrieren wie wir die in [Kommunikation zwischen Angular 1.5.x Komponenten](https://jsperts.de/blog/angularjs-kommunikation-zwischen-komponenten/) gesehen haben.
Ein-Weg-Datenbindungen können auch in der scope- bzw. in der bindToController-Eigenschaft einer Direktive definiert werden.

Die Idee hinter der ein-Weg-Datenbindung ist es Daten der parent Komponente einer child Komponente zu übergeben.
Dabei fließen die Daten nur in einer Richtung und zwar von oben (parent) nach unten (child).
Zumindest ist das die Theorie.
In der Praxis werden wir sehen, dass Daten manchmal in beide Richtungen fließen.
Nun ein kleines Beispiel, um die ein-Weg-Datenbindung zu demonstrieren.

```js
var parentComponent = {
  template: '<p>Parent:</p><div>Primitive: {{$ctrl.primitive}}</div><div>Object: {{$ctrl.object}}</div>' +
            '<button ng-click="$ctrl.change()">Change</button>' +
            '<div>Child:<child-component parent-object="$ctrl.object" parent-primitive="$ctrl.primitive"></child-component></div>',
  controller: function($scope) {
    this.primitive = 'I am a string';
    this.object = {
      name: 'Max'
    };
    this.change = function() {
      this.primitive = 'New parent primitive';
      this.object = {
        name: 'Parent change'
      };
    }
  }
};

var childComponent = {
  template: '<div>Primitive: {{$ctrl.childPrimitive}}</div>' +
            '<div>Object: {{$ctrl.childObject}}</div>' +
            '<button ng-click="$ctrl.change()">Change</button>',
  controller: function() {
    this.change = function() {
      this.childPrimitive = 'New child primitive';
      this.childObject = {
        name: 'Child change'
      };
    };
  },
  bindings: {
    childObject: '<parentObject',
    childPrimitive: '<parentPrimitive'
  }
};

angular.module('app', []).
    component('parentComponent', parentComponent).
    component('childComponent', childComponent)
```

[Live Beispiel auf Plunker](http://plnkr.co/edit/HVXrOBeqkt3irMDxnxxs?p=preview)

Erklärung:

Wir haben hier zwei Komponenten definiert die mittels ein-Weg-Datenbindung kommunizieren.
Die parentComponent hat zwei Eigenschaften (primitive und object) und diese werden der childComponent übergeben (Zeile 4).
Wenn wir jetzt auf den Change-Button der parentComponent klicken, werden die Daten in der parentComponent und in der childComponent aktualisiert.
Wenn wir hingegen den Button der childComponent klicken, werden nur die Daten in der childComponent aktualisiert.
Wir haben also eine ein-Weg-Datenbindung wo Datenänderungen in der parentComponent in die childComponent propagiert werden und Änderungen in der childComponent nicht in der parentComponent angezeigt werden.

In beiden Komponenten hatten wir die object-Eigenschaft geändert, indem wir diese durch ein neues Objekt ersetzt hatten.
Wir haben also die Referenz der Eigenschaft geändert.
Solange wir Referenzen ändern, funktionieren ein-Weg-Datenbindungen wie gewollt.
Sobald wir aber interne Eigenschaften eines Objektes ändern, werden diese Änderungen in beide Komponenten sichtbar.
Wir haben hier noch ein kleines Beispiel, um die Aussage zu verdeutlichen.

```js
var parentComponent = {
  template: '<p>Parent:</p><div>Object: {{$ctrl.object}}</div>' +
            '<button ng-click="$ctrl.change()">Change</button>' +
            '<div>Child:<child-component parent-object="$ctrl.object"></child-component></div>',
  controller: function($scope) {
    this.object = {
      name: 'Max'
    };
    this.change = function() {
      this.object.name = 'Parent change';
    }
  }
};

var childComponent = {
  template: '<div>Object: {{$ctrl.childObject}}</div>' +
            '<button ng-click="$ctrl.change()">Change</button>',
  controller: function() {
    this.change = function() {
      this.childPrimitive = 'New child primitive';
      this.childObject.name = 'Child change';
    };
  },
  bindings: {
    childObject: '<parentObject'
  }
};

angular.module('app', []).
    component('parentComponent', parentComponent).
    component('childComponent', childComponent)
```
[Live Beispiel auf Plunker](http://plnkr.co/edit/N0pKS5eG2u3iFDWmBBwN?p=preview)

Erklärung:

Genau wie im Beispiel oben nutzen wir auch hier eine ein-Weg-Datenbindung.
Der Unterschied ist, dass wir hier eine interne Eigenschaft des Objekts ändern und zwar die name-Eigenschaft.
Da Angular bei der Datenbindung die Werte nicht kopiert, sondern die Eigenschaften übergibt haben wir in der childComponent und in der parentComponent die gleiche Referenz auf das Objekt.
Wenn wir die name-Eigenschaft in der parentComponent ändern, wird die Änderung in der childComponent sichtbar.
Wenn wir die name-Eigenschaft in der childComponent ändern, wird die Änderung in der parentComponent sichtbar auch, wenn wir dies vielleicht nicht erwarten, da wir hier von eine ein-Weg-Datenbindung sprechen.
Genau auf dieses Verhalten müssen wir bei ein-Weg-Datenbindungen achten.
Änderungen im Objekt werden in beide Richtungen fließen.

Es ist also sinnvoll ein-Weg-Datenbindungen zu nutzen, wenn wir in der childComponent ein Objekt nicht verändern.
Wenn wir vor haben das Objekt zu verändern, ist es sinnvoller eine zwei-Weg-Datenbinding zu nutzen, um unser Vorhaben auch anderen klar zu machen.


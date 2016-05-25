<aside style="border: 1px dotted #f37726; padding: 4px; margin-bottom: 20px;">
Dieser Artikel ist der vierte Teil in einer Serie von 5 kleinen Blogartikel über "life cycle hooks" in Angular 1.5.x.

* [Angular life cycle Hooks. Teil 1: Einführung](https://jsperts.de/blog/angularjs_life_cycle_hooks_teil_1_einfuehrung)
* [Angular life cycle Hooks. Teil 2: $onInit](https://jsperts.de/blog/angularjs_life_cycle_hooks_teil_2_oninit)
* [Angular life cycle Hooks. Teil 3: $postLink](https://jsperts.de/blog/angularjs_life_cycle_hooks_teil_3_postlink)
* [Angular life cycle Hooks. Teil 4: $onChanges](https://jsperts.de/blog/angularjs_life_cycle_hooks_teil_4_onchanges)
* Angular life cycle Hooks. Teil 5: $onDestroy - Wird demnächst hochgeladen
</aside>

### $onChanges

Der $onChanges-Hook ist der einzige Hook der mehrmals aufgerufen werden kann während des Lebenszyklus einer Komponente.
Genauer gesagt wird dieser jedes mal aufgerufen, wenn sich eine [ein-Weg-Datenbindung](https://jsperts.de/blog/angularjs-ein-weg-datenbindung-komponenten/) oder der Wert einer Interpolation (@) verändert.
Bei Änderungen in zwei-Weg-Datenbindungen wird der Hook nicht aufgerufen.

Der Hook existiert seit Version 1.5.3, allerdings wurde in der Version 1.5.5 sein Verhalten leicht verändert.
In der 1.5.3 Version wurde bei der Initialisierung einer Komponente der Hook nicht aufgerufen.
Dieser wurde nur aufgerufen, wenn nach der Initialisierung sich die Werte von ein-Weg-Datenbindungen bzw. Interpolationen veränderten.
Seit 1.5.5 wird der $onChanges-Hook auch bei der Initialisierung aufgerufen und zwar ist er der erste Hook der aufgerufen wird (auch vor $onInit).
Wir können also jetzt Initialisierungscode, der die Werte von ein-Weg-Datenbindungen braucht auch als Teil des $onChanges-Hooks schreiben.
Für den Beispielcode, werden wir die 1.5.5 Version nutzen.

### $onChanges definieren und nutzen

Genau wie alle andere Hooks, wird der $onChanges-Hook als Eigenschaft der Controller-Instanz definiert.
Der Unterschied ist, dass diesem Hook ein Parameter beim Aufruf übergeben wird.
Dieser Parameter ist ein Objekt mit den Namen der geänderte Bindings als Eigenschaftsnamen und die Werte sind Objekte, die den aktuellen Wert, den alten Wert und eine Funktion beinhalten.
Wir können uns den $onChanges-Hook als ein __$watch__ vorstellen das bei jede Änderung der ein-Weg-Datenbingungen bzw. der interpolierten Werten aufgerufen wird.

```javascript
function controllerFn() {
  this.oneWay = ['a', 'b'];
  this.interpolated = 'a';

  this.changeOneWay = function() {
    this.oneWay = ['c', 'd', 'e'];
  };

  this.changeInterpolated = function() {
    this.interpolated = 'b';
  };
}

var component = {
  template: `
    <p>
      <button type="button" ng-click="$ctrl.changeOneWay()">Change one way</button>
      <button type="button" ng-click="$ctrl.changeInterpolated()">Change Interpolated</button>
    </p>
    <child one-way="$ctrl.oneWay" inter="{{$ctrl.interpolated}}"></child>
  `,
  controller: controllerFn
};

function childControllerFn() {
  this.$onChanges = function(changeObject) {
    console.log(changeObject);
  };
}

var childComponent = {
  template: '',
  controller: childControllerFn,
  bindings: {
    oneWay: '<',
    inter: '@'
  }
};

angular.module('app', [])
    .component('myApp', component)
    .component('child', childComponent);
```
[Plunker Link](https://plnkr.co/edit/7MMkQXbKRYrTacyatRq9?p=preview)

Beim Laden des Beispielcodes sieht das __changeObject__ wie folgt aus:

```javascript
changeObject = {
  inter: {
    currentValue: 'a',
    previousValue: {}
  },
  oneWay: {
    currentValue: ['a', 'b'],
    previousValue: {}
  }
}
```

Wie man sehen kann hat das Objekt zwei Eigenschaften mit Namen "inter" und "oneWay".
Das sind die Namen die wir im bindings-Objekt der Child-Komponente definiert haben.
Jede Eigenschaft ist ein Objekt mit zwei Eigenschaften namens "currentValue" und "previousValue".
Beim ersten Aufruf des $onChanges-Hook ist "previousValue" immer ein leeres Objekt.
Die Eigenschaft "currentValue" repräsentiert den Wert des Bindings nach eine Änderung, "previousValue" ist der Wert vor der Änderung.
Wenn wir jetzt auf den "Change Interpolated"-Button klicken, sieht das __changeObject__ wie folgt aus:

```javascript
changeObject = {
  inter: {
    currentValue: 'b',
    previousValue: 'a'
  }
}
```

Wie wir also sehen beinhaltet das __changeObject__ nur die Bindings die sich tatsächlich geändert haben.

### Erster Aufruf

Es kann in manchen Fällen nützlich sein zu wissen, ob der Hook-Aufruf der initiale Aufruf beim Laden ist.
Dafür gibt es seit 1.5.5 eine Methode namens "isFirstChange".
Jedes Binding hat die eigene isFirstChange-Methode und diese gibt __true__ zurück, wenn die aktuelle Änderung die erste für dieses Binding ist.
Bei weitere Änderungen gibt die Methode __false__ zurück.

```javascript
...

function childControllerFn() {
  this.$onChanges = function(changeObject) {
    if (changeObject.oneWay) {
      console.log(changeObject.oneWay.isFirstChange());
    }
    if (changeObject.inter) {
      console.log(changeObject.inter.isFirstChange());
    }
  };
}

...
```
[Plunker Link](https://plnkr.co/edit/BBMEnvi4tE6w6ZmktFzS?p=preview)

Bei Laden der Anwendung werden die beiden console.log-Aufrufe __true__ sein.
Wenn wir die Werte der Bindings mithilfe der Buttons ändern, wird console.log() __false__ ausgeben.

Der Vorteil vom $onChanges-Hook ist, dass wir jetzt Bindings klonen oder an anderen Variablen zuweisen können und trotzdem mitkriegen, wann sich diese ändern.
Das war auch früher möglich aber wir mussten dafür $watches für jedes Binding definieren.
Mit $onChanges wird uns dies erspart.

### Fazit

Der $onChanges-Hook kann uns bei der Initialisierung der Komponente helfen und informiert uns, wenn sich die Werte der Bindings ändern.
Somit können wir z. B. Arrays klonen und diese verändern ohne, das das Array sich in der Parent-Komponente ändert und falls die Parent-Komponente das Array ändert, können wir dieses im $onChanges-Hook erneut klonen.


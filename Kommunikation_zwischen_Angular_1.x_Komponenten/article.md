Seit Version 1.5.0 von Angular gibt es eine neue Methode mit derer wir Komponenten definieren können.
Wie das geht, haben wir in einem früheren [Blogartikel](https://jsperts.de/blog/angularjs-komponenten/) gezeigt.
In diesem Blogartikel schauen wir uns eine Möglichkeit an, um Daten zwischen verschachtelte Komponenten zu übermitteln.

Was den Datenfluss angeht, gibt es zwei Richtungen:
* von der Parent-Komponente in die verschachtelte Komponente (Parent ➔ Child) und
* von der verschachtelte Komponente zurück in die Parent-Komponente (Child ➔ Parent)

Wir werden uns die zwei Richtungen einzeln anschauen, und zeigen wie wir jede Richtung implementieren können damit die Daten in der Anwendung auch zwischen den Komponenten fließen können.
Bevor wir uns mit den Datenfluss beschäftigen können, müssen wir zwei Komponenten definieren wobei die eine innerhalb der andere sein wird.

## Verschachtelte Komponenten

```js
var parentComponent = {
  template: '<div><child-component></child-component></div>,
  controller: function() {
    var self = this;
    this.data = [1, 2, 3];

    this.setData = function(data) {
      self.data = data;
    }
  }
};

var childComponent = {
  template: '<div>Child data: </div><button ng-click="sendData()">Send Data to Parent</button>',
  controller: function() {
    this.sendData = function() {

    }
  }
};

angular.module('app', []).
    component('parentComponent', parentComponent).
    component('childComponent', childComponent)
```

Erklärung:

Hier sehen wir die Definition von unseren Komponenten. Die eine Komponenten (parentComponent) referenziert die andere (childComponent) in ihrem Template.
Somit haben wir verschachtelte Komponenten definiert.
Unser Ziel ist es jetzt das data-Array der parentComponent in der childComponent zu nutzen und bei klick auf den "Send Data to Parent"-Button Daten von der childComponent der parentComponent zu übergeben.

## Parent ➔ Child

Um Daten der parentComponent in der childComponent nutzen zu können, müssen wir eine Datenbindung definieren.
Die Schreibweise für die Datenbindung in Komponenten ist die gleiche wie diese für Direktiven nur das wir hier statt eine scope- bzw. bindToController-Eigenschaft nutzen, nutzen wir die bindings-Eigenschaft einer Komponenten.
Wer sich mit Datenbindung in der scope-Eigenschaft einer Direktive nicht auskennt, kann in der [Angular API](https://docs.angularjs.org/api/ng/service/$compile#-scope-) lesen wie das geht.
Wir werden uns hier die zwei-Weg-Datenanbindung anschauen, da diese am häufigsten benutzt wird.
Die neue ein-Weg-Datenbindung wird Thema eines anderen Blogartikels sein.

```js
var parentComponent = {
  template: '<div><child-component parent-data="$ctrl.data"></child-component></div>,
  controller: function() {
    var self = this;
    this.data = [1, 2, 3];

    this.setData = function(data) {
      self.data = data;
    }
  }
};

var childComponent = {
  template: '<div>Child data: {{$ctrl.childData}}</div><button ng-click="sendData()">Send Data to Parent</button>',
  controller: function() {
    this.sendData = function() {

    }
  },
  bindings: {
    childData: '=parentData'
  }
};

angular.module('app', []).
    component('parentComponent', parentComponent).
    component('childComponent', childComponent)
```

Erklärung:

Verglichen zum ursprünglichen Code, haben wir jetzt drei Änderungen gemacht.
Eine im parentComponent und zwei im childComponent.

Im parentComponent haben wir im Template eine neues Attribut für unser childComponent definiert.
Das Attribut hat den Namen __parent-data__ und als Wert das data-Array der parentComponent.
Mittels dieses Attributs geben wir der childComponent Zugriff auf die Daten, aber die können wir noch nicht nutzen.
Dafür brauchen wir die bindings-Eigenschaft und die wird in den Zeilen 15-17 definiert.
Die bindings-Eigenschaft hat als Wert ein Objekt und in diesem Objekt sind die einzelne Datenanbindungen definiert die wir haben möchten.
Jede Datenanbindung hat als Eigenschaft den Namen der Bindung den wir intern in unsere Komponente nutzen können, hier __childData__.
Als Wert für die Datenbindung haben wir ein Gleichheitszeichen (=) gefolgt vom Attributsnamen den wir in der parentComponent definiert haben, hier __=parentData__.
Falls der interne Namen und der Attributsnamen gleich sind, können wir die Datenbindung auch nur mit dem Gleichheitszeichen definieren ohne explizit den Attributsnamen anzugeben.
Als letzte Änderung, zeigen wir unser data-Array in der childComponent an.
Dafür haben wir __{{$ctrl.childData}}__ in das Template von der childComponent verwendet.

Jetzt sind wir fertig mit der Definition der Datenbindung und unser data-Array kann als Teil der childComponent angezeigt werden.
Jede Änderung im data-Array (parentComponent) wird auch automatisch in die childComponent propagiert und angezeigt.
Allerdings werden Änderungen im __childData__ auch in der parentComponent sichtbar sein.
Da ist es die beste Strategie die Daten in der childComponent gar nicht zu verändern, sondern die parentComponent zu informieren wann Daten verändert werden müssen.
Wie man das machen könnten, werden wir gleich sehen.

## Child ➔ Parent

Wir haben gesehen wie man mittels zwei-Weg-Datenbindung, Daten von der parentComponent der childComponent übergeben kann.
Hier geht es jetzt um die andere Richtung.
Wir möchten Daten die sich in der childComponent befinden, der parentComponent übergeben.
Um dies zu erreichen, werden wir in der childComponent eine Funktion im Kontext der parentComponent aufrufen.

```js
var parentComponent = {
  template: '<div><child-component parent-data="$ctrl.data" parent-fn="$ctrl.setData(data)"></child-component></div>,
  controller: function() {
    var self = this;
    this.data = [1, 2, 3];

    this.setData = function(data) {
      self.data = data;
    }
  }
};

var childComponent = {
  template: '<div>Child data: {{$ctrl.childData}}</div><button ng-click="sendData()">Send Data to Parent</button>',
  controller: function() {
    this.sendData = function() {
      this.childFn({
        data: 'My data'
      });
    }
  },
  bindings: {
    childData: '=parentData'
    childFn: '&parentFn'
  }
};

angular.module('app', []).
    component('parentComponent', parentComponent).
    component('childComponent', childComponent)
```


Zum Schluß möchte ich noch sagen, dass es sicherlich einfachere Wege gibt, um zwischen Komponenten Daten auszutauschen.
Dieser Weg hat aber gewisse Vorteile:
Als erstes haben unsere Komponente keine Services, Factories oder $scope als Abhängigkeiten.
Als zweitens gibt es ein klares Interface zwischen parentComponent und childComponent und zwar das Objekt der bindings-Eigenschaft.
Wir können die Implementierung von der childComponent problemlos austauschen sofern die neue Implementierung das gleiche Interface besitzt.
Es ist auch einfach die childComponent wieder zu verwenden, da diese keine fest Abhängigkeiten zu der parentComponent besitzt.
Nur die Attribute die der childComponent übergeben werden müssen zum Interface passen.


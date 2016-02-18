Seit Version 1.5.0 von Angular gibt es eine neue Methode mit derer wir Komponenten definieren können.
Wie das geht, haben wir in einem früheren [Blogartikel](https://jsperts.de/blog/angularjs-komponenten/) gezeigt.
In diesem Blogartikel schauen wir uns eine Möglichkeit an, um Daten zwischen verschachtelte Komponenten zu übermitteln.

Was den Datenfluss angeht, gibt es zwei Richtungen:
* von der Parent-Komponente in die verschachtelte Komponente (Parent ➔ Child) und
* von der verschachtelten Komponente zurück in die Parent-Komponente (Child ➔ Parent)

Wir werden uns die zwei Richtungen einzeln anschauen und zeigen wie wir jede Richtung implementieren können, damit die Daten in der Anwendung auch zwischen den Komponenten fließen können.
Bevor wir uns mit den Datenfluss beschäftigen können, müssen wir zwei Komponenten definieren wobei die eine innerhalb der andere sein wird.

## Verschachtelte Komponenten

```js
var parentComponent = {
  template: '<div><child-component></child-component></div>',
  controller: function() {
    this.data = [1, 2, 3];

    this.setData = function(data) {
      this.data = data;
    };
  }
};

var childComponent = {
  template: '<div>Child data: </div><button ng-click="$ctrl.sendData()">Send Data to Parent</button>',
  controller: function() {
    this.sendData = function() {

    };
  }
};

angular.module('app', []).
    component('parentComponent', parentComponent).
    component('childComponent', childComponent)
```

Erklärung:

Hier sehen wir die Definition von unseren Komponenten. Die eine Komponenten (parentComponent) referenziert die andere (childComponent) in ihrem Template.
Somit haben wir verschachtelte Komponenten definiert.
Wir haben jetzt zwei Ziele:
Als Erstes möchten wir das data-Array der parentComponent in der childComponent nutzen.
Als Zweites möchten wir Daten die sich in der childComponent befinden an die parentComponent übergeben.
Das soll passieren, wenn der Nutzer auf den "Send Data to Parent"-Button klickt.

## Parent ➔ Child

Um Daten der parentComponent in der childComponent nutzen zu können, müssen wir eine Bindung definieren.
Die Schreibweise für die Bindung in Komponenten ist die gleiche wie diese für Direktiven.
Was sich ändert ist die Eigenschaft die wir dafür nutzen.
Statt die scope- bzw. bindToController-Eigenschaft einer Direktive, nutzen wir die bindings-Eigenschaft einer Komponente.
Wer sich mit Bindung in der scope-Eigenschaft einer Direktive nicht auskennt, kann in der [Angular API](https://docs.angularjs.org/api/ng/service/$compile#-scope-) lesen wie das geht.
Wir werden uns hier die zwei-Weg-Datenanbindung anschauen, da diese am häufigsten benutzt wird.
Die neue ein-Weg-Datenbindung wird Thema eines anderen Blogartikels sein.

```js
var parentComponent = {
  template: '<div><child-component parent-data="$ctrl.data"></child-component></div>',
  controller: function() {
    this.data = [1, 2, 3];

    this.setData = function(data) {
      this.data = data;
    };
  }
};

var childComponent = {
  template: '<div>Child data: {{$ctrl.childData}}</div><button ng-click="$ctrl.sendData()">Send Data to Parent</button>',
  controller: function() {
    this.sendData = function() {

    };
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

Verglichen zum ursprünglichen Code, haben wir jetzt drei Unterschiede.
Eine in der parentComponent und zwei in der childComponent.
Wir haben im Template der parentComponent ein neues Attribut für unsere childComponent definiert.
Das Attribut hat den Namen __parent-data__ und als Wert das data-Array der parentComponent.
Mittels dieses Attributs geben wir der childComponent Zugriff auf die Daten.
Um die Daten nutzen zu können, brauchen wir die bindings-Eigenschaft die in den Zeilen 15-17 definiert wird.
Die bindings-Eigenschaft hat als Wert ein Objekt und in diesem Objekt sind die einzelnen Bindungen definiert die wir haben möchten.
Jede Bindung hat als Eigenschaftsnamen den Namen der Bindung die wir intern in unsere Komponente nutzen können, hier __childData__.
Als Wert für die Bindung haben wir ein Gleichheitszeichen (=) gefolgt vom Attributsnamen den wir in der parentComponent definiert haben, hier __=parentData__.
Falls der interner Namen und der Attributsnamen gleich sind, können wir die Bindung auch nur mit dem Gleichheitszeichen definieren ohne explizit den Attributsnamen anzugeben.
Die zweite Änderung in den childComponent ist das Anzeigen der Daten.
Dafür haben wir __{{$ctrl.childData}}__ in dem Template von der childComponent verwendet.

Jetzt sind wir fertig mit der Definition der zwei-Weg-Datenbindung und unser data-Array kann als Teil der childComponent angezeigt werden.
Jede Änderung im data-Array (parentComponent) wird auch automatisch in die childComponent propagiert und angezeigt.
Allerdings werden Änderungen im __childData__ auch in der parentComponent sichtbar sein.
Da ist es die beste Strategie die Daten in der childComponent gar nicht zu verändern, sondern die parentComponent zu informieren wann Daten verändert werden müssen.

## Child ➔ Parent

Wir haben gesehen wie man mittels zwei-Weg-Datenbindung, Daten von der parentComponent der childComponent übergeben kann.
Hier geht es jetzt um die andere Richtung.
Wir möchten Daten die sich in der childComponent befinden, der parentComponent übergeben.
Um dies zu erreichen, werden wir in der childComponent eine Methode im Kontext der parentComponent aufrufen.

```js
var parentComponent = {
  template: '<div><child-component parent-data="$ctrl.data" parent-fn="$ctrl.setData(data)"></child-component></div>',
  controller: function() {
    this.data = [1, 2, 3];

    this.setData = function(data) {
      this.data = data;
    };
  }
};

var childComponent = {
  template: '<div>Child data: {{$ctrl.childData}}</div><button ng-click="$ctrl.sendData()">Send Data to Parent</button>',
  controller: function() {
    this.sendData = function() {
      this.childFn({
        data: 'My data'
      });
    };
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

Erklärung:

Wir haben hier drei weitere Änderungen vorgenommen.
Eine in der parentComponent und zwei in der childComponent.
Als Erstes schauen wir uns die parentComponent an.
Dort haben wir im Template ein neues Attribut namens __parent-fn__ definiert.
Der Wert des Attributs ist ein Angular-Ausdruck der uns Zugriff auf die setData-Methode der parentComponent gibt.
Um die Methode in der childComponent aufrufen zu können, müssen wir dafür eine Bindung definieren.
Die Bindungsdefinition funktioniert ähnlich wie oben nur, dass wir hier statt mit einem Gleichheitszeichen mit einem Und-Zeichen/Et-Zeichen (&) arbeiten.
Durch die Bindungsdefinition können wir in der childComponent die Methode __childFn__ aufrufen die wiederum die __$ctrl.setData__ aufruft.
Dies machen wir in den Zeilen 17-19.
Wir rufen aber nicht nur eine Methode auf.
Wir übergeben der Methode auch Parameter.
Damit Angular weiß welcher Wert zur welche Methodenparameter gehört, müssen wir unsere Werte als Objekt übergeben.
Die Eigenschaftsnamen im Objekt passen zu den Parameternamen die wir im Template der parentComponent definiert haben.
In Zeile 2 haben wir __data__ als Parametername für die __setData__ Methode definiert und darum nutzen wir in Zeile 18 __data__ als Eigenschaftsname und "My data" als den Wert der für den Parameter übergeben wird.
In Zeile 8 wird als __data__ gleich "My data" sein.
Nach dem Aufruf von __setData__ wird sich nicht nur die this.data-Eigenschaft der parentComponent ändern, sondern auch die childData-Eigenschaft der childComponent.
Um das Testen zu erleichtern, gib es den Code auch in Plunker: [Beispielcode](http://plnkr.co/edit/B0VTgq61JQ90VAb78N6y?p=preview)

Zum Schluß möchte ich noch sagen, dass es sicherlich einfachere Wege gibt, um zwischen Komponenten Daten auszutauschen.
Dieser Weg hat aber gewisse Vorteile:
Erstens haben unsere Komponente keine Services, Factories oder $scope als Abhängigkeiten.
Zweitens gibt es ein klares Interface zwischen parentComponent und childComponent und zwar das Objekt der bindings-Eigenschaft.
Wir können die Implementierung von der childComponent problemlos austauschen sofern die neue Implementierung das gleiche Interface besitzt.
Es ist auch einfach die childComponent wieder zu verwenden, da diese keine fest Abhängigkeiten zu der parentComponent besitzt.
Nur die Attribute die der childComponent übergeben werden müssen zum Interface passen.


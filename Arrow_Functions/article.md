__Achtung__: _Arrow-Funktionen_ sind Teil der nächsten ECMAScript-Version (ES6/ES2015) und stehen uns derzeit in den meisten Browsern nicht oder nur teilweise zur Verfügung. Wer sie dennoch benutzen möchte, kann einen Transpiler wie Babel oder Traceur nutzen.

Wahrscheinlich kennt jeder das Problem mit dem __this-Wert__ und Callback-Funktionen. Wer das Problem nicht kennt, kann in unserem Post [hier](https://jsperts.de/blog/this-binding) darüber lesen. Eine beliebte Lösung für das Problem ist es, außerhalb der Callback-Funktion den _this-Wert_ einer anderen Variablen zuzuweisen. Oft wird diese Variable __that__, __self__ oder auch __\_this__ benannt. Mit den _Arrow-Funktionen_, auch bekannt als _fat arrow functions_, haben wir eine weitere Lösung für dieses Problem.

Bei einer normalen Funktion/Methode wird der _this-Wert_ beim Aufruf gebunden, aber bei den _Arrow-Funktionen_ wird der _this-Wert_ bei der Definition lexikalisch gebunden. Das hat einige Konsequenzen. In _Arrow-Funktionen_ werden strict-Modus-Regeln für den _this-Wert_ ignoriert. Des weiteren wird bei __call()__- oder __apply()__-Aufrufen in Kombination mit _Arrow-Funktionen_ der gegebene _this-Wert_ auch ignoriert.

Nun ein paar Beispielen um das Ganze zu veranschaulichen.

### Arrow-Funktion mit strict-Modus:

```javascript
// Traditionelle Funktion
var normal = function() {
  'use strict';
  console.log(this);
}
normal(); // undefined

// Arrow-Funktion
var arrow = () => {
  'use strict';
  console.log(this);
}
arrow(); // window-Objekt
```

Im Beispiel sehen wir, dass beim __normal()__-Aufruf der _this-Wert_ __undefined__ ist, da wir __'use strict'__ nutzen. Beim __arrow()__-Aufruf wird aber die __'use strict'__-Anweisung, für den _this-Wert_, ignoriert und darum ist __this__ das _window-Objekt_.

### Arrow-Funktion mit call():

```javascript
var myThisObj = {};

// Traditionelle Funktion
var normal = function() {
  console.log(this);
}
normal.call(myThisObj); // myThisObj

// Arrow-Funktion
var arrow = () => {
  console.log(this);
}
arrow.call(myThisObj); // window-Object (myThisObj wird ignoriert)
```

Wenn wir __normal__ mit Hilfe von __call(myThisObj)__ aufrufen, ist der _this-Wert_ der Funktion __myThisObj__. Hingegen ist das bei __arrow__  nicht so, da der _this-Wert_ schon bei der Definition an das _window-Objekt_ gebunden worden ist.

Am besten eignen sich _Arrow-Funktionen_ als Ersatz für anonyme Callback-Funktionen. Ihre Vorteile: kürzere Syntax, und wir müssen uns keine Gedanken über den _this-Wert_ machen. Hierfür ein Beispiel:

```javascript
var timerArrow = {
  time: 100,
  getTime: function() {
    setTimeout(() => {
      console.log(this.time) // this ist das timer-Objekt
    }, 1000);
  }
};

timerArrow.getTime(); // nach 1 Sekunde wird 100 in der Konsole angezeigt
```

Zum Schluss zeige ich noch weitere Schreibweisen der _Arrow-Funktionen_, je nach Anzahl von Funktionsparametern sowie von Anweisungen im Funktionsrumpf. Unten aufgeführt sind Beispiele von _Arrow-Funktionen_ und die entsprechenden traditionellen Funktionen.

#### Funktion ohne Parameter und nur eine Anweisung im Funktionsrumpf:

```javascript
() => 21

function() {
  return 21;
}
```

#### Funktion mit einem Parameter und mehrere Anweisungen im Funktionsrempf:

```javascript
(param1) => {
  Anweisung1;
  Anweisung2;
}

function (param1) {
  Anweisung1;
  Anweisung2;
}
```

ODER

```javascript
param1 => {
  Anweisung1;
  Anweisung2;
}

function (param1) {
  Anweisung1;
  Anweisung2;
}
```

#### Funktion mit mehreren Parametern:

```javascript
(param1, param2) => 21

function (param1, param2) {
  return 21;
}
```

Mehr Informationen zur _Arrow-Funktionen_ finden Sie bei [Mozilla Developer Network](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions).

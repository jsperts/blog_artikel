Bei einem Funktionsaufruf werden in der Regel alle Argumente auf einmal übergeben, aber es geht auch anders. Manchmal möchten wir ein oder mehrere Argumente fixieren, um dann die Funktion mit weniger Argumenten aufrufen zu müssen. Ein kleines Beispiel dürfte dies illustrieren.

```javascript
function add(a, b) {
  function adder(b) {
    return a + b;
  }
  return adder;
}

var add2 = add(2); // Fixiere erstes Argument
add2(1); // 3
add2(2); // 4
```

Hier haben wir den Parameter __a__ der Funktion __add__ auf den Wert 2 fixiert (Zeile 8). Der Aufruf der __add()__ Funktion hat uns eine neue Funktion mit [Stelligkeit](http://de.wikipedia.org/wiki/Stelligkeit) 1 zurückgegeben (__add()__ ist eine [Funktion höherer Ordnung](https://jsperts.de/blog/higher-order-functions)). Die Funktion namens __add2__ können wir jetzt mit beliebigen Zahlen aufrufen, und das Resultat wird immer _gegebene Zahl_ + 2 sein.

Leider ist unsere __add()__ Funktion nicht sehr flexibel. Sie kann nur ein Argument fixieren. Man kann die Funktion auch so schreiben, dass sie mit beliebig vielen fixierten Argumente funktioniert, aber das ist nicht so einfach. Zum Glück bietet uns JavaScript eine vordefinierte Funktion, mit der man genau dies machen kann. Die Funktion heißt _bind_ und befindet sich im _Function.prototype_-Objekt ([Function.prototype.bind](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)).

Signatur von Function.prototype.bind:

```javascript
fn.bind(thisArg, [arg1], [arg2], ...)
```

__arg1__, __arg2__, __...__ sind die Funktionsargumente, die wir fixieren möchten, und sind optional. __thisArg__ ist der Wert von __this__ in der Funktion. Siehe auch: [Funktionsaufruf mit gebundenem _this_-Wert](https://jsperts.de/blog/this-binding/).

Hier noch ein Beispiel der __add()__ Funktion, aber diesmal mit Hilfe von _Function.prototype.bind_.

```javascript
function add(a, b) {
  return a + b;
}

var add2 = add.bind(undefined, 2); // this ist undefined. Fixiere erstes Argument
add2(1); // 3
add2(2); // 4
```

Wir sehen: Mit Hilfe von __bind()__ ist unser Code kürzer und lesbarer geworden. Die __add()__-Funktion benutzt kein __this__, und in einem solchen Fall können wir bedenkenlos __undefined__ als _thisArg_ übergeben.

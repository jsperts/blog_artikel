Funktionen in JavaScript sind Objekte, die ausführbaren Code beinhalten. Die Funktionen sind Werte, und das ermöglicht es uns, _Funktionen höherer Ordnung_ zu definieren und zu nutzen.

Definition: Funktionen höherer Ordnung sind Funktionen, die:
* eine oder mehrere Funktionen als Parameter erwarten
* eine Funktion als Ergebnis zurückgeben
* beides

JavaScript hat einige eingebaute Methoden, die eine Funktion als Parameter erwarten. Beispiele dafür sind Methoden, die zum Array-Prototyp gehören wie __forEach__, __map__, __sort__ und weitere. Auch Funktionen, die Callbacks erwarten, sind Funktionen höherer Ordnung. Es ist also kaum denkbar, ein JavaScript-Programm zu schreiben, ohne Funktionen höherer Ordnung zu nutzen.
Solche Funktionen können unseren Code lesbarer machen, und sie können uns helfen, unseren Code zu reduzieren.

### Ohne Funktion höherer Ordnung:

```javascript
var array = [1, 2, 3];
function addTwo(val) {
  return val + 2;
}

function addTwoToEveryArrayElement(arr) {
  var resultArray = [];
  for (var i = 0; i &lt; array.length; i++) {
    resultArray(addTwo(array[i]));
  }
  return resultArray;
}

console.log(addTwoToEveryArrayElement(arr)); // [3, 4, 5]
```
### Mit Array.prototype.map:

```javascript
var array = [1, 2, 3];

function addTwo(val) {
  return val + 2;
}

console.log(array.map(addTwo)); // [3, 4, 5]
```

Beide Codebeispiele liefern dasselbe Resultat, aber mit Hilfe von __map()__ ist unser Code kürzer geworden und dadurch auch weniger fehleranfällig. Wer öfters das Wort __length__ schreibt weiß vermutlich, wie einfach es ist, die Buchstaben "t" und "h" zu vertauschen. Die __map()__-Funktion abstrahiert über die Iteration, und somit bleibt uns dieser Fehler erspart.

Natürlich können wir auch eigene Funktionen höherer Ordnung implementieren. Weitere Vorteile von Funktionen höherer Ordnung sind nachzulesen in unserem Blogartikel über [partielle Funktionsanwendung](https://jsperts.de/blog/partial-function-application/).

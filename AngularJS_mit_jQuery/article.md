Es war schon immer einfach, AngularJS in Kombination mit jQuery zu nutzen. Angular selbst bietet uns eine API-kompatible Submenge von jQuery namens jqLite an. Diese Submenge von jQuery implementiert die am meisten genutzten jQuery-Funktionen, um uns die einfache Cross-Browser-DOM-Manipulation in unseren Angular-Anwendungen zu ermöglichen. Wir interagieren mit jqLite jedesmal, wenn wir die Angular _element_-Funktion nutzen. Falls man die vollständige jQuery-Funktionalität braucht, reicht es, in unserer index.html-Datei jQuery vor Angular zu definieren. Somit wird ein _element()_-Aufruf zu einem Alias für den _jQuery()_-Aufruf.

Seit der Version 1.4 haben wir die Möglichkeit, selbst anzugeben, ob wir die _element_-Funktion als jQuery-Ersatz oder als jqLite nutzen möchten, gleichviel, ob jQuery vor Angular geladen wird. Dies können wir mit der __ng-jq__-Direktive tun. Wichtig dabei ist es, die Direktive mit einem Element vor dem script-Tag zu nutzen, bei dem wir Angular laden. Der html-Tag bietet sich für solch ein Element an. Ferner ist es immer noch nötig, jQuery vor Angular zu definieren, falls wir jqLite durch jQuery ersetzen möchten.

Die Nutzung der __ng-jq__-Direktive hat zwei Vorteile:
1. Wir können jQuery frühzeitig definieren und trotzdem jqLite für die _element_-Funktion behalten.
2. Wir können jQuery-ähnliche Bibliotheken verwenden, indem wir __ng-jq__ den Namen der Bibliothek übergeben.

### jqLite beibehalten

Es kann, je nach Projekt, sinnvoll sein, jqLite beizubehalten, um keine jQuery-Abhängigkeit in unserem Angular-Code zu haben. Beispiel: Wir nutzen Bootstrap-Komponenten, die jQuery benötigen, und schreiben gerade unsere Anwendung um mit dem Ziel, Bootstrap zu entfernen. In diesem Fall kann es sinnvoll sein, auch jQuery zu entfernen, um die Anzahl der Bytes zu reduzieren. Es wird schwieriger sein, jQuery zu entfernen, wenn unser Code implizit (über __angular.element__) jQuery-abhängig ist. Um jqLite beizubehalten, reicht es, __ng-jq__ ohne Parameter zu nutzen.

```html
<html ng-app ng-jq>
  <head>
    <script src="jquery.js"></script>
    <script src="angular.js"></script>
  </head>
  <body></body>
</html>
```
Obwohl jQuery vor Angular definiert wird, wird jqLite für die _element_-Funktion verwendet.

### jQuery-ähnliche Bibliothek nutzen

In manchen Fällen möchte man jQuery-ähnliche Bibliotheken nutzen wie z. B. zepto, die eine jQuery-ähnliche API unter einem anderen Namen zur Verfügung stellen. Es kann aber auch sein, dass wir zwei unterschiedliche jQuery-Versionen nutzen und wir eine der beiden Versionen unter einem anderen Namen als dem Standard-jQuery-Namen zur Verfügung stellen. In einem solchen Fall können wir die __ng-jq__-Direktive mit dem Bibliothek-Namen als Parameter nutzen. Der gegebene Name muss ein Attribut des globalen Objektes __window__ sein.

```html
<html ng-app ng-jq="jQueryAehnlich">
  <head>
    <script src="jquery_aehnlich.js"></script>
    <script src="angular.js"></script>
  </head>
  <body></body>
</html>
```
Hier wird die _element_-Funktion durch die _jQueryAehnlich_-Funktion ersetzt.

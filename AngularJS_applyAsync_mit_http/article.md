AngularJS Version 1.3 hatte mehrere Neuerungen mit sich gebracht. Ein davon ist eine __$rootScope__-Funktion namens _$applyAsync_. Mit Hilfe dieser Funktion können wir Angular sagen, dass es __$apply__ zu einem späteren Zeitpunkt aufrufen soll. Laut AngularJS-Dokumentation wird der Aufruf um ~10 Millisekunden verzögert. Somit können wir mehrere Änderungen bündeln die dann in einem _$digest_-Zyklus evaluiert werden. Die _$applyAsync_-Funktion hilft uns auch _$apply already in progress_ [Exceptions](https://docs.angularjs.org/error/$rootScope/inprog?p0=$apply) zu vermeiden. Einfach den _$apply_-Aufruf durch ein _$applyAsync_-Aufruf ersetzen.

Aber jetzt zum eigentlichen Thema dieses Blog-Artikels, __$applyAsync$__ mit __$http__. Es ist oft der Fall, dass wir beim laden einer AngularJS-Anwendung viele _$http_-Anfragen an den Server schicken um Daten für die Anwendung zu bekommen. Jedes mal wenn eine Antwort vorliegt, wird AngularJS ein _$digest_-Zyklus starten um die Daten zu evaluieren und anschliessend anzuzeigen. Je mehr _$digest_-Zyklen die Anwendung durchlaufen muss, desto langsamer wird sie. Um die Anzahl an _$digest_-Zyklen zu reduzieren, können wir __$applyAsync__ nutzen. AngularJS bietet uns ein sehr einfachen Weg um mehreren _$http_-Antworten die fast zeitgleich ankommen in einem _$applyAsync_-Aufruf zu bündeln. Dafür müssen wir nur den __$httpProvider__ konfigurieren. Wie das geht, zeige ich im Code-Beispiel unten.

```javascript
  // Neue angular App definieren
  angular.module('testApp', []).
    config(function($httpProvider) {
      // $http soll $applyAsync nutzen für die Antworten
      $httpProvider.useApplyAsync(true);
    });
```

Diese paar Zeilen Code können erhebliche Performance-Verbesserungen mit sich bringen, vor allem bei größere Anwendungen die in einem begrenzen Zeitraum mehrere Antworten auf _$http_-Anfragen bekommen.

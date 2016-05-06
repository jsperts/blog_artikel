<aside style="border: 1px dotted #f37726; padding: 4px; margin-bottom: 20px;">
Dieser Artikel ist der erster Teil in einer Serie von 5 kleinen Blogartikeln über "life cycle hooks" in Angular 1.5.x.

* [Angular life cycle Hooks. Teil 1: Einführung](https://jsperts.de/blog/angularjs_life_cycle_hooks_teil_1_einfuehrung)
* Angular life cycle Hooks. Teil 2: $onInit - Wird demnächst hochgeladen
* Angular life cycle Hooks. Teil 3: $postLink - Wird demnächst hochgeladen
* Angular life cycle Hooks. Teil 4: $onChanges - Wird demnächst hochgeladen
* Angular life cycle Hooks. Teil 5: $onDestroy - Wird demnächst hochgeladen
</aside>

### Einführung

In Angular hat jede [Komponente](https://jsperts.de/blog/angularjs-komponenten/) einen sogenannten Lebenszyklus, auf Englisch "life cycle".
In diesem Lebenszyklus gibt es bestimmte Ereignisse auf die wir reagieren können, indem wir Angular bestimmte Methoden zur Verfügung stellen.
Diese Methoden werden "life cycle hooks" genannt.
Sie werden als Teil der Controller-Instanz der Komponente definiert.
Derzeit gibt es vier Hooks die wir nutzen können:

* __$onInit__: Wird aufgerufen bei der Initialisierung der Komponente
* __$postLink__: Wird aufgerufen nachdem die Komponente und deren Kindelemente gelinkt wurden sind
* __$onChanges__: Wird aufgerufen immer, wenn sich die [ein-Weg-Datenbindungen](https://jsperts.de/blog/angularjs-ein-weg-datenbindung-komponenten/) der Komponente aktualisieren
* __$onDestroy__: Wird aufgerufen, wenn die Komponente zerstört wird z. B. weil diese nicht mehr angezeigt wird

Der $onInit-Hook existiert seit Angular 1.5.0. Die restlichen Hooks erst seit Version 1.5.3.
Alle Hooks sind optional und werden nur dann aufgerufen, wenn diese definiert sind und das entsprechende Ereignis eintritt.
Da Komponenten nur spezialisierte Direktiven sind, können wir diese Hooks auch für den Controller einer Direktive definieren.

Die Reihenfolgen in derer die Hooks aufgerufen werden ist wie folgt:

* $onInit
* $postLink
* $onChanges
* $onDestroy

Die __Init__ und __postLink__ Ereignisse gibt es immer für alle Komponenten.
Das __Change__ Ereignis existiert nur bei Komponenten die ein-Weg-Datenbindungen besitzen, die sich während der Laufzeit ändern.
Beim Laden der Anwendung wird der $onChanges-Hook nicht aufgerufen.
Das __Destroy__ Ereignis ist relevant nur für Komponenten, die initialisiert worden sind und später nicht mehr angezeigt werden.
Ähnliche Hooks aber mit leicht veränderten Namen werden auch von Angular 2 bereit gestellt.
Das ist auch ein Grund weshalb wir in Angular 1.5.3 Hooks haben.
Ziel ist es die Unterschiede zwischen Angular 1 und 2 zu verringern.

Im nächsten Artikel werden wir uns den $onInit-Hook genauer anschauen und sehen wie wir diesen bei der Implementierung unserer Komponenten nutzen können.

### Fazit

Life cycle Hooks bringen unsere Komponenten ein Schritt näher zu Angular 2 Komponenten und können uns auch bei der Implementierung helfen, indem diese uns die Reaktion auf bestimmte Ereignisse ermöglichen.

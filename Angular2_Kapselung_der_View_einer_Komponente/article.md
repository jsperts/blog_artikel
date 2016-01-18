__Warnung__: Angular 2 befindet sich noch im Beta-Stadion. Es ist möglich, dass manche Sachen die hier beschrieben sind, in der Zukunft nicht oder anders funktionieren.
In dem Artikel wird die Version 2.0.0-beta.1 verwendet.

Es wird erwartet das die Leser des Artikels, rudimentäre Angular 2 Kenntnisse haben. Mehr Informationen über Angular 2 gibt es in unser [Angular 2 Kochbuch](https://leanpub.com/angular2kochbuch/read)

Normalerweise wenn man CSS nutzt, um das Design einer Webseite festzulegen, wird das Design global für die Webseite angewendet.
Es ist dabei egal, ob wir inline-styles mittels style-Tag oder CSS-Dateien mittels link-Tag nutzten.
In Angular 2 hat man die Möglichkeit den Anwendungsbereich von CSS-Styles zu begrenzen und zwar auf einzelnen Komponenten und deren View.
Diese Art von Begrenzung nennt man in Angular 2 __View Encapsulation__.
Angular bietet drei Möglichkeiten um diese Kapselung zu erreichen und die sind:
1. Keine Kapselung (ViewEncapsulation.None)
2. Emulierte Kapselung (ViewEncapsulation.Emulated)
3. Shadow DOM (ViewEncapsulation.Native)

Die möglichen Optionen werden in [ViewEncapsulation](https://angular.io/docs/ts/latest/api/core/ViewEncapsulation-enum.html) definiert.
Wir werden uns jetzt diese drei Möglichkeiten genauer anschauen.

### Keine Kapselung

In diesem Fall wird keine Kapselung angewendet und alle CSS-Styles werden wie gewohnt angewendet.
Das ist das Default-Verhalten, wenn eine Komponente keine eigene CSS-Styles definiert.
Falls unsere Komponente, eigene CSS-Styles definiert, können wir dieses Verhalten erzwingen in dem wir die encapsulation-Eigenschaft der View auf ViewEncapsulation.None setzen.
Natürlich können wir CSS-Styles die wir global definiert haben, auch im Template unserer Komponente nutzen.

#### Beispiel-Komponente mit CSS-Styles und ViewEncapsulation.None

```javascript
import {Component, View, ViewEncapsulation} from 'angular2/core';

@Component({
  selector: 'my-app'
})
@View({
  template: `
    <style>
      .box {
        width: 100px;
        height: 100px;
        border: 1px solid black;
      }
    </style>
    <div class="box"></div>
  `,
  encapsulation: ViewEncapsulation.None
})
class MyApp {}
```

#### DOM der aus unsere Komponente generiert wird

```html
<!DOCTYPE html>
<html>
  <head>
    <style>
      .box {
        width: 100px;
        height: 100px;
        border: 1px solid black;
      }
    </style>
  </head>
  <body>
    <my-app>
      <div class="box"></div>
    </my-app>
  </body>
</html>
```

Erklärung:

Wie wir hier sehen kann, werden die CSS-Styles aus dem Template der Komponenten herausgezogen und im head-Element global definiert.
Das Problem dabei: falls mehrere Komponenten die box-Klasse definieren bzw. falls die box-Klasse schon vorher global definiert ist, werden die späteren Definitionen der Klasse das initiale Verhalten von "box" überschreiben.
In einem kleinen Projekt ist es nicht so schwer eindeutige Namen für die CSS-Klassen zu nutzen, aber je größer das Projekt, desto schwerer wird es.
Vor allem steigt die Gefahr wenn man 3rd-Party-Komponenten nutzt, die auch ViewEncapsulation.None gesetzt haben.

### Emulierte Kapselung

In diesem Fall werden die CSS-Styles die wir in unsere Komponente definieren, von globalen CSS-Styles und von CSS-Styles die anderen Komponenten definieren gekapselt.
Die CSS-Styles von unserer Komponente, werden nur auf das Template der Komponente angewendet.
Das ist das Default Verhalten, wenn eine Komponente eigene CSS-Styles definiert.
Falls eine Komponente keine CSS-Styles definiert, können wir dieses Verhalten erzwingen in dem wir ViewEncapsulation.Emulated für die encapsulation-Eigenschaft setzen.
CSS-Styles die global definiert worden sind, können weiterhin in unsere Komponente benutzt werden.

#### Beispiel-Komponente mit CSS-Styles und ViewEncapsulation.None

```javascript
import {Component, View, ViewEncapsulation} from 'angular2/core';

@Component({
  selector: 'my-app'
})
@View({
  template: `
    <style>
      .box {
        width: 100px;
        height: 100px;
        border: 1px solid black;
      }
    </style>
    <div class="box"></div>
  `,
  encapsulation: ViewEncapsulation.Emulated
})
class MyApp {}
```

#### DOM der aus unsere Komponente generiert wird

```html
<!DOCTYPE html>
<html>
  <head>
    <style>
      .box[_ngcontent-khh-1] {
        width: 100px;
        height: 100px;
        border: 1px solid black;
      }
    </style>
  </head>
  <body>
    <my-app _nghost-khh-1>
      <div _ngcontent-khh-1 class="box"></div>
    </my-app>
  </body>
</html>
```

Erklärung:

Genau wie im Beispiel ohne Kapselung, wurden unsere CSS-Styles in das head-Element geschrieben.
Nur diesmal wird den CSS-Selektor noch ein Attribut hinzugefügt.
Wenn wir den DOM genauer betrachten werden wir sehen, dass das div in dem my-app-Tag genau dieses Attribut auch besitzt.
Mit Hilfe von HTML-Attributen, kann Angular den Anwendungsbereich eines CSS-Styles beschränken.
In diesem Fall wird der Anwendungsbereich der box-Klasse auf Elementen mit dem \_ngcontent-khh-1-Attribut beschränkt.
Angular ist klug genug, dieses konkrete Attribut nur an Elementen unserer my-app-Komponente zu vergeben.
Weitere Komponente bekommen andere Attribute.

Der eine oder andere Leser/Leserin mag sich jetzt fragen was das \_nghost-khh-1-Attribut zu bedeuten hat und warum wir diese Art von Kapselung als "emuliert" bezeichnen.
Emuliert bezieht sich auf die echte Kapselung die man durch den Shadow DOM erreichen kann.
Wenn wir den Shadow DOM benutzen, wird eine Komponente in zwei Teile aufgespaltet.
In einem Host-Element, das ist der Tag den wir in der Komponente als Selektor nutzt (hier "my-app") und dem Content.
Der Content ist der Inhalt der template-Eigenschaft einer Komponente.
Da "my-app" ein Host-Element ist, hat es die Bezeichnung "\_nghost" im Namen des Attributs.
Die drei Zeichen nach dem minus definieren einen internen Namen für unsere Komponente und die Zahl signalisiert die Tiefe in der sich eine Komponente befindet.
Hätten wir z. B. noch eine weitere Komponente innerhalb der my-app-Komponente, hätte diese die Zahl 2.

Der Vorteil von ViewEncapsulation.Emulated, ist dass man auch ohne Shadow DOM Unterstützung eine adäquate Kapselung der CSS-Styles von einzelnen Komponenten erreichen können.
CSS-Styles die in Komponenten definiert wurden haben keine Auswirkung auf CSS-Styles die global definiert worden sind.

### Shadow DOM

Mit Hilfe vom Shadow DOM, können wir unsere Komponente komplett von globalen CSS-Styles und von CSS-Styles anderer Komponenten kapseln.
Allerdings muss der Browser die Shadow DOM API unterstützen damit wir diese Kapselungs-Möglichkeit nutzen können.
Derzeit wird die Shadow DOM API nur von Chrome und Opera unterstützt.
Um Shadow DOM zu nutzen, müssen wir die encapsulation-Eigenschaft auf ViewEncapsulation.Native setzen.

#### Beispiel-Komponente mit CSS-Styles und ViewEncapsulation.Native

```javascript
import {Component, View, ViewEncapsulation} from 'angular2/core';

@Component({
  selector: 'my-app'
})
@View({
  template: `
    <style>
      .box {
        width: 100px;
        height: 100px;
        border: 1px solid black;
      }
    </style>
    <div class="box"></div>
  `,
  encapsulation: ViewEncapsulation.Native
})
class MyApp {}
```

#### DOM der aus unsere Komponente generiert wird

```html
<!DOCTYPE html>
<html>
  <head></head>
  <body>
    <my-app>
      #shadow-root
      |  <style>
      |    .box {
      |      width: 100px;
      |      height: 100px;
      |      border: 1px solid black;
      |     }
      |  </style>
      |  <div class="box"></div>
    </my-app>
  </body>
</html>
```

Erklärung:

Dieses mal wurden die CSS-Styles der Komponente nicht in das head-Element geschrieben, sondern als Teil des Contents in den shadow-root.
Das HTML-Template der template-Eigenschaft und die CSS-Styles der Komponente, bilden den Content für den Shadow DOM.
Bei der emulierte Kapselung wurde erwähnt, dass der Shadow DOM unsere Komponente in zwei teilt.
Genauer gesagt wird die Komponente in drei geteilt.
Es gibt einmal das Host-Element, hier "my-app", den shadow root (#shadow-root) der als root-Element für den Content fungiert.
Alles was sich in dem shadow root befindet ist vom restlichen DOM-Bau getrennt.

Mit Shadow DOM, können wir die größtmögliche Kapselung erreichen, allerdings nur wenn der Browser Shadow DOM auch unterstützt.
Für jetzt ist die emulierte Kapselung unsere beste Möglichkeit und auch, die die ich empfehlen würde.


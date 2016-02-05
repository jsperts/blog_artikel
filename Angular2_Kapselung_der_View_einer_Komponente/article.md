__Update__: 05.02.2016 - Manche Formulierungen wurden korrigiert. Dank an Pascal Precht für sein Feedback.

__Warnung__: Angular 2 befindet sich noch im Beta-Stadium. Es ist möglich, dass manches, das hier beschrieben ist, in der Zukunft nicht oder anders funktioniert.
In dem Artikel wird die Version 2.0.0-beta.1 verwendet.

Es wird erwartet, dass die Leser des Artikels rudimentäre Angular 2- Kenntnisse haben. Mehr Informationen über Angular 2 gibt es in unserem [Angular 2 Kochbuch](https://leanpub.com/angular2kochbuch/read)

Normalerweise wird, wenn man CSS nutzt, um das Design einer Webseite festzulegen, das Design global auf die Webseite angewendet.
Es ist dabei gleich, ob wir Inline-Styles mittels style-Tag oder CSS-Dateien mittels link-Tag nutzen.
Angular 2 erlaubt es, den Anwendungsbereich von CSS-Styles zu begrenzen, und zwar auf einzelne Komponenten und deren View.
Diese Art der Begrenzung nennt man in Angular 2 __View Encapsulation__.
Angular bietet drei Möglichkeiten, um diese Kapselung zu erreichen; diese sind:
1. Keine Kapselung (ViewEncapsulation.None)
2. Emulierte Kapselung (ViewEncapsulation.Emulated)
3. Shadow DOM (ViewEncapsulation.Native)

Die Optionen werden in [ViewEncapsulation](https://angular.io/docs/ts/latest/api/core/ViewEncapsulation-enum.html) definiert.
Wir werden uns jetzt diese drei Möglichkeiten genauer anschauen.

### Keine Kapselung

In diesem Fall wird keine Kapselung angewendet, und alle CSS-Styles werden wie gewohnt angewendet.
Das ist das Default-Verhalten, wenn eine Komponente keine eigenen CSS-Styles definiert.
Falls unsere Komponente eigene CSS-Styles definiert, können wir dieses Verhalten erzwingen, indem wir die encapsulation-Eigenschaft der View auf ViewEncapsulation.None setzen.
Natürlich können wir CSS-Styles, die wir global definiert haben, auch im Template unserer Komponente nutzen.

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

#### DOM, das aus unserer Komponente generiert wird

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

Wie wir hier sehen können, werden die CSS-Styles aus dem Template der Komponenten herausgezogen und im head-Element global definiert.
Das Problem dabei: Falls mehrere Komponenten die box-Klasse definieren bzw. falls die box-Klasse schon vorher global definiert ist, werden die späteren Definitionen der Klasse das initiale Verhalten von "box" überschreiben.
In einem kleinen Projekt ist es nicht schwer, eindeutige Namen für die CSS-Klassen zu nutzen, aber je größer das Projekt, desto schwieriger wird es.
Vor allem steigt die Gefahr, dass Styles überschrieben werden, wenn man 3rd-Party-Komponenten nutzt, die auch ViewEncapsulation.None gesetzt haben.

### Emulierte Kapselung

In diesem Fall werden die CSS-Styles, die wir in unserer Komponente definieren, von globalen CSS-Styles und von CSS-Styles, die andere Komponenten definieren, gekapselt.
Die CSS-Styles unserer Komponente werden nur auf das Template der Komponente angewendet.
Das ist das Default-Verhalten, wenn eine Komponente eigene CSS-Styles definiert.
Falls eine Komponente keine CSS-Styles definiert, können wir dieses Verhalten erzwingen, indem wir ViewEncapsulation.Emulated für die encapsulation-Eigenschaft setzen.
CSS-Styles, die global definiert worden sind, können weiterhin in unserer Komponente benutzt werden.

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

#### DOM, das aus unserer Komponente generiert wird

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

Genau wie im Beispiel ohne Kapselung wurden unsere CSS-Styles in das head-Element geschrieben.
Nur wird diesmal dem CSS-Selektor noch ein Attribut hinzugefügt.
Wenn wir das DOM genauer betrachten, werden wir sehen, dass das _div_ in dem my-app-Tag genau dieses Attribut auch besitzt.
Mit Hilfe von HTML-Attributen kann Angular den Anwendungsbereich eines CSS-Styles beschränken.
In diesem Fall wird der Anwendungsbereich der box-Klasse auf Elemente mit dem \_ngcontent-khh-1-Attribut beschränkt.
Angular ist klug genug, dieses konkrete Attribut nur an Elemente unserer my-app-Komponente zu vergeben.
Weitere Komponenten bekommen andere Attribute.

Der/die eine oder andere Leser/Leserin mag sich jetzt fragen, was das \_nghost-khh-1-Attribut zu bedeuten hat und warum wir diese Art der Kapselung als "emuliert" bezeichnen.
Emuliert bezieht sich auf die echte Kapselung, die man durch das Shadow DOM erreichen kann.
Wenn wir das Shadow DOM benutzen, wird ein __shadow root__ erzeugt und dadurch wird unser Tag (hier "my-app") zu einem sogenannten Host-Element.
Der Inhalt der template-Eigenschaft wird zum Inhalt des Shadow DOMs.
Da "my-app" ein Host-Element ist, hat es die Bezeichnung "\_nghost" im Namen des Attributs.
Die drei Zeichen nach dem Minus definieren einen internen Namen für unsere Komponente, und die Zahl signalisiert die Tiefe, in der sich eine Komponente befindet.
Hätten wir z. B. noch eine weitere Komponente innerhalb der my-app-Komponente, hätte diese die Zahl 2.

Der Vorteil von ViewEncapsulation.Emulated ist, dass man auch ohne Shadow DOM-Unterstützung eine adäquate Kapselung der CSS-Styles einzelner Komponenten erreichen kann.
CSS-Styles, die in Komponenten definiert wurden, haben keine Auswirkung auf global definierte CSS-Styles.

### Shadow DOM

Mit Hilfe des Shadow DOM können wir unsere Komponente komplett vor globalen CSS-Styles und vor solchen anderer Komponenten kapseln.
Allerdings muss der Browser die Shadow DOM-API unterstützen, damit wir diese Kapselungsmöglichkeit nutzen können.
Derzeit wird die Shadow DOM-API nur von Chrome und Opera unterstützt.
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

#### DOM, das aus unserer Komponente generiert wird

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

Dieses Mal wurden die CSS-Styles der Komponente nicht in das head-Element geschrieben, sondern als Teil des Content in die shadow-root.
Das HTML-Template der template-Eigenschaft und die CSS-Styles der Komponente, bilden den Content für das Shadow DOM.
Alles, was sich in der shadow root befindet, ist vom restlichen DOM-Bau getrennt.

Mit Shadow DOM, können wir die größtmögliche Kapselung erreichen, allerdings nur wenn der Browser Shadow DOM auch unterstützt.
Für jetzt ist die emulierte Kapselung unsere beste Möglichkeit und auch, die die ich empfehlen würde.


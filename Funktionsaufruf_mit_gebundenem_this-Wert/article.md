Es gibt zwei Hauptfälle, in denen es sinnvoll ist, den _this-Wert_ selbst zu definieren. Bei einer _Callback-Funktion_, und wenn man eine Methode einer Variable zuweisen möchte. Bevor wir uns mit der _this-Bindung_ beschäftigen, ist es wichtig zu verstehen, warum solch eine Bindung überhaupt nötig ist.

Normalerweise wird in JavaScript der _this-Wert_ beim Funktionsaufruf gebunden. Derjenige, der die Funktion aufruft, definiert auch den _this-Wert_. Wird zum Beispiel eine Funktion als Methode aufgerufen, wird __this__ das Objekt sein. Bei einem normalen Funktionsaufruf ist __this__ das _window-Objekt_ im normalen Modus oder __undefined__ im [strict-Modus](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Strict_mode).

```javascript
function fn() {
  console.log(this);
}

var obj = {
  fn: fn
};

// Funktion wird als Methode aufgerufen
obj.fn(); // this ist das Objekt obj

// Normaler Funktionsaufruf
fn(); // this ist das window-Objekt
```

Um eine _this-Bindung_ zu realisieren, können wir die [Function.prototype.bind](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)-Funktion benutzen. Diese Funktion bekommt als ersten Parameter den _this-Wert_ und gibt eine Funktion mit gebundenem _this-Wert_ zurück. Alle weiteren Parameter sind optional. Was man damit anfangen kann, steht in unserem Blogartikel über [partielle Funktionsanwendung](https://jsperts.de/blog/partial-function-application/).

### Beispiel für eine this-Bindung mit Methode

```javascript
var obj = {
  name: 'Max',
  getName: function() {
    return this.name;
  }
};

// Aufruf als Methode
// this-Wert ist an das Objekt gebunden
obj.getName; // 'Max'

// Zuweisung an Variable
var getName = obj.getName;

// Normaler Funktionsaufruf
// this-Wert ist an das window-Objekt gebunden
getName(); // undefined
```

In Zeile 9 wurde der _this-Wert_ an das Objekt __obj__ gebunden wie erwartet. Aber in Zeile 15 wurde der _this-Wert_ an das _window-Objekt_ gebunden, und darum ist das Resultat des Aufrufs __undefined__. Eine mögliche Lösung ist es, bei der Zuweisung den _this-Wert_ an das Objekt __obj__ zu binden.

```javascript
var obj = {
  name: 'Max',
  getName: function() {
    return this.name;
  }
};

// this-Wert von getName() an das obj binden
var getName = obj.getName.bind(obj);

getName(); // 'Max'
```

Jetzt ist der _this-Wert_ der __getName()__-Funktion an __obj__ gebunden, und der __getName()__-Aufruf funktioniert wie erwartet.

### Beispiel für eine this-Bindung mit Callback

Ein ähnliches Problem wie oben sehen wir auch, wenn wir __this__ in einem Callback benutzen wollen. Die klassische Lösung ist es, den _this-Wert_ einer anderen Variablen zuzuweisen wie unten dargestellt.

```javascript
var obj = {
  name: 'Max',
  fn: function() {
    var that = this;
    function callback() {
      console.log('Name', that.name); // 'Max'
      console.log(this); // this ist das window-Objekt
    }
    setTimeout(callback, 0);
  }
};

obj.fn();
```

Diese Lösung funktioniert ohne Probleme und wird auch sehr häufig benutzt, aber wir können das Problem auch mit Hilfe von __bind()__ lösen. Hier derselbe Code, aber diesmal mit __bind()__.

```javascript
var obj = {
  name: 'Max',
  fn: function() {
    var callback = function () {
      console.log('Name', this.name); // 'Max'
      console.log(this); // das Objekt obj
    }.bind(this);
  }
};

obj.fn();
```

Beide Lösungen sind meiner Meinung nach nicht besonders schön. In Zukunft werden wir noch eine weitere Möglichkeit haben, und zwar [Arrow-Funktionen](https://jsperts.de/blog/arrow-functions/).

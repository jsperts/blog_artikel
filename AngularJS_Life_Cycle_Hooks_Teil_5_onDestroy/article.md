
<aside style="border: 1px dotted #f37726; padding: 4px; margin-bottom: 20px;">
Dieser Artikel ist der letzte Teil in einer Serie von 5 kleinen Blogartikel über "life cycle hooks" in Angular 1.5.x.

* [Angular life cycle Hooks. Teil 1: Einführung](https://jsperts.de/blog/angularjs_life_cycle_hooks_teil_1_einfuehrung)
* [Angular life cycle Hooks. Teil 2: $onInit](https://jsperts.de/blog/angularjs_life_cycle_hooks_teil_2_oninit)
* [Angular life cycle Hooks. Teil 3: $postLink](https://jsperts.de/blog/angularjs_life_cycle_hooks_teil_3_postlink)
* [Angular life cycle Hooks. Teil 4: $onChanges](https://jsperts.de/blog/angularjs_life_cycle_hooks_teil_4_onchanges)
* [Angular life cycle Hooks. Teil 5: $onDestroy](https://jsperts.de/blog/angularjs_life_cycle_hooks_teil_5_ondestroy)
</aside>

### $onDestroy

Der letzte Hook der uns Angular anbietet, ist der $onDestroy-Hook.
Dieser wird aufgerufen, wenn eine Komponente aus dem DOM entfernt wird z. B. durch __ng-if__.
Wir können den $onDestroy-Hook nutzen, um ein Cleanup durchzuführen z. B. um Event-Listeners zu entfernen.
Vor der 1.5.3 Version mussten wir dafür das $destroy-Event des __$scope__ nutzen und hatten $scope als Abhängigkeit in unsere Komponente.

```js
function controllerFn() {
  this.isChildVisible = true;

  this.handleVisibilityToggle = function() {
    this.isChildVisible = !this.isChildVisible;
  }
}

var component = {
  template: `
    <div>
      <button ng-click="$ctrl.handleVisibilityToggle()">Toggle Visibility</button>
    </div>
    <div ng-if="$ctrl.isChildVisible">
      <child></child>
    </div>
  `,
  controller: controllerFn
};

function childControllerFn() {
  this.$onDestroy = function() {
    console.log('Cleanup');
  };
}

var childComponent = {
  template: '<p>Child</p>',
  controller: childControllerFn
};

angular.module('app', [])
    .component('myApp', component)
    .component('child', childComponent);
```

[Plunker Link](https://plnkr.co/edit/chiRgpbguqghX2cvZjLN?p=preview)

Wenn wir jetzt im Beispiel auf Plunker den Button klicken, sehen wir in der Konsole, dass der $onDestroy-Hook aufgerufen wird jedes mal wo "isChildVisible" gleich __false__ ist.

### Fazit

Wenn wir beim Entfernen einer Komponente ein Cleanup durchführen müssen, ist der $onDestroy-Hook der ideale Ort dafür.
Und mit Hilfe des Hooks können wir auf __$scope__ verzichten, wenn dieses nur für das $destroy-Event benutzt worden ist.

Jetzt haben wir uns alle Hooks angeschaut, die uns Angular anbietet und gesehen wie und wann wir diese nutzten können.
Angular 2 bietet alle erwähnte Hooks an (ggf. unter einem anderen Namen) und noch mehr.
Die Zukunft wird zeigen, ob es noch mehr Hooks in Angular 1 geben wird.


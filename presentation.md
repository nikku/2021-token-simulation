---
title: Making of bpmn-js Token Simulation - Understanding bpmn-js extensibility one token at a time.
description: This slide deck introduces the bpmn-js-token-simulation tool and showcases how it was built on top of bpmn-js extensible foundations. The tool is a more complex bpmn-js extention and thus, serves as a good example how bpmn-js can be extended.
author: Nico Rehwaldt
authorTwitter: '@nrehwaldt'
type: website
tags:
  - bpmn-js
  - token-simulation
  - bpmn.io
showProgress: true
---

<!--config
theme=cccon
-->

<style>

  :root {
    --color-camunda-red: #ff3000;
  }

  .slide:not([data-theme]):before,
  .slide[data-theme='cccon-end']:before {
    content: '';
    position: absolute;
    left: 0;
    top: 0;
    right: 0;
    background: var(--color-camunda-red);
    height: .2em;
  }

  ref {
    display: block;
    font-size: .5em;
    margin-top: 2em;
  }

  .slide[data-theme='cccon'] {
    --font-family-body: Arial;
    --font-family-heading: Arial;
    --slide-margin: 4rem;
    --block-margin: calc(var(--slide-margin) * .25);
    background: url('./community-summit-bg.png');
    justify-content: start;
    color: white;
  }

  .slide[data-theme='cccon'] h5,
  .slide[data-theme='cccon'] p {
    margin-top: var(--block-margin);
  }

  .slide[data-theme='eco'] {
    --color-highlight-bg: var(--color-camunda-red);
  }
</style>


<img src="./community-summit-logo-white.svg" style="height: 4em; display: block;" />

## Making of bpmn-js Token Simulation

##### Understanding bpmn-js extensibility one token at a time.

<small style="font-size: .5em">Nico Rehwaldt</small>


---

## About Me

* Software developer at Camunda
* bpmn.io creator and project lead
* [`@nikku`](https://github.com/nikku) on GitHub

---

## Agenda

:one: Token simulation?

:two: About bpmn-js

:three: bpmn-js extensibility

:four: How does token simulation plug in?

---

## :one: Token Simulation?

---

<!--config
align=right
theme=eco
-->

### A _picture_ is worth _a thousand words_.

---

<!--config
align=right
theme=eco
-->

### A _moving token_ is worth _a whole bunch of static BPMN diagrams_.

---

[<img src="./token-simulation.png" alt="Token simulation overview" width="85%">](https://github.com/bpmn-io/bpmn-js-token-simulation)

---

## Core Idea: Token Flow = :bulb:

* Understand wait, join, and split semantics
* Learn BPMN execution in a playful manner
* Aid your understanding of a diagrams semantics

---

## What it is not

* Batch processing simulator
* Business intelligence tool
* Verifier / dead lock / live lock analyzer

---

## :two: About bpmn-js

---

[<img src="./bpmn-io.png" title="bpmn.io website screenshot" width="85%">](https://bpmn.io)

---

## bpmn-js

* A BPMN diagram renderer and editing toolkit
* Embeds into any web page
* Extensible by design

<ref>[`https://github.com/bpmn-io/bpmn-js`](https://github.com/bpmn-io/bpmn-js)</ref>

---

<img src="./application-processing.svg" title="Huge diagram" width="85%">

---

[<img src="./kids-at-home-edition.png" alt="Modeling, Kids at Home" width="85%">](https://bpmn.io/blog/posts/2020-modeling-kids-at-home-edition.html)

---

[![Nyan cat](./nyan.gif)](https://github.com/bpmn-io/bpmn-js-nyan)

---

[<img src="./camunda-modeler.png" title="Camunda Modeler" width="85%">](https://github.com/camunda/camunda-modeler)

---

## :three: bpmn-js Extensibility

---

## A (BPMN) Diagram Toolbox

* Element discovery, rendering and interaction
* Selection, navigation, search
* Palette and context pad
* Modeling primitives and stacked behaviors
* Overlays
* ...

---

## An Extensible Architecture

* Named services offer behavior
* Modules group services into logical units
* Service instantiation library managed
* [Dependency injection](https://en.wikipedia.org/wiki/Dependency_injection) / [IoC](https://en.wikipedia.org/wiki/Inversion_of_control) at the core

---


## Extension Cases

* :orange_book: Interface with bpmn-js via API
* :blue_book: Build your own extensions
* :green_book: Replace an existing service / functionality

---

## :orange_book: Select an Element

```javascript
const bpmnModeler = new BpmnModeler();

const selection = bpmnModeler.get('selection');
const elementRegistry = bpmnModeler.get('elementRegistry');

selection.select([
  elementRegistry.get('Task_1')
]);
```

---

## :orange_book: Hook into Events

```javascript
const bpmnModeler = new BpmnModeler();

const eventBus = bpmnModeler.get('eventBus');

eventBus.on('element.changed', function(event) {
  console.log('Changed', event.element);
});
```

---

## :orange_book: Model Programmatically

```javascript
const modeling = bpmnModeler.get('modeling');

modeling.createShape(
  { type: 'bpmn:ServiceTask' },
  { x: 10, y: 20 }
);
```

---

<a style="max-width: 70%; max-height: 70%; display: block;" href="https://bpmn.io/blog/posts/2021-wasdenn-ai-modeling-assistant.html">
  <img src="./002_wasdenn-2.gif" alt="002_wasdenn-2.gif" style="width: 100%;
    border: solid 4px #F0F0F0;
    border-radius: 4px;">
</a>

<ref>[`https://bpmn.io/blog/posts/2021-wasdenn-ai-modeling-assistant.html`](https://bpmn.io/blog/posts/2021-wasdenn-ai-modeling-assistant.html)</ref>

---

## :blue_book: Implement a Service

```javascript
// TaskSelection.js
export default function TaskSelection(selection, elementRegistry) {

  /**
   * Select this very special task
   */
  this.selectTask1 = function() {
    selection.select([
      elementRegistry.get('Task_1')
    ])
  };
}
```

---

## :blue_book: Create a Module

```javascript
// TaskSelectionModule.js
import TaskSelection from './TaskSelection';

export default {
  taskSelection: [ 'type', TaskSelection ]
};
```

---

## :blue_book: Extend bpmn-js

```javascript
import taskSelectionModule from './TaskSelectionModule';

const bpmnModeler = new BpmnModeler({
  additionalModules: [
    taskSelectionModule
  ]
});

const taskSelection = bpmnModeler.get('taskSelection');

taskSelection.selectTask1();
```

---

## :four: How does token simulation plug in?

---

<!--config
align=center
theme=eco
-->

## Code Review!

---

## In a Nutshell

* Uses bpmn-js extensible architecture
* Visualizations on top of the actual BPMN diagram
* Accounts for _editor_ or _viewer_
* Custom controls to interact with the simulator
* Disabled modeling interaction :skull_and_crossbones:
* A (single instance) process engine

---

<!--config
theme=cccon-end
-->

<style>
  .slide[data-theme='cccon-end'] {
    --slide-margin: 4rem;
    --block-margin: calc(var(--slide-margin) * .25);
    justify-content: start;
  }

  .slide[data-theme='cccon-end'] h2 {
    margin-top: var(--block-margin);
  }
</style>

<img src="./community-summit-logo-black.svg" style="height: 4em; display: block; margin-bottom: calc(var(--block-margin) * 8)" />

## Thank you. <span style="color: #ff5500">Questions?</span>

---

<!--config
theme=eco
-->

## Contribute to Token Simulation

#### You like the token simulation tool? Consider contributing [on Github](https://github.com/bpmn-io/bpmn-js-token-simulation) and help us to make it even better :heart:.

---

## Resources

* https://github.com/bpmn-io/bpmn-js
* https://github.com/bpmn-io/bpmn-js-examples
* https://github.com/bpmn-io/bpmn-js-token-simulation
* https://forum.bpmn.io
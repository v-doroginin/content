---
title: "React и альтернативы"
authors:
  - windrushfarer
keywords:
  - web application
  - frontend
  - фронтенд
  - SPA
  - react
  - vue
  - svelte
tags:
  - article
---

В мире фронтенда много различных фреймворков для разработки [одностраничных приложений](/js/web-app-types/#single-page-applications-spa). Большинство из них различаются подходами к разработке и инструментами, которыми они обладают. Но в основе всех этих фреймворков часто лежат одни и те же концепции. Эти концепции и различия в подходах мы рассмотрим в этой статье.

## Кратко

Чтобы облегчить поиск изменений в приложении и работу с DOM браузера, некоторые фреймворки используют Virtual DOM. Virtual DOM – объект, который хранит структуру дерева компонентов и их текущее состояние.

React использует Virtual DOM для отслеживания изменений. После каждого изменения состояния, React создаёт новый Virtual DOM и сравнивает его с предыдущим, чтобы узнать какие части приложения изменились.

Vue использует Virtual DOM и специальные объекты Proxy, чтобы узнавать когда изменились данные.

Angular не использует Virtual DOM, но модифицирует браузерный DOM API, чтобы иметь возможность следить из изменением данных. Таким образом, если пользователь взаимодействует со страницей, то Angular проверяет не изменились ли какие-то данные приложения.

Svelte не использует Virtual DOM, но во время компиляции приложения формирует функции, которые следят за изменением данных и обновляют соответствующие DOM-элементы.

## Реактивность

Все фреймворки стараются следовать принципам [реактивности](/js/reactivity/), чтобы сделать интерфейс быстрым и отзывчивым. При этом каждый использует разные способы, чтобы её организовать. Основные различия кроются в том, как фреймворк отслеживает изменения и проводит синхронизацию модели в [DOM браузера](/js/dom).

Однако следование принципам реактивности не значит, что фреймворки на самом деле работают реактивно. Для реактивности некоторые фреймворки используют вспомогательные сущности, например, Virtual DOM.

## Virtual DOM

Virtual DOM — это легковесный объект, который описывает структуру приложения. Объект Virtual DOM отображает своё дерево компонентов в браузерное [DOM-дерево](/js/dom/). Так фреймворк узнаёт, какой компонент соответствует какому DOM-элементу на странице. Это дерево хранится в памяти и обновляется каждый раз при изменении данных в приложении.

Например, если наш интерфейс состоит из кнопок, нажатие на которые открывает модальные окна, то стартовый Virtual DOM будет содержать только компоненты кнопок. При отображении в браузере мы увидим эти кнопки. Если нажать на одну из них, то Virtual DOM обновится и будет содержать кнопки и модальное окно. Так как модального окна нет в браузере, фреймворк понимает, что нужно его дорисовать.

<aside>

☝️ Факт обновления браузерного DOM не означает, что элементы будут удаляться и создаваться заново. Для экономии времени, фреймворки будут стараться сохранить как можно больше элементов на странице и производить только точечные изменения.

</aside>

Virtual DOM существует и работает только в рантайме, то есть во время выполнения программы, и все своё состояние он хранит в памяти. Большие приложения могут создавать очень большие деревья, а многочисленные изменения будут приводить к многократным пересозданиям отдельных поддеревьев и их проверкам с предыдущей версией. Все эти факторы влияют на производительность — чем больше дерево, тем больше работы придётся сделать после его изменений.

## React

Каждое приложение (или отдельные компоненты) в React имеет состояние. Состояние — это данные, за которыми React следит и перерисовывает интерфейс в браузере при их изменении. У React [однонаправленный поток данных](/js/architecture-data-flow) и он предоставляет специальный API для обновления состояния.

Когда нужно обновить состояние, разработчик вызывает функцию для установки нового значения. Благодаря этому React узнаёт, когда начинать перерисовку. Такой подход является pull-реактивностью, так как мы явно даём знать React, что данные изменились, но фреймворк сам решит в какой момент времени нужно обновить данные в браузере.

Главная особенность React – это Virtual DOM. Когда React только появился, это было его главным нововведением. Вся логика отображения, выявления изменений и обновление браузерного DOM строится вокруг Virtual DOM.

Результат функции `render()` в классовом компоненте или функционального компонента является Virtual DOM. Все элементы в Vitual DOM  — это объекты с одинаковым набором свойств, они очень похожи на [DOM-элементы](/js/element/).

![Структура Vitual DOM](images/virtual_dom.png)

Вложенность элементов Virtual DOM можно увидеть с помощью свойства `children` внутри свойства `props`. Так получается древовидная структура. На картинке выше можно увидеть, что внутри исходного компонента находится `div`, который содержит строку `"Hello Doka"`.

Когда происходит изменение в приложении (к примеру поменялось состояние), React запускает алгоритм сравнения двух Virtual DOM: старого и нового. Он [рекурсивно](/js/recursion/) проходит по всему дереву и пытается найти отличия в их структуре. Если различия найдены, то DOM в браузере обновится соответственно. Все это происходит в реальном времени, но работает очень быстро, поэтому пользователь не видит никаких задержек.

Алгоритм сравнения содержит множество нюансов, подробности лучше посмотреть в [официальной документации](https://ru.reactjs.org/docs/reconciliation.html).

## Angular

Angular, чтобы связывать данные приложения и отображение, использует HTML-подобный синтаксис для своих шаблонов и компилирует эти шаблоны в набор инструкций, которые создают браузерные DOM-элементы. Angular не использует Virtual DOM, чтобы хранить дерево компонентов в памяти.

У Angular есть две стратегии для обнаружения изменений: `default` и `onPush`.

По умолчанию в приложении включена стратегия `default`.

Стратегия `default` работает просто: Angular рекурсивно проходит по дереву компонентов и для каждого выражения, используемого в шаблоне, он сравнивает текущее значение с предыдущим. Если значения отличаются, то фреймворк помечает их, и в итоге меняет отображение в DOM.

При этом Angular умеет самостоятельно запускать обнаружение изменений, например после клика или после вызова асинхронного `setTimeout`. Чтобы отлавливать изменения, которые происходят через браузерное API, Angular использует `zone.js`. Это библиотека, которая связывает асинхронный API браузера и Angular. При запуске приложения `zone.js` патчит браузерный API, например [addEventListener](/js/element-addeventlistener/), `setTimeout`, `setInterval` и API для AJAX-запросов. Тем самым Angular добавляет возможность отслеживать изменения автоматически после запуска функций из браузерного API.

```js
// Пример как это может выглядеть
function addEventListener(name, handler, options) {
    this.addEventListener(name, (...args) => {
      handler(...args);

      angular.runChangeDetection();
    })
}

EventTarget.prototype.addEventListener = addEventListener
```

Таким образом, фреймворк старается самостоятельно отслеживать изменения, снимая с разработчика эту рутину.

Стратегия `default` очень удобна, так как разработчику ничего не нужно проверять самостоятельно. Но в больших приложениях может вызывать проблему с производительностью из-за частых перезапусков проверок на любое событие. Чтобы решить эти проблемы и сделать обнаружение изменений более явным используют стратегию `onPush`.

Стратегия `onPush` заменяет механизм обнаружения изменения. При ней компонент обновляется, только если меняются входные данные — примерно как работают *props* в React компонентах. Когда входные данные компонента изменяются, Angular пройдётся по всему поддереву и обновит дочерние элементы.

Обе стратегии обновления могут использоваться одновременно. Стратегию `onPush` применяют для оптимизации высоконагруженных частей приложения.

## Vue

Vue реализует реактивность с помощью Proxy. Proxy – это специальный объект в JavaScript, который позволяет следить за изменениями свойств другого объекта. Это даёт Vue возможность отслеживать, когда свойство объекта было изменено или считано, и реагировать на это. При этом объект, отслеживаемый с помощью Proxy, ничем не отличается от обычного.

Vue не только отслеживает свойства объектов, но и оборачивает функции в *эффекты*. Эффект – это обёртка вокруг функции, которая формирует порядок вызова и следит какую функцию нужно вызвать.

Упрощённо, эффекты во Vue устроены так:

* есть место, где хранится список запущенных эффектов `effects`

```js
const effects = []
```

* есть функция `addEffect`, которая создаёт эффекты. Перед тем, как вызвать эффект, он записывается в список запущенных, а после выполнения эффекта, удаляется:

```js
const addEffect = fn => {
  const runEffect = () => {
    effects.push(runEffect)

    fn()

    effects.pop()
  }

  runEffect()
}
```

При использовании функции `addEffect` можно увидеть, что функция `fn`  добавляется в список эффектов:

```js
const makeChanges = () => {
  console.log(effects) // Выведет массив с одной функцией [ ƒ ]
}

const fn = () => {
  // ...
  makeChanges()
}

addEffect(fn)
```

Для обновления DOM в браузере Vue тоже использует Virtual DOM. Vue использует свой синтаксис шаблонов, похожий на Angular, но при компиляции приложения эти шаблоны преобразуются в вызовы render-функций, создающих Virtual DOM. Алгоритм обхода и свойства объекта в Virtual DOM во Vue отличаются от React, но основные подходы очень похожи.

## Svelte

Svelte отличается от других фреймворков тем, что функции для отслеживания изменений формируются во время компиляции приложения.

Svelte имеет свой язык шаблонов, похожий на тот, что есть в Angular или Vue. Благодаря ему компилятор умеет просчитывать все зависимости заранее. В итоге Svelte сформирует набор функций, которые следят только за определёнными значениями и обновляют DOM при их изменениях. Это быстрее подхода Virtual DOM, так как не нужно проверять все дерево, чтобы узнать где произошли изменения, и не нужно хранить все дерево компонентов в памяти.

Предположим есть код, который увеличивает значение переменной.

```js
let count = 0;

function increment() {
  count = count + 1;
}
```

Во время компиляции Svelte ищет все места, где меняются данные, и на выходе добавляет вызов специальной функций, которая помечает, что изменение произошло.

```js
function instance($$self, $$props, $$invalidate) {
  let count = 0;

  function increment(event) {
    $$invalidate(0, count = count + 1);
  }

  return [count, increment];
}
```

Код выше это небольшая часть того, что скомпилировал Svelte. Здесь видно, как в изначальную функцию добавилась специальная функция `$$invalidate`, которая пометила, что данные изменились, и тем самым запланировала обновление DOM-элемента, где используется это значение.

## Заключение

Все рассмотренные фреймворки подойдут для написания любого типа приложения. Но каждый использует свои подходы для решения проблем с обнаружением изменений и работой с DOM.

Virtual DOM используется в React и Vue помогает держать актуальное состояние приложения прямо в памяти. Он позволяет быстро находить изменения, путём сравнения двух версий. Большие приложения будут занимать много места в памяти, а частые обновления будут занимать больше времени.

Angular помогает разработчику с помощью автоматических проверок изменений. Но при необходимости имеет вторую стратегию, чтобы помочь оптимизировать приложение.

Svelte уникален тем, что формирует код, который будет отслеживать изменения, во время сборки приложения. В итоге получается только необходимый для приложения код.

Любой фреймворк – это инструмент для решения конкретной задачи. Потому знание того, как фреймворк работает внутри, поможет принять взвешенное решение.

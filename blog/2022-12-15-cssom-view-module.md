---
slug: cssom-view-module
title: Полное руководство по геометрии DOM
description: Полное руководство по геометрии DOM
authors: harryheman
tags: [javascript, js, cssom, cssom view module, dom]
---

Привет, друзья!

Представляю вашему вниманию перевод [этой замечательной статьи](https://www.smashingmagazine.com/2022/11/document-object-model-geometry-guide/).

Знаете ли вы, что кроме интерфейсов, определенных спецификациями объектной модели документа (Document Object Model, DOM) и объектной модели каскадных таблиц стилей (Cascading Style Sheets (CSS) Object Model, CSSOM), существует также набор свойств и методов, определяемых в модуле отображения CSSOM (CSSOM View Module)? Этот интерфейс предназначен для определения и манипулирования геометрией элементов DOM.

<!--truncate-->

## Модуль отображения CSSOM

CSSOM - это набор интерфейсов, позволяющих манипулировать CSS с помощью JavaScript. Подобно тому, как DOM предоставляет интерфейс для манипулирования HTML, CSSOM позволяет читать и модифицировать CSS.

Отображение CSS - это модуль CSS, содержащий большое количество свойств и методов, предоставляющий интерфейс для получения информации о визуальном представлении элементов. Большинство свойств этого модуля доступны только для чтения (read-only) и вычисляются при доступе к ним - возвращают живые значения (live values).

В настоящее время модуль отображения CSSOM имеет статус [рабочего черновика](https://www.w3.org/TR/cssom-view-1/), однако большинство предоставляемых им свойств и методов поддерживаются всеми современными браузерами.

## Зачем нам свойства и методы геометрии?

Во-первых, они позволяют программно манипулировать визуальным представлением элементов DOM, что открывает широкие возможности по созданию динамичных пользовательских интерфейсов (User Interfaces, UI).

Во-вторых, при отображении документов HTML в браузере, элементы DOM рендерятся как визуальные или видимые фигуры (visual shapes), они имеют определенное визуальное представление, которое отрисовывается браузером. Живые значения свойств элементов DOM, доступные через свойства и методы отображения CSSOM, являются более точными, чем значения обычных свойств CSS.

Это, в свою очередь, делает более надежным код, который полагается на значения текущей позиции элементов DOM.

## Геометрия узла элемента

### Смещения / Offsets

_Координаты, определенные с помощью модели "смещений", используют верхний левый угол исследуемого элемента или элемента, на котором произошло событие - [MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/CSSOM_View/Coordinate_systems#offset)_

В отличие от других свойств отображения CSSOM, свойства смещения доступны только узлам `HTMLElement`, производным от узла `Element`. Поэтому у `SVGElement`, например, такие свойства отсутствуют.

<img src="https://habrastorage.org/webt/pa/rs/ut/parsutrvklnc8sfg8f3xb7undsc.png" />
<br />

__Смещения слева и сверху__

Свойства `offsetLeft` и `offsetTop` содержат координаты `x` и `y` элемента относительно его `offsetParent`. Свойство `offsetLeft` возвращает расстояние между внешней левой границей элемента и внутренней левой границей его `offsetParent`. Свойство `offsetTop` возвращает расстояние между внешней верхней границей элемента и внутренней верхней границей его `offsetParent`.

__offsetParent__

`offsetParent` любого элемента - это его ближайший родительский элемент, который не имеет CSS-свойства `position` со значением `static`, а также не является HTML-элементами `td`, `th`, `table` или `body`.

__Ширина и высота смещения__

Эти доступные только для чтения свойства предоставляют полную информацию о внешнем размере узлов элемента. Свойство `offsetWidth` возвращает общую ширину вертикальных границ, внутренних отступов (padding) и содержимого элемента, включая полосы прокрутки (scrollbars) (при наличии). Свойство `offsetHeight` возвращает общую высоту горизонтальных границ, внутренних отступов и содержимого элемента, включая полосы прокрутки (при наличии).

### Значения клиента / Clients

<img src="https://habrastorage.org/webt/tl/ru/aw/tlruaw4mqh87dcbyzvlf5fdmn7y.png" />
<br />

__Смещения слева и сверху__

Эти доступные только для чтения свойства возвращают ширину левой границы и высоту верхней границы, соответственно. Точнее, значения свойств `clientLeft` и `clientTop` представляет собой координаты внутренней части (внутренних отступов) элемента относительно его внешней части (внешней границы).

__Ширина и высота клиента__

Доступные только для чтения свойства `clientWidth` и `clientHeight` возвращают размеры внутренней части элемента (области, находящейся внутри границ элемента). `clientWidth` возвращает ширину содержимого элемента и его вертикальных внутренних отступов без полос прокрутки. `clientHeight` возвращает высоту содержимого элемента и его горизонтальных внутренних отступов без полос прокрутки.

### Прокрутка / Scrolls

<img src="https://habrastorage.org/webt/km/th/qh/kmthqhkfct03uvfe_rrzj0u3rjo.png" />
<br />

__Прокрутка слева и сверху__

При доступе к свойствам `scrollLeft` и `scrollTop` элемента с содержимым без переполнения (overflow) по оси `x` или `y`, возвращается `0`.

`scrollLeft` возвращает расстояние в пикселях, на которое элемент прокручен горизонтально. `scrollTop` возвращает расстояние в пикселях, на которое элемент прокручен вертикально.

Пиксели, возвращаемые `scrollLeft` и `scrollTop`, не обязательно должны находиться в области просмотра, они вполне могут находиться за ее пределами.

_Обратите внимание_, что `scrollLeft` и `scrollTop` возвращают как целые числа, так и числа с плавающей запятой.

__Ширина и высота прокрутки__

Свойство `scrollWidth` - это сумма `clientWidth` и ширины содержимого с переполнением справа и слева от клиента. Свойство `scrollHeight` - это сумма `clientHeight` и высоты содержимого с переполнением сверху и снизу от клиента.

Поэтому для элемента без переполнения значения `scrollWidth` и `scrollHeight` совпадают со значениями `clientWidth` и `clientHeight`, соответственно.

Вот как `scrollWidth` и `scrollHeight` определяются на MDN:

_...равняются минимальной ширине или высоте, требуемой элементу для размещения всего его содержимого в области просмотра без горизонтальной или вертикальной прокрутки_

## Геометрия окна и документа

_Интерфейс `Window` представляет окно, содержащее документ DOM; свойство `document` содержит ссылку на документ, загруженный в данном окне_

Свойства геометрии документа, загруженного в окне, и самого окна являются важными по нескольким причинам. Иногда нам требуется прочитать ширину всей области просмотра или высоту всего документа. Иногда нам требуется прокрутить страницу к определенной позиции.

Доступ к свойствам документа можно получить через корневой элемент `html` (`Document.documentElement` в DOM, доступный как `document.documentElement` в `Window`).

<img src="https://habrastorage.org/webt/wp/ra/cf/wpracfz7xvxzjwniudz0okeigow.png" />
<br />

### Ширина и высота окна

Свойства для вычисления ширины и высоты окна делятся на внешние и внутренние. Доступные только для чтения свойства `outerWidth` и `outerHeight` возвращают ширину и высоту всего окна браузера.

Для получения внутренней ширины и высоты окна используются свойства `innerWidth` и `innerHeight`. Они возвращают ширину и высоту (включая полосы прокрутки) всей области просмотра, т.е. видимой части документа.

Свойства `clientWidth` и `clientHeight` корневого элемента (`document.documentElement`) возвращают ширину и высоту окна без полос прокрутки и границ.

### Ширина и высота документа

Мы никогда не устанавливаем границы, внутренние или внешние отступы для корневого элемента. Для получения общей ширины и высоты документа используются свойства `scrollWidth` и `scrollHeight` `document.documentElement`.

### Значения прокрутки окна и документа

__Прокрутка слева и сверху__

Свойства `scrollLeft` и `scrollTop` корневого элемента возвращают размер прокрученной вправо или вниз (невидимой) части документа в пикселях.

Значения прокрутки документа также можно получить, и это является рекомендуемым способом, с помощью свойств `window.pageXOffset` и `window.pageYOffset`.

### Методы прокрутки окна и документа

__Методы `scroll()` и `scrollTo()`__

Эти два метода, которые по сути являются идентичными, позволяют прокручивать страницу к определенным координатам (`x` и `y`) документа. Значения координат представляют абсолютную позицию от верхнего левого угла документа.

Пример:

```javascript
window.scrollTo(0, 500);
// Вертикально прокручивает страницу на 500 пикселей от начала координат (0, 0)

window.scrollTo(0, 500);
// Страница остается на той же позиции
```

После запуска второго `window.scrollTo()` ничего не происходит, поскольку страница уже находится на позиции 500 пикселей от начала координат по оси `y`.

Методы `scroll` и `scrollTo` принимают параметры `x` и `y`, представляющие количество пикселей прокрутки вдоль горизонтальной или вертикальной оси. Они также принимают объект, который может содержать свойства `top`, `left` и `behavior`.

Значение свойства `behavior` определяет поведение прокрутки, то, как она выполняется. Значение `smooth` означает плавную прокрутку, а значение `auto` - моментальную.

__Метод `scrollBy()`__

Это относительный метод прокрутки. Он прокручивает страницу относительно ее текущей позиции и не привязан к началу координат документа.

Пример:

```javascript
window.scrollBy(0, 500);
// Вертикально прокручивает страницу на 500 пикселей от ее текущей позиции, например, от 0, 0 до 0, 500

window.scrollBy(0, 500);
// Вертикально прокручивает страницу еще на 500 пикселей, т.е. от 0, 500 до 0, 1000
```

## Координаты

Системы координат лежат в основе того, как определяются позиции элементов в свойствах и методах отображения CSSOM.

_При определении локации пикселя в графическом контексте, его позиция определяется относительно некоторой фиксированной точки контекста. Эта точка называется началом координат или источником (origin). Позиция определяется как количество пикселей, смещенных относительно каждого направления контекста. CSSOM использует стандартные системы координат, поэтому все зависит от локации источника_

### Координаты окна и документа

В стандарте CSSOM используется 4 системы координат. В модуле отображения CSSOM - только 2: системы координат клиента и страницы. Направления или позиции элементов, как правило, определяются относительно документа или области видимости.

### Координаты клиента (относительно окна)

_Система координат "клиента" в качестве источника использует верхний левый угол области видимости контекста браузера, в котором произошло событие. Это вся видимая часть, в которой представлен документ. Прокрутка значения не имеет_

Значения клиентских координат похожи на использование `position: fixed` в CSS и вычисляются от верхнего левого угла отображения.

### Координаты страницы (относительно документа)

_Система координат "страницы" представляет позицию пикселя относительно верхнего левого угла документа, в котором этот пиксель находится. Это означает, что определенная точка в элементе, находящемся в документе, будет сохранять прежние координаты до тех пор, пока элемент не начнет перемещаться (как вследствие прямого изменения его позиции, так и в результате добавления или масштабирования другого содержимого)_

Значения координат страницы похожи на использование `position: absolute` в CSS и вычисляются от верхнего левого угла документа. Позиция элемента относительно страницы будет оставаться прежней независимо от прокрутки, хотя его позиция относительно окна будет меняться.

### Координаты элемента

<img src="https://habrastorage.org/webt/21/sz/n7/21szn7zltzf5qnfp49if7gqsemw.png" />
<br />

__Метод `getBoundingClientRect()`__

Этот метод возвращает объект `DOMRect`, содержащий позиции и направления элемента относительно окна в пикселях. Это основной метод для манипулирования положением элемента относительно области просмотра.

Однако, следует помнить, что в некоторых случаях `DOMRect` будет содержать разные значения или направления для одного и того же элемента, особенно, в случае трансформации элемента (искажение, вращение, масштабирование) (`transform: skew rotate scale`).

_В случае с трансформациями `offsetWidth` и `offsetHeight` возвращают ширину и высоту макета (layout) элемента, а `getBoundingClientRect()` - ширину и высоту рендеринга. Например, если элемент имеет `width: 100px` и к нему применяется `transform: scale(0.5)`, `getBoundingClientRect()` вернет `50`, а `offsetWidth` - `100` - [MDN](https://developer.mozilla.org/en-US/docs/Web/API/CSS_Object_Model/Determining_the_dimensions_of_elements)_

Посмотрите на результаты, возвращаемые `getBoundingClientRect()`, при применении к элементам различных техник трансформации:

<oembed>https://codepen.io/smashingmag/pen/KKedmYx</oembed>

`DOMRect` содержит следующие свойства:

- свойства `x` и `y` возвращают координаты `x` и `y` источника элемента относительно окна;
- свойства `top` и `bottom` возвращают координату `y` для верхней и нижней границ элемента;
- свойства `left` и `right` возвращают координату `x` для левой и правой границ элемента;
- свойства `width` и `height` возвращают полную ширину и высоту элемента (как будто свойство `box-sizing` элемента имеет значение `border-box`).

### Координаты мыши и указателя

Все объекты событий мыши или указателя (pointer) содержат координаты локации, в которой произошло событие, как относительно окна, так и относительно документа.

Координаты относительно окна хранятся в свойствах `clientX` и `clientY`, а координаты относительно документа - в свойствах `pageX` и `pageY`.

## Случаи использования

Интерфейсы модуля отображения CSSOM предоставляют полезные свойства и методы для доступа к геометрии элементов DOM, которые рендерятся в браузере. Поскольку значения этих свойств и методов являются живыми, они более предпочтительны, чем соответствующие значения CSS. Но как можно использовать эти значения?

Рассмотрим 4 простых примера.

### Программная прокрутка к началу (на самый верх) страницы

Кнопка для выполнения прокрутки наверх (scroll-to-top) позволяет пользователю легко вернуться к началу страницы. Для реализации такой кнопки отлично подходит метод `scrollTo` (или аналогичный метод `scroll`).

Реализация:

<oembed>https://codepen.io/smashingmag/pen/VwdvWwK</oembed>

Добавляем кнопку в HTML и регистрируем обработчик ее нажатия в JS:

```javascript
scrollToTop.addEventListener("click", (e) => {
  // в данном случае `left: 0` можно опустить, поскольку наша страница прокручивается только по вертикали
  window.scrollTo({ left: 0, top: 0, behavior: "smooth" });
});
```

Для улучшения пользовательского опыта (User Experience, UX) кнопка отображается при выполнении пользователем прокрутки на 500 пикселей вниз:

```javascript
document.addEventListener("scroll", (e)=> {
  // вместо `window.pageYOffset` можно использовать `document.scrollTop`
  if (window.pageYOffset >= 500) {
      scrollToTop.style.display = "block";
  } else {
    scrollToTop.style.display = "none";
  }
});
```

### Бесконечная прокрутка

Суть бесконечной прокрутки заключается в загрузке дополнительного контента при достижении пользователем конца (нижней части) страницы при прокрутке.

Как нам определить, что пользователь достиг нижней части страницы?

Мы знаем, что `document.scrollHeight` возвращает общую высоту документа, `document.clientHeight` - высоту видимого экрана или области просмотра, а `window.pageYOffset` или `document.scrollTop` - высоту прокрученной части документа. Можем ли мы предположить, что если `document.scrollTop + document.clientHeight >= document.scrollHeight`, значит, пользователь добрался до конца страницы?

<oembed>https://codepen.io/smashingmag/pen/OJEygVz</oembed>

В примере техника бесконечной прокрутки используется для загрузки следующей порции (9) карточек до достижения установленного лимита (90) карточек.

Мы начинаем с объявления нескольких констант и одной переменной:

```javascript
// родительский элемент карточек
const cardContainer = document.querySelector("main");
// элемент для отображения количества загруженных карточек
const currentCardStats = document.querySelector(".currentCardNo");
// количество загружаемых карточек
const cardLoadAmount = 9;
// максимальное количество карточек
const totalCardsNo = 90;
// индекс последней загруженной карточки (не индекс массива)
let lastIndex;
```

Регистрируем обработчик прокрутки на документе:

```javascript
document.addEventListener("scroll", (e) => {
  if (document.documentElement.scrollTop + document.documentElement.clientHeight >= document.documentElement.scrollHeight) {
    const children = cardContainer.children;
    lastIndex = children.length;
  }
});
```

Если индекс последней загруженной карточки меньше максимального количества карточек, загружаем очередную порцию карточек:

```javascript
if (lastIndex < totalCardsNo) {
  for (let i = 1; i <= cardLoadAmount; i++) {
    const tile = document.createElement("div");
    tile.classList.add("card");
    tile.textContent = `${lastIndex + i}`;
    cardContainer.appendChild(tile);
  }
  currentCardStats.textContent = `${children.length}`;
}
```

### Запуск анимации в ответ на прокрутку

<oembed>https://codepen.io/smashingmag/pen/oNyjwxz</oembed>

В основе анимирования элемента при прокрутке лежит видимость элемента. Поэтому нам нужен способ определения пересечения элемента с областью просмотра.

На вертикально прокручиваемой странице элемент становится видимым, когда значение его `getBoundingClientRect().top` становится меньше значения высоты области просмотра (`document.documentElement.clientHeight`).

Собираем ссылки на анимируемые элементы в массив:

```javascript
const animatingElements = Array.from(document.getElementsByClassName("item"));
```

Мы хотим, чтобы анимация запускалась при пересечении элемента с областью просмотра более чем на 50 пикселей, поэтому добавляем `50` к `getBoundingClientRect().top` элемента:

```javascript
if (el.getBoundingClientRect().top + 50 < document.documentElement.clientHeight) {
  el.classList.add("animated");
}
```

За анимацию отвечает класс `animated`, определенный в CSS.

Регистрируем обработчик прокрутки на документе:

```javascript
document.addEventListener("scroll", (e) => {
  animatingElements.forEach((el) => {
    if (el.getBoundingClientRect().top + 50 < document.documentElement.clientHeight) {
      el.classList.add("animated");
    }
  });
});
```

Прим. пер.: гораздо более простым и производительным способом реализации анимирования элемента при прокрутке является использование [Intersection Observer API](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API).

### Слайдер диапазона

<oembed>https://codepen.io/smashingmag/pen/QWxjgKJ</oembed>

Как правило, такие слайдеры реализуются с помощью техники перетаскивания (drag) указателя (thumb) вдоль трека (track), определяющего некоторый диапазон значений.

Начинаем с получения ссылок на элементы указателя и трека, а также объявления 2 переменных:

```javascript
const thumb = document.querySelector(".thumb");
const slider = document.querySelector(".track");
let draggable;
let shiftX;
```

В общем виде последовательность перетаскивания (drag-and-drop) выглядит следующим образом:

- перемещение указателя на элемент;
- нажатие и удерживание указателя мыши или другого устройства для "захвата" элемента (событие `pointerdown`);
- "перетаскивание" (dragging) элемента в нужную локацию посредством перемещения указателя (событие `pointermove`);
- "бросание" (drop) элемента путем освобождения (release) указателя (событие `pointerup`).

Определяем обработчик события `pointerdown`:

```javascript
function prepDrag(event) {
  draggable = event.target;
  shiftX = event.clientX - draggable.getBoundingClientRect().left;
  document.addEventListener("pointermove", startDrag);
  document.addEventListener("pointerup", endDrag);
}
```

Задача этого обработчика - подготовить элемент к перемещению. Мы присваиваем значения переменным, объявленным в глобальном области видимости, а также регистрируем обработчики событий `pointermove` и `pointerup` на документе. Почему мы регистрируем эти обработчики на документе, а не на элементе указателя? Дело в том, что событие `mousemove / pointermove` возникает часто, но не для каждого пикселя. Это может привести к непредсказуемым ответам перетаскивания.

Функция `startDrag` содержит логику перемещения элемента указателя и его позиционирования путем манипулирования значениями его CSS-свойства `left`:

```javascript
function startDrag(event) {
  // мы не хотим, чтобы указатель выходил за пределы трека
  if (event.clientX < track.offsetLeft || event.clientX > slider.getBoundingClientRect().right){
    return;
  }
  draggable.style.left = event.clientX - shiftX - track.getBoundingClientRect().left +  'px';
}
```

Функция `endDrag` просто удаляет обработчики событий `pointermove` и `pointerup`:

```javascript
function endDrag() {
  document.removeEventListener("pointermove", startDrag);
  document.removeEventListener("pointerup", endDrag);
}
```

Наконец, регистрируем обработчик нажатия указателя на соответствующем элементе:

```javascript
thumb.addEventListener("pointerdown", prepDrag);
```

Прим. пер.: обработчики можно зарегистрировать один раз и использовать индикатор состояния перетаскивания, например, `isDragging`, для определения необходимости выполнения кода `startDrag`:

```javascript
let isDragging = false

function prepDrag(e) {
  // ...
  isDragging = true
}

function startDrag() {
  if (!isDragging) return
  // ...
}

function endDrag() {
  isDragging = false
}
```

Также в данном случае переменную `draggable` можно инициализировать при объявлении, поскольку на странице имеется только один перетаскиваемый элемент - элемент указателя.

Благодарю за внимание и happy coding!
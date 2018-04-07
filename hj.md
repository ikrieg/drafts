Во-первых, не стоит пугаться и хейтить код, в котором все сделано на функциях, глобальных переменных и замыканиях. В рамках курса мы этому не уделяли много внимания. Поэтому и в дипломе можно давать рекомендации, но не требовать модульности, снижения уровня зависимовстей, и использования паттернов проектирования.

Во-вторых, на курсе мы изучали DOM API, и сам проект — это упражнение, в рамках которого студент должен продемонстрировать что DOM API он знает и понимает как с его помощью решить задачи проекта. Именно на этом в первую очередь нужно сосредоточиться. Так же входным требованием курса является хорошее знание JavaScript (не ниже ES2015) а так же HTML и CSS. Поэтому эти знания студент тоже должен продемонстировать в работе.
Поэтому дальше соберу типичные замечания, которые я давал при проверке, сгруппировав их по направлениям.

## Следование стилю оформления кода
* Наличие неиспользуемого кода, закомментированного кода
* Наличие неиспользуемых файлов
* Использование табов или 4 пробелов для отступов
* Отсутствие горизонтальных и вертикальных отступов, или отсутсвие какой-либо логики их расстановки
* Отсутствие точек с запятой после функционального выражения, объектного литерала, и в других случаях, где точка с запятой нужна (но не обязательна)
* Открывающая фигурная скобка на новой строке `{`
* Плохие имена переменных и функций (`t`, `tmp`, `l`)
* Имена которые не поясняют назначение (функция `createXML` которая отправляет запрос на сервер)
* Другие отклонения от [стиля оформления кода](https://github.com/netology-code/codestyle/tree/master/js)

## Знание HTML и CSS
* Слишком подробные селекторы, избыточные селекторы:
```javascript
document.querySelector('body > main > nav.menu > ul li.active'); 

this.uploadItem = this.menu.querySelector('li.mode.new');
```
* Самозакрытые теги
```html
<img class="mask" src="" />
```
* Использование неподходящих тегов:
```html
<p class="button">Сохранить</p>
<span class="header">Ошибка!</span>
<div class="info">Новых комментариев <b class="output">7</b></div>
```

## Знание современного JavaScript
* Использование всех оттенков `let`, `var` и `const` вообще без какой-либо логики их выбора.
* Использование конкатенации вместо шаблона
```javascript
let a = document.querySelector('[data-left="' + allComents[key].left + '"][data-top="' + allComents[key].top + '"]');
```
* Использование функций, вместо функций стрелок
```javascript
setInterval(function () {    
 if (isNewLine && updateMaskCompleted) {     
	sendMask();    
 }   
}, 1000);
```
* Использование циклов for и for-in:
```javascript
for (let property in details) {
 let encodedKey = encodeURIComponent(property);
 let encodedValue = encodeURIComponent(details[property]);
 formBody.push(encodedKey + '=' + encodedValue);
}

for (var i = 0; i < inp.length; i++) {
 if (inp[i].type == 'radio' && inp[i].checked) {
	return inp[i].value;
 }
}
```
* Использование колбеков, вместо промисов
```javascript
function doSomething(data, onSuccess, onError) {}
```
* Не использование короткого синтаксиса объектного литерала:
```javascript
const request = {
 url: url,
 type: type
};

request[key] = value;
```
* Не использование деструктуризации:
```javascript
const x = event.x;
const y = event.y;
```
* Использование `arguments` или проверки передан ли аргумент вместо значений по умолчанию и «все остальные аргументы»
```javascript
function sendRequest(url, type) {
 if (!type) {
	type = 'GET';
 }
 // …
}

function createNode(type, props) {
 const childs = Array.from(arguments).slice(2);
 // …
}
```
* Использование прототипов
```javascript
function Menu(selector) {}

Menu.prototype.show = function () {};

const menu = new Menu('.main__menu');
```
* Потеря контекста
```javascript
node.addEventListener('click', this.handleClick);
```
* Оборачивание промисов в промисы:
```javascript
function sendRequest(url) {
 return new Promise((done, fail) => {
  fetch(url)
	 .then(checkResponse)
	 .then(res => res.json())
	 .then(done)
	 .catch(fail);
 });
}
```
* Не понимание особенностей асинхронного кода:
```javascript
canvas.toBlob(mask => sendMask(mask));
clear(canvas);
```
* Получение таймстемп не через `Date.now()`:
```javascript
const date = new Date();
let timer = date.getTime();
```
* Использование регулярных выражений там, где они не нужны:
```javascript
const slicedLink = location.search.split(/\?|\&/gi);
let currentID = slicedLink.find(attr => {
 return attr.indexOf('id=') + 1
});
currentID = currentID.split('id=')[1];
```
* Использований `self` для хранения контекста:
```javascript
class Menu {
 init() {
  const self = this;
  this.node.addEventListener('click', function (e) {
   self.selectItem(e.target);
  });
 }
}
```

## Простое улучшение качества кода
* Огромные функции (рекомендация 7 строк):
```javascript
function doEverything() {
 // 746 строк кода
}
```
* Смешивание в теле функции кода с разной степенью абстракции:
```javascript
function handleCommentForm(event) {
 const data = getFormData(event.target);

 // низкоуровневая возня, которую надо вынесли в showLoader();
 const loader = document.createElement('div');
 loader.className = 'loader_view';
 // …
 event.target.insertBefore(loader);
 // конец низкоуровневой возни

 server.addComment(data).then(showComment);
}
```
* Дублирование кусков идентичного или одинакового кода, вместо выноса его в функцию:
```javascript
let xhr = new XMLHttpRequest();
xhr.open('POST', 'https://neto-api.herokuapp.com/pic');
xhr.addEventListener('load', () => { 
 if (xhr.status >= 300) {
 } else {
  const data = JSON.parse(xhr.responseText);
  /* … */ 
 }
});
xhr.send(data);

// …

let xhr = new XMLHttpRequest();
xhr.open('POST', `https://neto-api.herokuapp.com/pic/${id}/comments');
xhr.addEventListener('load', () => { 
 if (xhr.status >= 300) {
 } else {
  const data = JSON.parse(xhr.responseText);
  /* … */ 
 }
});
xhr.send(data);
```
* Необоснованное повышения уровня вложенности кода:
```javascript
function doSomething() {
 if (flag) {
  op1();
  op2();
  op3();
  op4();
  op5();
  op6();
 }
}

// против
function doSomething() {
 if (!flag) {
	return;
 }
 
 op1();
 op2();
 op3();
 op4();
 op5();
 op6();
}
```
* Использование замыканий вместо аргументов:
```javascript
function saveComment(message) {
 const data = {
	message,
  id, x, y
 }
 // …
}
```

## Web API
* Листание коллекции циклом `for`:
```javascript
const comments = document.querySelectorAll('.comment');
for (var i = 0; i < comments.length; i++) {
 // …
}
```
* Навешивание обработчиков событий через свойства или атрибуты:
```javascript
form.onsubmit = handleSubmit;
body.setAttribute('ondragover', 'dragover_handler(event)');
```
* Обработка события `click` у кнопки формы вместо события `submit`:
```javascript
submitButton.addEventListener('click', handleForm);
```
* Очистка формы вручную, вместо использования reset
```javascript
name.value = '';
message.value = '';
```
* Не использование формы, там где форма актуальна.
* Использование события `readystatechange` вместо `load` у XHR.
* Работа с классами узла через атрибут или через свойство `className`
```javascript
node.className.replace('hidden', '');
form.setAttribute('class', 'visible');
```
* Работа с data-атрибутами через атрибуты
```javascript
comment.setAttribute('data-id', id);
```
* Использование одновременно `canvas.toBlob` и `canvas.toDataURL`
```javascript
canvas.toBlob(blob => {
 mask.src = canvas.toDataURL();
 sendMask(blob);
});
```
* Делигирование событий слишком высоко по дереву DOM:
```javascript
document.addEventListener('mousedown', event => {
 if (event.target.classList.contains('drag')) {
  // …
 }
});
```
* Многократный поиск по селектору без особой надобности:
```javascript
commentForm
 .querySelector('.comments__marker-checkbox').checked = true;  
	
commentForm
 .querySelector('.comments__marker-checkbox')
 .focus();    

commentForm
 .querySelector('.comments__marker-checkbox')
 .addEventListener(/* … */);
```
* Самостоятельный поиск узлов:
```javascript
const commentForm = Array
 .from(document.querySelectorAll('.comments__form'))
 .find(form => (form.style.top === `calc(${y}px - 1.5rem)` 
	&& form.style.left === `calc(${x}px - 2.2rem)`);
```
* Использование коллекций там где они не нужны:
```javascript
const item = document.getElementsByClassName('.item')[0];
const form = document.querySelectorAll('.form')[0];
```
* Тяжелые операции в высокочастотных событиях:
```javascript
canvas.addEventListener('mousemove', (event) => {
 const isTool = document
  .querySelector('.draw-tools')
  .classList
  .contains('tool');
 if (!isTool) {
  // …
 }
});
```
* Ничем не обоснованная отмена всплытия или действия по умолчанию:
```javascript
function handleDragOver(evt) {   
 evt.stopPropagation();
 // …
}

function handleMouseOut(e) {
 evt.preventDefault();
}
```
* Не отменять действия бразера по умолчанию там, где они не желательны:
```javascript
function handleFileDrop(e) {
 // код без preventDefault
}

function handleLinkClick(e) {
 // код без preventDefault
}

function handleFormSubmit(e) {
 // код без preventDefault
}
```
* Использование `FileReader` для отправки файла или отображения:
```javascript
let fileReader = new FileReader();
fileReader.addEventListener('load', event => {
 sendImageToServer(selectedFile);
});
fileReader.readAsDataURL(selectedFile);
```
* Работа со списком фалов, а отправка только первого:
```javascript
const files = event.dataTransfer;
for (let file of files) {   
 if (!checkFile(file)) { 
  createMsgElem('Неверный формат файла! Выберите jpg, png.');    
  return;   
 }
}  

if (files.length > 0) {   
 createMsgElem(`Выбрано изображение ${files[0].name}`);  
}   
const formData = new FormData();  
formData.append('title', files[0].name);  
formData.append('image', files[0]);
```

## Организация HTML5-приложения
* Манипуляция стилями в JS-коде:
```javascript
item.style.backgroundColor = '#f00';
item.style.border = 'solid 1px red';
```
* Создание/удаление узлов в JS вместо их скрытия и отображения по необходимости:
```javascript
const mask = document.createElement('img');
mask.className = 'mask-elem';
mask.src = data.url;
```
* Постоянное добавление/удаление обработчиков событий, вместо использования флагов:
```javascript
function stopDrawing() {
 document.removeEventListener('mousemove', drawLine);
}
```
* Сильная завязка кода на структуру DOM дерева:
```javascript
document
 .getElementById('draw')
 .nextSibling
 .nextSibling
 .firstChild
 .classList
 .add('active');

this.uploadItem = this.menu.querySelector('li.mode.new');
```
* *Упражнение*: Клонировать и заполнять сложные узлы для данных, вместо создания их с нуля.
* Индивидуальное навешивание обработчиков событий, вместо делигирования:
```javascript
document
 .querySelectorAll('.comment')
 .forEach(comment => 
  comment.addEventListener('click', showComment));
```
* Скрытие всех узлов, вместо скрытия общего контейнера:
```javascript
document
 .querySelectorAll('.comment')
 .forEach(comment => 
  comment.classList.toggle('hidden'));
```
* Размещение данных, адресов и настроек в JS коде
```javascript
function getBrushColor() {
 switch (currentBrush.value) {
 case 'red':
  return '#f00';
 case 'green':
  return '#0f0';
 case 'blue':
  return '#00f';
 }
}

function createLink(id) {
 return `https://netology-code.github.io/hj-neto-pic-vylenna/index.html?id=${id}`;
}

function loadImage(id) {
 return fetch(`https://neto-api.herokuapp.com/pic/${id}`)
	.then(res => res.json());
}
```
* Многократное навешивание одинаковых обработчиков событий:
```javascript
function switchOnComments() {
 img.addEventListener('click', showForm);
}

function switchOffComments() {
 hideComments();
}

commentMenuItem.addEventListener('click', switchOnComments);
otherMenuItem.addEventListener('click', switchOffComments);
```

## Особенности реализации проекта
* В верстке дана разметка всех элементов для примера. При создании приложения многие удаляют их с помощью JS, вместо того чтобы удалить в HTML коде.
* Очень часто копируют функцию `throttle` из домашнего задания, и используют для «ограничения отправки маски не более 1 раза в секунду», даже не понимая как она работает, и зачем она им нужна. Ограничение проще реализовать с помощью `setInterval`.
* Булавка комментария должна встать остриём в место клика. Но в верстке при задании `left` и `top` для окна комментария, то в точку встает левый верхний угол окна, а не остриё. Чтобы добиться установки острия в нужную точку, студенты часто просто добавляют смещение в расчеты координат `left` и `top`. А нужно поправить CSS, чтобы булавка вставала правильно.
```javascript
let l = tmpPointX - tmpHeight / 2 - 21;  
let t = tmpPointY - tmpWidth / 2 - 13;
```
* Хранение текущего изображения в `localStorage` или `sessionStorage`. Так как все равно требуется реализовать открытие изображения по ссылке, то правильнее использовать `pushState` с адресом изображения обновив текущий адрес страницы.
* Пропадающие штрихи. Канвас очищается при отправке маски на сервер. А маска обновляется не сразу. Поэтому, нарисованные и отправленные штрихи пропадают на время обновления маски. Для решения отправленные на сервер штрихи можно показать в отдельном теге изображения и скрывать полсе обновления маски.
* Показ маски в теге `<canvas>`, вместо `<img>`. Маска — обычная картинка. Чтобы в браузере показать картинку, `<canvas>` не нужен.
* Не обрабатывать ответ сервера при добавлении комметария. Информация о новых комментариях поступает так же по вебсокет-соединению. Но нет гарантии, что по сокету пришла информация именно о том комментарии, который мы добавляем. Плюс, если возникнет ошибка, то по сокету не придет никакой информации.
* *Упражнение*: Нет защиты от вероятных измнений. По заданию в режиме рисования есть кисти нескольких цветов. Очевидно что при развитии приложения сами цвета и их количество может меняться. Если сейчас просто изменить какой-то один цвет, то потребуется править HTML + CSS + JS. Легко ошибиться, не поправив цвет в одном из файлов. Как решение, поместить цвета в дата-атрибут тегов. С помощью JS прокинуть каждый цвет кисти в кастомное CSS-свойство `--brush-color`. В CSS использовать именно это свойство. В приложении брать цвет кисти из дата-атрибута.
* *Упражнение*: Вынос модели (работа с сервером) в отдельный объект.
* *Упражнение*:

# Загрузка изображений

Любое модальное окно имеет возможность отображаться и закрываться. Это поведение релизовано в классе `BaseModal`. Поэтому перед началом реализации модального окна загрузки изображений, необходимо реализовать базовый класс модального окна.

## Базовый класс модального окна.
Класс `BaseModal` состоит из:
1. Конструктора, в котором необходимо выполнить:
    1. Сохраните переданный элемент в свойство объекта класса для дальнейшего взаимодействия. **ВАЖНО!!!** В приложении есть взаимодействия к двум элементам: (1) к семантик элементу и (2) к DOM элементу. DOM элемент находится на нулевой позиции элемента семантика. Для удобства элементы можно сохранить в разные свойства объекта класса.
2. Метод `open`, который вызывает метод `modal` на семантик элементе. Аргументом передаётся `'show'`.
3. Метод `close`, который вызывает метод `modal` на семантик элементе. Аргументом передаётся `'hide'`.

## Класс FileUploaderModal
Класс `FileUploaderModal` должен наследоваться от класса `BaseModal` и иметь следующий функционал:
1. Конструктор, в котором необходимо:
    1. Вызвать конструктор родителя для предварительных настроек.
    2. Выполнить регистрацию событий с помощью метода `registerEvents`.
    3. Для удобства взаимодействия с блоками загрузки изображений, их можно сразу найти и сохранить в отдельное свойство класса (если использовать `HTMLCollection`, то элементы в свойстве объекта класса будут отражать состояние в DOM).

2. Метод `registerEvents`, который выполняет подписки на события кликов по элементам:
    1. Клик по элементу крестика (в заголовке модалки) должен закрывать модальное окно.
    2. Клик по кнопке "Закрыть" должен закрывать модальное окно.
    3. Клик по кнопке "Отправить все файлы" должен вызывать метод `sendAllImages`, который будет отправлять все файлы на ЯДиск.
    4. Так как мы изначально не знаем сколько элементов изображений будет отображаться в модалке (сколько полей ввода и кнопок будет), то добавим обработчик события на весь блок тела модалки (с классом `content`). А внутри обработчика клика будем конкретно проверять, на каком элементе был клик:
        1. Если клик был на элементе с классом `input`, то необходимо удалить класс `error` у этого блока.
        2. Если клик был на кнопке отправки, то необходимо вызывать класс `sendImage` и передавать весь блок контейнер изображения (из которого будем получать исходный путь изображения для загрузки и путь куда необходимо загружать изображение (из поля ввода)).

3. Метод `showImages` получает все изображения, меняет порядок на противоположный (с помощью метода `reverse`). Для каждого изображения получает блок контейнер (с изображением, полем ввода и кнопкой загрузки). Объединяет все полученные разметки (с помощью метода `join`) и сохраняет полученную разметку (из блоков контейнеров ко всем изображениям) в свойство `innerHTML` содержимого модального окна (блока `content`).

4. Метод `getImageHTML` возвращает строку формата:
```javascript
<div class="image-preview-container">
  <img src='XXX' />
  <div class="ui action input">
    <input type="text" placeholder="Путь к файлу">
    <button class="ui button"><i class="upload icon"></i></button>
  </div>
</div>
```
где `XXX` путь к изображению.

5. Метод `sendAllImages` перебирает все блоки контейнеры изображений и для каждого вызывает метод `sendImage` для отправки на сервер.

6. Метод `sendImage` выполняет отправку изображений на ЯДиск по следующему алгоритму:
    1. Получает значение поля ввода и валидирует строку пути для загрузки изображения.
    2. Если поле ввода пустое, то необходимо добавить класс `error` блоку с классом `input` (не поле ввода, а [родительский элемент](https://semantic-ui.com/elements/input.html)). И завершить выполнение метода. Дальнейшие действия не актуальны, если путь к файлу не введён.
    3. Добавьте класс `disabled` семантик элементу поля ввода (блоку с классом `input`). 
    4. Получите путь добавляемого изображения. Необходимо загружать изображение, путь которого находится в аттрибуте `src` элемента изображения. Путь добавляемого изображения используйте из поля ввода (которое валидировалось в п.п. 6.1 - 6.2).
    5. Выполните запрос на отправку изображения (с помощью метода `Yandex.uploadFile`). После выполнения запроса (в колбеке) выполняйте следующие действия:
        1. Удалите блок контейнер добавленного изображения.
        2. Если не осталось никаких изображений в модалке, то закрывайте модальное окно.

![](./img/FileUploaderModal.png)
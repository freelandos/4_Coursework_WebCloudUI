# Взаимодействие с отображаемыми изображениями
Основным блоком в приложении на странице является элемент `ImageViewer`. В этом классе присутствуют следующие методы:
1. Конструктор, в котором необходимо выполнить:
    1. Полученный DOM элемент сохранить в свойство объекта класса.
    2. Для дальнейшего взаимодействия можно так же сохранить блок предпросмотра изображения.
    3. Так же для дальнейшего взаимодействия, можно сохранить блок, в котором будут отрисовываться все изображения.
    4. Вызовите метод добавления обработчиков событий (`registerEvents`).

2. Метод `registerEvents` должен добавлять следующие обработчики событий.
    1. Так как мы изначально не знаем сколько элементов изображений будет отображаться в блоке, то добавим обработчик события двойного клика на весь блок (который сохраняли в пункте 1.3). А внутри обработчика клика будем конкретно проверять, был ли клик на изображении. Если двойной клик был на изображении, то это изображение необходимо отобразить в области предпросмотра (элемент, который сохраняли в п.п. 1.2)
    2. Аналогичный подход необходимо реализовать для обработчика клика (одиночного клика). Если клик был по изображению, то необходимо:
        1. Инвертировать наличие класса `selected`
        2. Проверить текст в кнопке "Выбрать всё" / "Снять выделение" и работоспособность кнопки "Отправить на диск". Для проверки кнопки, используется метод `checkButtonText`.
    3. Клик по кнопке "Выбрать всё" / "Снять выделение":
        1. Получает все изображения
        2. Если во всех изображениях *хотябы одно* изображение имеет класс `selected`, то этот класс необходимо удалить для всех изображений.
        3. Если ни одно изображение не имеет класс `selected`, то этот класс необходимо добавить для всех изображений.
        4. Вызывайте метод `checkButtonText` для проверки текста в кнопке "Выбрать всё" / "Снять выделение" и работоспособность кнопки "Отправить на диск".
    4. Клик по кнопке "Посмотреть загруженные файлы":
        1. С помощью метода `App.getModal` получает модальное окно [просмотра загруженных изображений](./control_uploaded_images.md).
        2. В содержимом модального окна необходимо отобразить большой лоадер. Для этого используйте блок `'<i class="asterisk loading icon massive"></i>'`.
        3. Открывайте модальное окно с помощью метода `open`.
        4. Получите все загруженные изображения с помощью метода `Yandex.getUploadedFiles`.
        5. После выполнения метода (в колбеке) отрисовывайте все полученные изображения с помощью метода `showImages` у объекта модального окна.
    5. Клик по кнопке "Отправить на диск":
        1. С помощью метода `App.getModal` получает модальное окно [загрузки изображений](./upload_images.md).
        2. Получите все выделенные изображения (с классом `selected`).
        3. Откройте полученное модальное окно (с помощью метода `open`).
        4. Отрисуйте все модальные изображения в открытом модальном окне с помощью метода `showImages` у объекта модального окна

3. Метод `clear` должен очищать блок в котором отображаются ранее отрисованные изображения (из п.п. 1.3)

4. Метод `drawImages` выполняет следующие действия:
    1. Проверяет количество переданных изображений:
        1. Если количество изображений положительное, необходимо удалить класс `disabled` у кнопки "Выбрать всё" / "Снять выделение"
        2. В ином случае (если изображения отсутсвуют), необходимо добавить класс `disabled` кнопке "Выбрать всё" / "Снять выделение"
    2. Для каждого изображения должна быть следующая разметка: `<div class='four wide column ui medium image-wrapper'><img src='XXX' /></div>`, где `XXX` - путь к изображению. 
    3. Сформируйте разметку для всех полученных изображений и добавьте сформированную разметку к уже существующей.

5. Метод `checkButtonText`
    1. Получите все отрисованные изображения
    2. Получите кнопки с классом `select-all` и `send`.
    3. Если все полученные изображения имеют класс `selected`, то в кнопке `select-all` текст должен быть `"Снять выделение"`. В ином случае, текст должен быть `"Выбрать всё"`.
    4. Если есть хотя бы одно изображение с классом `selected`, то в кнопке `send` необходимо удалить класс `disabled`. В ином случае, добавляйте класс `disabled`.

![](./img/ImageViewer.png)
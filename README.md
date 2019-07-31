# МойСклад Phone API 1.0

МойСклад Phone API 1.0 - инструментарий для манипуляции с сущностями и создания отчетов в онлайн-сервисе МойСклад.

## Документация
Документация для `Phone API 1.0` описана с помощью нотации [API Blueprint](https://apiblueprint.org/). 
Для генерации html из исходников используется npm пакет `aglio`. 

### Правила описания сущностей
Для того, чтобы описать новую сущность в документации нужно:

1. Создать новый .apib файл. `#group` именуется так же, как и сама сущность.
2. Включить с помощью `#include` новый файл в `general.apib`. Для этого нужно понять к какой группе в панели навигации будет относиться новая сущность: Сущности, Документы, Отчёты, Розница.
После этого, в зависимости от группы в навигации, в наименование `#group` в новом apib файле добавляется соответствующий префикс с пробелом:
   * Сущности - нет префикса
   * Документы - Документ
   * Отчёты - Отчёт
   * Розница - Розница 
3. Далее описываем Resource Sections, т.е. отдельные ресурсы нашего API. Для каждого отдельного ресурса описываются все методы, применяемые к нему. Только потом идёт описание следующего (более вложенного) ресурса.
К примеру в `demand.apib` сначала описывается всё, что касается URI _/entity/demand_, а именно:

    * Основное описание сущности Документ Отгрузка
    * Все HTTP методы применяемые к данному URI, а именно:
        ```
        ### Получить список Отгрузок [GET]
        ### Создать Отгрузку[POST]
        ```
    * Описание ресурса с URI _/entity/demand/metadata_
    * Описание ресурса с URI _/entity/demand/{id}_
    * Затем - _/entity/demand/{id}/positions_
    * И в конце самый вложенный ресурс _/entity/demand/{id}/positions/{positionID}_

При описании отдельного ресурса важно:
* (Опционально) Описать атрибуты объекта, с которым работает данный ресурс. Дать важные комментарии по данному ресурсу. Этот пункт выполняется перед описанием параметров и операций.
* Описать URI параметры к данному ресурсу, такие как {id} и {positionID} из предыдущего пункта.
* Описать все применяемые к данному ресурсу операции (HTTP методы).

При описании отдельного HTTP метода:
* Описать назначение каждого Request'a (если они есть) и Respons'а
* Для каждого описываемого Request'a или Respons'а включить соответствующий .json файл представляющий собой Body запроса или ответа. Если Body отсутствует будет фраза `This response has no content`.
* После того как все Resource Section описаны, нужно в описание #group вставить краткую сводку о том, для чего предназначена данная сущность в API какие операции с ней можно производить.

### Примеры запросов
При описании Request'ов и Respons'ов нужно включить примеры в виде json. Все json файлы хранятся в папке body. Внутри body они разделены по папкам, имена которых совпадают с именами соответствующих описываемым сущностям исходных .apib файлов. К примеру все json'ы для отгрузок можно найти в body/demand; исходный файл для отгрузок - demand.apib. Также для более быстрого понимания того, что же это за json в наименовании .json пишется:
* http метод, для которого создаётся этот json
* является ли этот json телом для запроса или ответа (request, response)
* какие-либо другие указывающие на особенность данных в этом json'е пометки.

Для примеров вернёмся к нашим отгрузкам в body/demand:
* Имя файла positions_get_list.json говорит нам очень много, т.к. в этом файле лежит json, который относится к позициям отгрузки. Также это ответ на метод get (т.к. у get запроса не может быть body запроса) и слово list говорит нам о том, что в этом json описан список позиций, возвращаемых get запросом для позиций загрузки.
* Также по имени файла positions_post_one_request.json понятно, что это тело запроса на создание одной позиции отгрузки. 

### Верстка
Если вдруг необходимо поправить вёрстку финального html файла, нам нужны файлы .jade и файл .less; 
* Файлы .jade (основной из которых - mixins.jade т.к. в нём описана структура всей страницы) - шаблоны, заполняемые данными, полученными в ходе pars'а .apib файлов. Здесь можно поменять вообще всё - и то, какими данными будет заполняться шаблон, и сам шаблон (index.jade, tripleXX.jade). Также для изменения поведения элементов в шаблоне помошником вам будет scripts.js. 
* Файл .less сам по себе является css файлом, содержащим значения переменных, которые подставляются в соответствующие файлы в корневой `style` директории aglio (/usr/local/lib/node_modules/aglio/node_modules/aglio-theme-olio/styles). Если вам непонятно что конкретно переопределяется в .less файле в директории `src` документации и куда подставляются значения переменных - заходим в вышеуказанную директорию и смотрим файлы layout-default.less и variables-default.less.

### Настройка окружения для сборки документации
_Чтобы локально развернуть копию документации:_
1. Установить docker CE и docker-compose, следуя инструкциям на https://docs.docker.com/ для своей ОС
2. Склонировать репозиторий `https://github.com/moysklad/api-phone-1.0-doc.git`
3. В командной строке перейти в папку репозитория
4. Собрать docker-образ `aglio`
```
docker build -t aglio .
```
5. Предоставить право на выполнение скрипта `build.sh`
```
chmod +x build.sh
```

### Сборка документации
Выполнить скрипт
```
./build.sh
```
В папке `/output` будет сформирован файл `index.html`, содержащий документацию.

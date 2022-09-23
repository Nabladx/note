# Сервис уведомлений

Сервис разработан на django rest framework с celery и flower


## Установка и запуск

1. Склонировать репозиторий с Github:

````
git clone git@github.com:Witaly3/notification_service.git
````
2. Перейти в директорию проекта

3. Создать виртуальное окружение:

````
python -m venv venv
````

4. Активировать окружение: 

````
source\venv\bin\activate
````
5. В файле .evn заполнить необходимые данные: ```TOKEN = '<your token>'```
 
6. Установка зависимостей:

```
pip install -r requirements.txt
```

7. Создать и применить миграции в базу данных:
```
python manage.py makemigrations
python manage.py migrate
```
8. Запустить сервер
```
python manage.py runserver
```
9. Запустить celery
```
celery -A notification_service worker -l info
```
10. Запустить flower

```
celery -A notification_service flower --port=5555
```
***
### Запуск тестов
``` 
python manage.py test
```
***
## Установка проекта с помощью docker-compose


1. Склонировать репозиторий с Github
```
git clone git@github.com:Witaly3/notification_service.git
```
2. Перейти в директорию проекта
3. В файле .evn заполнить необходимые данные: ```TOKEN = '<your token>'```
4. Запустить контейнеры 
``` 
sudo docker-compose up -d
 ```
5. Остановка работы контейнеров 
```
sudo docker-compose stop
```
***
```http://0.0.0.0:8000/api/``` - api проекта

```http://0.0.0.0:8000/api/clients/``` - клиенты

```http://0.0.0.0:8000/api/mailings/``` - рассылки

```http://0.0.0.0:8000/api/mailings/fullinfo/``` - общая статистика по всем рассылкам

```http://0.0.0.0:8000/api/mailings/<pk>/info/``` - детальная статистика по конкретной рассылке

```http://0.0.0.0:8000/api/messages/``` - сообщения

```http://0.0.0.0:8000/docs/``` - docs проекта

```http://0.0.0.0:5555``` - celery flower

***

**Техзадание:** 
[https://www.craft.do/s/n6OVYFVUpq0o6L](https://www.craft.do/s/n6OVYFVUpq0o6L)

## Задача

<p>Необходимо разработать сервис управления рассылками API администрирования и получения статистики</p>

## Описание
<ul>
<li>Необходимо реализовать методы создания новой рассылки, просмотра созданных и получения статистики по выполненным рассылкам.</li>
<li>Реализовать сам сервис отправки уведомлений на внешнее API.</li>
<li>Опционально вы можете выбрать любое количество дополнительных пунктов описанных после основного.</li>
</ul>

<p>Для успешного принятия задания как выполненного достаточно корректной и рабочей реализации требований по основной части, но дополнительные пункты помогут вам продемонстрировать ваши навыки в смежных технологиях.</p>

## Критерии приёмки

<ul>
<li>Выполненное задание необходимо разместить в публичном репозитории на gitlab.com</li>
<li>Понятная документация по запуску проекта со всеми его зависимостями</li>
<li>Документация по API для интеграции с разработанным сервисом</li>
<li>Описание реализованных методов в формате OpenAPI</li>
<li>Если выполнено хотя бы одно дополнительное задание - написать об этом в документации, указав на конкретные пункты из списка ниже.</li>
</ul>

## Основное задание

<p>Спроектировать и разработать сервис, который по заданным правилам запускает рассылку по списку клиентов.</p>

### Сущность "рассылка" имеет атрибуты:

<ul>
<li>уникальный id рассылки</li>
<li>дата и время запуска рассылки</li>
<li>текст сообщения для доставки клиенту</li>
<li>фильтр свойств клиентов, на которых должна быть произведена рассылка (код мобильного оператора, тег)</li>
<li>дата и время окончания рассылки: если по каким-то причинам не успели разослать все сообщения - никакие сообщения клиентам после этого времени доставляться не должны</li>
</ul>

### Сущность "клиент" имеет атрибуты:

<ul>
<li>уникальный id клиента</li>
<li>номер телефона клиента в формате 7XXXXXXXXXX (X - цифра от 0 до 9)</li>
<li>код мобильного оператора</li>
<li>тег (произвольная метка)</li>
<li>часовой пояс</li>
</ul>

### Сущность "сообщение" имеет атрибуты:

<ul>
<li>уникальный id сообщения</li>
<li>дата и время создания (отправки)</li>
<li>статус отправки</li>
<li>id рассылки, в рамках которой было отправлено сообщение</li>
<li>id клиента, которому отправили</li>
</ul>

## Спроектировать и реализовать API для:

<ul>
<li>добавления нового клиента в справочник со всеми его атрибутами</li>
<li>обновления данных атрибутов клиента</li>
<li>удаления клиента из справочника</li>
<li>добавления новой рассылки со всеми её атрибутами</li>
<li>получения общей статистики по созданным рассылкам и количеству отправленных сообщений по ним с группировкой по статусам</li>
<li>получения детальной статистики отправленных сообщений по конкретной рассылке</li>
<li>обновления атрибутов рассылки</li>
<li>удаления рассылки</li>
<li>обработки активных рассылок и отправки сообщений клиентам</li>
</ul>

## Логика рассылки

<ul>
<li>После создания новой рассылки, если текущее время больше времени начала и меньше времени окончания - должны быть выбраны из справочника все клиенты, которые подходят под значения фильтра, указанного в этой рассылке и запущена отправка для всех этих клиентов.</li>
<li>Если создаётся рассылка с временем старта в будущем - отправка должна стартовать автоматически по наступлению этого времени без дополнительных действий со стороны пользователя системы.</li>
<li>По ходу отправки сообщений должна собираться статистика (см. описание сущности "сообщение" выше) по каждому сообщению для последующего формирования отчётов.</li>
<li>Внешний сервис, который принимает отправляемые сообщения, может долго обрабатывать запрос, отвечать некорректными данными, на какое-то время вообще не принимать запросы. Необходимо реализовать корректную обработку подобных ошибок. Проблемы с внешним сервисом не должны влиять на стабильность работы разрабатываемого сервиса рассылок.</li>
</ul>

## API внешнего сервиса отправки

<p>Для интеграции с разрабатываемым проектом в данном задании существует внешний сервис, который может принимать запросы на отправку сообщений в сторону клиентов.</p>
<p>OpenAPI спецификация находится по адресу: https://probe.fbrq.cloud/docs </p>
<p>В этом API предполагается аутентификация с использованием JWT. Токен доступа предоставлен вам вместе с тестовым заданием.</p>

## Дополнительные задания

<p>Опциональные пункты, выполнение любого количества из приведённого списка повышают ваши шансы на положительное решение о приёме</p>
<ol>
<li>организовать тестирование написанного кода</li>
<li>подготовить docker-compose для запуска всех сервисов проекта одной командой</li>
<li>сделать так, чтобы по адресу <i> /docs/ </i> открывалась страница со Swagger UI и в нём отображалось описание разработанного API. Пример: <a href="https://petstore.swagger.io" target="_blank">https://petstore.swagger.io</a></li>
<li>реализовать администраторский Web UI для управления рассылками и получения статистики по отправленным сообщениям</li>
<li>удаленный сервис может быть недоступен, долго отвечать на запросы или выдавать некорректные ответы. Необходимо организовать обработку ошибок и откладывание запросов при неуспехе для последующей повторной отправки. Задержки в работе внешнего сервиса никак не должны оказывать влияние на работу сервиса рассылок.</li>
<li>реализовать дополнительную бизнес-логику: добавить в сущность "рассылка" поле "временной интервал", в котором можно задать промежуток времени, в котором клиентам можно отправлять сообщения с учётом их локального времени. Не отправлять клиенту сообщение, если его локальное время не входит в указанный интервал.</li>

</ol>

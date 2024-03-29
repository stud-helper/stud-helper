# 1. Цель проекта

Цель проекта — разработать образовательную платформу (далее Система) с уклоном 
на практическое решение задач. Целевая аудитория - учителя, репетиторы,
работающие с группами учеников, а так же ученики старших классов, готовящиеся
к экзаменам. В Системе должен быть реализован "банк задач" с возможностью 
удобного поиска и фильтрации по категориям/сложности
подходящих задач, а так же функционал, предоставляющий возможность решать задачи с
автоматической проверкой в Системе.

# 2. Описание системы

Система состоит из следующих основных функциональных блоков:

1. Регистрация, аутентификация, верификация и авторизация
2. Функционал для преподавателя
3. Функционал для учеников
4. Функционал интеграции с Telegram
5. Функционал нотификации пользователей

Система должна быть разработана в микросервисной архитектуре.
Вот предполагаемые сервисы:
- Users - работа с пользователями, их профилями
- Groups - работа с группами пользователей
- Auth - работа с токенами, авторизационными данными
- Tasks - работа с заданиями разных типов
- Courses - работа с курсами (создание, удаление, наполнение)
- Notifier - уведомление пользователей о событиях системы
- Review - хранение комментариев к работе


## 2.1. Типы пользователей

Система, как минимум в первой своей версии, предусматривает
два типа пользователей системы: Преподаватель и Ученик.
В дальнейшем предусматривается добавление роли Администратор.


## 2.2. Регистрация

Система, как минимум в своей первой версии, не предполагается как SaaS для
разных Преподавателей. Одна конкретная инсталляция Системы привязана к одному
конкретному Преподавателю. 

Процесс заведения преподавателя в инсталляции Системы может быть реализован
в первой версии не в интерфейсе Системы, а при помощи команды на сервере,
это допускается. Этой команде заведения автора должны быть переданы на вход
следующие данные:
- username
- telegram (ссылка на профиль/тег пользователя/номер привязанного телефона)
- ФИО - полное фио пользователя

Процесс заведения автора через команду на сервере должен быть описан в
документации Системы.

Процесс регистрации учеников, конечно, должен быть реализован в
интерфейсах Системы. При регистрации ученика должны быть запрошены
следующие поля:
* username - обязательно
* telegram (ссылка на профиль/тег пользователя/номер привязанного телефона) - обязательно
* email (в дальнейшем можно будет поменять telegram на email)
* Фамилия - обязательно
* Имя - обязательно
* Отчество

Также хочется отметить, что для доступа к большинству функций системы
должен быть реализован интерфейс через "telegram бота" (далее - Бот).

При первом запуске Бота для конкретного пользователя Бот должен: 
Смотреть, есть ли пользователь с указанным именем пользователя в базе.
Если пользователь не найден, то опрашивать, есть ли аккаунт и запросить
имя пользователя.
Если нет, то запросить обычную информацию для профиля и создать аккаунт
с указанными данными.

## 2.3. Аутентификация пользователей

Аутентификация автора и подписчиков осуществляется по имени пользователя 
(username) и одноразовому паролю который можно запросить в Боте (как
реализовано при входе в Skype у Microsoft).

## 2.4. Функционал для автора

Преподаватель после аутентификации получает доступ к
своему автороскому функционалу в Системе. Этот функционал состоит из
следующих блоков:

1. Редактирование данных профиля
2. Заведение и редактирование учебных групп
3. Заведение и редактирование домашних заданий для групп
4. Создание и редактирование теоретического наполнения для групп
5. Рассылки
6. Аналитика


### 2.4.1. Редактирование профиля

В этом разделе у Преподавателя есть возможность редактирования данных
своего профиля — email, название школы/организации, описание, соц сети.
Возможные соц сети:
- Telegram
- VK 
- YouTube
- Habr

Если дизайн Системы будет подразумевать какие-то изображения для кастомизации
страницы Системы, то эти изображения тоже должны редактироваться из профиля
Преподавателя.

### 2.4.2. Заведение и редактирование учебных групп 

Преподаватель может создавать учебные группы для организации работы с группой
учеников.
Для создания в инерфейсе Системы должны быть запрошены следующие поля:
* Название группы
* Приглашенные пользователи в группу (идентификаторы пользователей)

Преподаватель будет иметь возможность добавить пользователя в обход приглашения
в следующих случаях:
- Пользователь еще не активировал бота
- Пользователь не находился в группах
- Пользователь не был создан (в таком случае будет создан пользователь без профиля, только заглушка - телеграм + id)
| В таком случае пользователь при активации Бота пользователь не увидит разницы, только по завершении регистрации у него уже будет группа

Преподаватель может приглашать любых Пользователей Системы или на прямую (указав id/tg username)
или при помощью ссылки-приглашения или с помощью кода приглашения.
| У ссылки, кода можно указать лимиты приглашений

### 2.4.3. Заведение и редактирование домашних заданий для групп

Преподаватель имеет доступ у Банку задач. В Банке заданий хранятся задания,
у каждого задания имеется Название, задание, правильный ответ.
Так же есть Квизы - задания с выбором ответа из нескольких. Будет разделение
Квизов по количеству ответов.

Преподаватель может создавать любое количество заданий в Банк задач.
Редактировать Задания в Банке задач может только пользователь, создавший задачу
или администратор системы.

Для группы Преподаватель может создавать домашние задания.
| **Важно:** Ученики могут отправлять ответ на задание только один раз.
Для создания домашнего задания для группы должны быть переданы следующие поля:
* Группа
* Список заданий (список идентификаторов)
* Комментарий - не обязательно
* Время начала (когда пользователи получат доступ к заданию) - не обязательно
* Время окончания (когда пользователи потеряют доступ к заданию) - не обязательно

При создании (когда откроется доступ) система должна уведомить пользователей об этом.

Преподавателю должно приходить уведомление о сданных домашних заданиях.

### 2.4.4. Создание и редактирование теоретического наполнения для групп

Как минимум в первой версии интерфейса Системы данный функционал не должен
быть реализован. В дальнейшем может быть реализован функционал создания теоретических
уроков, курсов по примеру платформы Stepik.

### 2.4.5. Рассылки

Преподаватель может осуществлять email/telegram рассылки из Системы. Задаётся текст письма,
выбирается, кому отправить письмо - конкретным пользователям, группе.

### 2.4.6. Аналитика

Преподаватель видит следующую аналитику:

1. Процент выполнения заданий, как в контексте выбранных задач, всего
    домашнего задания и за все ДЗ, как в контексте выбранных людей, так и всей группы.

2. Среднее время выполнение задания/задачи среди выбранных/всех учеников за период.


## 2.5. Функционал для подписчика

Пользователь сможет просматривать, принимать приглашения в учебные группы.

Также у подписчика должна быть возможность на странице своего профиля
отредактировать его данные (email, username, telegram).

В профиле должна быть возможность отключить email сообщения от системы.

На главной странице будет информация по группам, домашним заданиям.

Через Бота пользователь сможет решать часть домашних заданий.


## 2.6. Функционал интеграции с Telegram

Пользователь может работать почти со всем функционалом по средствам Бота.
Так же должна быть предусмотрена возможность создать чат для конкретной группы,
куда будут присылаться уведомления о новых домашних заданиях.


## 2.7. Уведомления о новых домашних заданиях.

Автоматически Пользователям должны приходить уведомления на Email/Telegram о всех новых
домашних заданиях в Системе. Подписчики могут отменить эти и другие письма
настройкой в профиле.

## 2.8. Подписки.

~~ Как минимум в первой версии системы это не должно быть реализовано. ~~
Должна быть реализована возможность создавать платные учебные группы,
в которые могут зайти любые пользователи за определенную плату.
Так же может быть предусмотрена модель, где для неограниченного доступа к Банку задач
пользователю надо будет иметь соответствующую подписку.


# 3. Предлагаемый стек технологий

Для реализации системы предлагается следующий стек технологий:

* Бэкенд (Python):
    - Язык Python
    - Фреймворк FastAPI
    - SQLAlchemy ORM
    - Alembic для миграций
    - Aiogram для интеграции с Telegram
* Бэкенд (Goland):
    - фреймфорк echo
    - pgx driver
    - fx DI container
    - zap logger
* Бэкенд (общие технологии)
    - gRPC - внутренние коммуникации между сервисами.
    - Postgres основная бд
    - Redis - cache
    - ElasticSearch - поисковая бд
    - Kafka - брокер сообщений
    - k8s + docker
* Фронтенд:
    - React
    - TypeScript

Для интернет-эквайринга рассматриваются Тинькофф и CloudPayments,
в ходе проекта надо сравнить технические возможности платформ и комиссии
за платежи. Важно иметь возможность оплаты с Google Pay и Apple Pay, в том
числе рекуррентные платежи (если Google Pay и Apple Pay позволяют такое
делать, уточнить в ходе проекта). Важно открыть в эквайринге другие страны
для возможности приёма оплаты не только из РФ, но и из стран СНГ и других
стран. Обсудить это в ходе проекта с эквайрингом.

Хранение файлов и изображений, загружаемых автором, должно осуществляться
в S3-совместимом хранилище.


# 4. Требования к дизайну

Минимализм, лаконичность, акцент на контент. Белый фон. Должен присутствовать
логотип Системы где-то на странице. Логотип надо разработать в рамках
этого проекта.

В нижней части страницы (в подвале) должно быть написано:

«Работает на Open Source» со ссылкой на GitHub проекта.

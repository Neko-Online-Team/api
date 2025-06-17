# HTTP API
Основное взаимодействие клиента с сервером (регистрация, получение списка котов и так далее) осуществляется через HTTP API.

Для аутентефикации через сессию, некоторые запросы требуют специальный заголовок `Authorization`. К примеру:
```
Authorization: Bearer Od8C7-....
```

> [!TIP]
> 
> Понятие *сессия* обьяснялась в [начале](/readme.md)

> [!WARNING]
> 
> Некоторые запросы (к примеру `/cats`) имеет в себе несколько HTTP-методов

CORS: \*

## Запросы
+ [/register](#register)
+ [/login](#login)
+ [/cats](#cats)
+ [/userprefs](#userprefs)
+ [/catControls](#catControls)
    + [/catControls/food](#catcontrolsfood)
    + [/catControls/water](#catcontrolswater)
    + [/catControls/toy](catcontrolstoy)
+ [/sessions](#sessions)
+ [/sessions/force](#sessionsforce)
+ [/sessions/terminate](#sessionsterminate)
+ [/sessions/terminateAll](#sessionsterminateall)
+ [/version](#version)
+ [/privacyPolicy](#privacypolicy)
+ [/ns](#ns)
+ [/market](#market)
---
# /register
+ Метод - **POST**
+ Принимает и возвращает данные в `application/json`

Не требует заголовок `Authorization`. Этот запрос служит для регистрации игрока.

```json
// Запрос принимает такие данные
{
  "name": "Chara",               // Имя аккаунта
  "username": "codechara",       // Юзернейм аккаунта (уникальный)
  "password": "chocolatelover89" // Пароль аккаунта
}

// Отвечает этим, нужно сохранить.
{
  "id": 1,                       // Айди игрока
  "session": "0c7db8-..."        // UUID Сессия
}
```

При регистрации, запрос всегда возвращает зафорсенную сессию.

Коды ошибок: 
 + 200 (OK) - Аккаунт создан (возвращает json)
 + 409 (Conflict) - Аккаунт с таким юзернеймом существует.
 + 406 (Not Acceptable) - Имя/юзернейм/пароль слишком велики. (**Ограничение в 25 символов**)
 + 500 (Internal Server Error) - Внутрення ошибка сервера, из за которой он не может обработать запрос

# /login
+ Метод - **POST**
+ Принимает и возвращает данные в `application/json`

Не требует заголовок `Authorization`. Этот запрос служит для входа игрока.

```json
// Принимает
{
  "username": "codechara",       // Юзернейм
  "password": "chocolatelover89" // Пароль
}

// Отправляет, нужно сохранить.
{
  "id": 1,                       // Айди игрока
  "session": "03nd8-..."         // Сессия
}
```

> [!NOTE]
> 
> Сессия созданная `/login` не является зафорсенной для безопасности.

Коды ошибок:
+ 200 (OK) - Успешный вход в аккаунт (возвращает json)
+ 404 (Not Found) - Аккаунт не найден
+ 500 (Internal Server Error) - Ошибка сервера.

# /cats
### GET
* Необходим заголовок `Authorization`
* Возвращает `application/json` со списком котов

Этот запрос возвращает котов игрока

```json
// Отправляет
[
  { // Кот
    "id": 1,              // Айди кота
    "name": "queuejw",    // Имя кота
    "seed": -19484378594  // Сид кота
  },
  ... // и тд
]
``` 

Коды ошибок:
+ 200 (OK) - Успешно (возвращает json)
+ 401 (Unauthorized) - Заголовок аутентефикации не указан или сессия не существует.
+ 500 (Internal Server Error) - Ошибка сервера

### PATCH
* Необходим заголовок `Authorization`
* Принимает данные в `application/json`

Этот запрос меняет имя указанному коту

```json
// Принимает
{
  "id": 1,         // Айди кота
  "name": "Кракал" // Новое имя кота
}
```

Коды ошибок:
+ 200 (OK) - Успешно (возвращает json)
+ 401 (Unauthorized) - Заголовок аутентефикации не указан или сессия не существует.
+ 500 (Internal Server Error) - Ошибка сервера

### DELETE
* Необходим заголовок `Authorization`
* Принимает данные в `application/json`

Этот запрос удаляет кота (к сожалению.)
<!-- на часах 22:37, а IDEA жручая 3 гигабайта оперативки становится всё вкуснее и вкуснее. -->

```json
// Принимает
{
  "id": 34434747483578437 // Айди кота
}
```

Коды ошибок:
+ 200 (OK) - Успешно (возвращает json)
+ 401 (Unauthorized) - Заголовок аутентефикации не указан или сессия не существует.
+ 500 (Internal Server Error) - Ошибка сервера

# /userprefs 
* **GET**-запрос
* Необходим заголовок `Authorization`
* Отправляет данные в `application/json`

Этот запрос отправляет userprefs.

> [!TIP]
> 
> Userprefs хранят в себе имя игрока, ncoins и количество припасов в хранилище.

```json
// Отправляет
{
  "name": "Chara",
  "ncoins": 994,
  "food": 42,
  "water": 2000,
  "toys": 993
}
```

Коды ошибок:
+ 200 (OK) - Успешно (возвращает json)
+ 401 (Unauthorized) - Заголовок аутентефикации не указан или сессия не существует.
+ 500 (Internal Server Error) - Ошибка сервера

# /catControls
* **GET**-запрос
* Необходим заголовок `Authorization`
* Отправляет данные в `application/json`

Этот запрос возвращает состояние поилок

```json
// Отправляет
{
  "food": true,
  "water": 200,
  "toy": false
}
```

+ 200 (OK) - Успешно (возвращает json)
+ 401 (Unauthorized) - Заголовок аутентефикации не указан или сессия не существует.
+ 500 (Internal Server Error) - Ошибка сервера

# /catControls/food
* **PATCH**-запрос
* Необходим заголовок `Authorization`
* Принимает данные в `application/json`

Меняет состояние тарелки с едой

```json
// Принимает
{
  "value": true
}
```

+ 200 (OK) - Успешно (возвращает json)
+ 401 (Unauthorized) - Заголовок аутентефикации не указан или сессия не существует.
+ 500 (Internal Server Error) - Ошибка сервера


# /catControls/water
* **PATCH**-запрос
* Необходим заголовок `Authorization`
* Принимает данные в `application/json`

Меняет состояние тарелки с водой

```json
// Принимает
{
  "value": 134 
}
```

> [!NOTE]
> 
> Обьём воды равен от 0 до 200. Если указать значение уходящее за этот диапазон, то оно подгониться под этот диапазон и вернёт 200.

+ 200 (OK) - Успешно (возвращает json)
+ 401 (Unauthorized) - Заголовок аутентефикации не указан или сессия не существует.
+ 500 (Internal Server Error) - Ошибка сервера


# /catControls/toy
* **PATCH**-запрос
* Необходим заголовок `Authorization`
* Принимает данные в `application/json`

Меняет состояние игрушки

```json
// Принимает
{
  "value": true
}
```

+ 200 (OK) - Успешно (возвращает json)
+ 401 (Unauthorized) - Заголовок аутентефикации не указан или сессия не существует.
+ 500 (Internal Server Error) - Ошибка сервера

# /sessions
* **GET**-запрос
* Необходим заголовок `Authorization`
* Отправляет данные в `application/json`

Отправляет подключённые сессии

```json
[
  { // Сессия
    "agent": "Mozilla/5.0 (Windows; U; Windows NT 10.0; Win64; x64; en-US) Gecko/20100101 Firefox/47.3", // user-agent клиента/браузера
    "createdAt": 1750019052,                                                                             // Время входа в аккаунт в unixtime
    "force": true,                                                                                       // форс сессия?
    "ip": "0.0.0.0"                                                                                      // IP
  },
  ... // другие
]
```

+ 200 (OK) - Успешно (возвращает json)
+ 401 (Unauthorized) - Заголовок аутентефикации не указан или сессия не существует.
+ 500 (Internal Server Error) - Ошибка сервера
+ 403 (Forbidden) - Сессия слишком новая.

# /sessions/force
* **PATCH**-запрос
* Необходим заголовок `Authorization`
* Принимает данные в `application/json`

Этот запрос форсит указанную сессию.

```json
// Принимает
{
  "session": "0d3bc-..." 
}
```

+ 200 (OK) - Успешно
+ 401 (Unauthorized) - Заголовок аутентефикации не указан или сессия не существует.
+ 500 (Internal Server Error) - Ошибка сервера
+ 403 (Forbidden) - Сессия слишком новая.

# /sessions/terminate
* **DELETE**-запрос
* Необходим заголовок `Authorization`
* Принимает данные в `application/json`

Удаляет указанную сессию

```json
// Принимает
{
  "session": "d83bd-..."
}
```

+ 200 (OK) - Успешно
+ 401 (Unauthorized) - Заголовок аутентефикации не указан или сессия не существует.
+ 500 (Internal Server Error) - Ошибка сервера
+ 403 (Forbidden) - Сессия слишком новая.

# /sessions/terminateAll

* **DELETE**-запрос
* Необходим заголовок `Authorization`
* Не принимает и не возвращает данные.

Удаляет все сессии кроме той, с которой совершён запрос.

+ 200 (OK) - Успешно
+ 401 (Unauthorized) - Заголовок аутентефикации не указан или сессия не существует.
+ 500 (Internal Server Error) - Ошибка сервера
+ 403 (Forbidden) - Сессия слишком новая.

# /version
+ **GET**-запрос
+ Возвращает `application/json`

Возвращает поддерживаемую версию Neko: Online

* 200 (OK) - Успешно

# /privacyPolicy
+ **GET**-запрос
+ Возвращает `text/plain`

Возвращает политику конфиденциальности Neko: Online

* 200 (OK) - Успешно

# /ns
[Подключение к WebSocket уведомлениям](/wsapi.md)

# /market
### GET
+ Возвращает данные в `application/json`

```json
[
  { // Карточка
    "id": 0,        // Айди карточки
    "type": "food", // Тип товара (food, water, toy)
    "amount": 1,    // Количество
    "price": 99     // Цена (в NCoins)
  },
  ... // Далее...
]
```

+ 200 (OK) - Успешно (отправляет json)

### POST
+ Требует заголовок `Authorization`
+ Принимает данные в `application/json`

```json
{
  "id": 1 // Айди карточки
}
```

+ 200 (OK) - Товар куплен
+ 401 (Unauthorized) - Заголовок аутентефикации не указан или сессия не существует.
+ 500 (Internal Server Error) - Ошибка сервера
+ 406 (Not Acceptable) - Недостаточно средств
+ 404 (Not Found) - Не найден такой товар

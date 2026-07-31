**1 Sequence-диаграмма**

```mermaid
%%{init: {
  "theme": "base",
  "themeVariables": {
    "primaryColor": "#3d74f3",
    "primaryTextColor": "#111827",
    "primaryBorderColor": "#1a1c74",
    "lineColor": "#6366f1",
    "signalColor": "#afbcd6",
    "signalTextColor": "#8eaee6",
    "actorBkg": "#1a2f4b",
    "actorBorder": "#2563eb",
    "actorTextColor": "#a4b6df",
    "activationBkgColor": "#53f5f5",
    "activationBorderColor": "#3d74f3",
    "fontFamily": "Inter, Arial, sans-serif"
  },
  "sequence": {
    "showSequenceNumbers": true,
    "diagramMarginX": 14
  }
}}%%
sequenceDiagram
    actor K as Клиент
    participant API
    participant BD@{"type": "database"} as БД
    K->>API: Логин
    alt Присутствует HTTP-заголовок Authorization
      API->>BD: Запрос логина и хэша пароля пользователя
      activate BD
      BD->>API: Ответ БД
      deactivate BD
      alt Пользователь верифицирован
      API->>BD: Запрос данных профиля пользователя
      activate BD
      BD->>API: Ответ БД
      deactivate BD
      API->>K: 200 OK
      else Пользователь не верифицирован
      API->>K: 401 Unauthorized
      end
    else Отвутствует HTTP-заголовок Authorization
    API->>K: 401 Unauthorized
    end
```

**2 Таблицы контрактов API**

**2.1 Таблица входных параметров**

| Имя параметра | Тип данных | Обязательность | Описание                                                      |
| ------------- | ---------- | -------------- | ------------------------------------------------------------- |
| Authorization | string     | да             | Заголовок Basic Auth в формате Basic <base64(login:password)> |

**2.2 Таблица выходных параметров при успешном запросе**

| Имя параметра | Тип данных    | Описание                    |
| ------------- | ------------- | --------------------------- |
| id            | number        | Идентификатор сотрудника    |
| firstName     | string        | Имя сотрудника              |
| lastName      | string        | Фамилия сотрудника          |
| email         | string        | Корпоративная почта         |
| department    | string        | Подразделение               |
| position      | string        | Должность                   |
| phone         | string        | Телефон                     |
| roles         | array[string] | Роли пользователя в системе |
| createdAt     | string        | Дата создания профиля       |
| updatedAt     | string        | Дата последнего обновления  |

**2.1.3 Таблица выходных параметров при ответе с ошибкой**

| Имя параметра | Тип данных | Описание              |
| ------------- | ---------- | --------------------- |
| code          | string     | Код ошибки приложения |
| message       | string     | Текст ошибки          |

**3 Таблица маппинга**

| Поле нашей системы | Поле внешнего API | Логика преобразования                                                                  |
| ------------------ | ----------------- | -------------------------------------------------------------------------------------- |
| Authorization      | login, password   | Отделить схему Basic от токена, Декодировать из Base64, Разделить по первому двоеточию |

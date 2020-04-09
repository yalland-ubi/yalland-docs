#### Версия 1.3.6
#### Тестовый сервер: https://testbackend.yalland.com:5000/
#### Соглашения.
1. Request Method: POST.
2. Content-type: application/json.
3. Поля, содержащие фото, передаются в формате base64.
4. Поля, содержащие пароли, должны быть захэшированы в SHA256, хэш должен передаваться в ВЕРХНЕМ регистре.
5. Поля, содержащие дату, передаются в формате "%Y-%m-%d".
6. Поля, содержащие дату и время, передаются в формате "%Y-%m-%d %H:%M:%S".
7. Номер телефона передается в формате "+7XXXXXXXXXX".
8. Пол кодируется следующим образом: 0 – не определен, 1 – мужской, 2 – женский.
9. В запросах и ответах значения полей, указанных в кавычках передается как string.
10. В запросах и ответах значения полей, указанных без кавычек передается как Int, UInt, Int64, UInt64, float или double.
11. Все запросы / ответы идут в формате json.
12. ssid - уникальный идентификатор сессии, он создается при авторизации пользователя, администратора, или является фиксированным, прописанным в настройках сайта / приложения / бэкенда, через который проходит регистрация или администрирование.
13. Пользовательская сессия имеет время жизни 60 минут, регулируется конфигурационным файлом, обновление времени жизни происходит при каждом входящем запросе.
14. На все невалидные запросы отправляется ответ вида: 
```json
{
    "code":ERROR_CODE,
    "message":"ERROR_MESSAGE",
    "result":"fail"
}
```

#### Разрешения.
|  |                           |Без сессии|Сайт|Администратор|Оператор|Регистратор|Пользователь|Магазин|Приложение|Бэкенд|
|--|---------------------------|----------|----|-------------|--------|-----------|------------|-------|----------|------|
|1 |checkSession               |+         |    |             |        |           |            |       |          |      |
|2 |login                      |+         |    |             |        |           |            |       |          |      |
|3 |logout                     |+         |    |             |        |           |            |       |          |      |
|6 |personList                 |          |    |+            |+       |           |            |       |          |      |
|7 |personShow                 |          |    |+            |+       |           |            |       |          |      |
|8 |personCreate               |          |+   |             |        |           |            |       |+         |      |
|9 |personEdit                 |          |    |+            |        |           |            |       |          |      |
|10|confirmPhone               |          |+   |             |        |           |+           |       |          |      |
|11|personFind                 |          |    |+            |        |           |            |       |          |      |
|12|personRemove               |          |    |+            |        |           |            |       |          |      |
|15|personLogin                |+         |    |             |        |           |            |       |          |      |
|18|changePassword             |          |    |             |        |           |+           |       |          |      |
|19|restorePassword            |+         |    |             |        |           |            |       |          |      |
|22|createPromocodePool        |          |    |+            |        |           |            |       |          |      |
|23|createReferralAction       |          |    |+            |        |           |            |       |          |      |
|24|createPromocodePoolByAction|          |    |             |        |           |+           |       |          |      |
|25|getReferralActions         |          |    |+            |        |           |            |       |          |      |
|26|getActiveReferralActions   |          |    |             |        |           |+           |       |          |      |
|27|editPoolSettings           |          |    |+            |        |           |            |       |          |      |
|28|getReferralPayments        |          |    |+            |        |           |            |       |          |      |
|29|editReferralPayment        |          |    |+            |        |           |            |       |          |      |
|30|confirmPhoneRequest        |          |    |             |        |           |+           |       |          |      |
|31|personVerify               |          |    |             |        |           |+           |       |          |      |
|32|setPassword                |          |    |             |        |           |+           |       |          |      |
|33|setPhoto                   |          |    |             |        |           |+           |       |          |      |
|34|findPersonByWallet         |          |    |             |        |           |            |       |          |+     |
|35|getPersonTypeByWallet      |          |    |             |        |           |            |       |+         |      |
|36|logCreate                  |          |    |             |        |           |            |       |+         |      |
|37|getLogs                    |          |    |+            |        |           |            |       |          |      |
|38|getServerVersion           |          |+   |             |        |           |            |       |+         |+     |
|39|emailIsUnique              |          |+   |             |        |           |            |       |+         |+     |
|40|phoneIsUnique              |          |+   |             |        |           |            |       |+         |+     |
|41|acceptPersonVerify         |          |    |+            |+       |           |            |       |          |      |
|42|updateWalletState          |          |    |             |        |           |            |       |          |+     |
|43|setPersonSnId              |          |    |+            |        |           |            |       |          |      |
|44|getPersonSnId              |          |    |+            |        |           |            |       |          |      |
|45|getPersonDataByWallet      |          |    |             |        |           |            |       |          |+     |
|47|walletOwnerIsVerified      |          |    |             |        |           |            |       |          |+     |
|48|checkPromoPayByWallet      |          |    |             |        |           |            |       |          |+     |
|49|updatePaymentStatus        |          |    |             |        |           |            |       |          |+     |
|50|getTariffList              |          |    |+            |        |           |            |       |          |      |

Все разрешения динамически регулируются через БД.
    
#### [1] Проверка валидности сессии.
Запрос: 
```json
{
    "request":"checkSession",
    "ssid":"SESSION_ID"
}
```
Ответ: 
```json
{
    "result":"success",
    "role":ROLE_ID,
    "roleRights":
    [
        REQUEST_ID1,
        REQUEST_ID2,
        ...
    ]
    <,"person":
    {
        "personId":ID,
        "registerDate":"REGISTER_DATE",
        "firstName":"FIRST_NAME",
        "secondName":"SECOND_NAME",
        "gender":GENDER,
        "dateOfBirth":"DATE_OF_BIRTH",
        "phoneNumber":"PHONE_NUMBER",
        "email":"EMAIL",
        "passport":"PASSPORT_DATA",
        "dateOfIssue":"DATE_OF_ISSUE",
        "organization":"ORGANIZATION",
        "registerAddress":"REGISTER_ADDRESS",
        "walletAddress":"WALLET_ADDRESS",
        "rowType":ROW_TYPE,
        "rowOptions":ROW_OPTIONS,
        "isActive":IS_ACTIVE,
        "congeniality":CONGENIALITY,
        "balance":BALANCE
    }>
}
```
"role" - идентификатор роли, под которой создана сессия. На текущий момент существуют следующие роли:
- 1    - сайт;
- 2    - администратор;
- 3    - оператор;
- 4    - регистратор;
- 5    - пользователь;
- 6    - магазин;
- 7    - приложение;
- 8    - бэкенд.

Массив "person" передается только в случае, если сессия создана от пользователя системы (5).

#### [2] Авторизация управляющего персонала.
Запрос: 
```json
{
    "request":"login",
    "name":"USER_NAME",
    "password":"PASSWORD_INTO_SHA256"
}
```
Ответ:
```json
{
    "result":"success",
    "ssid":"SESSION_ID",
    "role":ROLE_ID,
    "roleRights":
    [
        REQUEST_ID1,
        REQUEST_ID2,
        ...
    ]
}
````

#### [3] Логаут.
Запрос:
```json
{
    "request":"logout"
}
```
Ответ:
```json
{
    "result":"success"
}
```

#### [6] Просмотр списка пользователей.
Запрос:
```json
{
    "request":"personList",
    "ssid":"SESSION_ID"
    <,"pageNumber":PAGE_NUMBER>
    <,"amountOnPage":AMOUNT_ON_PAGE>
    <,"orderBy":"FIELD_FOR_ORDER">
    <,"orderAsc":TRUE>
    <,"filter":
    {
        "personId":ID,
        "registerDate":"REGISTER_DATE",
        "firstName":"FIRST_NAME",
        "secondName":"SECOND_NAME",
        "gender":GENDER,
        "dateOfBirth":"DATE_OF_BIRTH",
        "phoneNumber":"PHONE_NUMBER",
        "email":"EMAIL",
        "passport":"PASSPORT_DATA",
        "dateOfIssue":"DATE_OF_ISSUE",
        "organization":"ORGANIZATION",
        "registerAddress":"REGISTER_ADDRESS",
        "walletAddress":"WALLET_ADDRESS",
        "rowType":ROW_TYPE,
        "rowOptions":ROW_OPTIONS,
        "isActive":IS_ACTIVE,
        "congeniality":CONGENIALITY,
        "balance":BALANCE
    }>
    <,"filterRules":
    {
        "personId":COMPARE_TYPE,
        "registerDate":COMPARE_TYPE,
        "firstName":COMPARE_TYPE,
        "secondName":COMPARE_TYPE,
        "gender":COMPARE_TYPE,
        "dateOfBirth":COMPARE_TYPE,
        "phoneNumber":COMPARE_TYPE,
        "email":COMPARE_TYPE,
        "passport":COMPARE_TYPE,
        "dateOfIssue":COMPARE_TYPE,
        "organization":COMPARE_TYPE,
        "registerAddress":COMPARE_TYPE,
        "walletAddress":COMPARE_TYPE,
        "rowType":COMPARE_TYPE,
        "rowOptions":COMPARE_TYPE,
        "isActive":COMPARE_TYPE,
        "congeniality":COMPARE_TYPE,
        "balance":COMPARE_TYPE
    }>
}
````
Ответ:
```json
{
    "result":"success",
    "pagesAmount":PAGES_AMOUNT,
    "pageNumber":PAGE_NUMBER,
    "personList":
    [
        {
            "personId":ID,
            "registerDate":"REGISTER_DATE",
            "firstName":"FIRST_NAME",
            "secondName":"SECOND_NAME",
            "gender":GENDER,
            "dateOfBirth":"DATE_OF_BIRTH",
            "phoneNumber":"PHONE_NUMBER",
            "email":"EMAIL",
            "passport":"PASSPORT_DATA",
            "dateOfIssue":"DATE_OF_ISSUE",
            "organization":"ORGANIZATION",
            "registerAddress":"REGISTER_ADDRESS",
            "walletAddress":"WALLET_ADDRESS",
            "rowType":ROW_TYPE,
            "rowOptions":ROW_OPTIONS,
            "isActive":IS_ACTIVE,
            "congeniality":CONGENIALITY,
            "balance":BALANCE
        },
        ...
    ]
}
```
"COMPARE_TYPE" - тип сравнения:
- 0    - like (применяется по умолчанию, если не задан тип сравнения);
- 1    - equal;
- 2    - less;
- 3    - less or equal;
- 4    - greater;
- 5    - greater or equal.

#### [7] Просмотр пользователя.
Запрос:
```json
{
    "request":"personShow",
    "ssid":"SESSION_ID",
    "personId":ID
}
````
Ответ:
```json
{
    "result":"success",
    "ssid":"SESSION_ID",
    <,"confirmPhone":true>,
    "person":
    {
        "personId":ID,
        "registerDate":"REGISTER_DATE",
        "firstName":"FIRST_NAME",
        "secondName":"SECOND_NAME",
        "gender":GENDER,
        "dateOfBirth":"DATE_OF_BIRTH",
        "phoneNumber":"PHONE_NUMBER",
        "email":"EMAIL",
        "passport":"PASSPORT_DATA",
        "dateOfIssue":"DATE_OF_ISSUE",
        "organization":"ORGANIZATION",
        "registerAddress":"REGISTER_ADDRESS",
        "walletAddress":"WALLET_ADDRESS",
        "rowType":ROW_TYPE,
        "rowOptions":ROW_OPTIONS,
        "isActive":IS_ACTIVE,
        "congeniality":CONGENIALITY,
        "balance":BALANCE,
        "photo":"PHOTO_ENCODED_BASE64",
        "passportPhoto":"PHOTO_ENCODED_BASE64"
    }
}
```

#### [8] Регистрация нового пользователя.
Запрос:
```json
{
    "request":"personCreate",
    "ssid":"SESSION_ID",
    "person":
    {
        "firstName":"FIRST_NAME",
        "secondName":"SECOND_NAME",
        "gender":GENDER,
        "dateOfBirth":"DATE_OF_BIRTH",
        "phoneNumber":"PHONE_NUMBER",
        "email":"EMAIL",
        "passport":"PASSPORT_DATA",
        "dateOfIssue":"DATE_OF_ISSUE",
        "organization":"ORGANIZATION",
        "registerAddress":"REGISTER_ADDRESS",
        "walletAddress":"WALLET_ADDRESS",
        "promocodeValue":"PROMOCODE_VALUE",
        "password":"PASSWORD_INTO_SHA256",
        "photo":"PHOTO_ENCODED_BASE64"
    }
}
```
Ответ:
```json
{
    "result":"success",
    "ssid":"SESSION_ID",
    "role":ROLE_ID
    "roleRights":
    [
        REQUEST_ID1,
        REQUEST_ID2,
        ...
    ]
    <,"confirmPhone":true>,
    "person":
    {
        "personId":ID,
        "registerDate":"REGISTER_DATE",
        "firstName":"FIRST_NAME",
        "secondName":"SECOND_NAME",
        "gender":GENDER,
        "dateOfBirth":"DATE_OF_BIRTH",
        "phoneNumber":"PHONE_NUMBER",
        "email":"EMAIL",
        "passport":"PASSPORT_DATA",
        "dateOfIssue":"DATE_OF_ISSUE",
        "organization":"ORGANIZATION",
        "registerAddress":"REGISTER_ADDRESS",
        "walletAddress":"WALLET_ADDRESS",
        "rowType":ROW_TYPE,
        "rowOptions":ROW_OPTIONS,
        "isActive":IS_ACTIVE,
        "congeniality":CONGENIALITY,
        "balance":BALANCE
    }
}
```
В случае, если в ответ приходит "confirmPhone":true, значит на телефон пользователя отправлен код для подтверждения.
После регистрации на почту пользователя (если она задана и в опциях сервера указана отправка писем) отправляется письмо.
Сразу после регистрации, если регистрация происходила с сайта или из приложения, устанавливается пользовательская сессия, как при входе в личный кабинет.

Если пользователь регистрировался по промокоду и в свойствах акции указано начисление хозяину промокода, то отправляется запрос на добавление валюты хозяину промокода. Запрос:
```json
{
    "request":"payUserByPromo",
    "walletAddress":"WALLET_ADDRESS",
    "stringTariffId":"TARIFF_ID",
    "yalAmount":AMOUNT,
    "referralPaymentId":PAYMENT_ID,
    "ssid":"SESSION_ID"
}
```
Ответ:
```json
{
    "result":"success"
}
```
Расшифровка полей:
- firstName - имя, передается в ВЕРХНЕМ регистре;
- secondName - фамилия, передается в ВЕРХНЕМ регистре;
- gender - пол (0 – не определен, 1 – мужской, 2 – женский);
- dateOfBirth - дата рождения в формате "%Y-%m-%d";
- phoneNumber - номер телефона в формате "+7XXXXXXXXXX";
- email - электронная почта
- passport - паспорт в формате "XXXX XXXXXX";
- dateOfIssue - дата выдачи паспорта в формате "%Y-%m-%d";
- organization - организация, выдавшая паспорт;
- registerAddress - регион регистрации;
- walletAddress - адрес кошелька;
- photo - фотография;
- promocodeValue - промокод;
- congeniality - процент сходства фото и фото в паспорте
- balance - временное поле, баланс на момент перехода к новой версии программы

Обязательные поля:
- firstName
- secondName
- dateOfBirth
- phoneNumber
- email
- registerAddress
- walletAddress

Уникальные поля (ищутся совпадения в БД):
- phoneNumber
- email
- passport (если не пустое)
- walletAddress
- photo (если не пустое, и включена валидация на сервере)

Поле "rowType" отмечает тип регистрации, представляет из себя битовую маску:
- RT_UNDEFINED = 0
- RT_MINIMAL = 1 - минимальная
- RT_PASSWORD = 2 - задан пароль
- RT_PHOTO = 4 - установлено фото
- RT_VERIFY_STARTED = 8 - запрошена верификация
- RT_VERIFIED = 16 - верифицирована (получено фото паспорта и валидированы его данные)
- RT_VERIFY_REJECT = 32 - верификация отклонена
- RT_BLOCKED = 64 - заблокирована
- RT_TARIFF_REQUESTED = 128 - отправлен запрос на установку тарифа
- RT_TARIFF_IS_SET = 256 - тариф установлен
- RT_INVALID_WALLET_ADDRESS = 512 - невалидный адрес кошелька

Поле "rowOptions" отмечает опции регистрации, представляет из себя битовую маску:
- O_UNDEFINED = 0
- O_PHONE_CONFIRMED = 1 - подтвержден телефон
- O_EMAIL_CONFIRMED = 2 - подтверждена электронная почта

Поле "isActive" – булевое, определяет, производятся-ли пользователю выплаты (устанавливалось через панель администрирования, при добавлении тарифа пользователю, устаревшее, в дальнейшем не планируется к использованию).

#### [9] Редактирование пользователя.
Запрос:
```json
{
    "request":"personEdit",
    "ssid":"SESSION_ID",
    "person":
    {
        "personId":ID,
        "firstName":"FIRST_NAME",
        "secondName":"SECOND_NAME",
        "gender":GENDER,
        "dateOfBirth":"DATE_OF_BIRTH",
        "phoneNumber":"PHONE_NUMBER",
        "email":"EMAIL",
        "passport":"PASSPORT_DATA",
        "dateOfIssue":"DATE_OF_ISSUE",
        "organization":"ORGANIZATION",
        "registerAddress":"REGISTER_ADDRESS",
        "walletAddress":"WALLET_ADDRESS",
        "photo":"PHOTO_ENCODED_BASE64",
        "passportPhoto":"PHOTO_ENCODED_BASE64",
        "rowType":ROW_TYPE,
        "rowOptions":ROW_OPTIONS,
        "isActive":IS_ACTIVE 
    }
}
```
Ответ:
```json
{
    "result":"success",
    "personId":ID,
    "rowType":ROW_TYPE,
    "rowOptions":ROW_OPTIONS,
    "congeniality":CONGENIALITY
}
```
Отправляться должны только те поля, которые подверглись изменениям.

#### [10] Подтверждение телефона.
Запрос:
```json
{
    "request":"confirmPhone",
    "ssid":"SESSION_ID",
    "confirmPhoneValue":"VALUE"
    <,"personId":ID>
}
```
Ответ:
```json
{
    "result":"success",
    "personId":ID,
    "rowOptions":ROW_OPTIONS
}
```
Если в запросе "personId":ID не указан, то пользователь определяется по сессии. Данный функционал предназначен для возможности регистрации и подтверждения телефона пользователем через сайт или панель администрирования.

#### [15] Авторизация пользователя.
Запрос:
```json
{
    "request":"personLogin",
    "email":"EMAIL" / "phoneNumber":"PHONE_NUMBER" / "walletAddress":"WALLET_ADDRESS",
    "password":"PASSWORD_INTO_SHA256"
}
```
Ответ:
```json
{
    "result":"success",
    "ssid":"SESSION_ID",
    "role":ROLE_ID ,
    "roleRights":
    [
        REQUEST_ID1,
        REQUEST_ID2,
        ...
    ],
    "person":
    {
        "personId":ID,
        "registerDate":"REGISTER_DATE",
        "firstName":"FIRST_NAME",
        "secondName":"SECOND_NAME",
        "gender":GENDER,
        "dateOfBirth":"DATE_OF_BIRTH",
        "phoneNumber":"PHONE_NUMBER",
        "email":"EMAIL",
        "passport":"PASSPORT_DATA",
        "dateOfIssue":"DATE_OF_ISSUE",
        "organization":"ORGANIZATION",
        "registerAddress":"REGISTER_ADDRESS",
        "walletAddress":"WALLET_ADDRESS",
        "rowType":ROW_TYPE,
        "rowOptions":ROW_OPTIONS,
        "isActive":IS_ACTIVE,
        "congeniality":CONGENIALITY,
        "balance":BALANCE
    }
}
```

#### [19] Восстановление пароля.
Запрос:
```json
{
    "request":"restorePassword",
    "email":"EMAIL" / "phoneNumber":"PHONE_NUMBER"
}
```
Ответ:
```json
{
    "result":"success"
}
```

#### [23] Создание реферальной акции.
Запрос:
```json
{
    "request":"createReferralAction",
    "ssid":"SESSION_ID",
    "referralAction":
    {
        "maxRegisterAmount":MAX_REGISTER,
        "tariffId":TARIFF_ID,
        "maxUseAmount":USE_AMOUNT,
        "expireDate":"DATE",
        "options":OPTIONS,
        "isActive":TRUE,
        "description":"DESCRIPTION"
    }
}
```
Ответ:
```json
{
    "result":"success"
}
```

#### [24] Создание пула промокодов по акции.
Запрос:
```json
{
    "request":"createPromocodePoolByAction",
    "ssid":"SESSION_ID",
    "poolSettingsId":ID
}
```
Ответ:
```json
{
    "result":"success"
}
```
Только верифицированные пользователи имеют право на участие в акции.
Пользователь имеет право создать только один промокод в рамках одной акции.

#### [25] Получение списка всех акций.
Запрос:
```json
{
    "request":"getReferralActions",
    "ssid":"SESSION_ID"
}
```
Ответ:
```json
{
    "result":"success",
    "referralActionsList":
    [
        {
            "poolSettingsId":ID,
            "createDate":"DATE",
            "maxRegisterAmount":MAX_REGISTER,
            "registerAmount":REGISTER_AMOUNT,
            "tariffId":TARIFF_ID,
            "maxUseAmount":USE_AMOUNT,
            "expireDate":"DATE",
            "options":OPTIONS,
            "isActive":TRUE,
            "description":"DESCRIPTION",
        },
        ...
    ]
}
```

#### [26] Получение списка активных акций.
Запрос:
```json
{
    "request":"getActiveReferralActions",
    "ssid":"SESSION_ID"
}
```
Ответ:
```json
{
    "result":"success",
    "referralActionsList":
    [
        {
            "poolSettingsId":ID,
            "createDate":"DATE",
            "maxRegisterAmount":MAX_REGISTER,
            "registerAmount":REGISTER_AMOUNT,
            "tariffId":TARIFF_ID,
            "maxUseAmount":USE_AMOUNT,
            "expireDate":"DATE",
            "options":OPTIONS,
            "isActive":TRUE,
            "description":"DESCRIPTION",
            "promocodesList":
            [
                {
                    "promocodeId":ID,
                    "promocodeValue":"PROMOCODE_VALUE",
                    "promocodeUseAmount":AMOUNT
                },
                ...
            ]
        },
        ...
    ]
}
```

#### [30] Запрос на подтверждения телефона.
Запрос:
```json
{
    "request":"confirmPhoneRequest",
    "ssid":"SESSION_ID"
}
```
Ответ:
```json
{
    "result":"success"
}
```

#### [31] Верификация пользователя.
Запрос:
```json
{
    "request":"personVerify",
    "ssid":"SESSION_ID",
    "person":
    {
        "passportPhoto":"PHOTO_ENCODED_BASE64",
        "passport":"PASSPORT_DATA",
        "dateOfIssue":"DATE_OF_ISSUE",
        "organization":"ORGANIZATION"
    }
}
```
Ответ:
```json
{
    "result":"success",
    "person":
    {
        "personId":ID,
        "registerDate":"REGISTER_DATE",
        "firstName":"FIRST_NAME",
        "secondName":"SECOND_NAME",
        "gender":GENDER,
        "dateOfBirth":"DATE_OF_BIRTH",
        "phoneNumber":"PHONE_NUMBER",
        "email":"EMAIL",
        "passport":"PASSPORT_DATA",
        "dateOfIssue":"DATE_OF_ISSUE",
        "organization":"ORGANIZATION",
        "registerAddress":"REGISTER_ADDRESS",
        "walletAddress":"WALLET_ADDRESS",
        "rowType":ROW_TYPE,
        "rowOptions":ROW_OPTIONS,
        "isActive":IS_ACTIVE,
        "congeniality":CONGENIALITY,
        "balance":BALANCE
    }
}
```

#### [32] Первичная установка пароля.
Запрос:
```json
{
    "request":"setPassword",
    "ssid":"SESSION_ID",
    "password":"PASSWORD_INTO_SHA256"
}
```
Ответ:
```json
{
    "result":"success",
    "rowType":ROW_TYPE
}
```

#### [33] Первичная установка фото.
Запрос:
```json
{
    "request":"setPhoto",
    "ssid":"SESSION_ID",
    "photo":"PHOTO_ENCODED_BASE64"
}
```
Ответ:
```json
{
    "result":"success",
    "rowType":ROW_TYPE
}
```

#### [34] Получение данных пользователя по адресу кошелька.
Запрос:
```json
{
    "request":"findPersonByWallet",
    "walletAddress":"WALLET_ADDRESS",
    "ssid":"SESSION_ID"
}
```
Ответ:
```json
{
    "result":"success",
    "person":
    {
        "personId":ID,
        "registerDate":"REGISTER_DATE",
        "firstName":"FIRST_NAME",
        "secondName":"SECOND_NAME",
        "gender":GENDER,
        "dateOfBirth":"DATE_OF_BIRTH",
        "phoneNumber":"PHONE_NUMBER",
        "email":"EMAIL",
        "passport":"PASSPORT_DATA",
        "dateOfIssue":"DATE_OF_ISSUE",
        "organization":"ORGANIZATION",
        "registerAddress":"REGISTER_ADDRESS",
        "walletAddress":"WALLET_ADDRESS",
        "rowType":ROW_TYPE,
        "rowOptions":ROW_OPTIONS,
        "isActive":IS_ACTIVE,
        "congeniality":CONGENIALITY,
        "balance":BALANCE
    }
}
```

#### [35] Получение типа записи пользователя по адресу кошелька.
Запрос:
```json
{
    "request":"getPersonTypeByWallet",
    "walletAddress":"WALLET_ADDRESS",
    "ssid":"SESSION_ID"
}
```
Ответ:
```json
{
    "result":"success",
    "rowType":ROW_TYPE
}
```
Значения поля "rowType" описано в пункте "[8] Регистрация нового пользователя".

#### [36] Сохранение логов приложения.
Запрос:
```json
{
    "request":"logCreate",
    "ssid":"SESSION_ID",
    "log":
    {
        "deviceId":"DEVICE_ID",
        "osVersion":"OS_VERSION",
        "firebaseToken":"FIREBASE_TOKEN",
        "walletAddress":"WALLET_ADDRESS"
    }
}
```
Ответ:
```json
{
    "result":"success"
}
```

#### [37] Получение логов приложения.
Запрос:
```json
{
    "request":"getLogs",
    "ssid":"SESSION_ID",
    "query":
    {
        <"deviceId":"DEVICE_ID">,
        <"osVersion":"OS_VERSION">,
        <"firebaseToken":"FIREBASE_TOKEN">,
        <"walletAddress":"WALLET_ADDRESS">,
        <"logsLimit":LOGS_LIMIT>
    }
}
```
Ответ:
```json
{
    "result":"success",
    "logs":
    [
        {
            "timestamp":TIMESTAMP,
            "deviceId":"DEVICE_ID",
            "osVersion":"OS_VERSION",
            "firebaseToken":"FIREBASE_TOKEN",
            "walletAddress":"WALLET_ADDRESS"
        },
        ...
    ]
}
```
Значения поля "logsLimit" в запросе определяет количество возвращаемых записей в порядке, обратном порядку их создания. Если значение этого поля не задано, возвращается 10 последних записей.

#### [38] Получение версии сервера.
Запрос:
```json
{
    "request":"getServerVersion",
    "ssid":"SESSION_ID"
}
```
Ответ:
```json
{
    "result":"success",
    "serverVersion":"SERVER_VERSION",
    "serverBuildDate":"BUILD_DATE"
}
```

#### [39] Проверка почты на уникальность.
Запрос:
```json
{
    "request":"emailIsUnique",
    "email":"EMAIL",
    "ssid":"SESSION_ID"
}
```
Ответ:
```json
{
    "result":"success"
}
```

#### [40] Проверка телефона на уникальность.
Запрос:
```json
{
    "request":"phoneIsUnique",
    "phoneNumber":"PHONE_NUMBER",
    "ssid":"SESSION_ID"
}
```
Ответ:
```json
{
    "result":"success"
}
```

#### [41] Подтверждение / отклонение верификации.
Запрос:
```json
{
    "request":"acceptPersonVerify",
    "ssid":"SESSION_ID",
    "personId":ID,
    "acceptVerify":TRUE
}
```
Ответ:
```json
{
    "result":"success",
    "rowType":ROW_TYPE
}
```
Значения поля "acceptVerify" определяет принятие (TRUE) или отклонение (FALSE) запроса на верификацию.

После подтверждения верификации отправляется запрос на бэкенд для добавления тарифа пользователю:
```json
{
    "request":"addTariff",
    "walletAddress":"WALLET_ADDRESS",
    "stringTariffId":"TARIFF_ID",
    "ssid":"SESSION_ID"
}
```
Ответ:
```json
{
    "result":"success"
}
```
Если пользователь регистрировался по промокоду и в свойствах акции указано начисление регистрирующемуся, то отправляется запрос на добавление валюты. Запрос:
```json
{
    "request":"payUserByPromo",
    "walletAddress":"WALLET_ADDRESS",
    "stringTariffId":"TARIFF_ID",
    "yalAmount":AMOUNT,
    "referralPaymentId":PAYMENT_ID,
    "ssid":"SESSION_ID"
}
```
Ответ:
```json
{
    "result":"success"
}
```

#### [42] Обновление состояния кошелька пользователя.
Запрос:
```json
{
    "request":"updateWalletState",
    "ssid":"SESSION_ID",
    "walletAddress":"WALLET_ADDRESS",
    "walletState":STATE
    <,"contractPersonId":"CONTRACT_PERSON_ID">
}
```
Ответ:
```json
{
    "result":"success",
}
```
Поле "walletState" типа UInt отмечает состояние кошелька:
- 0 - невалидный адрес кошелька
- 1 - тариф уже был установлен
- 2 - тариф установлен

#### [43] Связка аккаунта пользователя с соцсетями.
Запрос:
```json
{
    "request":"setPersonSnId",
    "personId":ID,
    "socialNetworkId":SN_ID,
    "socialNetworkPersonId":"SN_PERSON_ID",
    "ssid":"SESSION_ID"
}
```
Ответ:
```json
{
    "result":"success"
}
```

"socialNetworkId" - идентификатор соцсети в системе:
- 1    - ВКонтакте;
- 2    - Youtube;
- 3    - Instagram;
- 4    - Facebook.

#### [44] Получение списка идентификаторов пользователя в соцсетях.
Запрос:
```json
{
    "request":"getPersonSnId",
    "personId":ID,
    "ssid":"SESSION_ID"
}
```
Ответ:
```json
{
    "result":"success",
    "snPersonIdList":
    [
        {
            "personId":ID,
            "socialNetworkId":SN_ID,
            "socialNetworkPersonId":"SN_PERSON_ID"
        },
        ...
    ]
}
```

#### [45] Типа записи и идентификатора пользователя по адресу кошелька.
Запрос:
```json
{
    "request":"getPersonTypeByWallet",
    "walletAddress":"WALLET_ADDRESS",
    "ssid":"SESSION_ID"
}
```
Ответ:
```json
{
    "result":"success",
    "rowType":ROW_TYPE,
    "personId":ID
}
```
Значения поля "rowType" описано в пункте "[8] Регистрация нового пользователя".

#### [47] Проверка адреса кошелька на верификацию.
Запрос:
```json
{
    "request":"walletOwnerIsVerified",
    "walletAddress":"WALLET_ADDRESS",
    "ssid":"SESSION_ID"
}
```
Ответ:
```json
{
    "result":"success",
    "walletOwnerIsVerified":TRUE/FALSE
}
```

#### [48] Проверка выплаты по реферальной ссылке.
Запрос:
```json
{
    "request":"checkPromoPayByWallet",
    "walletAddress":"WALLET_ADDRESS",
    "ssid":"SESSION_ID",
    "yalAmount":AMOUNT,
    "referralPaymentId":PAYMENT_ID
}
```
Ответ:
```json
{
    "result":"success"
}
```

#### [49] Обновление состояния платежа.
Запрос:
```json
{
    "request":"updatePaymentStatus",
    "ssid":"SESSION_ID",
    "referralPaymentId":PAYMENT_ID,
    "paymentStatus":STATUS
}
```
Ответ:
```json
{
    "result":"success"
}
```

"paymentStatus" - состояние платежа:
- 1    - новый платеж;
- 2    - находится в обработке;
- 3    - выплачен;
- 4    - отменен.

#### [50] Получение списка всех тарифов.
Запрос:
```json
{
    "request":"getTariffList",
    "ssid":"SESSION_ID",
}
```
Ответ:
```json
{
    "result":"success",
    "tariffList":
    [
        {
            "tariffId":ID,
            "coinAmount":COIN_AMOUNT,
            "stringTariffId":"STRING_TARIFF_ID",
            "description":"DESCRIPTION",
            "isDefault":TRUE
        },
        ...
    ]
}
```

---
title: QIWI Универсальный платежный API 1.0.0

search: true

metatitle: Универсальный платежный API 1.0.0

metadescription: QIWI Универсальный платежный API открывает доступ к операциям со счетами на оплату из вашего приложения. Счет - универсальная заявка на оплату. Пользователь может оплатить его с помощью любого из доступных способов оплат, пока не истекло время жизни счета. В API поддерживаются операции выставления и отмены счетов, возврата средств по счетам, а также проверки статуса выполнения операций.

services:
 - <a href='#'>Swagger</a>  |  <a href='#'>Qiwi Demo</a>


toc_footers:
 - <a href='/'>На главную</a>
 - <a href='mailto:api_help@qiwi.com'>Обратная связь</a>

includes:


---

# Универсальный платежный API {#introduction}

###### Последнее обновление: 2017-12-21 | [Редактировать на GitHub](https://github.com/QIWI-API/bill-payments-docs/blob/master/bill-payments_ru.html.md)

QIWI Универсальный платежный API открывает доступ к операциям со счетами на оплату из вашего приложения. Счет - универсальная заявка на оплату. Пользователь может оплатить его с помощью любого из доступных способов оплат.

Возможности:
* [Открытие платежной страницы](#http)
* [Оповещения](#notification)
* [Проверка статуса](#invoice-status)
* [Возвраты](#cancel)

**Данное API можно использовать только после [регистрации и подключения](https://kassa.qiwi.com).**

## Последовательность операций {#steps}

![Operation Flow](/images/bill_payments.png)

* Пользователь формирует заказ на сайте провайдера.

* Провайдер перенаправляет пользователя на [Платежную форму QIWI Кассы](#http) для выставления счета по заказу и его оплаты.

* Пользователь выбирает наиболее удобный способ оплаты и оплачивает счет. Если какой-либо из способов не доступен, QIWI подберет оптимальный способ оплаты для пользователя.

* После успешной оплаты счета провайдер получает [уведомление](#notification) (предварительно необходимо настроить отправку уведомлений в [Личном кабинете](https://kassa.qiwi.com)). Уведомления об оплате счета содержат параметры авторизации, которые необходимо проверять на сервере провайдера. Уведомления высылаются при успешной оплате счета.

* При необходимости, провайдер через [Bill Payments API](#bill-payments-api):
  * [запрашивает текущий статус оплаты счета](#invoice-status),
  * [отменяет счет](#cancel) (при условии, что он еще не был оплачен).

* После подтверждения оплаты счета провайдер исполняет заказ пользователя.

## Средства для разработки {#node_sdk}

* [NODE JS SDK](https://github.com/QIWI-API/bill-payments-node-js-sdk) - Пакет готовых решений для разработки server2server интеграций приема платежей на вашем сайте c помощью Node.js.
* 

## Авторизация {#auth}

Запросы мерчанта авторизуются посредством секретного ключа API (SECRET_KEY).

~~~shell
--header "Authorization: Bearer MjMyNDQxMjM6NDUzRmRnZDQ0Mw=="
~~~

<aside class="notice">
Получить данные для авторизации можно на партнерском сайте <a href='https://kassa.qiwi.com'>kassa.qiwi.com</a>.
</aside>

Параметр|Описание|Тип
 ---------|--------|---
 SECRET_KEY | Секретный ключ для авторизации в API предоставляет доступ к операциям на оплату услуг товаров из вашего приложения.| String

<aside class="notice">
Важно! Не передаейте ключ третьим лицам! Параметр авторизации передается в заголовке <i>Authorization</i>, значение которого формируется как "Bearer <a href="#auth">SECRET_KEY</a>". 
</aside>


## 1. Платежная форма {#http}

Клиенту отображается платежная форма с выбором способа оплаты выставленного счета.

* Вызов веб-формы может выполняться двумя способами:

  * Простой. Для идентификации провайдера используется ключ в параметрах вызова.

  * С подписью. В параметрах вызова содержится цифровая подпись, которая гарантирует, что счет выставляет конкретный провайдер.

<h3 class="request method" id="payform_flow">REDIRECT → </h3>

<ul class="nestedList url">
    <li><h3>URL <span>https://oplata.qiwi.com/form/create</span></h3></li>
</ul>

~~~http

GET /form/create?public_key=08hvq08yw4fqw&amount=100.0&success_url=http%3A%2F%2Ftest.ru%3F&email=m@ya.ru HTTP/1.1
Host: oplata.qiwi.com
~~~

<ul class="nestedList params">
    <li><h3>Параметры</h3><span>В ссылке на веб-форму указываются параметры счета.</span></li>
</ul>

Параметр|Описание|Тип|С подписью|Простой|Обяз.
---------|--------|---|---------|---|----
public_key | Ключ идентификации провайдера, полученный в QIWI Кассе|String|+|+|+
bill_id|Уникальный идентификатор счета в системе провайдера|String(30)|+|+|С подписью
amount| Сумма, на которую выставляется счет, округленная в меньшую сторону до 2 десятичных знаков | Number(6.2)|+|+|С подписью
sign|[Подпись запроса](#http_sign)|String|+|-|+
phone | Идентификатор QIWI Кошелька, на который выставляется счет (в международном формате). | String(20)|+|+|-
email | E-mail пользователя, куда будет отправлена ссылка для оплаты счета. | String|+|+|-
user_id | Идентификатор пользователя в системе провайдера. | String|+|+|-
comment | Комментарий к счету.|String(255)|+|+|-
extra_\*|Дополнительные данные счета.|String(255)|+|+|-
lifetime | Дата, до которой счет будет доступен для оплаты. Если счет не будет оплачен до этой даты, ему присваивается финальный статус `expired` и последующая оплата станет невозможна.<br> **Внимание! По истечении 45 суток от даты выставления счет автоматически будет переведен в финальный статус.**|URL-закодированная строка<br>`ГГГГ-ММ-ДДTччмм`|+|+|-
success_url|URL для переадресации в случае успешного создания транзакции в QIWI Wallet. Ссылка должна вести на сайт провайдера. Если пользователь выбрал на платежной форме способ оплаты, отличный от оплаты с баланса QIWI Кошелька, то переадресация на сайт провайдера не выполняется.|URL-закодированная строка|+|+|-
fail_url|URL для переадресации в случае неуспеха при создании транзакции в QIWI Wallet. Ссылка должна вести на сайт провайдера. Если пользователь выбрал на платежной форме способ оплаты, отличный от оплаты с баланса QIWI Кошелька, то переадресация на сайт провайдера не выполняется.|URL-закодированная строка|+|+|-
pay_source |Способ оплаты по умолчанию, который необходимо отобразить пользователю при открытии платежной формы. Форма сама определяет наиболее удобный способ оплаты для клиента, поэтому **не советуем указывать** этот параметр.<br>Возможные значения:<br>`qw` – оплата с баланса QIWI Кошелька;<br> `mobile` – оплата с баланса мобильного телефона;<br> `card` – оплата банковской картой;<br>Если способ оплаты не доступен, пользователю отображается предупреждение, при этом на странице можно выбрать другие способы оплаты. |String|-|+|-

### Подпись {#http_sign}

Вызов Платежной формы с авторизацией требует вычисления параметра `sign` (цифровой подписи).

~~~http

GET /form/create?public_key=08hvq08yw4fqw&bill_id=Bill-1&sign=09jvrq09pwh3rq2e2&amount=100.0 HTTP/1.1
Host: oplata.qiwi.com
~~~

<aside class="notice">
Для вычисления подписи используется <a href='#auth'>SECRET_KEY</a> для Bill Payments REST API.
</aside>

Алгоритм вычисления подписи:

1.	Получить строку, содержащую значения всех **обязательных** параметров [ссылки переадресации](#payform_flow) и параметра `lifetime` (**если он присутствует в запросе**) в алфавитном порядке перечисления параметров, разделенных символами `|`:

    `Invoice_parameters = "{public_key}|{bill_id}|{amount}|{lifetime}"`

    где `{parameter}` – значение соответствующего параметра [запроса](#payform_flow). Все параметры трактуются как строки.

2.	Вычислить HMAC-хэш с шифрованием SHA256 от полученной строки и ключом, равным [SECRET_KEY](#auth):

    `sign = HMAС(SHA256, SECRET_KEY, Invoice_parameters)`

## 2. Redirect на страницу партнера

## 3. Уведомления об оплате счетов {#notification}

###### Последнее обновление: 2017-10-23 | [Редактировать на GitHub](https://github.com/QIWI-API/bill-payments-rest-api-docs/blob/master/_notification_ru.html.md)

Уведомление представляет собой POST-запрос. Тело запроса содержит JSON-сериализованные данные счета (кодировка UTF-8).

<h3 class="request method">Запрос → POST</h3>

~~~http
POST /qiwi-notify.php HTTP/1.1
Accept: application/json
Content-type: application/json
X-Api-Signature-SHA256: J4WNfNZd***V5mv2w=
Host: server.ru

{
  "bill": {
    "bill_id": "a475c739-0561-4a23-9d18-a96934a7d690",
    "site_id":270304,
    "amount": 1,
    "currency": "RUB",
    "status":  {
      "value" : "PAID",
      "update_datetime" : "2017-12-27T16:01:00Z" },
    "user": {
      "phone": "79261234567",
      "user_id" : "dsfc2recd123sdadx3dscfewcr234esdcf23",
      "email" : "example@gmail.com"
    },
    "creation_datetime": "2017-08-17T09:56:02.241Z",
    "expiration_datetime": "2017-12-27T16:01:00Z",
    "version" : "3.0"
  }
}
~~~

~~~http
HTTP/1.1 200 OK
Content-Type: application/json
{
 "error": 0
}
~~~

<ul class="nestedList url">
    <li><h3>URL</h3>
    </li>
</ul>

<aside class="notice">
Aдрес вашего сервера для уведомлений вы можете настроить на сайте <a href="https://kassa.qiwi.com/">kassa.qiwi.com</a>
</aside>

<ul class="nestedList header">
    <li><h3>HEADERS</h3>
        <ul>
             <li>X-Api-Signature-SHA256: ***</li>
             <li>Accept: application/json</li>
             <li>Content-type: application/json</li>
        </ul>
    </li>
</ul>

<ul class="nestedList params">
    <li><h3>Параметры</h3><span>В POST-запросе уведомления указываются параметры счета.</span>
    </li>
</ul>

Параметр|Описание|Тип
---------|--------|---
bill_id|Уникальный идентификатор счета в системе провайдера|String(30)
site_id|Идентификатор сайта провайдера в QIWI Кассе| Number
amount | Сумма счета, округленная до двух десятичных знаков в меньшую сторону | Number(6.2)
currency | Идентификатор валюты счета (Alpha-3 ISO 4217 код) | String(3)
status | Данные о статусе счета | Object
status.value |Строковое значение статуса | String
status.update_datetime|Дата обновления статуса| URL-закодированная строка<br>`ГГГГ-ММ-ДДTЧЧ:ММ:ССZ`
user | Данные о пользователе, на которого был выставлен счет| Object
user.phone |Номер телефона, на который был выставлен счет (если был указан при выставлении счета) |String
user.email|E-mail пользователя (если был указан при выставлении счета)|String
user.user_id| Идентификатор пользователя в системе провайдера (если был указан при выставлении счета)|String
creation_datetime | Дата создания счета | URL-закодированная строка<br>`ГГГГ-ММ-ДДTЧЧ:ММ:ССZ`
expiration_datetime | Срок оплаты счета | URL-закодированная строка<br>`ГГГГ-ММ-ДДTЧЧ:ММ:ССZ`
comment | Комментарий к счету | String(255)
extras | Дополнительные данные счета (если были указаны при выставлении счета).| Object
version | Версия уведомлений | String

<h3 class="request method">Ответ → POST</h3>
Ответ на запрос должен быть в формате JSON.

### Заголовки

*  `Content-type: application/json`

~~~http
HTTP/1.1 200 OK
Content-Type: application/json

{
 "error": 0
}
~~~

<aside class="warning">
В ответе должен вернуться код результата обработки уведомления. Код результата должен находиться в параметре <i>error</i>.

Если в ответе код результата обработки уведомления отличается от 0 и/или код состояния HTTP отличается от 200 (OK), это интерпретируется как временная ошибка провайдера. Сервер повторяет запрос 36 раз с интервалом в 15 минут, далее 15 раз с интервалом в 60 минут. Таким образом, он делает еще 51 попытку за следующие 24 часа.
</aside>


<aside class="notice">
<ul>
<li>Рекомендуется возвращать коды результата в соответствии с таблицей <a href="#notify_codes">кодов завершения</a>.</li>
<li>Для получения уведомлений провайдер должен принимать HTTP-запросы из следующих подсетей исключительно по портам 80, 443:</li>
<ul>
<li> 91.232.230.0/23</li>
<li> 79.142.16.0/20</li>
</ul>
</ul>
</aside>


### Авторизация уведомлений {#notifications_auth}

Используется авторизация по цифровой подписи. Подпись уведомления отправляется в заголовке `X-Api-Signature-SHA256`. Для формирования подписи используется механизм проверки целостности HMAC с хэш-функцией SHA256.

HMAC sha256 от части полей, с разделителем `|` + `secret_key`

Пример:

`X-Api-Signature-SHA256: J4WNfNZd***V5mv2w=`

* В качестве разделителей параметров используется символ `|`.
* В подписи участвуют следующие параметры (указаны в алфавитном порядке):
   * amount
   * bill_id
   * currency
   * email (если есть)
   * phone (если есть)
   * site_id
   * status.value
   * user_id (если есть)
* Параметры для подписи переводятся в байт-представление с UTF-8 и располагаются в алфавитном порядке.
* Ключ подписи равен [SECRET_KEY](#auth).

Алгоритм проверки подписи:

1. Получить строку, содержащую значения перечисленных параметров POST-запроса в алфавитном порядке перечисления параметров, разделенных символами `|`:

   `{parameter1}|{parameter2}|…`

   где `{parameter1}` – значение параметра уведомления. Все значения при проверке подписи должны трактоваться как строки.

2. Cтроку и ключ подписи преобразовать в байты с UTF-8.
3. Вычислить HMAC-хэш c шифрованием SHA256:

   `hash = HMAС(SHA256, secret_key_bytes, invoice_parameters_bytes)`
   Где:

   * `secret_key_bytes` – ключ функции (байт-представление секретного ключа);
   * `invoice_parameters_bytes` – байт-представление параметров POST-запроса для проверки подписи;
   * `hash` – результат хэш-функции.

4. HMAC-хэш преобразовать из строк в байты с использованием кодировки UTF-8 и base64-преобразовать.
5. Сравнить значение заголовка X-Api-Signature-SHA256 с результатом 4.


### Коды уведомлений {#notify_codes}

Код завершения|Описание
--------------|--------
0|Успех


## 4. Проверка статуса оплаты счета {#invoice-status}

Данный метод предназначен для получения текущего статуса оплаты счета клиентом.

<h3 class="request method">Запрос → GET</h3>

~~~http
GET /api/v3/bills/BILL-1 HTTP/1.1
Accept: application/json
Authorization: Bearer eyJ2ZXJzaW9uIjoicmVzdF92MyIsImRhdGEiOnsibWVyY2hhbnRfaWQiOjIwNDIsImFwaV91c2VyX2lkIjo1NjYwMzk3Miwic2VjcmV0IjoiQjIwODlDNkI5Q0NDNTdCNDQzNGHJK43JFJDK595FJFJMjlCRkFFRDM5OEZBOTBENDBEMiJ9fQ==
Host: api.qiwi.com
~~~

<ul class="nestedList url">
    <li><h3>URL <span>https://api.qiwi.com/api/v3/bills/<a>bill_id</a></span></h3>
        <ul>
        <strong>Параметр передается в pathname.</strong>
             <li><strong>bill_id</strong> - уникальный идентификатор счета в системе провайдера.</li>
        </ul>
    </li>
</ul>

<ul class="nestedList header">
    <li><h3>HEADERS</h3>
        <ul>
             <li>Authorization: Bearer <a href="#auth">SECRET_KEY</a></li>
             <li>Accept: application/json</li>
        </ul>
    </li>
</ul>

<h3 class="request">Ответ ←</h3>

~~~http
HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
{
  "bill": {
    "amount": "42.24",
    "site_id": 23044,
    "bill_id": "30192832",
    "comment": "Text comment",
    "creation_datetime": "2017-08-13T14:30:00.000Z",
    "currency": "RUB",
    "extras": {},
    "expiration_datetime": "2017-10-13T14:30:00.000Z",
    "pay_url": "https://oplata.qiwi.com/form/?invoice_uid=755ac889-6f94-4f82-a0b8-3dffc24afa60",
    "status": "WAITING",
    "user": {
             "email": "test@qiwi.com",
             "phone": "79191234567",
             "user_id": "shop_user_id"
    }
  },
  "result_code": "SUCCESS"
}
~~~

~~~http
HTTP/1.1 401 Unauthorized
Content-Type: application/json;charset=UTF-8
{
  "result_code": "AUTH_FAILED",
  "error_code": "CORE__150",
  "description": "Authorization failed",
  "datetime":"2017-06-28T21:57:45.540Z"
}
~~~

<ul class="nestedList header">
    <li><h3>HEADERS</h3>
        <ul>
             <li>Accept: application/json</li>
        </ul>
    </li>
</ul>

Параметр|Тип|Описание
--------|---|--------
result_code|String|[Результат запроса](#results)
error_code|String|Если ответ содержит ошибку: [Код ошибки](#err-codes)
description|String|Если ответ содержит ошибку: Описание ошибки
datetime|String|Если ответ содержит ошибку: Системная дата обработки запроса
bill|Object|Данные о счете
bill.bill_id|String|Уникальный идентификатор счета в системе провайдера
bill.site_id|Number|Идентификатор сайта провайдера в QIWI Кассе
bill.amount|String|Сумма счета, округленная до 2 знаков после запятой в меньшую сторону.
bill.currency	|String|Идентификатор валюты счета (Alpha-3 ISO 4217 код)
bill.status	|String|Текущий [статус счета](#status)
bill.extras|Object|Объект строковых дополнительных параметров, переданных партнером
bill.user|Object|Идентификаторы пользователя. Возможные опции: `email`, `phone`, `user_id`
bill.comment|String|Комментарий к счету
bill.creation_datetime|Date| Системная дата создания счета. Формат даты:<br>`ГГГГ-ММ-ДДTчч:мм:ссTMZ`
bill.pay_url|String|Ссылка для переадресации пользователя на созданную платежную форму
bill.expiration_datetime|Date|Срок действия созданной формы для оплаты. Формат даты:<br>`ГГГГ-ММ-ДДTчч:мм:ссTMZ`


## 5. Отмена неоплаченного счета {#cancel}

Метод позволяет отменить неоплаченный клиентом счет.

<h3 class="request method">Запрос → PATCH</h3>


~~~http
PATCH /api/v3/bills/BILL-1/reject HTTP/1.1
Accept: application/json
Authorization: Bearer eyJ2ZXJzaW9uIjoicmVzdF92MyIsImRhdGEiOnsibWVyY2hhbnRfaWQiOjIwNDIsImFwaV91c2VyX2lkIjo1NjYwMzk3Miwic2VjcmV0IjoiQjIwODlDNkI5Q0NDNTdCNDQzNGHJK43JFJDK595FJFJMjlCRkFFRDM5OEZBOTBENDBEMiJ9fQ==
Host: api.qiwi.com
Content-Type: application/x-www-form-urlencoded; charset=utf-8
~~~

<ul class="nestedList url">
    <li><h3>URL <span>https://api.qiwi.com/api/v3/bills/<a>bill_id</a>/reject</span></h3>
        <ul>
        <strong>Параметры:</strong>
             <li><strong>bill_id</strong> - уникальный идентификатор счета в системе провайдера, передается в pathname.</li>
        </ul>
    </li>
</ul>

<ul class="nestedList header">
    <li><h3>HEADERS</h3>
        <ul>
             <li>Authorization: Basic <a href="#auth">SECRET_KEY</a></li>
             <li>Accept: application/json</li>
        </ul>
    </li>
</ul>

<h3 class="request">Ответ ←</h3>

~~~http
HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
{
  "bill": {
    "amount": "42.24",
    "site_id": 23044,
    "bill_id": "30192832",
    "comment": "Text comment",
    "creation_datetime": "2017-08-13T14:30:00.000Z",
    "currency": "RUB",
    "extras": {},
    "expiration_datetime": "2017-10-13T14:30:00.000Z",
    "pay_url": "https://oplata.qiwi.com/form/?invoice_uid=755ac889-6f94-4f82-a0b8-3dffc24afa60",
    "status": "WAITING",
    "user": {
             "email": "test@qiwi.com",
             "phone": "79191234567",
             "user_id": "shop_user_id"
    }
  },
  "result_code": "SUCCESS"
}
~~~

~~~http

HTTP/1.1 401 Unauthorized
Content-Type: application/json;charset=UTF-8
{
  "result_code": "AUTH_FAILED",
  "error_code": "error.code.auth.unauthorized",
  "description": "Authorization failed",
  "datetime":"2017-06-28T21:57:45.540Z"
}
~~~

<ul class="nestedList header">
    <li><h3>HEADERS</h3>
        <ul>
             <li>Accept: application/json</li>
        </ul>
    </li>
</ul>

Параметр|Тип|Описание
--------|---|--------
result_code|String|[Результат запроса](#results)
error_code|String|Если ответ содержит ошибку: [Код ошибки](#err-codes)
description|String|Если ответ содержит ошибку: Описание ошибки
datetime|String|Если ответ содержит ошибку: Системная дата обработки запроса
bill|Object|Данные о счете
bill.bill_id|String|Уникальный идентификатор счета в системе провайдера
bill.site_id|Number|Идентификатор сайта провайдера в QIWI Кассе
bill.amount|String|Сумма счета, округленная до 2 знаков после запятой в меньшую сторону
bill.currency	|String|Идентификатор валюты счета (Alpha-3 ISO 4217 код)
bill.status	|String|Текущий [статус счета](#status)
bill.extras|Object|Объект строковых дополнительных параметров, переданных партнером
bill.user|Object|Идентификаторы пользователя. Возможные опции: `email`, `phone`, `user_id`
bill.comment|String|Комментарий к счету
bill.creation_datetime|Date| Системная дата создания счета. Формат даты:<br>`ГГГГ-ММ-ДДTчч:мм:ссTMZ`
bill.pay_url|String|Ссылка на созданную платежную форму
bill.expiration_datetime|Date|Срок действия созданной формы для оплаты. Формат даты:<br>`ГГГГ-ММ-ДДTчч:мм:ссTMZ`

## Результаты обработки запросов {#results}


Код| Описание |
---|----------|
AUTH_FAILED|Ошибка авторизации запроса
BAD_REQUEST|Ошибка в синтаксисе запроса
GENERAL_ERROR|Техническая ошибка
RETRYABLE_ERROR|Временная ошибка, запрос следует повторить еще раз
SUCCESS|Успешное выполнение запроса

### Статусы оплаты счетов {#status}

Статус|Описание|Финальный
------|--------|---------
WAITING | Счет выставлен, ожидает оплаты| -
PAID|Счет оплачен|+
REJECTED|Счет отклонен|+
UNPAID|Ошибка при проведении оплаты. Счет не оплачен|+
EXPIRED	|Время жизни счета истекло. Счет не оплачен|+

### Статусы операции возврата {#status_refund}

Статус|Описание|Финальный
------|--------|---------
processing | Платеж в проведении| -
success|Платеж проведен|+
fail|Платеж неуспешен|+

## Список ошибок {#err-codes}


Код| Описание
---|----------
error.code.internal.invoicing.core.error|Ошибка получения данных от сервиса invoicing, попробуйте позже
error.code.internal.invoicing.api.error|Внутренняя ошибка, попробуйте позже
error.code.api.invoice.not.found|Счёт не найден
error.code.auth.unauthorized|Неверные аутентификационные данные
error.code.default|Произошла ошибка, попробуйте позже
error.code.internal.error|Внутренняя ошибка
error.code.http.argument.type.mismatch|Неверный запрос
error.code.http.code.conversion.failed|Неверные параметры, измените запрос согласно документации API
error.code.http.media.type.not.acceptable|Неверный тип данных
error.code.http.media.type.not.supported|Неподдерживаемый тип данных
error.code.http.message.conversion.failed|Неверный запрос
error.code.http.method.not.supported|HTTP метод не поддерживается
error.code.http.missing.request.parameter|Неверный запрос: отсутствуют необходимые параметры
error.code.http.url.not.found|Ресурс не найден
error.code.validation.error|Неверные параметры, измените запрос согласно документации API



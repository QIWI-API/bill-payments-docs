---
title: QIWI Универсальный платежный API 1.0.0 beta

search: true

metatitle: Универсальный платежный API 1.0.0 beta

metadescription: QIWI Универсальный платежный API открывает доступ к операциям со счетами на оплату из вашего приложения. Счет - универсальная заявка на оплату. Пользователь может оплатить его с помощью любого из доступных способов оплат, пока не истекло время жизни счета. В API поддерживаются операции выставления и отмены счетов, возврата средств по счетам, а также проверки статуса выполнения операций.

language_tabs:
  - shell
  - javascript: Node.js SDK

services:
 - <a href='#'>Swagger</a>  |  <a href='#'>Qiwi Demo</a>


toc_footers:
 - <a href='/'>На главную</a>
 - <a href='mailto:api_help@qiwi.com'>Обратная связь</a>

includes:


---

# Универсальный платежный API {#introduction}

###### Последнее обновление: 2018-01-29 | [Редактировать на GitHub](https://github.com/QIWI-API/bill-payments-docs/blob/master/bill-payments_ru.html.md)

QIWI Универсальный платежный API открывает доступ к операциям со счетами на оплату из вашего приложения. Счет - универсальная заявка на оплату. Пользователь может оплатить его с помощью любого из доступных способов оплат.

**Данное API можно использовать только после [регистрации и подключения](https://kassa.qiwi.com).**

## Последовательность операций {#steps}

![Operation Flow](/images/bill_payments.png)

* Пользователь формирует заказ на сайте мерчанта.

* Мерчант перенаправляет пользователя на [Платежную форму](#http) для выставления счета по заказу. Или [выставляет счет по API](#create) и перенаправляет пользователя на созданную платежную форму.

* Пользователь выбирает наиболее удобный способ оплаты и оплачивает счет. Если какой-либо из способов не доступен, QIWI подберет оптимальный способ оплаты для пользователя.

* После успешной оплаты счета мерчант получает [уведомление](#notification) (предварительно необходимо настроить отправку уведомлений в [Личном кабинете](https://kassa.qiwi.com)). Уведомления об оплате счета содержат параметры авторизации, которые необходимо проверять на сервере мерчанта. Уведомления высылаются при успешной оплате счета.

* При необходимости, мерчант через [Bill Payments API](#bill-payments-api):
  * [запрашивает текущий статус оплаты счета](#invoice-status),
  * [отменяет счет](#cancel) (при условии, что он еще не был оплачен).

* После подтверждения оплаты счета мерчант исполняет заказ пользователя.

## Средства для разработки {#node_sdk}

* [NODE JS SDK](https://github.com/QIWI-API/bill-payments-node-js-sdk) - Пакет готовых решений для разработки server2server интеграций приема платежей на вашем сайте c помощью Node.js.

## Авторизация {#auth}

Запросы мерчанта авторизуются посредством секретного ключа API (SECRET_KEY).

~~~javascript
const QiwiBillPaymentsAPI = require('bill-payments-node-js-sdk');
 
const SECRET_KEY = 'eyJ2ZXJzaW9uIjoicmVzdF92MyIsImRhdGEiOnsibWVyY2hhbnRfaWQiOjUyNjgxMiwiYXBpX3VzZXJfaWQiOjcxNjI2MTk3LCJzZWNyZXQiOiJmZjBiZmJiM2UxYzc0MjY3YjIyZDIzOGYzMDBkNDhlYjhiNTnONPININONPN090MTg5Z**********************';

const qiwiApi = new QiwiBillPaymentsAPI(SECRET_KEY);
~~~

~~~shell
--header "Authorization: Bearer MjMyNDQxMjM6NDUzRmRnZDQ0M*******"
~~~

<aside class="notice">
Получить данные для авторизации можно на партнерском сайте <a href='https://kassa.qiwi.com'>kassa.qiwi.com</a>.
</aside>

Параметр|Описание|Тип
 ---------|--------|---
 SECRET_KEY | Секретный ключ для авторизации в API. Предоставляет доступ к операциям для работы со счетами  в вашем приложении.| String
 PUBLIC_KEY | Публичный ключ идентификации мерчанта. Может быть предоставлен третьим лицам |String

<aside class="notice">
Важно! Не передавайте секретный ключ третьим лицам! Параметр авторизации передается в заголовке <i>Authorization</i>, значение которого формируется как "Bearer <a href="#auth">SECRET_KEY</a>". 
</aside>


## 1.1 Выставление счета через платежную форму {#http}

Простой способ для интеграции. При открытии формы клиенту автоматически выставляется счет. Все параметры счета передаются в открытом виде в ссылке на форму. Далее клиенту отображается платежная форма с выбором способа оплаты.
При использовании этого способа нельзя гарантировать, что все счета выставлены мерчантом, в отличии от [выставления по API](#create).

<h3 class="request method" id="payform_flow">REDIRECT → </h3>

<ul class="nestedList url">
    <li><h3>URL <span>https://oplata.qiwi.com/create</span></h3></li>
</ul>

~~~javascript
const public_key = 'Fnzr1yTebUiQaBLDnebLMMxL8nc6FF5zfmGQnypc*******';

const params = {
    public_key,
    amount: 42.24,
    bill_id: '893794793973',
    success_url: 'http://test.ru/',
    email: 'm@ya.ru'
};

const link = qiwiApi.createPaymentForm(params);
~~~

~~~shell
curl https://oplata.qiwi.com/create?public_key=Fnzr1yTebUiQaBLDnebLMMxL8nc6FF5zfmGQnypc*******&amount=100&bill_id=893794793973&success_url=http%3A%2F%2Ftest.ru%3F&email=m@ya.ru
~~~

<ul class="nestedList params">
    <li><h3>Параметры</h3><span>В ссылке на веб-форму указываются параметры счета.</span></li>
</ul>

Параметр|Описание|Тип|Обяз.
---------|--------|---|---------|---|----
public_key | Ключ идентификации мерчанта, полученный в QIWI Кассе|String|+
bill_id|Уникальный идентификатор счета в системе мерчанта|URL-закодированная строка String(200)|-
amount| Сумма, на которую выставляется счет, округленная в меньшую сторону до 2 десятичных знаков | Number(6.2)|-
phone | Номер телефона пользователя, на который выставляется счет (в международном формате) | URL-закодированная строка|-
email | E-mail пользователя, куда будет отправлена ссылка для оплаты счета | URL-закодированная строка|-
account | Идентификатор пользователя в системе мерчанта | URL-закодированная строка |-
comment | Комментарий к счету|URL-закодированная строка String(255)|-
extra_\*|Дополнительные данные счета|URL-закодированная строка String(255)|-
lifetime | Дата, до которой счет будет доступен для оплаты. Если счет не будет оплачен до этой даты, ему присваивается финальный статус `EXPIRED` и последующая оплата станет невозможна.<br> **Внимание! По истечении 45 суток от даты выставления счет автоматически будет переведен в финальный статус `EXPIRED`**|URL-закодированная строка<br>`ГГГГ-ММ-ДДTччмм`|-
success_url|URL для переадресации в случае успешного создания транзакции в QIWI Wallet. Ссылка должна вести на сайт мерчанта. Если пользователь выбрал на платежной форме способ оплаты, отличный от оплаты с баланса QIWI Кошелька, то переадресация на сайт мерчанта не выполняется.|URL-закодированная строка|-
fail_url|URL для переадресации в случае неуспеха при создании транзакции в QIWI Wallet. Ссылка должна вести на сайт мерчанта. Если пользователь выбрал на платежной форме способ оплаты, отличный от оплаты с баланса QIWI Кошелька, то переадресация на сайт мерчанта не выполняется.|URL-закодированная строка|-

Все параметры, кроме public_key, success_url и fail_url, будут возвращены в оповещении об оплате счета.


## 1.2 Выставление счета по API {#create}

Надежный способ для интеграции. Все параметры передаются server2server с использванием авторизации.
Метод позволяет выставить счет, при успешном выполнении запроса в ответе вернется параметр pay_url - ссылка для редиректа пользователя на платежную форму. 

<h3 class="request method">Запрос → POST</h3>

~~~javascript
const bill_id = '893794793973';

const fields = {
    amount: 1.00,
    currency: 'RUB',
    comment: 'Hello world',
    expiration_date_time: '2018-03-02T08:44:07'
};

qiwiRestApi.createBill( bill_id, fields ).then( data => {
    //do with data
});
~~~

~~~shell
curl https://api.qiwi.com/b2b/bills/v3/create \
-X POST \
-H 'Accept: application/json' \
-H 'Content-Type: application/json' \
-H 'Authorization: Bearer eyJ2ZXJzaW9uIjoicmVzdF92MyIsImRhdGEiOnsibWVyY2hhbnRfaWQiOjIwNDIsImFwaV91c2VyX2lkIjo1NjYwMzk3Miwic2VjcmV0IjoiQjIwODlDNkI5Q0NDNTdCNDQzNGHJK43JFJDK595FJFJMjlCRkFFRDM5OE***********************'
-d '{ \ 
   "amount": { \ 
     "currency": "RUB", \ 
     "value": 100.00 \ 
   }, \ 
   "bill_id": "893794793974", \ 
   "comment": "Text comment", \ 
   "expiration_date_time": "2018-04-13T14:30:00", \ 
   "customer": {}, \ 
   "extra": {} \ 
~~~


<ul class="nestedList url">
    <li><h3>URL <span>https://api.qiwi.com/b2b/bills/v3/create</span></h3>
        <ul>
        <strong>Параметры:</strong>
             <li><strong>bill_id</strong> - уникальный идентификатор счета в системе мерчанта.</li>
             <li><strong>amount.currency</strong> - валюта счета (Alpha-3 ISO 4217 код).	</li>
             <li><strong>amount.value</strong> - сумма счета, округленная до двух десятичных знаков в меньшую сторону.</li>
             <li><strong>comment</strong> - комментарий к счету.</li>
             <li><strong>expiration_date_time</strong> - срок оплаты счета. </li>
             <li><strong>customer.phone</strong> - номер телефона, на который был выставлен счет. </li>
             <li><strong>customer.email</strong> - e-mail пользователя.</li>
             <li><strong>customer.account</strong> - Идентификатор пользователя в системе мерчанта.</li>
             <li><strong>extra</strong> - Дополнительные данные счета.</li>
             
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

>Пример тела ответа

~~~json
{
  "result_code": "SUCCESS",
  "bill": {
    "site_id": 23044,
    "bill_id": "893794793974",
    "amount": {
      "value": 100,
      "currency": "RUB"
    },
    "status": {
      "value": "WAITING",
      "datetime": "2018-03-05T11:27:41"
    },
    "comment": "Text comment",
    "creation_datetime": "2018-03-05T11:27:41",
    "expiration_datetime": "2018-04-13T14:30:00",
    "pay_url": "https://oplata.qiwi.com/form/?invoice_uid=d875277b-6f0f-445d-8a83-f62c7c07be77"
  }
}
~~~

>Пример тела ответа при ошибке

~~~json
{
  "result_code": "AUTH_FAILED",
  "error_code": "auth.unauthorized",
  "description": "Неверные аутентификационные данные",
  "datetime": "2018-02-28T11:04:00.609Z"
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
bill.bill_id|String|Уникальный идентификатор счета в системе мерчанта
bill.site_id|Number|Идентификатор сайта мерчанта в QIWI Кассе
bill.amount|Number|Сумма счета, округленная до 2 знаков после запятой в меньшую сторону
bill.currency	|String|Идентификатор валюты счета (Alpha-3 ISO 4217 код)
bill.status	|String|Текущий [статус счета](#status)
bill.extra|Object|Объект строковых дополнительных параметров, переданных партнером
bill.customer|Object|Идентификаторы пользователя. Возможные опции: `email`, `phone`, `account`
bill.comment|String|Комментарий к счету
bill.creation_datetime|Date| Системная дата создания счета. Формат даты:<br>`ГГГГ-ММ-ДДTчч:мм:сс`
bill.pay_url|String|Ссылка на созданную платежную форму
bill.expiration_datetime|Date|Срок действия созданной формы для оплаты. Формат даты:<br>`ГГГГ-ММ-ДДTчч:мм:сс`


## 2. Уведомления об оплате счетов {#notification}

Уведомление представляет собой POST-запрос. Тело запроса содержит JSON-сериализованные данные счета (кодировка UTF-8).

<h3 class="request method">Запрос → POST</h3>

~~~http
POST /qiwi-notify.php HTTP/1.1
Accept: application/json
Content-type: application/json
X-Api-Signature-SHA256: J4WNfNZd***V5mv2w=
Host: server.ru

~~~

~~~json 
{ "bill":
  {  
     "site_id":"23044",
     "bill_id":"1519892138404fhr7i272a2",
     "amount":{  
        "value":"100",
        "currency":"RUB"
     },
     "status":{  
        "value":"PAID",
        "datetime":"2018-03-01T11:16:12"
     },
     "customer":{},
     "extra":{},
     "creation_datetime":"2018-03-01T11:15:39",
     "expiration_datetime":"2018-04-01T11:15:39"
   },
  "version":"3"
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
bill_id|Уникальный идентификатор счета в системе мерчанта|String(200)
site_id|Идентификатор сайта мерчанта в QIWI Кассе| Number
amount.value | Сумма счета, округленная до двух десятичных знаков в меньшую сторону | Number(6.2)
amount.currency | Идентификатор валюты счета (Alpha-3 ISO 4217 код) | String(3)
status | Данные о статусе счета | Object
status.value |Строковое значение статуса | String
status.update_datetime|Дата обновления статуса| URL-закодированная строка<br>`ГГГГ-ММ-ДДTЧЧ:ММ:ССZ`
customer | Данные о пользователе, на которого был выставлен счет| Object
customer.phone |Номер телефона, на который был выставлен счет (если был указан при выставлении счета) |String
customer.email|E-mail пользователя (если был указан при выставлении счета)|String
customer.account| Идентификатор пользователя в системе мерчанта (если был указан при выставлении счета)|String
creation_datetime | Дата создания счета | URL-закодированная строка<br>`ГГГГ-ММ-ДДTЧЧ:ММ:ССZ`
expiration_datetime | Срок оплаты счета | URL-закодированная строка<br>`ГГГГ-ММ-ДДTЧЧ:ММ:ССZ`
comment | Комментарий к счету | String(255)
extra | Дополнительные данные счета (если были указаны при выставлении счета).| Object
version | Версия уведомлений | String

<h3 class="request method">Ответ → POST</h3>
Ответ на запрос должен быть в формате JSON.

### Заголовки

*  `Content-type: application/json`

~~~
HTTP/1.1 200 OK
Content-Type: application/json

{
 "error":"0"
}
~~~

<aside class="warning">
В ответе должен вернуться код результата обработки уведомления. Код результата должен находиться в параметре <i>error</i>.

Если в ответе код результата обработки уведомления отличается от 0 и/или код состояния HTTP отличается от 200 (OK), это интерпретируется как временная ошибка мерчанта. Сервер повторяет запрос 36 раз с интервалом в 15 минут, далее 15 раз с интервалом в 60 минут. Таким образом, он делает еще 51 попытку за следующие 24 часа.
</aside>


### Авторизация уведомлений {#notifications_auth}

~~~javascript
const validSignatureFromNotificationServer =
      '07e0ebb10916d97760c196034105d010607a6c6b7d72bfa1c3451448ac484a3b';

const notificationData = {
    bill: {
        site_id: 'test',
        bill_id: 'test_bill',
        amount: { value: 1, currency: 'RUB' },
        status: { value: 'PAID', datetime: '2018-03-01T11:16:12' },
        customer: {},
        extra: {},
        creation_datetime: '2018-03-01T11:15:39',
        expiration_datetime: '2018-04-15T11:15:39'
    },
    version: '3'
};

const merchantSecret = 'test-merchant-secret-for-signature-check';

qiwiApi.checkNotificationSignature(
    validSignatureFromNotificationServer, notificationData, merchantSecret
); // true

~~~

Используется авторизация по цифровой подписи. Подпись уведомления отправляется в заголовке `X-Api-Signature-SHA256`. Для формирования подписи используется механизм проверки целостности HMAC с хэш-функцией SHA256.

HMAC sha256 от части полей, с разделителем `|` + `secret_key`
В подписи участвуют следующие параметры (указаны в алфавитном порядке):
* amount.currency
* amount.value
* bill_id
* site_id
* status
* Ключ подписи равен [SECRET_KEY](#auth)
* Cтрока и ключ подписи кодируются в UTF-8.

Алгоритм проверки подписи:

1. Объединить значения параметров в одну строку с разделителем  `|`:

   `invoice_parameters = {amount.currency}|{amount.value}|{bill_id}|{site_id}|{status}`

   где `{*}` – значение параметра уведомления. Все значения при проверке подписи должны трактоваться как строки.



2. Вычислить HMAC-хэш c алгоритмом хэширования SHA256:

   `hash = HMAС(SHA256, secret_key, invoice_parameters)`
   Где:

   * `secret_key` – ключ функции ;
   * `invoice_parameters` – строка из п.1;

3. Сравнить значение заголовка X-Api-Signature-SHA256 с результатом из п.2.


### Коды уведомлений {#notify_codes}

Код завершения|Описание
--------------|--------
0|Успех


## 3. Проверка статуса оплаты счета {#invoice-status}

Данный метод предназначен для получения текущего статуса оплаты счета клиентом. Рекомендуется его использовать после получения уведомления об оплате.

<h3 class="request method">Запрос → GET</h3>

~~~javascript
const bill_id = '893794793973';

qiwiApi.getBillInfo(bill_id).then( data => {
    //do with data
});
~~~

~~~shell
curl https://api.qiwi.com/b2b/bills/v3/get?bill_id=893794793973 \
-H 'Accept: application/json' \
-H 'Authorization: Bearer eyJ2ZXJzaW9uIjoicmVzdF92MyIsImRhdGEiOnsibWVyY2hhbnRfaWQiOjIwNDIsImFwaV91c2VyX2lkIjo1NjYwMzk3Miwic2VjcmV0IjoiQjIwODlDNkI5Q0NDNTdCNDQzNGHJK43JFJDK595FJFJMjlCRkFFRDM5OE***********************'
~~~

<ul class="nestedList url">
    <li><h3>URL <span>https://api.qiwi.com/b2b/bills/v3/get?bill_id=<a>bill_id</a></span></h3>
        <ul>
             <li><strong>bill_id</strong> - уникальный идентификатор счета в системе мерчанта.</li>
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

>Пример тела ответа

~~~json
{
  "result_code": "SUCCESS",
  "bill": {
    "site_id": "23044",
    "bill_id": "893794793973",
    "amount": {
      "value": 2.42,
      "currency": "RUB"
    },
    "status": {
      "value": "WAITING",
      "datetime": "2018-02-28T11:43:23"
    },
    "customer": {
      "email": "test@qiwi.com",
      "phone": "79191234567",
      "account": "user_account"
    },
    "extra": {
      "city": "Moscow"
    },
    "comment": "Text comment",
    "creation_datetime": "2018-02-28T11:43:23",
    "expiration_datetime": "2018-04-14T11:43:23",
    "pay_url": "https://oplata.qiwi.com/form/?invoice_uid=6848dd49-e260-4343-b258-62199cffe8c1"
  }
}
~~~

>Пример тела ответа при ошибке

~~~json
{
  "result_code": "AUTH_FAILED",
  "error_code": "auth.unauthorized",
  "description": "Неверные аутентификационные данные",
  "datetime": "2018-02-28T08:52:10.482Z"
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
bill.bill_id|String|Уникальный идентификатор счета в системе мерчанта
bill.site_id|Number|Идентификатор сайта мерчанта в QIWI Кассе
bill.amount|Number|Сумма счета, округленная до 2 знаков после запятой в меньшую сторону.
bill.currency	|String|Идентификатор валюты счета (Alpha-3 ISO 4217 код)
bill.status	|String|Текущий [статус счета](#status)
bill.extra|Object|Объект строковых дополнительных параметров, переданных партнером
bill.customer|Object|Идентификаторы пользователя. Возможные опции: `email`, `phone`, `account`
bill.comment|String|Комментарий к счету
bill.creation_datetime|Date| Системная дата создания счета. Формат даты:<br>`ГГГГ-ММ-ДДTчч:мм:сс`
bill.pay_url|String|Ссылка для переадресации пользователя на созданную платежную форму
bill.expiration_datetime|Date|Срок действия созданной формы для оплаты. Формат даты:<br>`ГГГГ-ММ-ДДTчч:мм:сс`


## 4. Отмена неоплаченного счета {#cancel}

Метод позволяет отменить неоплаченный клиентом счет.

<h3 class="request method">Запрос → POST</h3>

~~~javascript
const bill_id = '893794793973';

qiwiApi.cancelBill(bill_id).then( data => {
    //do with data
});
~~~

~~~shell
curl https://api.qiwi.com/b2b/bills/v3/reject \
-X POST \
-H 'Accept: application/json' \
-H 'Content-Type: application/json' \
-H 'Authorization: Bearer eyJ2ZXJzaW9uIjoicmVzdF92MyIsImRhdGEiOnsibWVyY2hhbnRfaWQiOjIwNDIsImFwaV91c2VyX2lkIjo1NjYwMzk3Miwic2VjcmV0IjoiQjIwODlDNkI5Q0NDNTdCNDQzNGHJK43JFJDK595FJFJMjlCRkFFRDM5OE***********************'
-d '{
           "bill_id": "893794793973" \ 
    }'
~~~

<ul class="nestedList url">
    <li><h3>URL <span>https://api.qiwi.com/b2b/bills/v3/reject</span></h3>
        <ul>
        <strong>Параметры:</strong>
             <li><strong>bill_id</strong> - уникальный идентификатор счета в системе мерчанта.</li>
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

>Пример тела ответа

~~~json
{
  "result_code": "SUCCESS",
  "bill": {
    "site_id": "23044",
    "bill_id": "893794793973",
    "amount": {
      "value": 2.42,
      "currency": "RUB"
    },
    "status": {
      "value": "REJECTED",
      "datetime": "2018-02-28T11:43:23"
    },
    "customer": {
      "email": "test@qiwi.com",
      "phone": "79191234567",
      "account": "user_account"
    },
    "extra": {
      "city": "Moscow"
    },
    "comment": "Text comment",
    "creation_datetime": "2018-02-28T11:43:23",
    "expiration_datetime": "2018-04-14T11:43:23",
    "pay_url": "https://oplata.qiwi.com/form/?invoice_uid=6848dd49-e260-4343-b258-62199cffe8c1"
  }
}
~~~

>Пример тела ответа при ошибке

~~~json
{
  "result_code": "AUTH_FAILED",
  "error_code": "auth.unauthorized",
  "description": "Неверные аутентификационные данные",
  "datetime": "2018-02-28T11:04:00.609Z"
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
bill.bill_id|String|Уникальный идентификатор счета в системе мерчанта
bill.site_id|Number|Идентификатор сайта мерчанта в QIWI Кассе
bill.amount|Number|Сумма счета, округленная до 2 знаков после запятой в меньшую сторону
bill.currency	|String|Идентификатор валюты счета (Alpha-3 ISO 4217 код)
bill.status	|String|Текущий [статус счета](#status)
bill.extra|Object|Объект строковых дополнительных параметров, переданных партнером
bill.customer|Object|Идентификаторы пользователя. Возможные опции: `email`, `phone`, `account`
bill.comment|String|Комментарий к счету
bill.creation_datetime|Date| Системная дата создания счета. Формат даты:<br>`ГГГГ-ММ-ДДTчч:мм:сс`
bill.pay_url|String|Ссылка на созданную платежную форму
bill.expiration_datetime|Date|Срок действия созданной формы для оплаты. Формат даты:<br>`ГГГГ-ММ-ДДTчч:мм:сс`


## 5. Возврат средств {#refund}

Метод позволяет сделать возврат средств.

<h3 class="request method">Запрос → POST</h3>

~~~javascript
const bill_id = '893794793973';
const refund_id = '899343443';
const amount = 12;
const currency = 'RUB'

qiwiApi.refund(bill_id, refund_id, amount, currency).then( data => {
    //do with data
});

~~~

~~~shell
curl https://api.qiwi.com/b2b/bills/v3/refund \
-X POST \
-H 'Accept: application/json' \
-H 'Content-Type: application/json' \
-H 'Authorization: Bearer eyJ2ZXJzaW9uIjoicmVzdF92MyIsImRhdGEiOnsibWVyY2hhbnRfaWQiOjIwNDIsImFwaV91c2VyX2lkIjo1NjYwMzk3Miwic2VjcmV0IjoiQjIwODlDNkI5Q0NDNTdCNDQzNGHJK43JFJDK595FJFJMjlCRkFFRDM5OE***********************' \
-d '{
    "amount": {
    "currency": "RUB",
    "value": 1
    },
  "bill_id": "893794793973",
  "refund_id": "1"
}'
~~~

<ul class="nestedList url">
    <li><h3>URL <span>https://api.qiwi.com/b2b/bills/v3/refund</span></h3>
        <ul>
        <strong>Параметры:</strong>
             <li><strong>bill_id</strong> - уникальный идентификатор счета в системе мерчанта.</li>
             <li><strong>refund_id</strong> - уникальный идентификатор возврата в системе мерчанта.</li>
             <li><strong>amount.value</strong> - сумма возврата.</li>
             <li><strong>amount.currency</strong> - валюта возврата.</li>
        </ul>
    </li>
</ul>

<ul class="nestedList header">
    <li><h3>HEADERS</h3>
        <ul>
             <li>Authorization: Bearer <a href="#auth">SECRET_KEY</a></li>
             <li>Accept: application/json</li>
             <li>application/json;charset=UTF-8</li>
        </ul>
    </li>
</ul>

<h3 class="request">Ответ ←</h3>

>Пример тела ответа

~~~json
{
  "result_code": "SUCCESS",
  "bill": {
    "site_id": "23044",
    "bill_id": "893794793973",
    "amount": {
      "value": 100,
      "currency": "RUB"
    },
    "status": {
      "value": "PAID",
      "datetime": "2018-03-01T11:01:52"
    },
    "comment": "Text comment",
    "creation_datetime": "2018-03-01T11:01:10",
    "expiration_datetime": "2018-03-13T14:30:00",
    "pay_url": "https://oplata-stage.qiwi.com/form/?invoice_uid=39b18de0-4a11-4b1d-9056-d3fba22a9592"
  },
  "refund": {
    "amount": {
      "value": 50.50,
      "currency": "RUB"
    },
    "date_time": "2018-03-01T16:06:57",
    "refund_id": "1",
    "status": "PARTIAL"
  }
}
~~~

>Пример тела ответа при ошибке

~~~json
{
  "result_code": "GENERAL_ERROR",
  "error_code": "api.refund.incorrect.amount",
  "description": "Неверная сумма возврата",
  "datetime": "2018-02-28T12:51:58.286Z"
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
refund|Object|Данные о возврате
refund.refund_id|String|Уникальный идентификатор возврата в системе мерчанта
refund.amount|Number|Сумма счета, округленная до 2 знаков после запятой в меньшую сторону
refund.currency	|String|Идентификатор валюты счета (Alpha-3 ISO 4217 код)
refund.status	|String|Статус возврата [статус возврата](#status)
refund.datetime|Date| Системная дата проведения возврата. Формат даты:<br>`ГГГГ-ММ-ДДTчч:мм:сс`
refund.txn_id|String| Уникальный номер счета (используется для идентификации конкретного счета)

## 6. Статус возврата {#refund-status}

Метод запрашивает статус возврата.

<h3 class="request method">Запрос → GET</h3>#

~~~javascript
const bill_id = '893794793973';
const refund_id = '899343443';

qiwiApi.getRefundInfo(bill_id, refund_id).then( data => {
    //do with data
});
~~~

~~~shell
curl https://api.qiwi.com/api/v3/prv/bills/893794793973/refund/899343443 \
-H 'Accept: application/json' \
-H 'Content-Type: application/json' \
-H 'Authorization: Bearer eyJ2ZXJzaW9uIjoicmVzdF92MyIsImRhdGEiOnsibWVyY2hhbnRfaWQiOjIwNDIsImFwaV91c2VyX2lkIjo1NjYwMzk3Miwic2VjcmV0IjoiQjIwODlDNkI5Q0NDNTdCNDQzNGHJK43JFJDK595FJFJMjlCRkFFRDM5OE***********************'
~~~

<ul class="nestedList url">
    <li><h3>URL <span>https://api.qiwi.com/api/v3/prv/bills/<a>bill_id</a>/refund/<a>refund_id</a></span></h3>
        <ul>
        <strong>Параметры:</strong>
             <li><strong>bill_id</strong> - уникальный идентификатор счета в системе мерчанта, передается в pathname.</li>
             <li><strong>refund_id</strong> - уникальный идентификатор возврата в системе мерчанта, передается в pathname.</li>
        </ul>
    </li>
</ul>

<ul class="nestedList header">
    <li><h3>HEADERS</h3>
        <ul>
             <li>Authorization: Bearer <a href="#auth">SECRET_KEY</a></li>
             <li>Accept: application/json</li>
             <li>application/json;charset=UTF-8</li>
        </ul>
    </li>
</ul>

<h3 class="request">Ответ ←</h3>

>Пример тела ответа

~~~json
{
  "result_code": "SUCCESS",
  "refund": {
    "refund_id": "2",
    "status": "FULL",
    "amount": {
      "value": 1.42,
      "currency": "RUB"
    },
    "date_time": "2018-02-28T16:18:36"
  }
}
~~~

>Пример тела ответа при ошибке

~~~json
{
  "result_code": "AUTH_FAILED",
  "error_code": "auth.unauthorized",
  "description": "Неверные аутентификационные данные",
  "datetime": "2018-02-28T13:22:12.513Z"
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
refund|Object|Данные о возврате
refund.refund_id|String|Уникальный идентификатор возврата в системе мерчанта
refund.amount|Number|Сумма счета, округленная до 2 знаков после запятой в меньшую сторону
refund.currency	|String|Идентификатор валюты счета (Alpha-3 ISO 4217 код)
refund.status	|String|Статус возврата [статус возврата](#status)
refund.datetime|Date| Системная дата проведения возврата. Формат даты:<br>`ГГГГ-ММ-ДДTчч:мм:сс`

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
PARTIAL | Частичный возврат суммы| -
FULL| Полный возврат суммы|+

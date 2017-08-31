# QIWI Bill Payments REST API {#bill-payments-api}

###### Последнее обновление: 2017-07-22 | [Редактировать на GitHub](https://github.com/QIWI-API/bill-payments-rest-api-docs/blob/master/_payments_invoice_api_ru.html.md)

## Последовательность операций {#steps}

![Operation Flow](images/bill_payments.png)

* Пользователь формирует заказ на сайте провайдера.

* Провайдер перенаправляет пользователя на [Платежную форму](#create-payment-form) QIWI Кассы для оплаты счета.

* После успешной оплаты счета провайдер получает [уведомление](#notification) (предварительно необходимо настроить отправку уведомлений в [Личном кабинете](https://kassa.qiwi.com)). Уведомления об оплате счета содержат параметры авторизации, которые необходимо проверять на сервере провайдера. Уведомления могут высылаться как при оплате счета, так и в других случаях (если пользователь отклонил счет или при оплате возникла ошибка).

* При необходимости, провайдер:
  * [запрашивает текущий статус оплаты счета](#invoice-status),
  * [отменяет счет](#cancel) (при условии, что он еще не был оплачен).

* После подтверждения оплаты счета провайдер исполняет заказ пользователя.

## Авторизация {#auth}

Запросы мерчанта авторизуются посредством HTTP basic-авторизации. Для авторизации используется [SECRET_KEY](#auth_param).<br>

~~~shell
header "Authorization: Bearer MjMyNDQxMjM6NDUzRmRnZDQ0Mw=="
~~~

<aside class="notice">
Заголовок представляет собой параметр Authorization, значение которого представлено как: "Bearer <a href="#auth_param">SECRET_KEY</a>"
</aside>

## Проверка статуса оплаты счета {#invoice-status}

Проверка статуса позволяет получить текущий статус оплаты счета клиентом.

<h3 class="request method">Запрос → GET</h3>

~~~http
GET /api/v3/prv/bills/BILL-1 HTTP/1.1
Accept: application/json
Authorization: Bearer eyJ2ZXJzaW9uIjoicmVzdF92MyIsImRhdGEiOnsibWVyY2hhbnRfaWQiOjIwNDIsImFwaV91c2VyX2lkIjo1NjYwMzk3Miwic2VjcmV0IjoiQjIwODlDNkI5Q0NDNTdCNDQzNGHJK43JFJDK595FJFJMjlCRkFFRDM5OEZBOTBENDBEMiJ9fQ==
Host: api.qiwi.com
Content-Type: application/x-www-form-urlencoded; charset=utf-8

HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
{
  "bill": {
    "amount": 42.24,
    "bill_id": "30192832",
    "comment": "Text comment",
    "create_datetime": "2017-08-13T14:30:00.000Z",
    "currency": "RUB",
    "extras": {},
    "lifetime_datetime": "2017-10-13T14:30:00.000Z",
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

<ul class="nestedList url">
    <li><h3>URL <span>https://api.qiwi.com/api/v3/prv/bills/<a>bill_id</a></span></h3>
        <ul>
        <strong>Параметр передается в pathname.</strong>
             <li><strong>bill_id</strong> - уникальный идентификатор счета в системе провайдера.</li>
        </ul>
    </li>
</ul>

<ul class="nestedList header">
    <li><h3>HEADERS</h3>
        <ul>
             <li>Authorization: Bearer <a href="#auth_param">SECRET_KEY</a></li>
             <li>Accept: application/json</li>
        </ul>
    </li>
</ul>

<h3 class="request">Ответ ←</h3>

[Параметры ответа](#response_bill)

[Ответ в случае ошибки](#response_error)



## Отмена неоплаченного счета {#cancel}

Запрос позволяет отменить неоплаченный клиентом счет.

<h3 class="request method">Запрос → PATCH</h3>


~~~http

GET /api/v3/prv/bills/BILL-1/REJECT HTTP/1.1
Accept: application/json
Authorization: Bearer eyJ2ZXJzaW9uIjoicmVzdF92MyIsImRhdGEiOnsibWVyY2hhbnRfaWQiOjIwNDIsImFwaV91c2VyX2lkIjo1NjYwMzk3Miwic2VjcmV0IjoiQjIwODlDNkI5Q0NDNTdCNDQzNGHJK43JFJDK595FJFJMjlCRkFFRDM5OEZBOTBENDBEMiJ9fQ==
Host: api.qiwi.com
Content-Type: application/x-www-form-urlencoded; charset=utf-8

HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
{
  "bill": {
    "amount": 42.24,
    "bill_id": "30192832",
    "comment": "Text comment",
    "create_datetime": "2017-08-13T14:30:00.000Z",
    "currency": "RUB",
    "extras": {},
    "lifetime_datetime": "2017-10-13T14:30:00.000Z",
    "pay_url": "https://oplata.qiwi.com/form/?invoice_uid=755ac889-6f94-4f82-a0b8-3dffc24afa60",
    "status": "WAITING",
    "user": {}
  },
  "result_code": "SUCCESS"
}
~~~

<ul class="nestedList url">
    <li><h3>URL <span>https://api.qiwi.com/api/v3/prv/bills/<a>bill_id</a>/reject</span></h3>
        <ul>
        <strong>Параметры:</strong>
             <li><strong>bill_id</strong> - уникальный идентификатор счета в системе провайдера, передается в pathname.</li>
        </ul>
    </li>
</ul>

<ul class="nestedList header">
    <li><h3>HEADERS</h3>
        <ul>
             <li>Authorization: Basic <a href="#auth_param">SECRET_KEY</a></li>
             <li>Accept: application/json</li>
        </ul>
    </li>
</ul>

<h3 class="request">Ответ ←</h3>

[Параметры ответа](#response_bill)

[Ответ в случае ошибки](#response_error)



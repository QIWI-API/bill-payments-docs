---
title: QIWI Универсальный платежный API 1.0.0 beta

search: true

metatitle: Универсальный платежный API 1.0.0 beta

metadescription: Универсальный платежный API открывает доступ к операциям с выставляемыми счетами. Счет - универсальная заявка на оплату. По-умолчанию пользователю доступно несколько способов оплаты. В API поддерживаются операции выставления и отмены счетов, возврата средств по оплаченным счетам, а также проверки статуса выполнения операций.

language_tabs:
  - shell
  - javascript: Node.js SDK
  - php: PHP SDK
  - java: Java SDK
  
services:
 - <a href='#'>Swagger</a>  |  <a href='#'>Qiwi Demo</a>


toc_footers:
 - <a href='/'>На главную</a>
 - <a href='mailto:bss@qiwi.com'>Обратная связь</a>

---

# Универсальный платежный API {#introduction}

###### Последнее обновление: 2018-10-08 | [Редактировать на GitHub](https://github.com/QIWI-API/bill-payments-docs/blob/master/bill-payments_ru.html.md)

Универсальный платежный API открывает доступ к операциям с выставляемыми счетами. Счет - универсальная заявка на оплату. По-умолчанию пользователю доступно несколько способов оплаты. 
В API поддерживаются операции выставления и отмены счетов, возврата средств по оплаченным счетам, а также проверки статуса выполнения операций.

**Для работы API потребуются публичный и секретный ключи. Ключи создаются в личном кабинете после [регистрации и подключения](https://kassa.qiwi.com/pay).**

## Оформление

При переходе на платежную страницу с вашего сайта мы рекомендуем <b>выводить иконки всех способов оплаты</b>, доступных на форме. Это увеличивает конверсию в платеж, т.к. позволяет быстрее находить привычный способ оплаты. Например:

 ![Operation Flow](/images/var_1.png)

 <a href="/images/QIWI_Kassa.svg" download>Скачать логотип QIWI Кассы</a>

 <a href="/images/icons.svg" download>Скачать иконки способов оплаты</a>

 Все варианты оформления  <a href="#design">в соответствующем разделе</a>

## SDK и библиотеки {#sdk}

* [NODE JS SDK](https://github.com/QIWI-API/bill-payments-node-js-sdk) - Готовое решение для разработки server2server интеграции c помощью Node.js.
* [PHP SDK](https://github.com/QIWI-API/bill-payments-php-sdk) - Готовое решение для разработки server2server интеграции c помощью PHP.
* [Java SDK](https://github.com/QIWI-API/bill-payments-java-sdk) - Готовое решение для разработки server2server интеграции c помощью Java.

## Решения для CMS {#cms}

* [Wordpress](https://wordpress.org/plugins/woo-qiwi-payment-gateway/) -  расширение под Woocommerce для работы с заказами
* [Онлайн Лейка](https://wordpress.org/plugins/leyka/) -  Wordpress расширение для благотворительности
* [1С-Битрикс](http://marketplace.1c-bitrix.ru/solutions/qiwikassa.checkout/) - решение для работы с заказами

## Персонализация {#pers}

Персонализация  позволяет создать платежную форму под Ваш стиль, настраивается лого , фон и цвет кнопок. Настройка возможна на kassa.qiwi.com.
Необходимо передавать экстру: "themeCode":"кодСтиля"

Параметр|Описание|Тип|
---------|--------|---|---------|---
customFields[]|Дополнительные данные счета|URL-закодированная строка String(255)

 >Пример выставления счета через платежную форму

~~~shell
curl https://oplata.qiwi.com/create?publicKey=Fnzr1yTebUiQaBLDnebLMMxL8nc6FF5zfmGQnypc*******&amount=100&billId=893794793973&successUrl=http%3A%2F%2Ftest.ru%3F&customField[themeCode]=кодСтиля
~~~

 >Пример выставления счета через API

~~~shell
curl https://api.qiwi.com/partner/bill/v1/bills/893794793973 
-X PUT 
-H 'Accept: application/json' 
-H 'Content-Type: application/json' 
-H 'Authorization: Bearer eyJ2ZXJzaW9uIjoicmVzdF92MyIsImRhdGEiOnsibWVyY2hhbnRfaWQiOjIwNDIsImFwaV91c2VyX2lkIjo1NjYwMzk3Miwic2VjcmV0IjoiQjIwODlDNkI5Q0NDNTdCNDQzNGHJK43JFJDK595FJFJMjlCRkFFRDM5OE***********************'
-d '{ 
   "amount": {  
     "currency": "RUB",  
     "value": 100.00 
   }, 
   "comment": "Text comment", 
   "expirationDateTime": "2018-04-13T14:30:00+03:00", 
   "customer": {}, 
   "customFields"{"themeCode":"кодСтиля"} 
   }
~~~




## Последовательность операций {#steps}

![Operation Flow](/images/bill_payments.png)

* Пользователь формирует заказ на сайте мерчанта.

* Мерчант перенаправляет пользователя на [Платежную форму](#http) для выставления счета по заказу. Или [выставляет счет по API](#create) и перенаправляет пользователя на созданную платежную форму.

* Пользователь выбирает способ оплаты и оплачивает счет. По умолчанию подбирается наиболее оптимальный способ оплаты.

* После оплаты счета мерчант получает [уведомление](#notification) (предварительно настройте отправку уведомлений в [Личном кабинете](https://kassa.qiwi.com)). Уведомления об оплате счета содержат параметры авторизации, которые необходимо проверять на сервере мерчанта.

* Также есть возможность
  * [запросить текущий статус оплаты счета](#invoice-status),
  * [отменить неоплаченный счет](#cancel).

## Авторизация {#auth}

Запросы мерчанта авторизуются посредством секретного ключа API (SECRET_KEY). Параметр авторизации указывается в заголовке <i>Authorization</i>, значение которого формируется как "Bearer SECRET_KEY"
Публичный ключ (PUBLIC_KEY) используется для выставления счетов через веб-форму.
**Ключи создаются в личном кабинете после [регистрации и подключения](https://kassa.qiwi.com/pay).**
<aside class="notice">
Важно! Не передавайте секретный ключ третьим лицам!. 
</aside>


~~~javascript
const QiwiBillPaymentsAPI = require('bill-payments-node-js-sdk');

const SECRET_KEY = 'eyJ2ZXJzaW9uIjoicmVzdF92MyIsImRhdGEiOnsibWVyY2hhbnRfaWQiOjUyNjgxMiwiYXBpX3VzZXJfaWQiOjcxNjI2MTk3LCJzZWNyZXQiOiJmZjBiZmJiM2UxYzc0MjY3YjIyZDIzOGYzMDBkNDhlYjhiNTnONPININONPN090MTg5Z**********************';

const qiwiApi = new QiwiBillPaymentsAPI(SECRET_KEY);
~~~

~~~shell
--header "Authorization: Bearer MjMyNDQxMjM6NDUzRmRnZDQ0M*******"
~~~

~~~php
<?php

const SECRET_KEY = 'eyJ2ZXJzaW9uIjoicmVzdF92MyIsImRhdGEiOnsibWVyY2hhbnRfaWQiOjUyNjgxMiwiYXBpX3VzZXJfaWQiOjcxNjI2MTk3LCJzZWNyZXQiOiJmZjBiZmJiM2UxYzc0MjY3YjIyZDIzOGYzMDBkNDhlYjhiNTnONPININONPN090MTg5Z**********************';

/** @var \Qiwi\Api\BillPayments $billPayments */
$billPayments = new Qiwi\Api\BillPayments(SECRET_KEY);

?>
~~~

~~~java
String secretKey = "eyJ2ZXJzaW9uIjoicmVzdF92MyIsImRhdGEiOnsibWVyY2hhbnRfaWQiOjUyNjgxMiwiYXBpX3VzZXJfaWQiOjcxNjI2MTk3LCJzZWNyZXQiOiJmZjBiZmJiM2UxYzc0MjY3YjIyZDIzOGYzMDBkNDhlYjhiNTnONPININONPN090MTg5Z**********************";
 BillPaymentClient client = BillPaymentClientFactory.createDefault(secretKey);
~~~


## 1.1 Выставление счета через платежную форму {#http}

Простой способ для интеграции. При открытии формы клиенту автоматически выставляется счет. Параметры счета передаются в открытом виде в ссылке. Далее клиенту отображается платежная форма с выбором способа оплаты.
При использовании этого способа нельзя гарантировать, что все счета выставлены мерчантом, в отличие от [выставления по API](#create).


<h3 class="request method" id="payform_flow">REDIRECT → </h3>

<ul class="nestedList url">
    <li><h3>URL <span>https://oplata.qiwi.com/create</span></h3></li>
</ul>

~~~javascript
const publicKey = 'Fnzr1yTebUiQaBLDnebLMMxL8nc6FF5zfmGQnypc*******';

const params = {
    publicKey,
    amount: 42.24,
    billId: '893794793973',
    successUrl: 'http://test.ru/',
    email: 'm@ya.ru'
};

const link = qiwiApi.createPaymentForm(params);
~~~

~~~shell
curl https://oplata.qiwi.com/create?publicKey=Fnzr1yTebUiQaBLDnebLMMxL8nc6FF5zfmGQnypc*******&amount=100&billId=893794793973&successUrl=http%3A%2F%2Ftest.ru%3F&email=m@ya.ru
~~~

~~~php
<?php

$publicKey = '2tbp1WQvsgQeziGY9vTLe9vDZNg7tmCymb4Lh6STQokqKrpCC6qrUUKEDZAJ7mvFnzr1yTebUiQaBLDnebLMMxL8nc6FF5zf******';
$params = [
  'publicKey' => $publicKey,
  'amount' => 200,
  'billId' => '893794793973'
];

/** @var \Qiwi\Api\BillPayments $billPayments */
$link = $billPayments->createPaymentForm($params);

echo $link;

?>
~~~

~~~java
String publicKey = "2tbp1WQvsgQeziGY9vTLe9vDZNg7tmCymb4Lh6STQokqKrpCC6qrUUKEDZAJ7mvFnzr1yTebUiQaBLDnebLMMxL8nc6FF5zfmGQnypdXCbQJqHEJW5RJmKfj8nvgc";
 MoneyAmount amount = new MoneyAmount(
        BigDecimal.valueOf(499.90),
        Currency.getInstance("RUB")
);
String billId = UUID.randomUUID().toString();
String successUrl = "https://merchant.com/payment/success?billId=893794793973";
 String paymentUrl = client.createPaymentForm(new PaymentInfo(key, amount, billId, successUrl));
~~~

<ul class="nestedList params">
    <li><h3>Параметры</h3><span>В ссылке на веб-форму указываются параметры счета.</span></li>
</ul>

Параметр|Описание|Тип|Обяз.
---------|--------|---|---------|---|----
publicKey | Ключ идентификации мерчанта, полученный в QIWI Кассе|String|+
billId|Уникальный идентификатор счета в системе мерчанта|URL-закодированная строка String(200)|-
amount| Сумма, на которую выставляется счет, округленная в меньшую сторону до 2 десятичных знаков | Number(6.2)|-
phone | Номер телефона пользователя, на который выставляется счет (в международном формате) | URL-закодированная строка|-
email | E-mail пользователя, куда будет отправлена ссылка для оплаты счета | URL-закодированная строка|-
account | Идентификатор пользователя в системе мерчанта | URL-закодированная строка |-
comment | Комментарий к счету|URL-закодированная строка String(255)|-
customFields[]|Дополнительные данные счета|URL-закодированная строка String(255)|-
lifetime | Дата, до которой счет будет доступен для оплаты. Если счет не будет оплачен до этой даты, ему присваивается финальный статус `EXPIRED` и последующая оплата станет невозможна.<br> **Внимание! По истечении 45 суток от даты выставления счет автоматически будет переведен в финальный статус**|URL-закодированная строка<br>`ГГГГ-ММ-ДДTччмм`|-
successUrl|URL для переадресации в случае успешной оплаты с баланса QIWI Кошелька. При ином способе оплаты переадресация не выполняется. Ссылка должна вести на сайт мерчанта.|URL-закодированная строка|-


## 1.2 Выставление счета по API {#create}

Надежный способ для интеграции. Параметры передаются server2server с использованием авторизации.
Метод позволяет выставить счет, при успешном выполнении запроса в ответе вернется параметр payUrl - ссылка для редиректа пользователя на платежную форму. 

<h3 class="request method">Запрос → PUT</h3>

~~~javascript
const billId = '893794793973';

const fields = {
    amount: 1.00,
    currency: 'RUB',
    comment: 'Hello world',
    expirationDateTime: '2018-03-02T08:44:07'
};

qiwiRestApi.createBill( billId, fields ).then( data => {
    //do with data
});
~~~

~~~shell
curl https://api.qiwi.com/partner/bill/v1/bills/893794793973 
-X PUT 
-H 'Accept: application/json' 
-H 'Content-Type: application/json' 
-H 'Authorization: Bearer eyJ2ZXJzaW9uIjoicmVzdF92MyIsImRhdGEiOnsibWVyY2hhbnRfaWQiOjIwNDIsImFwaV91c2VyX2lkIjo1NjYwMzk3Miwic2VjcmV0IjoiQjIwODlDNkI5Q0NDNTdCNDQzNGHJK43JFJDK595FJFJMjlCRkFFRDM5OE***********************'
-d '{ 
   "amount": {  
     "currency": "RUB",  
     "value": 100.00 
   }, 
   "comment": "Text comment", 
   "expirationDateTime": "2018-04-13T14:30:00+03:00", 
   "customer": {}, 
   "customFields": {}  
   }
~~~

~~~php
<?php

$billId = '893794793973';
$fields = [
  'amount' => 1.00,
  'currency' => 'RUB',
  'comment' => 'test',
  'expirationDateTime' => '2018-03-02T08:44:07',
  'email' => 'example@mail.org',
  'account' => 'client4563'
];

/** @var \Qiwi\Api\BillPayments $billPayments */
$response = $billPayments->createBill($billId, $fields);

print_r($response);

?>
~~~

~~~java
CreateBillInfo billInfo = new CreateBillInfo(
                UUID.randomUUID().toString(),
                new MoneyAmount(
                        BigDecimal.valueOf(199.90),
                        Currency.getInstance("RUB")
                ),
                "comment",
                ZonedDateTime.now().plusDays(45),
                new Customer(
                        "example@mail.org",
                        UUID.randomUUID().toString(),
                        "79123456789"
                ),
                "http://merchant.ru/success"
        );
BillResponse response = client.createBill(billInfo);
~~~


<ul class="nestedList url">
    <li><h3>URL <span>https://api.qiwi.com/partner/bill/v1/bills/{billId}</span></h3>
        <ul>
        <strong>Параметры:</strong>
             <li><strong>billId</strong> - уникальный идентификатор счета в системе мерчанта.</li>
             <li><strong>amount.currency</strong> - валюта счета (Alpha-3 ISO 4217 код).	</li>
             <li><strong>amount.value</strong> - сумма счета, округленная до двух десятичных знаков в меньшую сторону.</li>
             <li><strong>comment</strong> - комментарий к счету.</li>
             <li><strong>expirationDateTime</strong> - срок оплаты счета. Время передается с указанием часового пояса. </li>
             <li><strong>customer.phone</strong> - номер телефона, на который был выставлен счет. </li>
             <li><strong>customer.email</strong> - e-mail пользователя.</li>
             <li><strong>customer.account</strong> - Идентификатор пользователя в системе мерчанта.</li>
             <li><strong>customFields</strong> - Дополнительные поля.</li>
             
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
    "siteId": 23044,
    "billId": "893794793973",
    "amount": {
      "value": 100,
      "currency": "RUB"
    },
    "status": {
      "value": "WAITING",
      "changedDateTime": "2018-03-05T11:27:41+03:00"
    },
    "comment": "Text comment",
    "creationDateTime": "2018-03-05T11:27:41",
    "expirationDateTime": "2018-04-13T14:30:00",
    "payUrl": "https://oplata.qiwi.com/form/?invoice_uid=d875277b-6f0f-445d-8a83-f62c7c07be77"
  }
~~~

>Пример тела ответа при ошибке

~~~json
{
	"serviceName": "invoicing-api",
	"errorCode": "auth.unauthorized",
	"description": "Неверные аутентификационные данные",
	"userMessage": "",
	"datetime": "2018-04-09T18:31:42+03:00",
	"traceId" : "48485a395dfsdf34v124"
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
billId|String|Уникальный идентификатор счета в системе мерчанта
siteId|Number|Идентификатор сайта мерчанта в QIWI Кассе
amount|Object|Данные о сумме счета
amount.value|Number|Сумма счета, округленная до 2 знаков после запятой в меньшую сторону
amount.currency	|String|Валюта счета (Alpha-3 ISO 4217 код)
status|Object|Данные о статусе счета
status.value	|String|Текущий [статус счета](#status)
status.changedDateTime|Date|Дата обновления статуса. Формат даты:<br>`YYYY-MM-DDThh:mm:ss±hh`
customFields|Object|Дополнительные поля
customer|Object|Идентификаторы пользователя. Возможные опции: `email`, `phone`, `account`
comment|String|Комментарий к счету
creationDateTime|Date| Системная дата создания счета. Формат даты:<br>`YYYY-MM-DDThh:mm:ss`
payUrl|String|Ссылка на созданную платежную форму
expirationDateTime|Date|Срок действия созданной формы для оплаты. Формат даты:<br>`YYYY-MM-DDThh:mm:ss`

## 2. Уведомления об оплате счетов {#notification}

 >Пример уведомления

~~~http
POST /qiwi-notify.php HTTP/1.1
Accept: application/json
Content-type: application/json
X-Api-Signature-SHA256: J4WNfNZd***V5mv2w=
Host: server.ru

{ "bill":
  {  
     "siteId":"23044",
     "billId":"1519892138404fhr7i272a2",
     "amount":{  
        "value":"100",
        "currency":"RUB"
     },
     "status":{  
        "value":"PAID",
        "datetime":"2018-03-01T11:16:12"
     },
     "customer":{},
     "customFields":{},
     "creationDateTime":"2018-03-01T11:15:39",
     "expirationDateTime":"2018-04-01T11:15:39"
   },
  "version":"1"
}
~~~


Уведомление представляет собой входящий POST-запрос. Тело запроса содержит JSON-сериализованные данные счета (кодировка UTF-8).
Aдрес сервера для уведомлений указывается на сайте <a href="https://kassa.qiwi.com/">kassa.qiwi.com</a> в разделе "Настройка протокола".

<ul class="nestedList header">
    <li><h3>HEADERS</h3>
        <ul>
             <li>X-Api-Signature-SHA256: ***</li>
             <li>Accept: application/json</li>
             <li>Content-type: application/json</li>
        </ul>
    </li>
</ul>

### Авторизация уведомлений {#notifications_auth}

После получения входящего уведомления необходимо проверить подлинность данного уведомления. Для этого используется механизм цифровой подписи. Подпись уведомления отправляется в заголовке `X-Api-Signature-SHA256`. Для формирования подписи используется механизм проверки целостности HMAC с хэш-функцией SHA256.

Алгоритм проверки подписи:

1. Объединить значения параметров в одну строку с разделителем "\|":

   `invoice_parameters = {amount.currency}|{amount.value}|{billId}|{siteId}|{status.value}`

   где `{*}` – значение параметра уведомления. Все значения при проверке подписи должны трактоваться как строки. 


2. Вычислить HMAC-хэш c алгоритмом хэширования SHA256:

   `hash = HMAС(SHA256, secret_key, invoice_parameters)`
   Где:

   * `secret_key` – ключ функции ;
   * `invoice_parameters` – строка из п.1;

3. Сравнить значение заголовка X-Api-Signature-SHA256 с результатом из п.2.

~~~javascript
const validSignatureFromNotificationServer =
      '07e0ebb10916d97760c196034105d010607a6c6b7d72bfa1c3451448ac484a3b';

const notificationData = {
    bill: {
        siteId: 'test',
        billId: 'test_bill',
        amount: { value: 1, currency: 'RUB' },
        status: { value: 'PAID', datetime: '2018-03-01T11:16:12+03' },
        customer: {},
        customFields: {},
        creationDateTime: '2018-03-01T11:15:39+03',
        expirationDateTime: '2018-04-15T11:15:39+03'
    },
    version: '1'
};

const merchantSecret = 'test-merchant-secret-for-signature-check';

qiwiApi.checkNotificationSignature(
    validSignatureFromNotificationServer, notificationData, merchantSecret
); // true
~~~
 
~~~php
<?php

$validSignatureFromNotificationServer = '07e0ebb10916d97760c196034105d010607a6c6b7d72bfa1c3451448ac484a3b';
$notificationData = [
  'bill' => [
    'siteId' => 'test',
    'billId' => 'test_bill',
    'amount' => ['value' => 1, 'currency' => 'RUB'],
    'status' => ['value' => 'PAID']
  ],
  'version' => '3'
];
$merchantSecret = 'test-merchant-secret-for-signature-check';

/** @var \Qiwi\Api\BillPayments $billPayments */
$billPayments->checkNotificationSignature(
  $validSignatureFromNotificationServer, $notificationData, $merchantSecret
); // true

?>
~~~

~~~java
String merchantSecret = "test-merchant-secret-for-signature-check";
Notification notification = new Notification(
        new Bill(
                "test",
                "test_bill",
                new MoneyAmount(
                        BigDecimal.ONE,
                        Currency.getInstance("RUB")
                ),
                BillStatus.PAID
        ),
        "3"
);
String validSignature = "07e0ebb10916d97760c196034105d010607a6c6b7d72bfa1c3451448ac484a3b";
 BillPaymentsUtils.checkNotificationSignature(validSignature, notification, merchantSecret); //true
~~~

Cтрока и ключ подписи кодируются в UTF-8.

<ul class="nestedList params">
    <li><h3>Параметры</h3><span>В POST-запросе уведомления указаны параметры счета.</span>
    </li>
</ul>

Параметр|Описание|Тип
---------|--------|---
bill|Данные о счете|Object
bill.billId|Уникальный идентификатор счета в системе мерчанта|String(200)
bill.siteId|Идентификатор сайта мерчанта в QIWI Кассе| String
bill.amount|Данные о сумме счета|Object
amount.value | Сумма счета, округленная до двух десятичных знаков в меньшую сторону | Number(6.2)
amount.currency | Идентификатор валюты счета (Alpha-3 ISO 4217 код) | String(3)
bill.status | Данные о статусе счета | Object
status.value |Строковое значение статуса | String
status.datetime|Дата обновления статуса| Формат даты<br>`ГГГГ-ММ-ДДTЧЧ:ММ:ССZ`
bill.customer | Данные о пользователе, на которого был выставлен счет| Object
customer.phone |Номер телефона, на который был выставлен счет (если был указан при выставлении счета) |String
customer.email|E-mail пользователя (если был указан при выставлении счета)|String
customer.account| Идентификатор пользователя в системе мерчанта (если был указан при выставлении счета)|String
bill.creationDateTime | Дата создания счета | Формат даты<br>`ГГГГ-ММ-ДДTЧЧ:ММ:ССZ`
bill.expirationDateTime | Срок оплаты счета | Формат даты<br>`ГГГГ-ММ-ДДTЧЧ:ММ:ССZ`
bill.comment | Комментарий к счету | String(255)
bill.customFields | Дополнительные данные счета (если были указаны при выставлении счета).| Object
version | Версия уведомлений | String

<h3 class="request method">Ответ → POST</h3>

~~~
HTTP/1.1 200 OK
Content-Type: application/json

{
 "error":"0"
}
~~~

После того, как был получен входящий запрос-уведомление, необходимо проверить подлинность цифровой подписи и отправить ответ в формате JSON. В ответе должен вернуться код результата обработки уведомления. Код результата должен находиться в параметре <i>error</i>.

<aside class="notice">
Если в ответе код результата обработки уведомления отличается от 0 и/или код состояния HTTP отличается от 200 (OK), это интерпретируется как временная ошибка мерчанта. Сервер QIWI будет повторять запрос с нарастающим интервалом в течение суток.
</aside>

### Заголовки

*  `Content-type: application/json`



## 3. Проверка статуса оплаты счета {#invoice-status}

Метод предназначен для получения статуса оплаты счета клиентом. Рекомендуется его использовать после получения уведомления об оплате.

<h3 class="request method">Запрос → GET</h3>

~~~javascript
const billId = '893794793973';

qiwiApi.getBillInfo(billId).then( data => {
    //do with data
});
~~~

~~~shell
curl https://api.qiwi.com/partner/bill/v1/bills/893794793973 
-X GET 
-H 'Accept: application/json' 
-H 'Authorization: Bearer eyJ2ZXJzaW9uIjoicmVzdF92MyIsImRhdGEiOnsibWVyY2hhbnRfaWQiOjIwNDIsImFwaV91c2VyX2lkIjo1NjYwMzk3Miwic2VjcmV0IjoiQjIwODlDNkI5Q0NDNTdCNDQzNGHJK43JFJDK595FJFJMjlCRkFFRDM5OE***********************'
~~~

~~~php
<?php

$billId = '893794793973';

/** @var \Qiwi\Api\BillPayments $billPayments */
$response = $billPayments->getBillInfo($billId);

print_r($response);

?>
~~~

~~~java
String billId = "fcb40a23-6733-4cf3-bacf-8e425fd1fc71";
 BillResponse response = client.getBillInfo(billId);
~~~

<ul class="nestedList url">
    <li><h3>URL <span>https://api.qiwi.com/partner/bill/v1/bills/{billid}</span></h3>
        <ul>
             <li><strong>billId</strong> - уникальный идентификатор счета в системе мерчанта.</li>
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
    "siteId": 23044,
    "billId": "893794793973",
    "amount": {
      "value": 100,
      "currency": "RUB"
    },
    "status": {
      "value": "WAITING",
      "changedDateTime": "2018-03-05T11:27:41+03:00"
    },
    "comment": "Text comment",
    "creationDateTime": "2018-03-05T11:27:41",
    "expirationDateTime": "2018-04-13T14:30:00",
    "payUrl": "https://oplata.qiwi.com/form/?invoice_uid=d875277b-6f0f-445d-8a83-f62c7c07be77"
  }
~~~

>Пример тела ответа при ошибке

~~~json
{
	"serviceName": "invoicing",
	"errorCode": "auth.unauthorized",
	"description": "Неверные аутентификационные данные",
	"userMessage": "",
	"datetime": "2018-04-09T18:31:42+03:00",
	"traceId" : ""
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
bill|Object|Данные о счете
bill.billId|String|Уникальный идентификатор счета в системе мерчанта
bill.siteId|String|Идентификатор сайта мерчанта в QIWI Кассе
bill.amount|Object|Данные о сумме счета
amount.value|Number|Сумма счета, округленная до 2 знаков после запятой в меньшую сторону.
amount.currency	|String|Идентификатор валюты счета (Alpha-3 ISO 4217 код)
status|Object|Данные о статусе счета
status.value  |String|Текущий [статус счета](#status)
status.datetime|Date|Дата обновления статуса
bill.customFields|Object|Объект строковых дополнительных параметров, переданных партнером
bill.customer|Object|Идентификаторы пользователя
customer.phone |Номер телефона, на который был выставлен счет (если был указан при выставлении счета) |String
customer.email|E-mail пользователя (если был указан при выставлении счета)|String
customer.account| Идентификатор пользователя в системе мерчанта (если был указан при выставлении счета)|String
bill.comment|String|Комментарий к счету
bill.creationDateTime|Date| Системная дата создания счета. Формат даты:<br>`ГГГГ-ММ-ДДTчч:мм:сс`
bill.payUrl|String|Ссылка для переадресации пользователя на созданную платежную форму
bill.expirationDateTime|Date|Срок действия созданной формы для оплаты. Формат даты:<br>`ГГГГ-ММ-ДДTчч:мм:сс`


## 4. Отмена неоплаченного счета {#cancel}

<h3 class="request method">Запрос → POST</h3>

~~~javascript
const bill_id = '893794793973';

qiwiApi.cancelBill(billId).then( data => {
    //do with data
});
~~~

~~~shell
curl https://api.qiwi.com/partner/bill/v1/bills/893794793973/reject 
-X POST 
-H 'Accept: application/json' 
-H 'Content-Type: application/json' 
-H 'Authorization: Bearer eyJ2ZXJzaW9uIjoicmVzdF92MyIsImRhdGEiOnsibWVyY2hhbnRfaWQiOjIwNDIsImFwaV91c2VyX2lkIjo1NjYwMzk3Miwic2VjcmV0IjoiQjIwODlDNkI5Q0NDNTdCNDQzNGHJK43JFJDK595FJFJMjlCRkFFRDM5OE***********************'
~~~

~~~php
<?php

$billId = '893794793973';

/** @var \Qiwi\Api\BillPayments $billPayments */
$response = $billPayments->cancelBill($billId);

print_r($response);

?>
~~~

~~~java
String billId = "fcb40a23-6733-4cf3-bacf-8e425fd1fc71";
 BillResponse response = client.cancelBill(billId);
~~~

<ul class="nestedList url">
    <li><h3>URL <span>https://api.qiwi.com/partner/bill/v1/bills/{billId}/reject</span></h3>
        <ul>
        <strong>Параметры:</strong>
             <li><strong>billId</strong> - уникальный идентификатор счета в системе мерчанта.</li>
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
  "bill": {
    "siteId": "23044",
    "billId": "893794793973",
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
    "customFields": {
      "city": "Moscow"
    },
    "comment": "Text comment",
    "creationDateTime": "2018-02-28T11:43:23",
    "expirationDateTime": "2018-04-14T11:43:23",
    "payUrl": "https://oplata.qiwi.com/form/?invoice_uid=6848dd49-e260-4343-b258-62199cffe8c1"
  }
}
~~~

>Пример тела ответа при ошибке

~~~json
{
	"serviceName": "invoicing",
	"errorCode": "auth.unauthorized",
	"description": "Неверные аутентификационные данные",
	"userMessage": "",
	"datetime": "2018-04-09T18:31:42+03:00",
	"traceId" : ""
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
bill|Object|Данные о счете
bill.billId|String|Уникальный идентификатор счета в системе мерчанта
bill.siteId|String|Идентификатор сайта мерчанта в QIWI Кассе
amount.value|Number|Сумма счета, округленная до 2 знаков после запятой в меньшую сторону
amount.currency	|String|Идентификатор валюты счета (Alpha-3 ISO 4217 код)
status|Object|Данные о статусе счета
status.value  |String|Текущий [статус счета](#status)
status.datetime|Date|Дата обновления статуса
bill.customFields|Object|Объект строковых дополнительных параметров, переданных партнером
bill.customer|Object|Идентификаторы пользователя. Возможные опции: `email`, `phone`, `account`
bill.comment|String|Комментарий к счету
bill.creationDateTime|Date| Системная дата создания счета. Формат даты:<br>`ГГГГ-ММ-ДДTчч:мм:сс`
bill.payUrl|String|Ссылка на созданную платежную форму
bill.expirationDateTime|Date|Срок действия созданной формы для оплаты. Формат даты:<br>`ГГГГ-ММ-ДДTчч:мм:сс`


## 5. Возврат средств {#refund}

Метод позволяет сделать возврат средств.

<h3 class="request method">Запрос → PUT</h3>

~~~javascript
const billId = '893794793973';
const refundId = '899343443';
const amount = 12;
const currency = 'RUB'

qiwiApi.refund(billId, refundId, amount, currency).then( data => {
    //do with data
});

~~~

~~~shell
curl https://api.qiwi.com/partner/bill/v1/bills/893794793973/refunds/899343443
-X PUT 
-H 'Accept: application/json' 
-H 'Content-Type: application/json' 
-H 'Authorization: Bearer eyJ2ZXJzaW9uIjoicmVzdF92MyIsImRhdGEiOnsibWVyY2hhbnRfaWQiOjIwNDIsImFwaV91c2VyX2lkIjo1NjYwMzk3Miwic2VjcmV0IjoiQjIwODlDNkI5Q0NDNTdCNDQzNGHJK43JFJDK595FJFJMjlCRkFFRDM5OE***********************' 
-d '{
    "amount": {
    "currency": "RUB",
    "value": 1
    }
}'
~~~

~~~php
<?php

$billId = '893794793973';
$refundId = '899343443';
$amount = 12;
$currency = 'RUB';

/** @var \Qiwi\Api\BillPayments $billPayments */
$response = $billPayments->refund($billId, $refundId, $amount, $currency);

print_r($response);
?>
~~~

~~~java
String billId = "fcb40a23-6733-4cf3-bacf-8e425fd1fc71";
String refundId = UUID.randomUUID().toString();
MoneyAmount amount = new MoneyAmount(
        BigDecimal.valueOf(104.90),
        Currency.getInstance("RUB")
);
 RefundResponse refundResponse = client.refundBill(paidBillId, refundId, amount);
~~~

<ul class="nestedList url">
    <li><h3>URL <span>https://api.qiwi.com/partner/bill/v1/bills/{billId}/refunds/{refundId}</span></h3>
        <ul>
        <strong>Параметры:</strong>
             <li><strong>billId</strong> - уникальный идентификатор счета в системе мерчанта.</li>
             <li><strong>refundId</strong> - уникальный идентификатор возврата в системе мерчанта.</li>
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
    "amount": {
      "value": 50.50,
      "currency": "RUB"
    },
    "datetime": "2018-03-01T16:06:57+03",
    "refundId": "1",
    "status": "PARTIAL"
}
~~~

>Пример тела ответа при ошибке

~~~json
{
	"serviceName": "invoicing",
	"errorCode": "refund.incorrect.amount",
	"description": "Неверная сумма возврата",
	"userMessage": "Неверная сумма возврата",
	"datetime": "2005-08-09T18:31:42+03:00",
	"traceId" : ""
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
datetime|String|Если ответ содержит ошибку: Системная дата обработки запроса
refundId|String|Уникальный идентификатор возврата в системе мерчанта
amount.value|Number|Сумма счета, округленная до 2 знаков после запятой в меньшую сторону
amount.currency	|String|Идентификатор валюты счета (Alpha-3 ISO 4217 код)
status	|String|[Статус возврата](#status_refund)
datetime|Date| Системная дата проведения возврата. Формат даты:<br>`ГГГГ-ММ-ДДTчч:мм:сс+hh`

## 6. Статус возврата {#refund-status}

Метод запрашивает статус возврата.

<h3 class="request method">Запрос → GET</h3>

~~~javascript
const billId = '893794793973';
const refundId = '899343443';

qiwiApi.getRefundInfo(billId, refundId).then( data => {
    //do with data
});
~~~

~~~shell
curl https://api.qiwi.com/partner/bill/v1/893794793973/refund/899343443
-H 'Accept: application/json' 
-H 'Content-Type: application/json' 
-H 'Authorization: Bearer eyJ2ZXJzaW9uIjoicmVzdF92MyIsImRhdGEiOnsibWVyY2hhbnRfaWQiOjIwNDIsImFwaV91c2VyX2lkIjo1NjYwMzk3Miwic2VjcmV0IjoiQjIwODlDNkI5Q0NDNTdCNDQzNGHJK43JFJDK595FJFJMjlCRkFFRDM5OE***********************'
~~~

~~~php
<?php

$billId = '893794793973';
$refundId = '899343443';

/** @var \Qiwi\Api\BillPayments $billPayments */
$response = $billPayments->getRefundInfo($billId, $refundId);

print_r($response);

?>
~~~

~~~java
String billId = "fcb40a23-6733-4cf3-bacf-8e425fd1fc71";
String refundId = '3444e8ca-cf68-4dbd-92ee-f68c4bf8f29b';
 RefundResponse response = client.getRefundInfo(paidBillId, refundId);
~~~

<ul class="nestedList url">
    <li><h3>URL <span>https://api.qiwi.com/partner/bill/v1/bills/{billId}/refunds/{refundId}</span></h3>
        <ul>
        <strong>Параметры:</strong>
             <li><strong>billId</strong> - уникальный идентификатор счета в системе мерчанта.</li>
             <li><strong>refundId</strong> - уникальный идентификатор возврата в системе мерчанта.</li>
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
    "amount": {
      "value": 50.50,
      "currency": "RUB"
    },
    "datetime": "2018-03-01T16:06:57+03",
    "refundId": "1",
    "status": "PARTIAL"
}
~~~

>Пример тела ответа при ошибке

~~~json
{
	"serviceName": "invoicing",
	"errorCode": "refund.incorrect.amount",
	"description": "Неверная сумма возврата",
	"userMessage": "Неверная сумма возврата",
	"datetime": "2005-08-09T18:31:42+03:00",
	"traceId" : ""
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
datetime|String|Если ответ содержит ошибку: Системная дата обработки запроса
refundId|String|Уникальный идентификатор возврата в системе мерчанта
amount.value|Number|Сумма счета, округленная до 2 знаков после запятой в меньшую сторону
amount.currency |String|Идентификатор валюты счета (Alpha-3 ISO 4217 код)
status  |String|[Статус возврата](#status_refund)
datetime|Date| Системная дата проведения возврата. Формат даты:<br>`ГГГГ-ММ-ДДTчч:мм:сс+hh`

### Статусы оплаты счетов {#status}

Статус|Описание|Финальный
------|--------|---------
WAITING | Счет выставлен, ожидает оплаты| -
PAID|Счет оплачен|+
REJECTED|Счет отклонен|+
EXPIRED	|Время жизни счета истекло. Счет не оплачен|+

### Статусы операции возврата {#status_refund}

Статус|Описание|Финальный
------|--------|---------
PARTIAL | Частичный возврат суммы| -
FULL| Полный возврат суммы|+

## Рекомендации по оформлению {#design}

Данные рекомендации помогут вашим пользователям быстрее сориентироваться на странице выбора способов оплаты.

* Если платежный протокол QIWI — один из способов оплаты на вашем сайте, то мы рекомендуем <b>выводить иконки всех способов оплаты</b>, доступных на форме:

 ![Operation Flow](/images/var_1.png)

  Допустимо отображение QIWI Кассы текстом, без логотипа. Иконки способов оплаты выводятся:

 ![Operation Flow](/images/var_2.png)

* Если платежный протокол QIWI Кассы — единственный способ оплаты на вашем сайте, то упоминание QIWI Кассы в этом случае необязательно.  Иконки способов оплаты, доступных на форме, в этом случае надо размещать под кнопкой оплаты:

 ![Operation Flow](/images/var_3.png)

## Логотипы для скачивания {#logos}

 <a href="/images/QIWI_Kassa.svg" download>Скачать логотип QIWI Кассы</a>

 <a href="/images/icons.svg" download>Скачать иконки способов оплаты</a>

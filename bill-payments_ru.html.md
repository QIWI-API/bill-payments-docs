---
title: QIWI Bill Payments REST API 1.0.0

search: true

metatitle: QIWI Bill Payments REST API 1.0.0

metadescription: QIWI Bill Payments API открывает доступ к операциям со счетами на оплату из вашего приложения. Счет - универсальная заявка на оплату. Пользователь может оплатить его с помощью любого из доступных способов оплат, пока не истекло время жизни счета. В API поддерживаются операции выставления и отмены счетов, возврата средств по счетам, а также проверки статуса выполнения операций.

services:
 - <a href='#'>Swagger</a>  |  <a href='#'>Qiwi Demo</a>


toc_footers:
 - <a href='/'>На главную</a>
 - <a href='mailto:api_help@qiwi.com'>Обратная связь</a>
 - <a href='http://pullapi-test.qiwi.com'>Песочница</a>

includes:
 - bill-payments/bill-payments-api_ru
 - bill-payments/webform_ru
 - bill-payments/notification_ru
 - bill-payments/responses_ru
 - bill-payments/results_ru
 - bill-payments/statuses_ru
 - bill-payments/errors_ru

---

# Универсальный платежный протокол

# Введение {#introduction}

###### Последнее обновление: 2017-07-22 | [Редактировать на GitHub](https://github.com/QIWI-API/bill-payments-docs/blob/master/bill-payments_ru.html.md)

QIWI Bill Payments API открывает доступ к операциям со счетами на оплату из вашего приложения. Счет - универсальная заявка на оплату. Пользователь может оплатить его с помощью любого из доступных способов оплат, пока не истекло время жизни счета. Поддерживаются следующие операции:

* [открытие платежной страницы](#http)
* [отмена неоплаченных счетов](#cancel)
* [проверка статуса оплаты счета](#invoice-status)
* [получение уведомлений](#notification)

## Способы оплаты {#payment_methods}

* Баланса QIWI Кошелька
* Карты VISA/MASTERCARD/МИР

**Данное API можно использовать только после [регистрации и подключения](https://kassa.qiwi.com).**


## Параметры Авторизации {#auth_param}

<ul class="nestedList params">
    <li><h3>Авторизация и работа с формами</h3>
    </li>
</ul>

Параметр|Описание|Тип
 ---------|--------|---
 PUBLIC_KEY | Публичный ключ для авторизации провайдера на вебформе | String
 SECRET_KEY | Секретный ключ для авторизации в API| String


<aside class="notice">
Получить служебные данные можно на партнерском сайте <a href='http://kassa.qiwi.com'>kassa.qiwi.com</a>.
</aside>



                               	

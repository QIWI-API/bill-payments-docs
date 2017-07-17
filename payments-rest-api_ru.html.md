---
title: QIWI Bill Payments REST API 1.0.0

search: true

metatitle: QIWI Bill Payments REST API 1.0.0

metadescription: QIWI Bill Payments API открывает доступ к операциям со счетами на оплату из вашего приложения. Счет - универсальная заявка на оплату. Пользователь может оплатить его с помощью любого из доступных способов оплат, пока не истекло время жизни счета. В API поддерживаются операции выставления и отмены счетов, возврата средств по счетам, а также проверки статуса выполнения операций.

services:
 - <a href='#'>Swagger</a>  |  <a href='#'>Qiwi Demo</a>


toc_footers:
 - <a href='/'>На главную</a>
 - <a href='http://pullapi-test.qiwi.com'>Песочница</a>

includes:
 - bill-payments-api_ru
 - webform_ru
 - checkout_ru
 - notification_ru
 - responses_ru
 - statuses_ru
 - errors_ru

---

# Введение {#introduction}

###### Последнее обновление: 2017-05-31 | [Редактировать на GitHub](https://github.com/QIWI-API/bill-payments-rest-api-docs/blob/master/payments-rest-api_ru.html.md)

IWI Bill Payments API открывает доступ к операциям со счетами на оплату из вашего приложения. Счет - универсальная заявка на оплату. Пользователь может оплатить его с помощью любого из доступных способов оплат, пока не истекло время жизни счета. Поддерживаются следующие операции:

* открытие платежной страницы
* отмена неоплаченных счетов
* возврат средств по оплаченным счетам (пользователь отказался от услуги)
* проверка статуса выполнения операции
* оплата счета на Платежной форме QIWI

## Способы оплаты {#payment_methods}

Пользователи могут оплачивать счета
* с карты Visa/MasterCard
* с баланса своего QIWI Кошелька
* с баланса мобильного телефона.

Для оплата пользователям доступна    
* в интерфейсе [платежной формы QIWI](https://oplata.qiwi.com)
* на сайте [qiwi.com](https://qiwi.com)
* в мобильных приложений QIWI    
* в QIWI Терминалах.

**Данное API можно использовать только после [регистрации и подключения](https://kassa.qiwi.com).**


## Служебные данные {#auth_param}

<ul class="nestedList params">
    <li><h3>Авторизация и работа с формами</h3><span>Данные могут быть получены на сайте <strong>kassa.qiwi.com</strong></span>
    </li>
</ul>

Параметр|Описание|Тип
 ---------|--------|---
 public_key | Открытый ключ для авторизации провайдера в платежной форме | String
 secret_key | Секретный ключ для авторизации в API| String


<aside class="notice">
Получить служебные данные можно на партнерском сайте <a href='http://kassa.qiwi.com'>kassa.qiwi.com</a>.

<ul class="nestedList notice_image">
    <li><h3>Подробнее</h3>
        <ul>
             <li><img src="images/pull_rest_auth.png" /></li>
        </ul>
    </li>
</ul>

</aside>



                               	

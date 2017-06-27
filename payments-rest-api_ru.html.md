---
title: QIWI Bill Payments REST API 1.0.0

search: true

metatitle: QIWI Bill Payments REST API 1.0.0

metadescription: QIWI Bill Payments API открывает доступ к операциям со счетами в Visa QIWI Wallet из вашего приложения. Поддерживаются операции выставления и отмены счетов, возврата средств по счетам, а также проверки статуса выполнения операций.

services:
 - <a href='#'>Swagger</a>  |  <a href='#'>Qiwi Demo</a>


toc_footers:
 - <a href='/'>На главную</a>
 - <a href='http://pullapi-test.qiwi.com'>Песочница</a>

includes:
 - payments_invoice_api_ru
 - webform_ru
 - checkout_ru
 - notification_ru
 - responses_ru

---

# Введение

###### Последнее обновление: 2017-05-31 | [Редактировать на GitHub](https://github.com/QIWI-API/bill-payments-rest-api-docs/blob/master/payments-rest-api_ru.html.md)

QIWI Bill Payments API открывает доступ к операциям со счетами в Visa QIWI Wallet из вашего приложения. Поддерживаются следующие операции:

* выставление счетов
* отмена неоплаченных счетов
* возврат средств по оплаченным счетам (пользователь отказался от услуги)
* проверка статуса выполнения операции
* оплата счета на Платежной форме QIWI

## Способы оплаты

* Пользователи могут оплачивать счета Visa QIWI Wallet
  * в интерфейсах [платежной формы QIWI](https://bill.qiwi.com)
  * на сайте [qiwi.com](https://qiwi.com)
  * в мобильных приложений QIWI
    * с баланса своего Visa QIWI Wallet
    * с баланса мобильного телефона или с любой карты Visa/MasterCard.
  * также доступна оплата счетов наличными в QIWI Терминалах.

**Данное API можно использовать только после [регистрации и подключения](https://kassa.qiwi.com).**

## Способы интеграции

Для работы с QIWI Bill Payments API доступны следующие способы:

* [Bill Payments REST API](#pull_rest_api) - Полнофункциональное RESTful API для всех операций со счетами.

* [Веб-форма выставления счета](#http) - Не требует сложной реализации и дополнительной переадресации пользователя на Платежную форму. Ограничена по функциональности - поддерживает только выставление счета. Вызов веб-формы авторизуется по цифровой подписи запроса или по ключу авторизации. 

## Служебные данные {#auth_param}

<ul class="nestedList params">
    <li><h3>Авторизация и работа с формами</h3><span>Данные могут быть получены на сайте <strong>kassa.qiwi.com</strong></span>
    </li>
</ul>

Параметр|Описание|Тип
 ---------|--------|---
 api_id | Идентификатор для авторизации провайдера в API | Integer
 api_secret | Секретный ключ для авторизации в API| String
 prv_id | Числовой идентификатор провайдера (идентификатор сайта) | Integer
 secret_key | Base64 хешированный набор параметров вида: `prv_id|api_id|md5(api_secret)`| String



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



                               	
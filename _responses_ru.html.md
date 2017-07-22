# Ответы {#responses}

###### Последнее обновление: 2016-07-22 | [Редактировать на GitHub](https://github.com/QIWI-API/bill-payments-rest-api-docs/blob/master/_responses_ru.html.md)

## Операции со счетами {#response_bill}

Возвращается объект с результатом выполнения операции.

<aside class="notice">
В ответ на два последовательных запроса с одинаковыми значениями параметров <i>{bill_id}</i> и <i>amount</i> будет возвращаться один и тот же код результата выполнения операции.
</aside>
	 
~~~json
{
 "response": {
  "result_code": "SUCCESS",
  "bill": {
    "bill_id": "BILL-1",
    "amount": "10.00",
    "currency": "RUB",
    "status": "waiting",
    "user": {
       "phone": "+79031234567"
    },
    "extras": {
       "name1": ""
    }
    "comment": "test",
    "create_datetime": "2017-06-28T21:57:45.540Z",
    "lifetime_datetime":"2017-06-28T21:57:45.540Z",
    "pay_url": "https://oplata.qiwi.com/?uid=87213878361287"
  }
}}
~~~

Параметр|Тип|Описание
--------|---|--------
result_code|String|[Результат запроса](#results)
bill_id|String|Уникальный идентификатор счета в системе провайдера
amount|String|Сумма счета, округленная до 2 или 3 знаков после запятой. Способ округления зависит от валюты.
currency	|String|Идентификатор валюты (Alpha-3 ISO 4217 код)
status	|String|Текущий [статус счета](#status)
extras|Object|Объект строковых дополнительных параметров, переданных партнером
user|Object|Идентификаторы пользователя. Возможные опции: email, phone, user_id
comment|String|Комментарий к счету
create_datetime|Date| Системная дата создания счета
lifetime_datetime|Date|Срок действия созданной формы для оплаты
pay_url|String|Ссылка на созданную платежную форму


<a href="#" onclick="history.back(); return false">Назад</a>

## Операции с возвратами {#response_refund}

Возвращается объект с результатом выполнения операции возврата.

~~~json
{
 "response": {
  "result_code": "SUCCESS",
  "refund": {
    "refund_id": "122swbill",
    "amount": "10.0",
    "status": "processing"
  }
}}
~~~

Параметр|Тип|Описание
--------|---|--------
result_code|String|[Результат ответа](#results)
refund_id|String|Уникальный идентификатор операции возврата счета в системе провайдера
amount|String|Сумма к возврату. Положительное число, округленное до 2 или 3 знаков после запятой. Способ округления зависит от валюты.
status	|String|Текущий [статус операции возврата](#status_refund)

<a href="#" onclick="history.back(); return false">Назад</a>

## Ответ в случае ошибки операции {#response_error}

~~~json
{ 
	"result_code": "AUTH_FAILED",
        "error_code": "CORE__150",
        "description": "Authorization failed",
        "datetime":"2017-06-28T21:57:45.540Z"
}

~~~

Параметр|Тип|Описание
--------|---|--------
result_code|String|[Код результата](#results)
error_code|String|[Код ошибки](#err-codes)
description|String|Описание ошибки
datetime|String|Системная дата обработки запроса

<a href="#" onclick="history.back(); return false">Назад</a>



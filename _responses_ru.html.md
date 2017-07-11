# Ответы {#responses}

###### Последнее обновление: 2016-10-09 | [Редактировать на GitHub](https://github.com/QIWI-API/bill-payments-rest-api-docs/blob/master/_responses_ru.html.md)

## Операции со счетами {#response_bill}

Возвращается объект с результатом выполнения операции.

<aside class="notice">
В ответ на два последовательных запроса с одинаковыми значениями параметров <i>{bill_id}</i> и <i>amount</i> будет возвращаться один и тот же код результата выполнения операции.
</aside>
	 
~~~json
{
 "response": {
  "result_code": 0,
  "bill": {
    "bill_id": "BILL-1",
    "amount": "10.00",
    "originAmount": "10.00", 
    "ccy": "RUB",
    "originCcy": "RUB",
    "status": "waiting",
    "error": 0,
    "user": "tel:+79031234567",
    "comment": "test"
  }
}}
~~~

Параметр|Тип|Описание
--------|---|--------
result_code|Integer|[Код результата](#errors)
bill_id|String|Уникальный идентификатор счета в системе провайдера
amount|String|Сумма счета, округленная до 2 или 3 знаков после запятой. Способ округления зависит от валюты.
originAmount|String|Сумма счета в исходной валюте счета (см. параметр `originCcy`), округленная до 2 или 3 знаков после запятой. Способ округления зависит от валюты.
ccy	|String|Идентификатор валюты (Alpha-3 ISO 4217 код)
originCcy|String|Идентификатор валюты выставленного счета (Alpha-3 ISO 4217 код)
status	|String|Текущий [статус счета](#status)
error	|Integer|Код ошибки
user|String|Идентификатор кошелька пользователя, которому выставлен счет (номер телефона в международном формате с префиксом "tel:")
comment|String|Комментарий к счету

<a href="#" onclick="history.back(); return false">Назад</a>

## Операции с возвратами {#response_refund}

Возвращается объект с результатом выполнения операции возврата.

~~~json
{
 "response": {
  "result_code": 0,
  "refund": {
    "refund_id": "122swbill",
    "amount": "10.0",
    "status": "processing",
    "error": 0,
    "user": "tel:+79161231212"
  }
}}
~~~

Параметр|Тип|Описание
--------|---|--------
result_code|Integer|[Код результата](#errors)
refund_id|String|Уникальный идентификатор операции возврата счета в системе провайдера
amount|String|Сумма к возврату. Положительное число, округленное до 2 или 3 знаков после запятой. Способ округления зависит от валюты.
status	|String|Текущий [статус операции возврата](#status_refund)
error	|Integer|Код ошибки при проведении возврата платежа. В случае если сумма, переданная в запросе, превышает сумму самого счета либо сумму счета, оставшуюся после предыдущих возвратов, в ответе будет возвращен код ошибки 242.
user|String|Идентификатор кошелька пользователя, которому выставлен счет. Представляет собой номер телефона пользователя в международном формате с префиксом "tel:"

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
result_code|String| BAD_REQUEST, AUTH_FAILED, FATAL_ERROR, TEMPORARY_ERROR, SUCCESS 
error_code|String|[Код результата](#errors)
description|String|Описание ошибки

<a href="#" onclick="history.back(); return false">Назад</a>


<<<<<<< HEAD
## Статусы оплаты счетов {#status}

Статус|Описание|Финальный
------|--------|---------
waiting | Счет выставлен, ожидает оплаты| -
paid|Счет оплачен|+
rejected|Счет отклонен|+
unpaid|Ошибка при проведении оплаты. Счет не оплачен|+
expired	|Время жизни счета истекло. Счет не оплачен|+

<a href="#" onclick="history.back(); return false">Назад</a>

## Статусы операции возврата {#status_refund}

Статус|Описание|Финальный
------|--------|---------
processing | Платеж в проведении| -
success|Платеж проведен|+
fail|Платеж неуспешен|+

<a href="#" onclick="history.back(); return false">Назад</a>

# Список ошибок {#errors}

Код| Описание 
---|----------
TODO

* Признак означает, что при повторном запросе результат не изменится (ошибка не временная, проанализируйте данные запроса или обратитесь в Support).

<a href="#" onclick="history.back(); return false">Назад</a>
=======
>>>>>>> stable

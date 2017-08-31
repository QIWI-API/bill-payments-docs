# Список ошибок {#err-codes}

###### Последнее обновление: 2017-07-11 | [Редактировать на GitHub](https://github.com/QIWI-API/bill-payments-docs/blob/master/_errors_ru.html.md)

Код| Описание |Fatal*
---|----------|------
error.code.internal.invoicing.core.error|Ошибка получения данных от сервиса invoicing, попробуйте позже|-
error.code.internal.invoicing.api.error|Внутренняя ошибка, попробуйте позже|-
error.code.api.invoice.not.found|Счёт не найден|+
error.code.auth.unauthorized|Неверные аутентификационные данные|+
error.code.default|Произошла ошибка, попробуйте позже|+
error.code.internal.error|Внутренняя ошибка|-
error.code.http.argument.type.mismatch|Неверный запрос|+
error.code.http.code.conversion.failed|Неверные параметры, измените запрос согласно документации API|+
error.code.http.media.type.not.acceptable|Неверный тип данных|+
error.code.http.media.type.not.supported|Неподдерживаемый тип данных|+
error.code.http.message.conversion.failed|Неверный запрос|+
error.code.http.method.not.supported|HTTP метод не поддерживается|+
error.code.http.missing.request.parameter|Неверный запрос: отсутствуют необходимые параметры|+
error.code.http.url.not.found|Ресурс не найден|+
error.code.validation.error|Неверные параметры, измените запрос согласно документации API|+



* Признак означает, что при повторном запросе результат не изменится (ошибка не временная, проанализируйте данные запроса или обратитесь в Support).

<a href="#" onclick="history.back(); return false">Назад</a>

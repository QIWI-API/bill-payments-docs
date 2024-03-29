# QIWI Bill Payments REST API {#bill-payments-api}

###### [Edit on GitHub](https://github.com/QIWI-API/bill-payments-docs/blob/master/_bill-payments-api_en.html.md)

## Authorization {#auth}

The merchant's requests are authorized via its API key.<br>

~~~shell
header "Authorization: Bearer MjMyNDQxMjM6NDUzRmRnZDQ0Mw=="
~~~

Parameter|Description|Type
 ---------|--------|---
 SECRET_KEY | API key for authorization| String

<aside class="notice">
Put the API key to <b>Authorization</b> header using this format: "Bearer <a href="#auth">SECRET_KEY</a>"
</aside>

## Checking the Invoice Status {#invoice-status}

Use this method to get current invoice payment status.

<h3 class="request method">Request → GET</h3>

~~~http
GET /api/v3/bills/BILL-1 HTTP/1.1
Accept: application/json
Authorization: Bearer eyJ2ZXJzaW9uIjoicmVzdF92MyIsImRhdGEiOnsibWVyY2hhbnRfaWQiOjIwNDIsImFwaV91c2VyX2lkIjo1NjYwMzk3Miwic2VjcmV0IjoiQjIwODlDNkI5Q0NDNTdCNDQzNGHJK43JFJDK595FJFJMjlCRkFFRDM5OEZBOTBENDBEMiJ9fQ==
Host: api.qiwi.com
Content-Type: application/x-www-form-urlencoded; charset=utf-8

~~~

<ul class="nestedList url">
    <li><h3>URL <span>https://api.qiwi.com/api/v3/bills/<a>bill_id</a></span></h3>
        <ul>
        <strong>Parameter is in the URL pathname.</strong>
             <li><strong>bill_id</strong> - unique invoice identifier generated by the merchant. Uniqueness means that the identifier must not coincide with any of previously issued invoices of the merchant</li>
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

<h3 class="request">Response ←</h3>

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
HTTP/1.1 500 Internal Server Error
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

Parameter|Type|Description
--------|---|--------
result_code|String|[Result of the request](#results)
error_code|String|If the response is with error: [Error code](#err-codes)
description|String|If the response is with error: Error description
datetime|String|If the response is with error: System date of the request processing
bill_id|String|Unique invoice identifier in the merchant's system
site_id|Number|Merchant's site identifier in QIWI Kassa
amount|String|The invoice amount. The number is rounded down with two decimal places.
currency|String|Currency identifier of the invoice (Alpha-3 ISO 4217 code)
status|String|Current [invoice status](#status)
extras|Object|Additional invoice data provided by the merchant
user|Object|User identifiers. Possible options:<br>`email` - the user’s e-mail,<br>`phone` - the user’s phone,<br>`user_id` - the user's identifier in the merchant's system
comment|String|Comment to the invoice
creation_datetime|Date|System data of the invoice creation
expiration_datetime|Date|Expiration date of the pay form link (invoice payment's due date)
pay_url|String|Pay form link


## Cancelling the Invoice {#cancel}

Use this method to cancel the unpaid invoice.

<h3 class="request method">Request → PATCH</h3>


~~~http
PATCH /api/v3/bills/BILL-1/REJECT HTTP/1.1
Accept: application/json
Authorization: Bearer eyJ2ZXJzaW9uIjoicmVzdF92MyIsImRhdGEiOnsibWVyY2hhbnRfaWQiOjIwNDIsImFwaV91c2VyX2lkIjo1NjYwMzk3Miwic2VjcmV0IjoiQjIwODlDNkI5Q0NDNTdCNDQzNGHJK43JFJDK595FJFJMjlCRkFFRDM5OEZBOTBENDBEMiJ9fQ==
Host: api.qiwi.com
Content-Type: application/x-www-form-urlencoded; charset=utf-8
~~~

<ul class="nestedList url">
    <li><h3>URL <span>https://api.qiwi.com/api/v3/bills/<a>bill_id</a>/reject</span></h3>
        <ul>
        <strong>Parameter is in the URL pathname.</strong>
             <li><strong>bill_id</strong> - invoice identifier.</li>
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

<h3 class="request">Response ←</h3>

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

Parameter|Type|Description
--------|---|--------
result_code|String|[Result of the request](#results)
error_code|String|If the response is with error: [Error code](#err-codes)
description|String|If the response is with error: Error description
datetime|String|If the response is with error: System date of the request processing
bill_id|String|Unique invoice identifier in the merchant's system
site_id|Number|Merchant's site identifier in QIWI Kassa
amount|String|The invoice amount. The number is rounded down with two decimal places.
currency|String|Currency identifier of the invoice (Alpha-3 ISO 4217 code)
status|String|Current [invoice status](#status)
extras|Object|Additional string data provided by the merchant
user|Object|User identifiers. Possible options:<br>`email` - the user’s e-mail,<br>`phone` - the user’s phone,<br>`user_id` - the user's identifier in the merchant's system
comment|String|Comment to the invoice
creation_datetime|Date|System data of the invoice creation
expiration_datetime|Date|Expiration date of the pay form link
pay_url|String|Pay form link

## Operation Results {#results}

###### [Edit on Github](https://github.com/QIWI-API/bill-payments-docs/blob/master/_bill-payments-api_en.html.md)

Code| Description |
---|----------|
AUTH_FAILED|Request authorization error
BAD_REQUEST|Wrong request syntax
GENERAL_ERROR|Technical error
RETRYABLE_ERROR|Temporary error. Repeat the request once again
SUCCESS|Successful result

## Operation Statuses {#statuses}

###### [Edit on Github](https://github.com/QIWI-API/bill-payments-docs/blob/master/_bill-payments-api_en.html.md)

### Invoice Status {#status}

Status|Description|Final
------|--------|---------
waiting | Invoice issued, pending payment| N
paid|Invoice has been paid|Y
rejected|Invoice has been rejected|Y
unpaid|Payment processing error. Invoice has not been paid|Y
expired |Invoice expired. Invoice has not been paid|Y

### Статусы операции возврата {#status_refund}

Status|Description|Final
------|--------|---------
processing | Payment refund is pending| N
success|Payment refund is successful|Y
fail|Payment refund is unsuccessful|Y

## Error Codes {#err-codes}

###### [Edit on Github](https://github.com/QIWI-API/bill-payments-docs/blob/master/_bill-payments-api_en.html.md)

Code| Description
---|----------
error.code.internal.invoicing.core.error|Error in receiving data from invoicing service. Repeat later
error.code.internal.invoicing.api.error|Internal error. Repeat later
error.code.api.invoice.not.found|Invoice not found
error.code.auth.unauthorized|Wrong authorization data
error.code.default|Error occured. Repeat later
error.code.internal.error|Internal error
error.code.http.argument.type.mismatch|Wrong request
error.code.http.code.conversion.failed|Wrong parameter in the request. Make сorrections according to API documentation
error.code.http.media.type.not.acceptable|Wrong data type
error.code.http.media.type.not.supported|Insupported data type
error.code.http.message.conversion.failed|Wrong request
error.code.http.method.not.supported|HTTP method unsupported
error.code.http.missing.request.parameter|Wrong request: required parameters absent
error.code.http.url.not.found|Resource not found
error.code.validation.error|Wrong parameter in the request. Make сorrections according to API documentation

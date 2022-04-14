# Invoice Payment Notifications {#notification}

###### Last update: 2017-11-14 | [Edit on GitHub](https://github.com/QIWI-API/bill-payments-rest-api-docs/blob/master/_notification_en.html.md)

Notification is a POST-request (callback). The request's body contains JSON-serialized invoice data encoded by UTF-8.

<h3 class="request method">Request → POST</h3>

~~~http
POST /qiwi-notify.php HTTP/1.1
Accept: application/json
Content-type: application/json
X-Api-Signature-SHA256: J4WNfNZd***V5mv2w=
Host: example.com

{
  "bill": {
    "bill_id": "a475c739-0561-4a23-9d18-a96934a7d690",
    "site_id":270304,
    "amount": 1,
    "currency": "RUB",
    "status":  {
      "value" : "PAID",
      "update_datetime" : "2017-12-27T16:01:00Z" },
    "user": {
      "phone": "79261234567",
      "user_id" : "dsfc2recd123sdadx3dscfewcr234esdcf23",
      "email" : "example@gmail.com"
    },
    "creation_datetime": "2017-08-17T09:56:02.241Z",
    "expiration_datetime": "2017-12-27T16:01:00Z",
    "version" : "3.0"
  }
}
~~~

~~~http
HTTP/1.1 200 OK
Content-Type: application/json
{
 "error": 0
}
~~~

<ul class="nestedList url">
    <li><h3>URL</h3>
    </li>
</ul>

<aside class="notice">
Specify notifications server URL in <a href="https://kassa.qiwi.com/">kassa.qiwi.com</a> (login is needed)
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
    <li><h3>Parameters</h3><span>Invoice parameters are in the POST-request's body.</span>
    </li>
</ul>

Parameter|Description|Type
---------|--------|---
bill_id|Invoice identifier in the merchant's system|String(30)
site_id|Merchant's site identifier in QIWI Kassa| Number
amount | The invoice amount. The number is rounded down with two decimal places. | Number(6.2)
currency | Currency identifier of the invoice (Alpha-3 ISO 4217 code)| String(3)
status | Invoice status data | Object
status.value | String representation of the status|String
status.update_datetime|Status refresh date|URL-encoded<br>`ГГГГ-ММ-ДДTЧЧ:ММ:ССZ`
user | User data|Object
user.phone | The user’s phone (if specified in the invoice)|String
user.email|The user's e-mail  (if specified in the invoice)|String
user.user_id|The user's identifier in the merchant's system (if specified in the invoice)| String
creation_datetime | System data of the invoice creation | URL-encoded<br>`ГГГГ-ММ-ДДTЧЧ:ММ:ССZ`
expiration_datetime |Invoice payment's due date | URL-encoded<br>`ГГГГ-ММ-ДДTЧЧ:ММ:ССZ`
comment | Comment to the invoice | String(255)
extras | Additional invoice data provided by the merchant (if specified in the invoice)| Object
version | Notification service version | String

<h3 class="request method">Response → POST</h3>

Response should be in JSON.

### Headers

*  `Content-type: application/json`

~~~http
HTTP/1.1 200 OK
Content-Type: application/json

{
 "error": 0
}
~~~

<aside class="warning">
Notification processing result code should be returned in response. The result code should be in <i>error</i> parameter.<br>
Any response with result code other than 0 ("Success") and/or HTTP status code other than 200 (OK) will be treated as a temporary error.<br>
The QIWI Wallet server repeates the notification request 36 times separated by 15 minutes each.<br>Then, it continues for 15 times separated by 60 minutes each.<br>In total, QIWI Wallet server makes 51 attempts within the next 24 hours.
</aside>

<aside class="notice">
<ul>
<li>We recommend that the result codes returned by the merchant be in accordance with <a href="#notify_codes">Notification codes table</a>.</li>
<li>To receive notifications merchant must whitelist following IP subnets connected by 80, 443 ports exclusively:
<ul>
<li>91.232.230.0/23</li>
<li>79.142.16.0/20</li>
</ul>
</li>
</ul>
</aside>


## Authorization on Merchant's Server {#notifications_auth}

Authorization by signature is used. The HTTP header `X-Api-Signature-SHA256` with signature is added to the POST-request. Signature is calculated as HMAC algorithm with SHA256-hash function.

HMAC SHA256-hash is taken on some parameters of the notification with `|` separator plus `secret_key`.

Header example:

`X-Api-Signature-SHA256: J4WNfNZd***V5mv2w=`

* Parameters' separator is `|`.
* Signed are the following parameters placed in alphabetical order and UTF-8 byte-encoded:
   * `amount`
   * `bill_id`
   * `currency`
   * `email` (if present)
   * `phone` (if present)
   * `site_id`
   * `status.value`
   * `user_id` (if present)
* The parameters are placed in alphabetical order and UTF-8 byte-encoded.
* A key for signature is the [SECRET_KEY](#auth).

Signature verification algorithm is as follows:

1. Prepare a string of all parameters values from the notification POST-request sorted in alphabetical order and separated by `|`:

    `{parameter1}|{parameter2}|…`

    where `{parameter1}` is the value of the notification parameter. All values should be treated as strings.

2. Transform obtained string and signature key into bytes encoded in UTF-8.
3. Apply HMAC-SHA256 function:

    `hash = HMAС(SHA256, SECRET_KEY_bytes, invoice_parameters_bytes)`
    Where:

    * `SECRET_KEY_bytes` – bytecoded secret key;
    * `invoice_parameters_bytes` – bytecoded POST-request body;
    * `hash` – hash-function result.

4. Transform HMAC-hash value into bytes with UTF-8 and Base64-encode it.
5. Compare `X-Api-Signature-SHA256` header's value with the result of step 4.


## Notification Codes {#notify_codes}

Result code|Description
--------------|--------
0|Success

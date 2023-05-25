# QIWI Pay Form {#http}

###### [Edit on GitHub](https://github.com/QIWI-API/bill-payments-rest-api-docs/blob/master/_webform_en.html.md)

Client receives a pay form with a number of ways to pay for the invoice.

* You may call the web form by two ways:

  * Simple. To identify merchant, a specific key in the form URL is used.

  * With signature. A digital signature in the form URL guarantees that the same merchant issued the invoice.

<h3 class="request method" id="payform_req">REDIRECT → </h3>

<ul class="nestedList url">
    <li><h3>URL <span>https://oplata.qiwi.com/form/create</span></h3></li>
</ul>

~~~http

GET /form/create?public_key=08hvq08yw4fqw&amount=100.0&success_url=http%3A%2F%2Ftest.ru%3F&email=m@ya.ru HTTP/1.1
Host: oplata.qiwi.com
~~~

<ul class="nestedList params">
    <li><h3>Parameters</h3><span>Invoice parameters are specified in the web form URL.</span></li>
</ul>

Parameter|Description|Type|W/signature|Simple|Required
---------|--------|---|---------|---|----
public_key | Merchant identifier key generated in QIWI Kassa|String|+|+|+
bill_id|Invoice identifier|String(30)|+|+|W/signature
amount| Invoice amount rounded down to 2 decimals|Number(6.2)|+|+|W/signature
sign|[Request signature](#http_sign)|String|+|-|+
phone |QIWI Wallet identifier (account) to which the invoice issued (with country code) | String(20)|+|+|-
email | The user's e-mail where invoice payment link will be sent | String|+|+|-
user_id | The user's identifier in the merhcant's system | String|+|+|-
comment | Comment to the invoice|String(255)|+|+|-
extra_\*|Additional invoice data|String(255)|+|+|-
lifetime | Data where the invoice would become inaccessible for payment. If the invoice is not paid until this date, it becomes void with `expired` final status. A payment for the invoice will be impossible then.<br> **Important! Invoice will be automatically expired when 45 days is passed after the invoicing date**|`YYYY-MM-DDThhmm` URL-encoded |+|+|-
success_url|The URL to which the user will be redirected in case of successful creation of QIWI Wallet transaction. May be parameter or anchor. URL must be within merchant's site. **Redirection is performed when user pays by QIWI Wallet account's balance only.**|URL-encoded string|+|+|-
fail_url|The user is redirected to the specified URL when QIWI Wallet transaction creation is unsuccessful. May be parameter or anchor. URL must be within merchant's site. **Redirection is performed when user chooses to pay by QIWI Wallet account's balance only.** |URL-encoded string|+|+|-
pay_source |Default payment method to show first for the client on QIWI Kassa Pay Form. As the Pay form automatically pre-selects the most comfortable way to pay for the user, we do not recommend to use this parameter. Possible values:<br>`qw` – QIWI Wallet account;<br> `mobile` – client’s cell phone account;<br> `card` – a credit/debit card.<br>When specified method is inaccessible, the page contains notice about it and the client can choose another method. |String (predefined)|-|+|-

## Request signature {#http_sign}

Calling invoicing Pay form  with authorization requires a signature of the request (`sign` parameter).

~~~http

GET /form/create?public_key=08hvq08yw4fqw&bill_id=Bill-1&sign=09jvrq09pwh3rq2e2&amount=100.0 HTTP/1.1
Host: oplata.qiwi.com
~~~

<aside class="notice">
To get signature, use <a href='#auth'>SECRET_KEY</a> for Bill Payments REST API.
</aside>

Signature algorithm follows:

1. Get a string with values of all **required** parameters of the [redirect](#payform_req) and `lifetime` parameter (**if exists in the request**) in alphabetical order separated by `|` symbol:

    `Invoice_parameters = "{public_key}|{bill_id}|{amount}|{lifetime}"`

    where `{parameter}` is the value of the corresponding notification's parameter. All parameters are treated as strings.

2.	Calculate HMAC-hash with SHA256-encryption on the obtained string using [SECRET_KEY](#auth) as a key:

    `sign = HMAС(SHA256, SECRET_KEY, Invoice_parameters)`

---
title: QIWI Bill Payments REST API 1.0.0

search: true

metatitle: QIWI Bill Payments REST API 1.0.0

metadescription: QIWI Bill Payments API opens a way to operate with invoices from your service or application. Invoice is the unique request for the payment. The user may pay the invoice with any accessible means till the invoice expired. API supports issuing and cancelling invoices, making refunds and checking operation status.

services:
 - <a href='#'>Swagger</a>  |  <a href='#'>Qiwi Demo</a>


toc_footers:
 - <a href='/'>Home page</a>
 - <a href='mailto:api_help@qiwi.com'>Feedback</a>

includes:
 - bill-payments/webform_en
 - bill-payments/bill-payments-api_en
 - bill-payments/notification_en

---

# Universal Payment Protocol {#introduction}

###### Last update: 2017-10-23 | [Edit on GitHub](https://github.com/QIWI-API/bill-payments-docs/blob/master/bill-payments_en.html.md)

QIWI Bill Payments API opens a way to operations with invoices from your service or application. Invoice is the unique request for the payment. The user may pay the invoice with any accessible means till the invoice expired. API supports the following operations:

* [payment web form opening](#http)
* [cancelling unpaid invoices](#cancel)
* [checking invoice payment status](#invoice-status)
* [receiving notifications](#notification)

## Payment means {#payment_methods}

* QIWI Wallet balance
* Visa/MasterCard/MIR cards

**To use API, complete [registration and approvement of the agreement](https://kassa.qiwi.com).**


## Invoicing Operations Flow {#steps}

![Operation Flow](/images/bill_payments_en.png)

* User submits an order on the merchant’s website or application.

* Merchant redirects the user to [QIWI Kassa Pay Form](#http) link to issue an invoice for the order and to make the user pay for it.

* The user chooses the most convenient way to pay for the invoice on the Pay Form.

* The merchant's service receives [notification](#notification) once the invoice is successfully paid by the user. You need to enable notifications on your [Personal Page](https://kassa.qiwi.com).

* If needed, via [Bill Payments REST API](#bill-payments-api) merchant can:
  * [request current status](#invoice-status) of the invoice,
  * [cancel invoice](#cancel) (if the user has not initiated payment yet).

* When the invoice payment is confirmed, merchant delivers ordered services/goods.

## Developer Tools {#node_sdk}

* [NODE JS SDK](https://github.com/QIWI-API/bill-payments-node-js-sdk) - Node JS package of ready-to-use solutions for server2server integration development to begin accepting payments on your site.

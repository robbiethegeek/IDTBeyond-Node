# API: Package: Node.js NPM

[![NPM](https://nodei.co/npm/idtbeyond.png?downloads=true)](https://nodei.co/npm/idtbeyond/)

The official node package for interacting with the IDT Beyond API.

## Getting started with IDT Beyond

To use this service, sign up for a free account at: [IDT Beyond App Portal](https://app.idtbeyond.com), or read the documentation at [IDT Beyond App Portal](https://app.idtbeyond.com/docs).

## Requirements

- [npm](https://www.npmjs.com/)

## Setup your environment

1. Make sure above requirements are fulfilled. (see site documentation in links above)
2. run `npm install idtbeyond --save` to add this module to your project 

## Testing

Running `npm test` will run the unit tests with jasmine.


## Using the IDTBeyond module
	
> Include the IDTBeyond library: 

```javascript
var idtBeyondApi = require('idtbeyond');
```

> Initialize the variable with your `appID` and `appKey` information:

```javascript
var idtBeyondIatu = idtBeyondApi.initializeIatu(
	{
		appId: '<your-app-id>',
		appKey: '<your-app-key>', 
		termId: '<your-unique-term-id>'
	}
);
```
> Each application has a unique `appID` and `appKey`. You can generate up to 5 keys per application, and they can be regenerated by you at any time. Make sure to use the correct ones, and update your code when new valid keys are generated.

> The calls defined below require that the initialization of the idtBeyond object has been completed.
  

## API Status

This returns the status of the IDT Beyond API.

> The `result` variable inside the then method's callback will contain the promise's result.

### Request:

```javascript
idtBeyondIatu.getStatus().then(function (result) { ... });
```

### Response:

```json
{
  "alive": true,
  "success": true
}
```

## Get all products

This endpoint retrieves a current list of products offered by our IATU API.

> The `products` variable inside the then method's callback will contain the promise's result.

### Request:

```javascript
idtBeyond.getProducts().then(function (products) { ... });
```

### Response:

```json
[
  {
    "display_name": "CLARO SV Range 4 to 100 gg",
    "country": "El Salvador",
    "country_code": "SV",
    "carrier": "Claro",
    "carrier_code": "Claro",
    "mask": "97355678888",
    "denomination": 100,
    "min_denomination": 400,
    "max_denomination": 10000,
    "product_code": "131",
    "commission": "8"
  },
  {
    "display_name": "Digicel SV Range 10 to 100",
    "country": "El Salvador",
    "country_code": "SV",
    "carrier": "Digicel",
    "carrier_code": "Digicel",
    "mask": "97355678888",
    "denomination": 100,
    "min_denomination": 1000,
    "max_denomination": 10000,
    "product_code": "99953073",
    "commission": "5"
  }  
]
``` 

## Local Value

Get the local value of a particular product.

> The `result` variable inside the then method's callback will contain the promise's result.

### Request:

```javascript
idtBeyondIatu.getLocalValue({
	carrierCode: 'Claro',
	countryCode: 'GT',
	amount: 500,
	currencyCode: 'USD'
}).then(function (result) { ... });
```

### Response:

```json
{	
	"carrier_code":"Claro",
	"country_code":"GT",
	"amount":"500",
	"currency_code":"USD",
	"local_currency":"GTQ",
	"local_amount":"3965",
	"divisor":"100",
	"success": true
}
```

#### Parameters

Parameter | Required | Type | Description
--------- | ------- | ----- | -----------
countryCode | true | string | 2-digit code of the country in ISO 3166 format. Ex: CO for Columbia.
carrierCode | true | string | Name of the mobile carrier. Ex: Tigo
currencyCode | true | string | The currency code (ISO 4217) on the product you are querying. Ex: USD
amount | true | integer | The amount of the product you would like to get the value of. Ex: 1000 = $10.00 (in cents).


## Balance

This call allows you get the balance on a specific application. 

> The `result` variable inside the then method's callback will contain the promise's result.

### Request:

```javascript
idtBeyondIatu.getBalance().then(function (results) { ... });
```

### Response:

```json
{
  "balance": 473500,
  "currency_code": "USD",
  "currency_symbol": "$",
  "currency_divisor": 100,
  "success": true
}
```

## Topup

This is how you topup a phone in another country.

> The `result` variable inside the then method's callback will contain the promise's result.

### Request:

```javascript
idtBeyondIatu.postTopup({
	countryCode: 'GT',
	carrierCode: 'Claro',
	amount: 500,
	phoneNumber: '50247280123'
}).then(function (result) { ... });
```

### Response:

```json
{
  "success": true,
  "amount": 500,
  "carrier_code": "Claro",
  "country_code": "GT",
  "client_transaction_id": "trans_id_12345",
  "country_name": "Guatemala",
  "currency_code": "USD",
  "currency_divisor": "100",
  "currency_symbol": "$",
  "from_service_number": "12345678901",
  "local_amount": 3965,
  "local_currency": "GTQ",
  "local_divisor": "100",
  "mobile_number": 50247280123,
  "plan": "Sandbox",
  "terminal_id": "Kiosk 5",
  "transaction_id": "6711555",
  "to_service_number": "01111461511",
  "wholesaler_commission": 100,
  "origin_country": "US"
}
```

> The recommended wait time for a topup response is 60 seconds.

#### Parameters

Parameter | Required | Type | Description
--------- | ------- | ----- | -----------
countryCode | true | string | 2-digit code of the country in ISO 3166 format. (Ex: **GT** for Guatemala).
carrierCode | true | string | Name of the mobile carrier. (Ex: **Claro**).
amount | true | integer | This is the amount, in cents, of the product you are purchasing. (Ex: **500** = $5.00).
phoneNumber | true | string | Mobile number to topup. VALIDATE prior to submission. (Ex: **50312345678**).

> We have three ways of generating a clientTransactionId. Your `clientTransactionId` is automatically generated by default using the `generateClientTransactionId` function, you can manually set one, or the server can generate one once it receives the request.

> The response to the topup will return the "client_transaction_id". So if you use the auto-generated function, capture this response to use for transaction tracking.

## Topups Reports (all)

This will return both the totals report, and the transactions during the dates requested.

> The `topups` variable inside the then method's callback will contain the promise's result.

### Request:

```javascript
idtBeyondIatu.getAllTopups({
	dateFrom: '2015-09-01',
	dateTo: '2015-09-02'
}).then(function (topups) { ... });
```

### Response:

```json
{
   "success":true,
   "totals":{
      "purchases":"0",
      "transfers":"-500",
      "commissions":"100",
      "transactions_count":"1",
      "actual_last_transaction_date":"2015-09-02T15:44:05",
      "query_start_date":"2015-09-01",
      "query_end_date":"2015-09-02"
   },
   "transactions":[
    {
      "mobile_number": "50212345678",
      "client_transaction_id": "wqRbQb6IC12456321",
      "currency_code": "US",
      "currency_symbol": "$",
      "currency_divisor": "100",
      "transaction_date": "2015-09-02T15:44:05",
      "transaction_method": "XFER",
      "transaction_description": "IMTU",
      "transaction_amount": "-500",
      "retail_commission": "100",
      "account_start_balance": "52110",
      "account_end_balance": "51710",
      "product_name": "IMTU - CLARO UNIVERSAL $5",
      "to_service_number": "01234113311",
      "from_service_number": "1233300000"
    }
   ]
}
```

#### Parameters

Parameter | Required | Type | Description
--------- | ------- | ----- | -----------
dateFrom | true | string | YYYY-MM-DD format. Starts at 00:00:00 Eastern Time (ET). (Ex: 2015-12-01).
dateTo | true | string | YYYY-MM-DD format. Ends at 23:59:59 Eastern Time (ET). (Ex: 2015-12-01).

> Remember — All date_to and date_from queries are in Eastern Time (ET), by default.


## Topups Reports (Totals)

This returns a report of topups totals, within a given time period. 

> The `totals` variable inside the then method's callback will contain the promise's result.

### Request:

```javascript
idtBeyondIatu.getAllTopupsTotals({
	dateFrom: '2015-09-01',
	dateTo: '2015-09-02'
}).then(function (topups) { ... });
``` 

### Response:

```json
{
   "success": true,
   "totals":{
      "from_service_number": "133333333",
      "sales": "50000",
      "cost": "40000",
      "commissions": "10000",
      "transactions_count": "100",
      "actual_last_transaction_date": "2015-09-02T23:59:59",
      "query_start_date": "2015-09-01",
      "query_end_date": "2015-09-02",
      "currency_code": "US",
      "currency_symbol": "$",
      "currency_divisor": "100"
   }
}
```

#### Parameters

Parameter | Required | Type | Description
--------- | ------- | ----- | -----------
dateFrom | true | string | YYYY-MM-DD format. Starts at 00:00:00 Eastern Time (ET). (Ex: 2015-12-01).
dateTo | true | string | YYYY-MM-DD format. Ends at 23:59:59 Eastern Time (ET). (Ex: 2015-12-01).

> Remember — All date_to and date_from queries are in Eastern Time (ET), by default.

## Topups Reports (Search)

### Request:

```javascript
idtBeyondIatu.clientTransactionIdSearch({
	dateFrom: '2015-09-02',
	dateTo: '2015-09-04',
	clientTransactionId: 'my-client-trans-id'
}).then(function (result) { ... });
```
> The `result` variable inside the then method's callback will contain
the promise's result.

### Response:

```json
{
   "success":true,
   "transaction_data":[
      {
         "currency_code":"USD",
         "currency_symbol":"$",
         "currency_divisor":"100",
         "client_transaction_id":"transaction246",
         "transaction_date":"2/2/2015 10:57:04 AM",
         "description":"Success",
         "commission":"100",
         "type":"IMTU - CLARO UNIVERSAL $5",
         "end_balance":"56915",
         "amount":"-500",
         "terminal_id":"this term is cool",
         "transaction_status":"Success",
         "to_service_number":"1177741290"
      }
   ]
}
```

#### Parameters

Parameter | Required | Type | Description
--------- | ------- | ----- | -----------
dateFrom | true | string | YYYY-MM-DD format. Starts at 00:00:00 Eastern Time (ET). (Ex: **2015-12-01**).
dateTo | true | string | YYYY-MM-DD format. Ends at 23:59:59 Eastern Time (ET). (Ex: **2015-12-01**).
clientTransactionId | true | string | A unique id you use to track topups (Limit of 20 characters).

> Remember — All dateTo and dateFrom queries are in Eastern Time (ET), by default.

## Topups Reports (Quick Status)

### Request:

```javascript
idtBeyondIatu.toServiceNumberSearch({
	toServiceNumber: "0123456789"
}).then(function (result) { ... });
```
> The `result` variable inside the then method's callback will contain
the promise's result.

### Response:

```json
{
	"success": true,
	"transaction_data": {
		"balance": "0",
		"status": "Redeemed"
	}
}
```
When you submit a transaction, we do some very detailed handling to complete the request. The **toServiceNumber** is a tracking method used to follow the status of this request, including the balance of the transaction handoff to the carrier. Use this endpoint to get a quick update on the status of the request.

#### Parameters

Parameter | Required | Type | Description
--------- | ------- | ----- | -----------
toServiceNumber | true | string | A unique IDT transaction number that gives the status of the transfer of funds from your account to the carrier.

## Topups (Reverse)

Use this method to search for the status of a particular topup transaction, using the unique **clientTransactionId** you supplied in the request, and use the **toServiceNumber**.

Occasionally, a carrier will not be able to successfully complete a topup request. In this case, we will attempt to automatically reverse the transaction for you, and return the money into your account. In the case when this is not possible, you may need to request a reverse on the transaction that has a status of **"status": "Not Redeemed"**.

> The `result` variable inside the then method's callback will contain the promise's result.

### Request:

```javascript
idtBeyondIatu.reverseTopup({
	toServiceNumber: '2712345678',
	clientTransactionId: 'my-client-trans-id'
}).then(function (result) { ... });
``` 

### Response:

```json
{
   "success":true,
   "account_id":"12345678",
   "client_transaction_id":"my-client-trans-id",
   "transaction_id":"111222333",
   "to_service_number":"2712345678",
   "remaining_amount":0,
   "currency_symbol":"$",
   "currency_divisor":"100"
}
```

#### Parameters

Parameter | Required | Type | Description
--------- | ------- | ----- | -----------
clientTransactionId | true | string | A unique id you use to track topups (Limit of 16 characters).
toServiceNumber | true | string | The service number of the financial transaction (moving the money between your balance and the carrier).

## Charges Reports (all)

Returns a list of any credit card charges, wire-transfers, or credit adjustments on your application account.

> The `charges` variable inside the then method's callback will contain the promise's result.

### Request:

```javascript
idtBeyondIatu.getAllCharges({
	dateFrom: '2015-09-01',
	dateTo: '2015-09-02'
}).then(function (charges) { ... });
```

### Response:

```json
[
   {
      "account_id":"12345678",
      "amount":"100000",
      "currency_code":"USD",
      "currency_symbol":"$",
      "currency_divisor":"100",
      "date":"2015-09-02 03:13:09",
      "start_balance":"0",
      "end_balance":"100000",
      "service_number":"03333333333",
      "transaction_id":"111222333",
      "type":"Credit"
   }
]
```

#### Parameters

Parameter | Required | Type | Description
--------- | ------- | ----- | -----------
dateFrom | true | string | YYYY-MM-DD format. Starts at 00:00:00 Eastern Time (ET). (Ex: 2015-12-01).
dateTo | true | string | YYYY-MM-DD format. Ends at 23:59:59 Eastern Time (ET). (Ex: 2015-12-01).

> Remember — All date_to and date_from queries are in Eastern Time (ET), by default.

## Number Validator

This method validates that a phone number has a valid area code and number of digits for the selected country.

> The `result` variable inside the then method's callback will contain the promise's result.

### Request:

```javascript
idtBeyondIatu.validateNumber({
	phoneNumber: '50247123456',
	countryCode: 'GT'
}).then(function (result) { ... });
``` 

### Response:

```json
{
  "country_code": "GT",
  "mobile_number": "50247123456",
  "valid": true,
  "success": true
}
```

#### Parameters

Parameter | Required | Type | Description
--------- | ------- | ----- | -----------
countryCode | true | string | 2-digit code of the country in ISO 3166 format. (Ex: **BR** for Brazil).
phoneNumber | true | string | Mobile number to topup. VALIDATE prior to submission. (Ex: **5521983339000**).

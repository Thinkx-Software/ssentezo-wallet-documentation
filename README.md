## Ssentezo Wallet Version 2.x 🤩 Api Documentation.

<b>Ssentezo Wallet</b> is a platform which enables a business and developers collect or deliver payments to the last mile through digital platforms like mobile money.

Our simple APIs make it easy to collect your payments in a few minutes. Add our web links in your website to collect payments with zero code.

### Ssentezo Wallet Environment/MODE

The Wallet has two types of environments in which it operates in and these are;

-   <b>LIVE</b>
-   <b>SANDBOX</b>

<b>LIVE</b>: In this environment, actual money is credited and debited on/from the msisdn/phone numbers that you specify during Deposit and Withdraw operations.
Real money is moved around so be sure to have tested your application well from <b>SANDBOX</b> before moving into this mode.

In order for you to access this environment Register at https://wallet.ssentezo.com/merchants/register.

#### LIVE mode endpoint: https://wallet.ssentezo.com/api

<b>SANDBOX</b>: This is a testing environment and no actual money is collected and disbursed. You can perform transaction just like in <b>LIVE</b> mode but all these are theoretical. The msisdn/phone numbers that you specify during Deposit and Withdraw operations will neither be credited or debited.

In order for you to access this environment Register at https://dev.wallet.ssentezo.com/merchants/register.

#### SANDBOX mode endpoint: https://dev.wallet.ssentezo.com/api

<br/>

<b>NOTE:</b> Functionality in LIVE and SANDBOX modes is exactly the same. All that changes is the endpoint that is being called, https://wallet.ssentezo.com/api for LIVE mode and https://dev.wallet.ssentezo.com/api for SANDBOX mode.

### Authorization.

The API utilizes Auth Basic authorization method. This requires you to possess valid API credentials which can be generated from the API Access menu in your Wallet Account.

### Limits

Api does not support entry of negative figures, exponential figures e.g e100, or characters while entering the amount to be transacted.

Only transactions between UGX 500 to UGX 7000000 are allowed provided the current wallet balance can meet your charges as defined in your <b>Ssentezo agreement</b>.

Charges are applicable to all transactions as specified in your contract for use of this service.

### Supported Currencies

The wallet currently supports only Uganda Shillings(UGX) for transactions.

| ISO Code | Currency Name    | status    |
| -------- | ---------------- | --------- |
| UGX      | Uganda Shillings | Supported |

### Setting up Authorization

Using your generated API USER and API KEY.  
The wallet follows a standard basic auth to secure the api where the Authorization header is sent encoded to base64.

#### NOTE: All requests must contain authorization header

#### NOTE: All responses are JSON-encoded strings

<br/>

#### Example - How to encode the string using php

```php
//your api user
$apiUser = '';

// your api key
$apiKey = '';

$encodedString = base64_encode($apiUser . ':' . $apiKey);

$header = ['Authorization' => 'Basic '. $encodedString];
$header = ['Content-Type' => "application/form-data"]
```

For other programming languages follow the permitted syntax respectively.

#### Expected HTTP Response Codes.

These are the possible response codes that can be received in the course of the transaction.

| Status Code | Interpretation                              |
| ----------- | ------------------------------------------- |
| 202         | Succeeded Transaction                       |
| 400         | Failed Transaction                          |
| 401         | No Authorization Header                     |
| 403         | Invalid Credentials                         |
| 500         | An error occurred to check the message      |
| 422         | Unprocessable Entity check the request body |

<br/>

#### Transaction Statuses

These are the available transaction statuses that a transaction can have.

| Status        | Description                                                        |
| ------------- | ------------------------------------------------------------------ |
| PENDING       | Transaction still pending. All transactions begin from this state. |
| SUCCEEDED     | Succeeded transaction                                              |
| FAILED        | Failed Transaction                                                 |
| INDETERMINATE | Status not yet determined. This can take up to 48hrs.              |

<br/>

#### API Request Responses

##### Successful Responses

For successful requests, json object response is sent back containing two keys and these are;

-   response
-   data

<b>response</b>: This has a value of <b>OK</b>.
This indicates that the request was processed successfully.

<b>data</b>: This is an object that holds any relevant data that should be returned as per the endpoint called.

Example:

```json
{
    "response": "OK",
    "data": {
        "amount": 1834665,
        "formatted": "1,834,665",
        "currency": "UGX"
    }
}
```

##### Erroneous Responses

For erroneous responses, json object response is sent back containing two keys and these are;

-   response
-   error

<b>response</b>: This has a value of <b>ERROR</b>.
This indicates that there was an error during the course of the request.

<b>error</b>: This is an object that holds any relevant data to the error that just occurred.
It always contains a message property that describes the error and may also have other properties relevant to that error.

Example:

```json
{
    "response": "ERROR",
    "error": {
        "message": "The currency field is required."
    }
}
```

<br/>

### Checking the account balance

-   <b>Endpoint</b>: https://wallet.ssentezo.com/api/acc_balance
-   <b>Method</b>: POST

The endpoint above is accessed via the POST method. It does not require a request body but a valid authorization header is mandatory.

##### Form Data Parameters

-   currency

<h5>Example Request   </h5>

```json
{
    "currency": "UGX"
}
```

<h5>Example success response</h5>

```json
{
    "response": "OK",
    "data": {
        "amount": 1834665,
        "formatted": "1,834,665",
        "currency": "UGX"
    }
}
```

<h5>Example error response</h5>

```json
{
    "response": "ERROR",
    "error": {
        "message": "The currency field is required."
    }
}
```

### MSISDN/Phone number Verification

-   <b>Endpoint</b>: https://wallet.ssentezo.com/api/msisdn-verification
-   <b>Method</b>: POST

##### Form Data Parameters

-   msisdn

#### Sample Request Body.

```json
{
    "msisdn": "256709920188"
}
```

#### Sample success response.

```json
{
    "response": "OK",
    "data": {
        "msisdn": "256709920188",
        "FirstName": "John",
        "Surname": "Doe"
    }
}
```

#### Sample error response

```json
{
    "response": "ERROR",
    "error": {
        "message": "The msisdn field format is invalid."
    }
}
```

```json
{
    "response": "ERROR",
    "error": {
        "message": "Failed to verify the name of the holder of the MSISDN"
    }
}
```

### Withdrawing Funds from your wallet account.

-   <b>Endpoint</b>: https://wallet.ssentezo.com/api/withdraw
-   <b>Method</b>: POST

##### Form Data Parameters

-   externalReference
-   msisdn
-   amount
-   currency
-   reason
-   name (Optional)

#### Example request body

```php
   //Example request body

   $requestBody => [
       'externalReference' => '{your external reference}', // This should always be unique. Duplicate references will cause an error
       'msisdn' => 256770691484,
       'amount' => 2000,
       'currency' => 'UGX', // should be a valid ISO code and this currency should be supported by your wallet
       'reason' => 'Your reason for the transaction',
       'name' => '{Optional Field, You may send  the name of the recipient}'
   ];

   //With php you can use curl, Http Request 2, or Guzzle Http  to perform this request.
```

#### Description

```json
{
    "externalReference": "This is the string or number or reference that you use to refer to your transaction in your own application. It supports (250) characters. This should always be UNIQUE",
    "msisdn": "This is a phone  number formatted to international standard",
    "amount": "The amount of money being transacted. It should not be formatted or contain any non-numeric characters. Amount should be between UGX 500 to UGX 7000000",
    "currency": "Valid ISO  format of the currency. Currently UGX is the only currency supported for transactions",
    "reason": "Your reason for the transaction",
    "name": "Name of the recipient"
}
```

#### Example success response.

```json
{
    "response": "OK",
    "data": {
        "transactionStatus": "PENDING", // Status of the transaction in our system. All transactions begin from the PENDING status
        "ssentezoWalletReference": "f245ddac-1622-4dad-9a94-4e289bb6b8a4", // A unique id that we use to identify the transaction in our system
        "financialTransactionId": "b997c60c6f445185fcd9a3a595533734b997c60c6f445185fcd9a3a595533734" // A unique id that identifies your transaction at the network provider eg MTN/Airtel
    }
}
```

<b>NOTE</b>: The financialTransactionId is the transaction reference from the Network service provider

#### Example error response

```json
{
    "response": "ERROR",
    "error": {
        "code": "UPPER_CEILING_BREACH", // Brief overview of what the error is about
        "message": "Maximum transaction amount is UGX 7,000,000" // A description of the error that occurred
    }
}
```

#### Example error response (Validation)

```json
{
    "response": "ERROR",
    "error": {
        "message": "The given data was invalid.",
        "errors": {
            "amount": ["The amount field must be at least 500."],
            "msisdn": ["The msisdn field is required."],
            "externalReference": [
                "The external reference has already been taken."
            ]
        }
    }
}
```

### Collecting money into the wallet (Deposit)

-   <b>Endpoint</b>: https://wallet.ssentezo.com/api/deposit
-   <b>Method</b>: POST

##### Form Data Parameters

-   externalReference
-   msisdn
-   amount
-   currency
-   reason
-   name (Optional)
-   success_callback (Optional)
-   failure_callback (Optional)

#### Example - Setting up a collection request.

```php
   //Example request body
   $requestBody => [
       'amount' => 1000,
       'msisdn' => 256770691484,
       'reason' => 'Your reason for the transaction',
       'currency' => 'UGX', // should be a valid ISO code and this currency should be supported by your wallet
       'name' => 'John Doe (Optional)' // A persons name you are collecting from
       'success_callback' => 'https://yourapp.com/my-success-callback (Optional)', // A POST endpoint the wallet will call when a transaction is successful
       'failure_callback' => 'https://yourapp.com/my-failure-callback (Optional)', // A POST endpoint the wallet will call when a transaction has failed
   ];

   //With php you can use curl, Http Request 2, or Guzzle Http  to perform this request.
   //To mimic responses use the provisioned testingPhoneNumbers  and eth the environment to sandbox.
```

#### Example success response.

```json
{
    "response": "OK",
    "data": {
        "transactionStatus": "PENDING", // Status of the transaction in our system. All transactions begin from the PENDING status
        "ssentezoWalletReference": "f245ddac-1622-4dad-9a94-4e289bb6b8a4", // A unique id that we use to identify the transaction in our system
        "financialTransactionId": "b997c60c6f445185fcd9a3a595533734b997c60c6f445185fcd9a3a595533734" // A unique id that identifies your transaction at the network provider eg MTN/Airtel
    }
}
```

#### Example error response

```json
{
    "response": "ERROR",
    "error": {
        "code": "UPPER_CEILING_BREACH", // Brief overview of what the error is about
        "message": "Maximum transaction amount is UGX 7,000,000" // A description of the error that occurred
    }
}
```

#### Example error response (Validation)

```json
{
    "response": "ERROR",
    "error": {
        "message": "The given data was invalid.",
        "errors": {
            "amount": ["The amount field must be at least 500."],
            "msisdn": ["The msisdn field is required."],
            "externalReference": [
                "The external reference has already been taken."
            ]
        }
    }
}
```

<b>NOTE</b>: A PENDING status with an HTTP status code of 202 means the transaction has been initiated and the benefactor must enter their Mobile Money Pin. Once the transaction has succeeded the callback endpoint is then hit using a <b><em>POST</em></b> request to notify you.

#### Sample of POST data sent using the success callback

```json
{
    "response": "OK", // Indicates a successful response
    "data": {
        "transactionStatus": "SUCCEEDED", // Indicates the status of the transaction
        "ssentezoWalletReference": "f245ddac-1622-4dad-9a94-4e289bb6b8a4",
        "externalReference": "16011650463271",
        "financialTransactionId": "f245ddac-1622-4dad-9a94-4e289bb6b8a4-f245ddac-1622-4dad-9a94-4e289bb6b8a4"
    }
}
```

### Checking Transaction status

-   <b>Endpoint</b>: https://wallet.ssentezo.com/api/get_status/{externalReference}
-   <b>Method</b>: POST

##### URL Parameters

-   externalReference

#### Sample success response

```json
{
    "response": "OK",
    "data": {
        "transactionStatus": "SUCCEEDED",
        "ssentezoWalletReference": "3181ead4-eff9-4b9b-b926-53b41e632ca5",
        "externalReference": "rfg54rj59033w4672326hi45h6j6456",
        "financialTransactionId": "QkK0UVtkMlCZN1ZEuKVv4NU5l2soRwtyeeUHa47DT8b56sqMpmWVar3WWprFA1uO82fb7ef5f6a2530bc5c2118b34620b5a",
        "amount": 3000,
        "reason": "Sending money to someone",
        "currency": "UGX",
        "msisdn": "256770691484",
        "transactionTime": "2024-04-24T16:24:58.000000Z"
    }
}
```

<br/>

#### That's it. Go build something great 💪🏼

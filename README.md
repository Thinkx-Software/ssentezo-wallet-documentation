# Ssentezo Wallet Api Documentation.

SignUP
at 
https://wallet.ssentezo.com
Official Documentation of the ssentezo wallet API
<br/>
Version 1.0.0
## Authorization. 
The API utilizes Auth Basic authorization method. This requires you to possess valid API credentials which can be generated from the API Access menu in your wallet account.

# Limits
Api does not support entry of negative figures , exponential figures e.g e100 , or characters while entering the amount to be transacted.
For withdrawing money only transactions of 500 Uganda Shillings and Above will be processed.
For collecting money transactions of every amount are allowed provided the current account balance of the wallet  can meet your charges as defined in your ssentezo agreement.
Charges are applicable to all transactions as specified in your contract for use of this service,
# Supported Currencies
The wallet of writing this documentation supports only Uganda Shillings for transactions.

|ISO Code    | Currency Name                 |  status     |
|------------|-------------------------------|-------------|
|   UGX      | Uganda Shillings              |Supported    |

Using unsupported currency codes will result into Unsupported currency error , with HTTP Status Code 500 <br/>.
## Setting up  Authorization.

Using your generated API USER and API KEY.  the wallet follows a standard basic auth to secure the api where the Authorization header is sent encoded to base64.
<b>NB:</b> All requests must contain authorization header.

## Example  - How to encode the string using php

```php
//your api user 
$apiUser = '';
// your api key 
$apiKey = '';

$encodedString = base64_encode($apiUser .':'.$apiKey);

$header = ['Authorization' => $encodedString];
$header = ['Content-Type' => "application/form-data"]
```
for other programming languages use follow the permitted syntax respectively.
## Checking the account balance
- Endpoint.  https://wallet.ssentezo.com/api/acc_balance
- The endpoint above is accessed via the POST method. It does not require a request body but a valid authorization is neccessary.
<h5>Example response provided the parameters are satisfying. </h5>
```json
{
    "data": {
        "amount": "2976.000",
        "formatted": "2,976",
        "currency": "UGX"
    },
    "message": "success"
}
```
In the case of falure an errorCode shall be returned. All possible error codes have been described at the last  section of this documentation.


## Withdrawing Funds from your wallet account.

- Endpoint.  https://wallet.ssentezo.com/api/withdraw
- The endpoint above is accessed via the POST method with the following request body.
NB: All requests must contain an authorization header.
## Example - Setting up a withdraw request.
 ```php
   $testingPhoneNumbers = ['256770691484', '256756291975', '256778292573'];
    //Example request body

    $requestBody => [
        'amount' => 100,
        'msisdn' => 256770691484,
        'reason' => 'your reason for the transaction',
        'currency' => 'UGX', // should be a valid ISO code and this currency should be supported by your wallet
        'environment' => 'sandbox',
        'externalReference' => '{your external reference}',
        'name' => '{Optional Field, You may send  the name of the recipient}'
    ];

    //With php you can use curl, Http Request 2, or Guzzle Http  to perform this request.
    //To mimic responses use the provisioned testingPhoneNumbers  and eth the environment to sandbox.
```
## Description
```json
        {
        "amount": "the amount of money being transacted, It should not be formatted  or contain any non-numeric items, amounts less or equal to Zero will throw an error ",
        "msisdn": "This is a phone  number formatted to international standard",
        "reason" : "your reason for the transaction",
        "currency": "Valid ISO  format of the currency, Currently UGX is the only currency supported for transactions",
        "environment": "specify the environment to be utilized \"sandbox\" is the testing environment set the environment property to production or live  to enable transactions ",
        "externalReference":"This is the string or number or reference that you use to refer to your transaction in your own application. It supports (250) characters",
        "account_name": "Name of the recipient "
        }
```
## Example - Success Response.
All responses responses are JSON-encoded strings
```json 
    {
        "message": "success",
        "data": {
            "externalReference" : "2391", 
            "request_id" : "b997c60c6f445185fcd9a3a595533734" , 
            "transactionStatus": "SUCCEEDED", 
            "financialTransactionId": "12282913328"  
        }
    }
```
NB: The financialTransactionId  is the transaction reference from the Network service provider
## Example - Failed Response
```json 
    {
        "message": "failed",
        "data": {
            "externalReference" : "2381", 
            "request_id" : "b997c60c6f445185fcd9a3a595533734" , 
            "transactionStatus": "FAILED", 
            "financialTransactionId": ""  
        }
    }
```
## Expected HTTP Response Codes.
These are the possible response codes that can be received in the course of the transaction.


|Status Code | Interpretation                |
|------------|-------------------------------|
|   202      | Succeeded Transaction         |
|   400      | Failed Transaction            |
|   401      | No Authorization Header       |
|   403      | Invalid Credentials           |
|   500      | An error occurred to check the message|
|   422      | Unprocessable Entity check the request body|


## Transaction Statuses
These are the available transaction statuses that a transaction can have.

|Status | Description                                                         |
|-------------|---------------------------------------------------------------|
|  SUCCEEDED  | Succeeded Transaction                                         |
|   FAILED    | Failed Transaction                                            |
|   PENDING   |  Transaction still pending                                    |
|   INDETERMINATE     | RESPONSE NOT YET DETERMINED CONTACT SUPPORT           |

# Collecting money into the wallet (Deposit)
 - Endpoint.  https://wallet.ssentezo.com/api/deposit
- The endpoint above is accessed via the POST method with the following request body.
## Example - Setting up a collection request.
 ```php
   $testingPhoneNumbers = ['256770691484', '256756291975', '256778292573'];
    //Example request body

    $requestBody => [
        'amount' => 1000,
        'msisdn' => 256770691484,
        'reason' => 'your reason for the transaction',
        'currency' => 'UGX', // should be a valid ISO code and this currency should be supported by your wallet
        'environment' => 'sandbox',
        'callback' => 'http://yourapp.com/callbackURL',
        'account_name' => '(Optional)' //A persons name you are collecting from
    ];

    //With php you can use curl, Http Request 2, or Guzzle Http  to perform this request.
    //To mimic responses use the provisioned testingPhoneNumbers  and eth the environment to sandbox.
```
## Example - Success Response.
All responses are JSON-encoded strings
```json 
    {
        "message": "success",
        "data": {
            "externalReference": "2391", 
            "request_id" : "b997c60c6f445185fcd9a3a595533734" , 
            "transactionStatus": "PENDING", 
        }
    }

```
## Example - Failure Response
```json 
    {
        "message": "failure",
        "data": {
            "externalReference": "2391", 
            "request_id" : "b997c60c6f445185fcd9a3a595533734" , 
            "transactionStatus": "FAILED", 
        }
    }

```

NB. A PENDING status with an HTTP status code of 202 means the transaction has been initiated and the benefactor must enter their Mobile Money Pin. Once the transaction has succeeded the callback endpoint is then hit using  a <em>POST</em> request to notify you.

## Sample of POST data sent using the callback
```json 
    {
        "message": "success",
        "externalReference": "2391", 
        "request_id" :"b997c60c6f445185fcd9a3a595533734" , 
        "status" : "SUCCEEDED", 
        "financialTransactionId": "82929007772"
        
    }
```


## Checking Transaction status
This is a POST request which accepts a parameter of the externalReference which was used while performing the transaction.
NB: This request must also contain a valid HTTP Authorization Header.
URL: https://wallet.ssentezo.com/api/get_status/{externalReference} 
## Sample Response for checking transaction status.
```json 
  
    {
        "request_id" : "ah28384949r4cc4849r82h",
        "externalReference": "3384",
        "amount": "1000",
        "reason": "your transaction reason",
        "status": "FAILED/SUCCEEDED",
        "currency": "UGX",
        "network_ref": "671199102930 (similar to financialTransactionId)",
        "msisdn":"256709920188"
    }
```
if an unknown reference was passed in the URL then a 404 HTTP status code is received with errorCode: NOT_FOUND.

In case you are in the sandbox environment you must send the environment variable in your request data.

e.g 
```php
    $requestBody = [
        'environment' =>  'sandbox'
    ];
```
However, this is not required while in production.
NB. The externalReference must be a valid externalReference that was used for a previous sandbox test transaction. the msisdn and network reference will be null since sandbox transactions are stored temporarily within our system.
## Possible Error Codes
In case errors have occurred both internally or at the third-party level the API will expose an errorCode.
sample error e.g 
```json 
    {
        "message": "failure",
        "data": null,
        "errorCode": "NOT_FOUND"
    }

```
The following is a list of all possible error codes

|  errorCode  |   Description       |
|-------------|---------------------|
|NOT_FOUND    | Transaction not found |
|INVALID_CREDENTIALS | The wrong username and password were passed in the HTTP_AUTHORIZATION header|
|INTERNAL_SERVER_ERROR | There was an internal server error your transaction may or may not be successful|
|DUPLICATE_REFERENCE| External Reference was already used|
|UNSPECIFIED_ENVIRONMENT| Please specify the environment to process this transaction either "live" or "sandbox"|
|INVALID_PARAMETERS| invalid parameters were passed|
|UNSUPPORTED_CURRENCY| An unsupported currency was used |
|NOT_LIVE| Your account is not live |
|INSUFFICIENT_FUNDS| You do not have enough funds to perform the transaction|
|AMOUNT_LIMIT_ERROR|The amount you entered is greater or lower than the allowed transaction limits|
|INVALID_PHONE_NUMBER|PhoneNumber You provided is not valid|
|UNSUPPORTED_NETWORK_OR_INVALID_PHONE_NUMBER| The phone number may be invalid or it not an MTN or Airtel phone number|


Author <b> Wandera Timothy Kizito.</b>
Software Developer<br/>
ThinkXSoftware LTD

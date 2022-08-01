# Ssentezo Wallet Api Documentation.
Official Documentation of the ssentezo wallet API
## Authorization. 
The API utilizes Auth Basic authorization method. This requires you to possess valid API credentials which can be generated from the API Access menu in your wallet account.

## Setting up  Authorization.

Using your generated API USER and API KEY.  the wallet follows a standard basic auth to secure the api where the Authorization header is sent encoded to base64.
## Example  - How to encode the string using php

```php
//your api user 
$apiUser = '';
// your api key 
$apiKey = '';

$encodedString = base64_encode($apiUser .':'.$apiKey);

$header = ['Authorization' => $encodedString];
```
for other programming languages use follow the permitted syntax respectively.

## Withdrawing Funds from your wallet account.

- Endpoint.  https://wallet.ssentezo.com/api/withdraw
- The endpoint above is accessed via the POST method with the following request body.
NB: All requests must contain authorization header.
## Example - Setting up a withdraw request.
 ```php
   $testingPhoneNumbers = ['256770691484','256756291975','256778292573'];
    //example request body

    $requestBody => [
        'amount' => 100,
        'msisdn' => 256770691484,
        'reason' => 'your reason for the transaction',
        'currency' => 'UGX', // should be a valid ISO code and this currency should be supported by your wallet
        'environment' => 'sandbox'
    ];

    // with php you can use curl , or Http Request 2 ,or Guzzle Http  to perform this request.
    //To mimic responses use the provisioned testingPhoneNumbers  and eth the environment to sandbox.
```
## Description
```json
        {
        "amount" : "the amount of money being transacted , It should not be formatted  or contain any non numeric items, amounts less or equal to Zero will throw an error ",
        "msisdn" : "This is a phone  number formatted to international standard",
        "reason" : "your reason for the transaction",
        "currency" : "Valid ISO  format of the currency , Currently UGX is the only currency supported for transactions",
        "environment" : "specify the environment to be utilized \"sandbox\" is the testing environment set the environment property to production or live  to enable transactions "
        }
```
## Example - Success Response.
All responses responses are json encoded strings
```json 
    {
        "message": "success",
        "data" : {
            "externalReference" : "2391", 
            "request_id" : "b997c60c6f445185fcd9a3a595533734" , 
            "transactionStatus" : "SUCCEEDED", 
            "financialTransactionId" : "12282913328"  
        }
    }
```
NB: The financialTransactionId  is the transaction reference from the Network service provider
## Example - Failed Response
```json 
    {
        "message": "failed",
        "data" : {
            "externalReference" : "2381", 
            "request_id" : "b997c60c6f445185fcd9a3a595533734" , 
            "transactionStatus" : "FAILED", 
            "financialTransactionId" : ""  
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
|   500      | An error occurred check the message|
|   422      |  Unprocessible Entity check the request body|


## Transaction Statuses
These are the available transaction status that a transaction can have.

|Status | Description                                                         |
|-------------|---------------------------------------------------------------|
|  SUCCEEDED  | Succeeded Transaction                                         |
|   FAILED    | Failed Transaction                                            |
|   PENDING   |  Transaction still pending                                    |
|   INDETERMINATE     | RESPONSE NOT YET DETERMINED CONTACT SUPPORT           |

# Collecting money into the wallet (Deposit)
 - Endpoint.  https://wallet.ssentezo.com/api/deposit
- The endpoint above is accessed via the POST method with the following request body.
## Example - Setting up a withdraw request.
 ```php
   $testingPhoneNumbers = ['256770691484','256756291975','256778292573'];
    //example request body

    $requestBody => [
        'amount' => 100,
        'msisdn' => 256770691484,
        'reason' => 'your reason for the transaction',
        'currency' => 'UGX', // should be a valid ISO code and this currency should be supported by your wallet
        'environment' => 'sandbox',
        'callback' => 'http://yourapp.com/callbackURL',
        'account_name' => '(Optional)' //A persons name you are collecting from
    ];

    // with php you can use curl , or Http Request 2 ,or Guzzle Http  to perform this request.
    //To mimic responses use the provisioned testingPhoneNumbers  and eth the environment to sandbox.
```
## Example - Success Response.
All responses responses are json encoded strings
```json 
    {
        "message": "success",
        "data" : {
            "externalReference" : "2391", 
            "request_id" : "b997c60c6f445185fcd9a3a595533734" , 
            "transactionStatus" : "PENDING", 
        }
    }
```
NB. A PENDING status with HTTP status Code of 202 means the transaction has been initiated and the benefactor must enter their Mobile Money Pin. Once the transaction has succeeded the call back endpoint is then hit using  a <em>POST</em> request to notify you.

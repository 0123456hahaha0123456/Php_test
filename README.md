# Php SDK for Merchant Integration 

## How to use SDK

Before using this SDK, you should run command below to install all needed packages.
```terminal
    composer install
```
For using this SDK, you might copy all files in [src](./src) folder and paste it to your project. 

### Config environment:

```
- stage : String : "PRD"/"STG"
- country : String : Country code of the merchant location. [Refer](https://countrycode.org/)
- partner_id : String - Unique ID for a partner. Retrieve from Developer Hom
- partner_secret : String - Secret key for a partner. Retrieve from Developer Home
- merchant_id : String - Retrieve from Developer Home.
- terminal_id : String - Retrieve from Developer Home. **Only available for POS (P2M)**
- client_id : String - Retrieve from Developer Home. **Only available for OnA**
- client_secret : String - Retrieve from Developer Home. **Only available for OnA**
- redirect_url : String - The url configured in Developer Home. **Only available for OnA**
```

### How to integration with SDK:

On your controller
1. Import MerchantIntegration 

```
use Moca\Merchant\MerchantIntegrationOffline;
use Moca\Merchant\MerchantIntegrationOnline;
```
2. Create new object of class MerchantIntegrationOffline or  MerchantIntegrationOnline
    - Online: 
    ```php
        $callOna = new MerchantIntegrationOnline(stage,country,partner_id,partner_secret,merchant_id,client_id,client_secret,redirect_url);
    ```
    - Offline: 
    ```php
        $callPos = new MerchantIntegrationOffline(stage,country,partner_id,partner_secret,merchant_id,terminal_id)
    ```
3. Set params for API you need to call.

### Detail for APIs online
1. Create the request:

```php
    $response = $callOna->onaChargeInit($partnerTxID, $partnerGroupTxID, $amount, $currency, $description, $isSync = false, array $metaInfo =[], array $items =[], array $shippingDetails = [], $hidePaymentMethods =[]);
```

2. Create web URL:
```php
    $webLink = $callOna->onaCreateWebUrl($partnerTxID, $partnerGroupTxID, $amount, $currency, $description, $isSync = false, array $metaInfo =[], array $items =[], array $shippingDetails = [], $hidePaymentMethods = [], $state = null);
```

3. get access token:

```php
    $respAuthToken = $callOna->onaOAuth2Token($partnerTxID, $code);
```

4. Confirm of transaction:

```php
    $respComplete = $callOna->onaChargeComplete($partnerTxID, $respAuthToken->accessToken);
```

5. Get status of transaction success: 

```php
    return $callOna->onaGetChargeStatus($partnerTxID, $currency, $respAuthToken->accessToken);
```

6. Refund transaction already success

```php
    $partnerTxID = md5(uniqid(rand(), true));

    $resp = $callOna->onaRefund($refundPartnerTxID, $partnerGroupTxID, $amount, $currency, $txID, $description, $respAuthToken->accessToken);

    if($resp->code ==200) {
        $this->partnerRefundTxID = $refundPartnerTxID;
    }
```

7. Get refund transaction status:

```php

    return $callOna->onaGetRefundStatus($refundPartnerTxID, $currency);
```

8. Get OTC status:
```php
    return $callOna->onaGetOTCStatus($partnerTxID,$currency);
```

### Detail for APIs online 

1. Create QR code:
```php
    $partnerTxID = md5(uniqid(rand(), true));
    $msgID = md5(uniqid(rand(), true));
    return $callPos->posCreateQRCode($msgID, $partnerTxID, $amount, $currency);
```

2. cancelTxn:
```php
    return $callPos->posCancel($msgID, $partnerTxID, $origPartnerTxID, $origTxID, $currency);
```

3. refundPosTxn:
```php
    return $callPos->posRefund($msgID, $refundPartnerTxID, $amount, $currency, $originTxID, $description);
```

4. performTxn:
```php
    return $callPos->posPerformQRCode($msgID, $partnerTxID, $currency, $amount, $code)
```

5. posGetTxnStatus:
```php
    return $callPos->posGetTxnStatus($msgID, $partnerTxID, $currency);
```

6. posGetRefundStatus:
```php
    return $callPos->posGetRefundStatus($msgID, $refundPartnerTxID, $currency);
```
---
title: API Reference

language_tabs:
  - python
  - ruby

toc_footers:
  - <a href='https://www.nifty.co.ke/users/register/'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/tripit/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Introduction

Welcome to the Nifty API! You can use our API to access Nifty Payment API endpoints, which can get information on transactions and events related to your wallet.

We have language bindings in Ruby and Python! You can view code examples in the dark area to the right, and you can switch the programming language of the examples with the tabs in the top right.

To proceed,

1. <a href='https://www.nifty.co.ke/users/register/'>Sign up</a> for an account. You will need to validate your email account and mobile phone number.
2. Navigate to your profile and create an API Key. Save the secret key somewhere safe since we will not show it again.
3. Install your language specific Nifty api client.
4. Configure your client with your API access key, secret key and user id.
5. Make calls

## Transport security

All API requests to us should be made over HTTPS and be signed. This provides us with certain guarantees that:

1. You made the API call.
2. The API call has not been tampered with.
3. The API call is not a replay attack.
4. Your API call was visible to you and us only.

To ensure that will always be true,

1. Rotate your keys frequently.
2. Conduct (or let your client) ssl certificate verification.
3. Use NTP/Time servers to ensure that your api callers have the correct time.

<aside class="notice">
To achieve all of this in a dead simple way, just use our api client libraries.
</aside>

Unfortunately, this means that shell access is a bit clunky due to signature generation.

## Notes

All API calls will return:

1. Timezone aware dates in iso8601 format.
2. Unicode strings when the data is unicode.
3. Transaction values in 2 decimal places.


We expect the reverse to be true. For example, if you wish to query for transactions between two time instants, provide a timezone aware iso8601 timestamp.


# Client initialisation

> To initialise, use this code:

```python
import nifty_client

client = nifty_client(user_id, access_key, secret_key)
```

```ruby
require 'niftyclient'

client = Nifty::APIClient.initialise!(user_id, access_key, secret_key)
```

> Make sure to replace `user_id`, `access_key`, and `secret_key` with your API details from the API tab on the dashboard.

Nifty clients use API keys to allow access to the API. You can register for your API key at our [developer portal](https://www.nifty.co.ke/users/profile#tab_api_settings).

Once you initialise your client, the api payload HMAC will be included in all API requests to the server in a header that looks like the following:

`Authorization: 'Signature headers="date (request-target) host content-length content-type x-content-sha256",keyId="e51cab9a86a9d045e3aea24dbca34536",algorithm="hmac-sha256",signature="iB2uqBBjVXIzVcV3jeZTGpnhQ1yTz3AnpT/JAcLzVKw="'`

For POST and PUT, the payload will HMAC'd and the checksum will be included in a header similar to:

`x-content-sha256: 'bvVpDJi7jhDkudLoYg09WtyEHnnyYDeldRHFsvuJKCk='`

<aside class="notice">
You must replace  provide a valid set of user id, access key and secret key. Not doing so result in a HTTP 403 response with the payload
<code>
{
    "message": "Access denied"
}
</code>.
</aside>

# Wallet

## Get All Wallets

```python
import nifty_client

client = nifty_client(user_id, access_key, secret_key)
client.get_wallet()

```

```ruby
require 'niftyclient'
client = Nifty::APIClient.initialise!(user_id, access_key, secret_key)

client.wallet.get
```

> The above command returns a dictionary/hash table representation of this JSON:

```json
[
   {
      "user_name":"mike",
      "created_at":"2017-04-24T23:33:52.747695+03:00",
      "balance":"1645.46",
      "user_id":"8dad2dea-7d7f-4e8b-a61c-53150f1b7452",
      "last_modified":"2017-04-25T00:12:36.050071+03:00"
   }
]
```

This endpoint retrieves all your wallets. If you don't have a wallet, you will get a response with an empty json list. Just issue the create wallet call to create one.

### HTTP Request

`GET http://api.nifty.co.ke/api/v1/user/<uuid:user_id>/wallet`

### Query Parameters

None


## Create A Wallet

```python
import nifty_client

client = nifty_client(user_id, access_key, secret_key)
client.create_wallet()

```

```ruby
require 'niftyclient'

client = Nifty::APIClient.initialise!(user_id, access_key, secret_key)
client.wallet.create
```

> The above command returns JSON structured like this:

```json
[
   {
      "user_name":"mike",
      "created_at":"2017-04-24T23:33:52.747695+03:00",
      "balance":"0.00",
      "user_id":"8dad2dea-7d7f-4e8b-a61c-53150f1b7452",
      "last_modified":"2017-04-25T00:12:36.050071+03:00"
   }
]
```

This endpoint creates a wallet.

<aside class="notice">This operation is idempotent and can be safely called multiple times.</aside>

### HTTP Request

`POST http://api.nifty.co.ke/api/v1/user/<uuid:user_id>/wallet`

### URL Parameters

None


## Consume A Token

```python
import nifty_client

client = nifty_client(user_id, access_key, secret_key)
client.consume_token(
    transaction_id="7BMKZ6K7E8", phone_number="254716622448", till_number=703648)

```

```ruby
require 'niftyclient'

client = Nifty::APIClient.initialise!(user_id, access_key, secret_key)
client.wallet.consume_token(
    transaction_id="7BMKZ6K7E8", phone_number="254722123456", till_number=703648)
```

> The above command returns JSON structured like this:

```json
[
   {
      "payment_id":"d2c29c60-0b1a-11e7-8f7b-061cf2e0e94d",
      "till_number":"703648",
      "user_id":"8dad2dea-7d7f-4e8b-a61c-53150f1b7452",
      "trans_amount":"581.37",
      "msisdn":"254722123456",
      "trans_time":"2017-01-24T21:30:52.739243+03:00",
      "last_modified":"2017-01-24T21:30:52.739243+03:00",
      "names":"First M Last",
      "trans_id":"0OZC02Q1OZ"
   }
]
```

This endpoint consumes a token and credits your wallet with the token amount.

<aside class="notice">This operation is not idempotent and cannot be safely called multiple times. However, in case of failure, use the transactions list api to refetch the call result.</aside>

<aside class="success">If successful, this call will credit your account with the token value</aside>

### HTTP Request

`PUT http://api.nifty.co.ke/api/v1/user/<uuid:user_id>/wallet`

### URL Parameters

Parameter        | Description
---------------- | -----------
transaction_id   | The unique token identifier (Ex: MPESA transaction code or Airtel Money transaction ID).
phone_number     | The phone number of the user who executed the money transfer (Ex: Phone Number of MPESA sender).
till_number      | The till number the user sent the money to. Please note that for Safaricom MPESA, this will correspond to the store_number. Use the till tab, on your profile page on the dashboard to get the correct value to use here.



## View Transactions

```python
import nifty_client

client = nifty_client(user_id, access_key, secret_key)
client.transactions()

```

```ruby
require 'niftyclient'

client = Nifty::APIClient.initialise!(user_id, access_key, secret_key)
client.transactions
```

> The above command returns JSON structured like this:

```json
[
   {
      "payment_id":"d2c29c60-0b1a-11e7-8f7b-061cf2e0e94d",
      "till_number":"703648",
      "last_modified":"2016-11-04T21:30:52.739569+03:00",
      "user_id":"15695297-f441-4b1c-a75b-831fbb1d1431",
      "names":"First M Last",
      "msisdn":"254722123456",
      "trans_time":"2016-11-04T21:30:52.739569+03:00",
      "trans_id":"DZ56D1KDZG",
      "trans_amount":"250.02"
   },
   {
      "payment_id":"d2c29c60-0b1a-11e7-8f7b-061cf2e0e94d",
      "till_number":"703648",
      "last_modified":"2016-11-10T21:30:52.739705+03:00",
      "user_id":"15695297-f441-4b1c-a75b-831fbb1d1431",
      "names":"First M Last",
      "msisdn":"254722123456",
      "trans_time":"2016-11-10T21:30:52.739705+03:00",
      "trans_id":"2ZS5XLSY3P",
      "trans_amount":"40.19"
   },
   {
      "payment_id":"d2c29c60-0b1a-11e7-8f7b-061cf2e0e94d",
      "till_number":"703648",
      "last_modified":"2016-08-29T21:30:52.739833+03:00",
      "user_id":"15695297-f441-4b1c-a75b-831fbb1d1431",
      "names":"First M Last",
      "msisdn":"254722123456",
      "trans_time":"2016-08-29T21:30:52.739833+03:00",
      "trans_id":"7BMKZ6K7E8",
      "trans_amount":"276.40"
   }
]
```

This endpoint lists the last 30 transactions made against your wallet. Use the limit and offset arguments to fetch paginated results.


### HTTP Request

`GET http://api.nifty.co.ke/api/v1/user/<uuid:user_id>/wallet/transactions`

### URL Parameters

Parameter        | Description
---------------- | -----------
limit            | The maximum number of results to return in this call.
offset           | Where to start the paginated result.

## Search For A Transaction

```python
import nifty_client

client = nifty_client(user_id, access_key, secret_key)
client.transactions(transaction_id='DZ56D1KDZG')

```

```ruby
require 'niftyclient'

client = Nifty::APIClient.initialise!(user_id, access_key, secret_key)
client.transactions(transaction_id='DZ56D1KDZG')
```

> The above command returns JSON structured like this:

```json
[
   {
      "payment_id":"d2c29c60-0b1a-11e7-8f7b-061cf2e0e94d",
      "till_number":"703648",
      "user_id":"8dad2dea-7d7f-4e8b-a61c-53150f1b7452",
      "trans_amount":"581.37",
      "msisdn":"254716622448",
      "trans_time":"2017-01-24T21:30:52.739243+03:00",
      "last_modified":"2017-01-24T21:30:52.739243+03:00",
      "names":"LABAN M KIMOTHO",
      "trans_id":"0OZC02Q1OZ"
   }
]
```

This endpoint lists the last 30 transactions made against your wallet. Use the limit and offset arguments to fetch paginated results.


### HTTP Request

`GET http://api.nifty.co.ke/api/v1/user/<uuid:user_id>/wallet/transactions`

### URL Parameters

Parameter        | Description
---------------- | -----------
transaction_id   | The id of the transaction token being looked up.
limit            | The maximum number of results to return in this call.
offset           | Where to start the paginated result.

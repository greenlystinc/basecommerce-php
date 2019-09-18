# Card


Included in the Base Commerce platform is a secured vault that can be used for storing customers payment information, both credit card and bank account information. 
This makes the application **PCI Level 1 Complaint** by storing the credit card information in the BaseCommerce Vault instead of storing this information within your software applications.

Protecting consumer data is a critical responsibility of software developers, and data breaches can be extremely costly should they occur. 

## Adding a Credit Card to the Vault

```php
use Greenlyst\BaseCommerce\Client;
use Greenlyst\BaseCommerce\Card;

// Creating the client instance
$client = new Client('sdk_username','sdk_password','sdk_key');

// Creating the Card instance
$card = new Card();

// Setting the card details
$card->setName('John Doe');
$card->setCardNumber('4012888888881881');
$card->setCardExpirationMonth('08');
$card->setCardExpirationYear('2020');
$card->setAlias('John Doe Card');

// Setting the $client instance to the Card Object
$card->setClient($client);

// Adding the card to the vault
$instance = $card->add();

// Getting the token returned by BaseCommerce for the card stored
echo $instance->getToken();
```

### Vault Token 

The vault works by assigning a unique token to each payment data record that is added to the system. Future transactions can then be processed by utilizing the token instead of the payment details. The scope of each stored record is dictated by the credentials that are used when creating the record.

If you did not specify your own token when creating the Card object, you can retrieve the system generated token by invoking the `getToken()` method on the Card object returned by `add()` as shown in the example above. 
This is the token that you should stored on your system for future transactions.

```php
$card->getToken();
```

::: warning Token Length
As of Sept 15 2019, the maximum length of the token that is returned by BaseCommerce does not exceed 128 characters. 
:::

You also have the option to specify your own Token if your don't want to use the one generated by BaseCommerce. 
If you want to specify your own token you can set it using the `setToken()` method:

```php
$card->setToken('some_token_for_the_card_goes_here');
```

### Address Validation
If the merchant has set the configuration AVS to required, then BaseCommerce requires that the Credit card being added to the vault has a billing address attached to it. 
You can set the Address to the card as shown in the following example.

```php
use Greenlyst\BaseCommerce\Address;

$address = new Address();
$address->setName(Address::TYPE_BILLING);
$address->setAddressLine1('123 Some Address');
$address->setCity('Tempe');
$address->setState('AZ');
$address->setZipCode('12345');

$card->setBillingAddress($address);
```

### Required Fields 

- Card Number `setCardNumber()`
- Expiration Month `setExpirationMonth()`
- Expiration Year `setExpirationYear()`
- Customer Name `setName()`

### Optional Fields

- Token (if one is not passed, BaseCommerce will generate one, refer [here](#vault-token))
- Alias (if one is not passed, BaseCommerce will generate one)
- Address (required if merchant has set the configuration AVS to required, refer [here](#address-validation))

## Updating Credit Card Info

Over the course of their existence, Vault Records may have certain aspects of their data changed. For example, 
if the customer moves and has their billing address linked to their card charged, you would just want to update that 
Vault Record within the system rather then adding a new one entirely.

### What can you update

- **Billing Address** `setBillingAddress(Address $address)` - The cardholder's billing address. _When this object is set on the BankCard, the entire current Billing Address set on the BankCard object will be overwritten with the new Billing Address._ 
- **Expiration Month** `setExpirationMonth()`
- **Expiration Year** `setExpirationYear()`

### What you cannot update
- **Card Number** `setCardNumber()` - If a card number is set, the SDK will ignore it and will not send it part of the request.

Here is an example showing how to update Credit Card information on the BaseCommerce Vault

```php
use Greenlyst\BaseCommerce\Client;
use Greenlyst\BaseCommerce\Card;

// Creating the client instance
$client = new Client('sdk_username','sdk_password','sdk_key');

// Creating the Card instance
$card = new Card();

// Setting the card details
$card->setCardExpirationMonth('08');
$card->setCardExpirationYear('2020');
$card->setToken('self_created_token_or_one_received_from_base_commerce_goes_here');

// Setting the $client instance to the Card Object
$card->setClient($client);

// Adding the card to the vault
$instance = $card->update();

```

## Deleting Credit Card

If a Credit Card requires to be deleted from the vault, you can use the `delete()` method to perform this task. Here is an example on how to do this.

```php

use Greenlyst\BaseCommerce\Client;
use Greenlyst\BaseCommerce\Card;

// Creating the client instance
$client = new Client('sdk_username','sdk_password','sdk_key');

// Creating the Card instance
$card = new Card();

// Setting the card details
$card->setToken('self_created_token_or_one_received_from_base_commerce_goes_here');

// Setting the $client instance to the Card Object
$card->setClient($client);

// Adding the card to the vault
$instance = $card->delete();

```
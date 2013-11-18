# Magento API Client

This library implements the [Magento](http://www.magentocommerce.com/) SOAP v2 (standard) API.
It allows wrappers for each call, dependencies injections (eg: used in [Symfony 2.x](http://symfony.com/)) and code completion.

[![Build Status](https://travis-ci.org/smalot/magento-client.png?branch=master)](https://travis-ci.org/smalot/magento-client)
[![Total Downloads](https://poser.pugx.org/smalot/magento-client/downloads.png)](https://packagist.org/packages/smalot/magento-client)
[![Current Version](https://poser.pugx.org/smalot/magento-client/v/stable.png)](https://packagist.org/packages/smalot/magento-client)

# Documentation

This API is fully modeled on top of [Magento SOAP API](http://www.magentocommerce.com/api/soap/introduction.html).

Supported modules are :
* Mage_Catalog
* Mage_CatalogInventory
* Mage_Checkout
* Mage_Customer
* Mage_Directory
* Mage_Sales
* Enterprise_CustomerBalance
* Enterprise_CustomerGiftCard
* Mage_GiftMessage
* Mage_Core
* Store View

Module's names has been standardized to be more clean :
* Catalog
* CatalogInventory
* Cart
* Customer
* Directory
* Order
* CustomerBalance
* GiftCard (*todo*)
* GiftMessage
* Core
* Store

**Note :** `login` and `logout` calls are made only if needed.

# Installation

Download using [composer](http://getcomposer.org/):

```js
{
    "require": {
        "smalot/magento-client": "*"
    }
}
```

Now tell composer to download the bundle by running the command:

``` bash
$ php composer.phar update smalot/magento-client
```

Composer will install the bundle to your project's `vendor/smalot` directory and `create`/`update` an autoload file.

# License

This library is provided under GPLv2 license. See the complete license :

    Resources/meta/LICENSE

# Implementation

## Single Call

Here is a sample code to load tree of categories of the `default` website in a single call.

```php
<?php

// Include composer's autoloader mecanism
include 'vendor/autoload.php';

// Init config
$path    = 'http://domainname.tld/shop-folder/';
$apiUser = 'username';
$apiKey  = 'xxxxxxxxxxxxxxxxxxx';

// Create remote adapter which wrap soapclient
$adapter  = new \Smalot\Magento\RemoteAdapter($path, $apiUser, $apiKey);

// Call any module's class
$categoryManager = new \Smalot\Magento\Catalog\Category($adapter);
$tree            = $categoryManager->getTree()->execute();

var_dump($tree);

```

## Multi Call

```php
<?php

// Include composer's autoloader mecanism
include 'vendor/autoload.php';

// Init config
$path    = 'http://domainname.tld/shop-folder/';
$apiUser = 'username';
$apiKey  = 'xxxxxxxxxxxxxxxxxxx';

// Create remote adapter which wrap soapclient
$adapter  = new \Smalot\Magento\RemoteAdapter($path, $apiUser, $apiKey);

// Build the queue for multicall
$queue = new \Smalot\Magento\MultiCallQueue($adapter);

// Call any module's class
$productManager = new \Smalot\Magento\Catalog\Product($adapter);
$productManager->getInfo(10)->addToQueue($queue);
$productManager->getInfo(11)->addToQueue($queue);
$productManager->getInfo(12)->addToQueue($queue);

// Request in one multicall information of 3 products (#10, #11, #12)
$products = $queue->execute();

var_dump($products);

```

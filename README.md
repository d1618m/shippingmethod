# Requirements

### Compatibale with magento 2.3.5,2.3.7,2.4.0 to 2.4.4
for this module i have follow this link https://www.mageplaza.com/devdocs/magento-2-create-shipping-method/


## installation guide

- download zip file and extract in magento app/code/

## Instructions

Download the code

```
git clone https://github.com/kgkrunch/shippingmethod
```

### Step 1: Create config.xml file


Create a module call: `Simpleshipping` located in `app/code/<vendor_name>/<module_name>`

![shipping method in magento 2](https://cdn.mageplaza.com/media/general/xNM7TOu.png)

- [app/code/Kgkrunch/InternationalShipping/registration.php](https://github.com/kgkrunch/shippingmethod/blob/master/registration.php)
- [app/code/Kgkrunch/InternationalShipping/etc/module.xml](https://github.com/kgkrunch/shippingmethod/blob/master/etc/module.xml)

Now create `config.xml` at `app/code/<vendor_name>/<module_name>/etc/config.xml`

- [app/code/Kgkrunch/InternationalShipping/etc/config.xml](https://github.com/kgkrunch/shippingmethod/blob/master/etc/config.xml)

### Step 2: Create shipping model

Next, we need to create a model class for this **shipping method in Magento 2**

Create a `Shipping.php` at `app/code/<vendor_name>/<module_name>/Model/Carrier/Shipping.php`

- [app/code/Kgkrunch/InternationalShipping/Model/Carrier/Shipping.php](https://github.com/kgkrunch/shippingmethod/blob/master/Model/Carrier/Shipping.php)

### Step 3: Create configuration file

Now create `system.xml` file at `app/code/<vendor_name>/<module_name>/etc/adminhtml/system.xml`

Learn more [System.xml configuration for Magento 2](https://www.mageplaza.com/magento-2-module-development/create-system-xml-configuration-magento-2.html)

- [app/code/Kgkrunch/InternationalShipping/etc/adminhtml/system.xml](https://github.com/kgkrunch/shippingmethod/blob/master/etc/adminhtml/system.xml)

### Step 4: Enable module

> This is final step, you need to run command to enable Simple Shipping method

```
        php bin/magento setup:upgrade
        php bin/magento setup:static-content:deploy -f
        php bin/magento indexer:reindex
        php bin/magento cache:flush

```    

check admin side steup->configuration->sales->delivarymethod->shippingrate is there you can now set your cuatom rate and min rate max rate 
which calculate the shipping rate betweeen min and max total







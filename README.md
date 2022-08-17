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
### 1. Enable module

> This is final step, you need to run command to enable Simple Shipping method

```
        php bin/magento setup:upgrade
        php bin/magento setup:static-content:deploy -f
        php bin/magento indexer:reindex
        php bin/magento cache:flush

``` 
## 2. Shipping Method user guide

Login to Magento Admin and do as the following:

![Magento 2 shipping method](https://prnt.sc/f-etMUH2ntZ6)

### Sample Shipping Configuration
#### Enable Social Login

Go to `Admin Panel > store > Configuration > Sales > Delivary Method >`

![enable sample shipping method](https://i.imgur.com/jNcIDpg.png)

Select `Yes` option to enable shipping method.

#### Set Configuration

![enable shipping method,price](https://prnt.sc/eN-814M76cBI)

set shipping method name,shipping price,and set custom min rate and max rate which calculate the shipping rate between min and max total








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

> For setup run command 

```
    php bin/magento setup:upgrade
	php bin/magento setup:static-content:deploy -f
	php bin/magento indexer:reindex
	php bin/magento cache:flush

```    

check admin side steup->configuration->sales->delivarymethod->kgkrunch international shippingrate is there you can now set your cuatom rate and min rate max rate 
which calculate the shipping rate betweeen min and max total







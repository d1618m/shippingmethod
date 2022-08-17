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

```
<?xml version="1.0"?>
<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:module:Magento_Store:etc/config.xsd">
    <default>
        <carriers>
            <simpleshipping>
                <active>1</active>
                <sallowspecific>0</sallowspecific>
                <model><vendor_name>\<module_name>\Model\Carrier\Shipping</model>
                <name>International Shipping Method</name>
                <price>10</price>
                <minprice>100</minprice>
                <maxprice>1000</maxprice>
                <title>International Shipping Method</title>
                <specificerrmsg>This shipping method is not available. To use this shipping method, please contact us.</specificerrmsg>
            </simpleshipping>
        </carriers>
    </default>
</config>

```

The shipping method code should be child of `default > carriers`. And this code should be exactly same with $_code in `app/code/<vendor_name>/<module_name>/Model/Carrier/Shipping.php:13`

### Step 2: Create shipping model

Next, we need to create a model class for this **shipping method in Magento 2**

Create a `Shipping.php` at `app/code/<vendor_name>/<module_name>/Model/Carrier/Shipping.php`

``` php
<?php
namespace vendor_name\module_name\Model\Carrier;

use Magento\Quote\Model\Quote\Address\RateRequest;
use Magento\Shipping\Model\Rate\Result;

class Shipping extends \Magento\Shipping\Model\Carrier\AbstractCarrier implements
    \Magento\Shipping\Model\Carrier\CarrierInterface
{
    /**
     * @var string
     */
    protected $_code = 'simpleshipping';

    /**
     * @var \Magento\Shipping\Model\Rate\ResultFactory
     */
    protected $_rateResultFactory;

    /**
     * @var \Magento\Quote\Model\Quote\Address\RateResult\MethodFactory
     */
    protected $_rateMethodFactory;

    public function __construct(
        \Magento\Framework\App\Config\ScopeConfigInterface $scopeConfig,
        \Magento\Quote\Model\Quote\Address\RateResult\ErrorFactory $rateErrorFactory,
        \Psr\Log\LoggerInterface $logger,
        \Magento\Shipping\Model\Rate\ResultFactory $rateResultFactory,
        \Magento\Quote\Model\Quote\Address\RateResult\MethodFactory $rateMethodFactory,
        array $data = []
    ) {
        $this->_rateResultFactory = $rateResultFactory;
        $this->_rateMethodFactory = $rateMethodFactory;
        parent::__construct($scopeConfig, $rateErrorFactory, $logger, $data);
    }

    /**
     * get allowed methods
     * @return array
     */
    public function getAllowedMethods()
    {
        return [$this->_code => $this->getConfigData('name')];
    }

    /**
     * @return float
     */
    private function getShippingPrice()
    {
        $configPrice = $this->getConfigData('price');

        $shippingPrice = $this->getFinalPriceWithHandlingFee($configPrice);

        return $shippingPrice;
    }

    /**
     * @param RateRequest $request
     * @return bool|Result
     */
    public function collectRates(RateRequest $request)
    {
         if (!$this->getConfigFlag('active')) {
            return false;
        }

        $objectManager = \Magento\Framework\App\ObjectManager::getInstance();
        $cart = $objectManager->get('\Magento\Checkout\Model\Cart'); 
 
        $grandTotal = $cart->getQuote()->getGrandTotal();
        $minTotal = $this->getConfigData('minprice');
        $maxTotal = $this->getConfigData('maxprice');
           // print_r($this->getConfigData('minprice'));die();
        // echo $grandTotal;die();

        if((!empty($minTotal) && ($grandTotal < $minTotal)) || (!empty($maxTotal) && ($grandTotal > $maxTotal))) 
        {
            return true;
        }

        /** @var \Magento\Shipping\Model\Rate\Result $result */
        $result = $this->_rateResultFactory->create();

        /** @var \Magento\Quote\Model\Quote\Address\RateResult\Method $method */
        $method = $this->_rateMethodFactory->create();

        $method->setCarrier($this->_code);
        $method->setCarrierTitle($this->getConfigData('title'));

         // die("hjdmcv");

        $method->setMethod($this->_code);
        $method->setMethodTitle($this->getConfigData('name'));

        // $amount = $this->getShippingPrice();

          $amount = $this->getConfigData('price');
          // print_r($this->getConfigData('price'));die();

        $method->setPrice($amount);
        $method->setCost($amount);

        $result->append($method);

        return $result;
    }
}
```

### Step 3: Create configuration file

Now create `system.xml` file at `app/code/<vendor_name>/<module_name>/etc/adminhtml/system.xml`

Learn more [System.xml configuration for Magento 2](https://www.mageplaza.com/magento-2-module-development/create-system-xml-configuration-magento-2.html)

with the following content:

``` xml
<?xml version="1.0"?>
<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:module:Magento_Config:etc/system_file.xsd">
    <system>
        <section id="carriers" translate="label" type="text" sortOrder="320" showInDefault="1" showInWebsite="1" showInStore="1">
            <group id="simpleshipping" translate="label" type="text" sortOrder="0" showInDefault="1" showInWebsite="1" showInStore="1">
                <label>International Shipping Rate</label>
                <field id="active" translate="label" type="select" sortOrder="1" showInDefault="1" showInWebsite="1" showInStore="0" canRestore="1">
                    <label>Enabled</label>
                    <source_model>Magento\Config\Model\Config\Source\Yesno</source_model>
                </field>
                <field id="name" translate="label" type="text" sortOrder="3" showInDefault="1" showInWebsite="1" showInStore="1" canRestore="1">
                    <label>Method Name</label>
                </field>
                <field id="price" translate="label" type="text" sortOrder="5" showInDefault="1" showInWebsite="1" showInStore="0" canRestore="1">
                    <label>Price</label>
                    <validate>validate-number validate-zero-or-greater</validate>
                </field>
                <field id="minprice" translate="label" type="text" sortOrder="6" showInDefault="1" showInWebsite="1" showInStore="0" canRestore="1">
                    <label>Minimum Price</label>
                    <validate>validate-number validate-zero-or-greater</validate>
                </field>
                <field id="maxprice" translate="label" type="text" sortOrder="7" showInDefault="1" showInWebsite="1" showInStore="0" canRestore="1">
                    <label>Maximum Price</label>
                    <validate>validate-number validate-zero-or-greater</validate>
                </field>
                <field id="sort_order" translate="label" type="text" sortOrder="100" showInDefault="1" showInWebsite="1" showInStore="0">
                    <label>Sort Order</label>
                </field>
                <field id="title" translate="label" type="text" sortOrder="2" showInDefault="1" showInWebsite="1" showInStore="1" canRestore="1">
                    <label>Title</label>
                </field>
                <field id="sallowspecific" translate="label" type="select" sortOrder="90" showInDefault="1" showInWebsite="1" showInStore="0" canRestore="1">
                    <label>Ship to Applicable Countries</label>
                    <frontend_class>shipping-applicable-country</frontend_class>
                    <source_model>Magento\Shipping\Model\Config\Source\Allspecificcountries</source_model>
                </field>
                <field id="specificcountry" translate="label" type="multiselect" sortOrder="91" showInDefault="1" showInWebsite="1" showInStore="0">
                    <label>Ship to Specific Countries</label>
                    <source_model>Magento\Directory\Model\Config\Source\Country</source_model>
                    <can_be_empty>1</can_be_empty>
                </field>
                <field id="showmethod" translate="label" type="select" sortOrder="92" showInDefault="1" showInWebsite="1" showInStore="0">
                    <label>Show Method if Not Applicable</label>
                    <source_model>Magento\Config\Model\Config\Source\Yesno</source_model>
                    <frontend_class>shipping-skip-hide</frontend_class>
                </field>
                <field id="specificerrmsg" translate="label" type="textarea" sortOrder="80" showInDefault="1" showInWebsite="1" showInStore="1" canRestore="1">
                    <label>Displayed Error Message</label>
                </field>
            </group>
        </section>
    </system>
</config>
```

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







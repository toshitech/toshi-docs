# TOSHI Integration Guide - Shopify
No manual integration is required for Shopify Basic -> Advanced. Installation is managed through a Shopify collaboration account.

## Try Before You Buy (TBYB)
Please note that Shopify does not natively support TBYB or items being sent on consignment. To enable this service, TOSHI provides a workaround at the /cart page for Shopify stores:

### Basket page integration 

First copy the following scripts on to the shopify cart page. Typically this is the cart.liquid file. 
```
<!-- TOSHI Scripts -->
<script type='text/javascript' src='https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js'></script>
<script type='text/javascript' defer src='https://integration-sandbox-cdn.toshi.co/3.0/main.js'></script>
<script type='text/javascript' id='toshi-modal-script' defer data-api-key='123abc' data-environment='staging' data-threshold='0' src='https://integration-sandbox-cdn.toshi.co/3.0/integrations/shopify-tbyb.js'></script>
```

There are various parameters to configure as part of the script: 

1. The Script source, this will differ for production or staging.
2. The data-environment this again will differ for production or staging. 
3. The data-api-key this is your API issued by TOSHI, contact engineering@toshi.co for API key provisioning. 
4. The data-threshold, this is the basket amount total the customer would have to meet or exceed to be shown the TOSHI button, 0 means no minimum threshold. 

For Staging access:

The script sources are: integration-sandbox-cdn.toshi.co.<br/>
The data-environment is: staging.<br/>
The API key is the staging API key provisioned to you.

For Production access: 

The script sources are: integration-cdn.toshi.co.<br/>
The data-environment is: production.<br/>
The API key is the production API key provisioned to you.


Once the scripts are on the page create a `<div id='toshi-tbyb'>` on the page. Typically this is underneath the existing checkout buttons. 

### Styling

Add following styles to your style sheet: 

```
/** TOSHI Try-Before-You-Buy Cart button styles **/
#toshi-tbyb{
  margin-top: 10px;
  display: inline-block;
  width: 100%;
  max-width: 300px;
  text-align: left;
}

@media only screen and (max-width: 500px){
  #toshi-tbyb{
	  max-width: none; 
  }
 ```
 
Once these steps are completed add an item to the basket and the TOSHI button will appear. 

## Shopify Plus
1. Install the TOSHI Shopify plugin
1. Request access to TOSHI's API. Email us at engineering@toshi.co
1. After access has been granted, configure the correct environment and API key in the respective fields on the scripts below. **
1. Place scripts on the checkout.liquid file in Shopify

** For production environment **
```
    <script type='text/javascript' defer src='https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js'></script> 
    <script type='text/javascript' src='https://integration-cdn.toshi.co/3.0/main.js'></script>
    <script type='text/javascript' id='toshi-modal-script' defer data-api-key='YOUR_PRODUCTION_API_KEY' data-environment='production' src='https://integration-cdn.toshi.co/3.0/integrations/shopify.js'></script>
    <script type='text/javascript'>var toshiData = { checkout_id: {{ checkout.id }}, customer: { email: {{ checkout.customer.email | json }} }, address: {{ checkout.shipping_address | json }}}</script>
```

** For staging environment **
```
    <script type='text/javascript' defer src='https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js'></script> 
    <script type='text/javascript' src='https://integration-sandbox-cdn.toshi.co/3.0/main.js'></script>
    <script type='text/javascript' id='toshi-modal-script' defer data-api-key='YOUR_STAGING_API_KEY' data-environment='staging' src='https://integration-sandbox-cdn.toshi.co/3.0/integrations/shopify.js'></script>
    <script type='text/javascript'>var toshiData = { checkout_id: {{ checkout.id }}, customer: { email: {{ checkout.customer.email | json }} }, address: {{ checkout.shipping_address | json }}}</script>
```

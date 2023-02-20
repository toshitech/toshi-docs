# TOSHI Integration Guide

## Working Example

[Checkout](https://codesandbox.io/s/toshi-checkout-integration-2qmf5)

[Try-Before-You-Buy](https://codesandbox.io/s/toshi-try-before-you-buy-k0t7p)

## Overview

TOSHI can be implemented in three forms: checkout, try-before-you-buy, and the TOSHI Booker. The checkout and try-before-you-buy products use the client-side Javascript booking API. The checkout product must also schedule orders using the server-side confirmation API. The TOSHI Booker requires no client-side javascript, and can be used to integrate with TOSHI in cases where the checkout page cannot be changed (e.g. Shopify), or when a brand wants to exercise more control over how their orders are distributed for delivery (e.g. a brand can send TOSHI orders that they would like TOSHI to deliver, rather than a customer opting in to a TOSHI delivery).

- [**The client-side Javascript booking API**](#client-side-integration-tutorial-checkout): A UI that can be placed anywhere on your site, but typically is found in checkout, on the product page or in the basket. This is responsible for allowing a customer to select their desired timeslots and services.

- [**The server-side confirmation API**](#server-side-integration): A simple endpoint which serves to confirm payment of an order. This request should also contain additional data about the order, allowing us to provide the best possible service for the customer. **For checkout products only**.

- [**The booker API**](#booker-api): A simple endpoint that receives the order information from a brand. An CSV upload via an SFTP endpoint can be used in lieu of a full API integration if that is preferrable to a brand.

## Example Journey (Checkout)
As a customer lands on your checkout -- if they are within the TOSHI serviceable zone -- they will be presented with a TOSHI shipping option. Upon selecting TOSHI a summary card will appear below the option, which will soon be filled with the details of the customer's scheduled service. Selecting the 'Add Details' button will present a modal overlay, displaying available timeslots and services.

When the customer selects a slot, a shadow order is created within the TOSHI system, which reserves the slot for 30 minutes. An ID/reference of your choice is also submitted (see [brand checkout reference](#brand-checkout-reference) below). This will be used to reconcile the order when the server-side confirmation is submitted.

Once a customer has selected their day, timeslot and services, they will close the modal and proceed to pay for the order. In order to confirm the order within the TOSHI system, a request must be made to our confirmation API containing the aforementioned [brand checkout reference](#brand-checkout-reference) along with the now completed order reference (see [brand order reference](#brand-order-reference)).

**Please note:** In order for us to correctly service orders, we require all products to have a SKU present in your ecommerce system.

## Example Journey (Try-Before-You-Buy)

A customer lands on one of your product pages, they click the TOSHI Try-Before-You-Buy button. The customer is presented with a TOSHI modal and proceed to fill in their details. They may only continue if their address is within the TOSHI serviceble zone. They choose their appointment time and services and confirm their details. No payment is taken and your store is informed a TOSHI order has been placed along with all associated order information. 

**Please note:** In order for us to correctly service orders, we require all products to have a SKU present in your ecommerce system.

## Determining TOSHI Service Elgibility

If you are using an e-commerce platform TOSHI has a plugin for then this is managed by the plugin. If no plugin is available for your platform then you will be required to determine TOSHI eligiblity using our API. 

TOSHI exposes an address eligiblity endpoint to determine whether we can service the client's address. You can use this endpoint to conidtionally display TOSHI as a delivery option at checkout. 

https://app.swaggerhub.com/apis-docs/toshitech/TOSHI-API-V2/1.0.0#/Address/addressEligible


## Client-side integration tutorial (Checkout & Try-Before-You-Buy)

Firstly, our Javascript library must be loaded from our CDN using the following URLs:

```
Production: https://integration-cdn.toshi.co/3.0/main.js
Sandbox: https://integration-sandbox-cdn.toshi.co/3.0/main.js
```

The relevant script should be loaded either just before the closing `</body>` tag, or  The library exposes a `toshi` property on the global window object.

A modal can be instantiated using the `window.toshi.createBookingIntegration` method, passing through the API URL and integration key (both will be supplied to you by TOSHI).

The TOSHI booking modal can be instantiated in one of various modes: 

```typescript
export enum ProductMode {
  Checkout = 'checkout',
  TbybWizard = 'tbyb_wizard',
  TbybCheckout = 'tbyb_checkout',
  TbybButton = 'tbyb_button',
}
```

For checkout: 

```typescript
var modal = window.toshi.createBookingIntegration({
  api: { 
    url: 'https://staging.api.toshi.co',
    key: '<your-toshi-client-api-key>' 
  }
});
```

For try-before-you-buy as a button: 

```typescript
var modal = window.toshi.createBookingIntegration({
  api: { 
    url: 'https://staging.api.toshi.co',
    key: '<your-toshi-client-api-key>' 
  },
  ui: {
    mode: 'tbyb_button'
  }
});
```

Customer details should be set. A list of the required fields can be found [here](#).

```typescript
modal.setFirstName('James');
modal.setLastName('Yorston');

// Calls to the property setters can be chained.
modal.setEmail('james@toshi.co')
  .setPhone('07700900000', 'GB')
  .setAddress({
    addressLine1: '41-42 Foley St',
    addressLine2: "Fitzrovia",
    town: 'London',
    province: 'Greater London',
    postcode: 'W1W7TS',
    country: CountryCodes.GB
  });
```
### Available Country Codes 

Below is a list of available country codes that can be used to specify a customer number.
Note: This is only required for a checkout integration and is an optional field as a best guess will be provided in this mode.

MY IS DZ NG NI XK FR PT ZA KR RO JP HR IE ID IN BR AT PL FI CA US VN TZ DE VI LV AE EC SE CZ KE IT NL CL ES PE TW MT BY BE CH AN AZ ZW ZM YE VG VE UZ UY GB UA UG TC TM TR TN TT TO TG TH TJ SZ SR SD VC LC KN LK SO SI SK SG SL SC RS SN SA ST SM AF WS RW RU RE QA PR PH PY PG PA PS PK OM NO NE NZ NC NP NA MM MZ MA MS ME MN MC MD MX MU MR MQ MH ML MV MW MG MK MO LU LT LI LY LR LS LB LA KG KW KZ JO JE JM CI IL IM IQ HU HK HN HT GY GW GN GT GU GP GD GL GR GI GH GE GM GA PF GF FJ FO FK ET EE GQ SV EG TL CD DO DM DJ DK CY CR CK CG KM CO CN CF KY CV CM KH BI BF BG BN BW BA BO BT BM BJ BZ BB BD BH BS AU AW AM AR AG AI AO AD AS AL VU SB PW PM NU NF KI FM ER CU GG SS TD IR SY

### Async Order Properties

Order properties can be set asynchronously - the TOSHI module will react to this accordingly. 

```typescript
setTimeout(() => {
  modal.setFirstName('James');
}, 3000);
```

### Basket Item Schema

Basket line items should be passed through as an array of objects following the `Item` schema:

```typescript
export interface Address {
  addressLine1: string;
  addressLine2?: string;
  town: string;
  province?: string;
  postcode: string;
  country: string;
}

export interface ItemSize {
  variantSku: string;
  size: string;
  isAvailable?: boolean;
}

export interface Item {
  name: string; // eg. Luna Coat
  quantity: number;
  season?: string; // eg. SS2019
  productCategory?: string; // eg. Coats / Jackets
  productSubcategory?: string; // eg.
  styleSubcategory?: string; // eg. Tall
  // description: string; // eg. A stunning double breasted pin-stripe coat.
  sku: string; // eg. BK10473
  variantSku?: string; // eg. BK10473-38
  // Price of the item represented in the smallest denomination of the currency (eg. cents/pence)
  retailPrice: number; // eg. 1350.00
  promotionPrice?: number; // eg. 1250.00
  promotionId?: string; // eg. SUMMER2019
  markdownPrice?: number; // eg. 1250.00
  finalPrice: number; // eg. 1250.00
  availabilityDate?: Date; // eg. "2019-01-02T09:00:00.001Z"
  pickupLocation?: Address;
  pickupLocations?: Address[];
  sourceLocation?: Address;
  compatibleServices?: string[];
  imageUrl?: string; // eg. https://
  productUrl?: string; // eg. https://
  gender?: string; //
  colour: string; // eg. Black
  size: string; // eg. 38
  subsize?: string; // eg. Tall
  material?: string; // eg. Wool
  description?: string;
  availableSizes?: ItemSize[];
}
```

Example:
```typescript
const createProduct = (name: string, sku: string) => {
  return {
    name: name,
    size: '36',
    sku: sku,
    quantity: 1,
    imageUrl:
      'https://cdn.shopify.com/s/files/1/0006/1495/7171/products/SEQUIN_COLUMN_DRESS_PEARL_FRONT_181x271@2x.jpg?v=1540388474',
    retailPrice: 2000,
    productCategory: 'dresses',
    description:
      'A modern take on sequins that is as clean as it is eye-catching, this style shimmers as you move and is a delight to wear.',
    promotionPrice: 1999.99,
    finalPrice: 1999.99,
    colour: 'red',
    availableSizes: [
      { variantSku: '902-34', size: '34', isAvailable: true },
      { variantSku: '902-38', size: '38' },
      { variantSku: '902-40', size: '40', isAvailable: true }
    ]
  };
};

modal.setProducts([
  createProduct('Paillette Column Dress', '902-36'),
  createProduct('Red Row Dress', '903-36')
]);
```

### Modal Property Setters

The following data setting operations are available on the modal instance:

📝 https://github.com/toshitech/toshi-modal/blob/master/ToshiModal.md

#### Properties

| Property            | Attribute            | Modifiers | Type                                                                         | Default    | Description                                      |
| ------------------- | -------------------- | --------- | ---------------------------------------------------------------------------- | ---------- | ------------------------------------------------ |
| `apiKey`            | `api-key`            |           | `String`                                                                     |            | TOSHI provided api key                           |
| `apiUrl`            | `api-url`            |           | `String`                                                                     |            | TOSHI provided api url                           |
| `bookerFallback`    | `booker-fallback`    |           | `Boolean`                                                                    | "false"    | allow user to use the TOSHI booker as a fallback |
| `integrationActive` | `integration-active` |           | `Boolean`                                                                    | "false"    | enable/disable integration entirely              |
| `isUsingBooker`     |                      | readonly  | `boolean`                                                                    |            |                                                  |
| `modalVisible`      | `modal-visible`      |           | `Boolean`                                                                    | "false"    | open/close the modal                             |
| `mode`              | `mode`               |           | `'checkout'\|'tbyb_wizard'\|'tbyb_checkout'\|'tbyb_button'\|'tbyb-checkout'` | "checkout" | integration mode                                 |
| `onHydrateAgs`      |                      | readonly  | `{ orderId: string \| undefined; orderData: string \| undefined; }`          |

#### Methods

| Method                         | Type                                                                                                  | Description                                                                                                                       |
| ------------------------------ | ----------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| `clearCache`                   | `(clearCurrentState: boolean): Promise<this>`                                                         |                                                                                                                                   |
| `clearSelectedDateTime`        | `(): this`                                                                                            |                                                                                                                                   |
| `handleBooleanAttributeChange` | `(name: string, value: boolean): void`                                                                |                                                                                                                                   |
| `hideModal`                    | `(): this`                                                                                            |                                                                                                                                   |
| `mount`                        | `(container: HTMLElement): void`                                                                      |                                                                                                                                   |
| `mountModalSeparately`         | `(container: HTMLElement, modalContainer: HTMLElement): void`                                         |                                                                                                                                   |
| `onHydrate`                    | `(callback: (args: { orderId: string \| undefined; orderData: string \| undefined; }): void) => this` |                                                                                                                                   |
| `onOrderConfirmed`             | `(callback: (orderId: string): void) => this`                                                         |                                                                                                                                   |
| `onOrderCreated`               | `(callback: (orderId: string): void) => this`                                                         |                                                                                                                                   |
| `onReady`                      | `(callback: (): void) => any`                                                                         |                                                                                                                                   |
| `onShadowOrderCreated`         | `(callback: (orderId: string): void) => this`                                                         |                                                                                                                                   |
| `setAddress`                   | `(address: Address): this`                                                                            | Sets the delivery address for the order.                                                                                          |
| `setBrandCheckoutReference`    | `(reference: string): this`                                                                           | Supply the store order reference (for example quote or basket ID).<br />This is later used when confirming the order via webhook. |
| `setEmail`                     | `(email: string): this`                                                                               | Sets the customer email address for the order.                                                                                    |
| `setFeaturedImage`             | `(url: string): this`                                                                                 |                                                                                                                                   |
| `setFirstName`                 | `(firstName: string): this`                                                                           | Sets the customer first name.                                                                                                     |
| `setInlineErrorMessage`        | `(message: string \| undefined): this`                                                                |                                                                                                                                   |
| `setLastName`                  | `(lastName: string): this`                                                                            | Sets the customer last name.                                                                                                      |
| `setOrderData`                 | `(data: string): this`                                                                                | Sets generic order data.                                                                                                          |
| `setOrderTotal`                | `(price: Price): this`                                                                                | Sets the order total prices.                                                                                                      |
| `setPhone`                     | `(phone: string, countryCode: CountryCodes): this`                                                    | Sets the customer contact number.                                                                                                 |
| `setProducts`                  | `(products: Item[]): this`                                                                            | Provides a list of products and their prices for the customer basket.                                                             |
| `setStore`                     | `(id: number): this`                                                                                  | Sets the store ID for the order.                                                                                                  |
| `showModal`                    | `(): this`                                                                                            |                                                                                                                                   |
| `unmount`                      | `(): void`                                                                                            |                                                                                                                                   |
```

### Store arbitrary JSON data with an Order

```typescript
  setOrderData(json: string)
```

This modal setter can be used to store arbitrary json data with an order for later retrieval and analysis.  The json must be valid and converted to a string using JSON.Stringify. 


### Item Availability Date

TOSHI can be scheduled to activate at a time in the future rather than defaulting to offer the next 3 available days to the end customer. Providing an availability date according to the basket item schema above will instruct the TOSHI availability algorithm to offer dates starting from the supplied availability date.  

The availability date will respect both the date and time part of the provided data (e.g. we won't show delivery times before the time sent in the availabilityDate field). It should be supplied as UTC.

This is useful in a number of scenarios: 

- Your brand has opted to use a drop-ship model with TOSHI where we can service items stored in a warehouse as well as at the store level.

- Your brand offers preorder items. 


### Brand Checkout Reference

The brand checkout reference is a unique string generated by your system which can be used to reconcile the order when the confirmation API call is submitted (for checkout products only). It can be an order ID, a checkout ID, or even a hash.

```typescript
modal.setBrandCheckoutReference('123123');
```

### Brand Order Reference

The brand order reference is the order reference/id/hash that you use internally to identify an order has been placed. This is the order number that the brand and the customer will see in communications. 

This is sent as part of the API request to TOSHI after an order has been placed (for checkout products only). 

See [server-side integration](#server-side-integration).


### Mounting and unmounting the summary card

To mount the TOSHI Summary Card, call `modal.mount()` and pass through the container DOM element. The state belongs to the modal, so this can be performed multiple times for different areas of your checkout and the state will be shared between them. 

```typescript
modal.mount(document.getElementById('main'));
```

To unmount, simply call:
```typescript
modal.unmount(document.getElementById('main'));
```

By default the modal HTML is rendered in the same area as the summary card, to mount the modal to a seperate area of the DOM you can use: 
```typescript
modal.mountModalSeparately(document.getElementById('div-for-summary-card'), document.getElementById('div-for-modal');
```

## Server-side integration

The server-side integration is relatively straightforward, with just a single API request made to TOSHI when the order has been confirmed within your system (ie. the customer has paid for the order)
**This is a requirement only for checkout versions of our product**

## Multiple stores/stock locations
The TOSHI API supports multiple stores and stock locations using one API key. To activate this feature, please contact TOSHI and ask for an additional store or stock location to be set up.

By default, we use a basic algorithm that looks at the location of the customer, and automatically selects the nearest store or stock location to the customer's location. This will not always be the most appropriate location (e.g. in the case with multiple stores, the nearest store might not have the product in stock). In this case, you can send TOSHI the required stock location as part of the API request, and we will ensure that this is set as part of the order creation process.

Before you can send TOSHI the required store or stock location, you need to send TOSHI a unique identified for the store - e.g. for a Covent Garden store, you might use an ID, or a string, e.g. `COVENTGARDEN001`. You must do this for all stores that share the same API key, setting a unique key for each one.

Once you have done this, you are then able to send a `store_reference` attribute in any API call to TOSHI. E.g. when initialising the modal, you can use the `setOrderData` function to add metadata: `{ 'store_reference': 'COVENTGARDEN001' }` to specify the store or stock location that you would like TOSHI to use.

When using the `confirm_store_order` endpoint, you can set an additional parameter `store_reference` in the main body, and the correct store will be set.

## Booker API

This API is still under active development, and is not available for public release yet. Please get in touch if you would like preview access to it, and we will discuss your requirements.

## TOSHI API

The TOSHI API schema details the format of our API requests and responses, it can be found [here](https://app.swaggerhub.com/apis-docs/toshitech/TOSHI-API-V2/1.0.0).

Staging: https://staging.api.toshi.co  
Production: https://api.toshi.co

### API Key Provisioning 

All API requests to TOSHI require either a server or client API key sent in the header of the request. 

TOSHI have two separate levels of authentication, a server API key and a client API key. You will be provided with both keys, for both our test staging environment, as well as for a production environment. 

The server side key is for use in more secure scenarios where we do not want to expose the key on the client side, the client key is used for authentication on less strict endpoints. 

Please see [the API schema](https://app.swaggerhub.com/apis-docs/toshitech/TOSHI-API-V2/1.0.0) for details on the appropriate key to use for each endpoint. 

Please contact TOSHI to be provided with API keys for production and staging environments. 

## Support

For support with any of our services, please contact [engineering@toshi.co](mailto:engineering@toshi.co).

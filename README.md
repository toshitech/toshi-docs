# TOSHI API Integration Information

## Contents
* [API](#api)
  * [Endpoints and performance](#endpoints-and-performance)
  * [Rate limits](#rate-limits)
  * [Changes and release management](#changes-and-release-management)
  * [Versioning](#versioning)
  * [SLAs and performance](#slas-and-performance)
  * [Integration documentation](#integration-documentation)
* [Security](#security)
* [Contacting support](#contacting-support)

## API

### Endpoints and performance

[**https://api.toshi.co/v2/address/eligible**](https://api.toshi.co/v2/address/eligible)

**Response time (last 7 days)**

Mean - 63.5ms
95th percentile - 84.8ms

[**https://api.toshi.co/v2/availability/check\_available**](https://api.toshi.co/v2/availability/check_available)

**Response time (last 7 days)**

Mean - 291ms
95th percentile - 375ms

[**https://api.toshi.co/v2/orders**](https://api.toshi.co/v2/orders)

**Response time (last 7 days)**

Mean - 187ms
95th percentile - 161ms

[**https://api.toshi.co/v2/order/confirm\_store\_order**](https://api.toshi.co/v2/order/confirm_store_order)

**Response time (last 7 days)**

Mean - 318ms
95th percentile - 245ms

### Rate limits

The TOSHI API is rate limited to 300 requests every 2 minutes per IP address, however we are likely to change this in the future to limit based on API key. We will notify any changes on the rate limit in advance of the change.

### Changes and release management

TOSHI's API does not change regularly, however the underlying application is under continuous development. TOSHI operates a CI and CD process so that new changes are automatically deployed to a staging environment once the code is merged and automated tests have passed, and deployed to a production environment multiple times per day.

Please see [Versioning](#versioning) for notices of change and deprecations specific to the API.

### Versioning

TOSHI currently has two versions of API:

**V2**

The current public API version, documentation included in this document.

**V3**

A new standard of API as outlined in our new API guidelines document. We will introduce v3 endpoints for our public endpoints in Q3 this year, but we do not have a deprecation date for the old endpoints at this time.

We will always give at least **6 months notice** for any API deprecations or breaking changes to existing APIs unless there is a security reason for us to make the change sooner. Notice will be sent via email to the technical contact(s) that we store for each brand or partner.

A breaking change would either be a change in behaviour for an existing endpoint, a removal of data from an existing endpoint, a change in response code from an existing endpoint, or a total removal of an endpoint.

### SLAs and performance

TOSHI's application live status is available to view at [https://updown.io/p/prd4c](https://updown.io/p/prd4c)

TOSHI application performance is monitored by Scout APM, with alerts setup for when performance strays outside our usual levels.

The V2 API schema is available at [https://app.swaggerhub.com/apis-docs/toshitech/TOSHI-API-V2](https://app.swaggerhub.com/apis-docs/toshitech/TOSHI-API-V2)

The V3 API schema is available at [https://app.swaggerhub.com/apis-docs/toshitech/TOSHI-API-V3](https://app.swaggerhub.com/apis-docs/toshitech/TOSHI-API-V3)

### Integration documentation

* [General guide](integration_general.md)
* [Ecommerce data flows](ecommerce_data_flows.md)
* [FAQ](integration_faq.md)
* [Shopify](integration_shopify.md)
* [Magento](https://github.com/toshitech/toshi-magento-plugin)
* [Salesforce (Site Genesis)](https://github.com/toshitech/toshi-salesforce-cartridge)
* [Salesforce (SFRA)](https://github.com/toshitech/toshi-salesforce-cartridge-sfra)

Please see [Journey states](journey_states.md) for a mapping of the tracking information that TOSHI can share back via webhooks or CSV/SFTP file upload.

Please note, the integration documentation is gradually being migrated to this Github repo and collection of Markdown files.

## Security

We think about application and data security via multiple strategies:

**Penetration test**

TOSHI performs an annual penetration test across the entire scope of its application, via an independent 3rd party. Reports are available on request.

**Code reviews and continuous integration**

All code that makes it into the TOSHI codebase is subject to a mandatory code review by at least one other team member. For critical infrastructure (e.g. authentication), at least two team members are required to review the code.

The TOSHI codebase is well tested on both the front- and back-end application, and for every code change that is proposed. The full test suite is run automatically as part of the continuous integration process. Linting and vulnerability analysis also happens automatically, and all code is blocked from being merged until all of the above automated tests have passed, and the relevant peers within the team have approved the change.

**Platform-level security**

All authentication into TOSHI's infrastructure platforms requires 2-factor authentication, and no engineers have direct access to any hosted servers, instead having to authenticate via the PaaS platform.

**DDoS**

TOSHI's platforms are protected by Cloudflare's DDoS protection.

**Questionnaires**

At a brand's request, TOSHI will complete cybersecurity questionnaires to confirm compliance with a brand's infosec policies.

**Logging**

All TOSHI's infrastructure logs to a central logging repository. Logs are deleted after 30 days. Sensitive data (e.g. passwords) are not captured within logs.

**Patching and software upgrades**

TOSHI takes advantage of automated package monitoring across the entire application, including for security patches for any software that TOSHI utilises. Package upgrades create an automatic pull request against the relevant application, and are reviewed daily.

**Security incidents**

All software security incidents should be reported to TOSHI in writing via email, to [security@toshi.co](mailto:security@toshi.co). TOSHI will respond to all active incidents within 24 hours.

## Contacting support

Please send any queries to [engineering@toshi.co](mailto:engineering@toshi.co), any security issues should be reported to [security@toshi.co](mailto:security@toshi.co)

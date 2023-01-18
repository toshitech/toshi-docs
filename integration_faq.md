# TOSHI FAQ Modal Integration

Copy the following script into your page (for staging):
`<script defer type="text/javascript" src="https://integration-sandbox-cdn.toshi.co/3.0/faq-modal/main.js"></script>`

Copy the following script into your page (for production):
`<script defer type="text/javascript" src="https://integration-cdn.toshi.co/3.0/faq-modal/main.js">`

Copy the following HTML into the location you would like the modal to appear:
`<div id="toshi-faq-modal"></div>`

Copy the following HTML into the location you would like the user to trigger the modal from:
```
<button onclick="window.toggleToshiFaqModal({location: 'LDN',
                                              service: 'TryBeforeYouBuy',
                                              learnMoreUrl: 'https://toshi-images.s3.eu-west-2.amazonaws.com/marketing/faq-pages/london-faq.html'})">
FAQ modal
</button>
```

The `window.toggleToshiFaqModal()` function can be passed to the onclick event of any element that should trigger the FAQ modal to appear.
This function can be configured with the following properties: location (required), service (required) and learnMoreUrl.

*location* can be passed the values **'LDN'** (for stores in London) or **'NYC'** (for stores in New York).

*service* can be pased the values **'TryBeforeYouBuy'** or **'OnDemand'**.

*learnMoreUrl* is optional and can be passed a page to your own website containing the TOSHI FAQ (see below for integration details). If not provided the LEARN MORE button will open TOSHI's FAQ hosted by us based on the location provided.


# TOSHI FAQ Integration
For a **LONDON** store, copy the following HTML into your page and style as required.

`<iframe id="toshi-faq" src="https://toshi-images.s3.eu-west-2.amazonaws.com/marketing/faq-pages/london-faq.html"></iframe>`

A full integration example can be seen [HERE](https://codesandbox.io/embed/toshi-faq-london-integration-8d1py?fontsize=14).

For a **NEW YORK** store, copy the following HTML into your page and style as required.

`<iframe id="toshi-faq" src="https://toshi-images.s3.eu-west-2.amazonaws.com/marketing/faq-pages/nyc-faq.html"></iframe>`

A full integration example can be seen [HERE](https://codesandbox.io/embed/toshi-faq-new-york-integration-gihtf?fontsize=14).

The FAQ is designed to be responsive and will scale with the size of it's container.

## Styling

Add following style to your style sheet and change as neccesary.

```
#toshi-faq {
  height: 1000px;
  width: 100%;
  border: none;
}
```

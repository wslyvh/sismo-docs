---
description: Request proofs from your user on your React project
---

# zkConnect React: Request

The [zkConnect](https://docs.sismo.io/sismo-docs/what-is-sismo/zkconnect) React package is a wrapper of the zkConnect client package which is a package build on top of the [Sismo Data Vault app](https://docs.sismo.io/sismo-docs/technical-documentation/data-vault-app) (the prover) to easily request proofs from your users.

### Installation

Install the zkConnect React package in your frontend with `npm` or `yarn`:

```bash
# with npm
npm install @sismo-core/zk-connect-react
# with yarn
yarn add @sismo-core/zk-connect-react
```

{% hint style="success" %}
Make sure to have at least v18.15.0 as Node version. You can encounter issues with ethers dependencies if not.
{% endhint %}

## Usage

To integrate zkConnect into your front end, you can use the zkConnect button. Clicking on this button will redirect your user to the Sismo Data Vault app, where they can generate their proof.

After your user generates the proof, they will be automatically redirected back to your app with a response containing the generated proof.

<figure><img src="../../.gitbook/assets/zkConnect.png" alt=""><figcaption><p>zkConnect button</p></figcaption></figure>

To integrate it you have access through the package to the `ZkConnectButton` component.

```typescript
import { ZkConnectButton } from "@sismo-core/zk-connect-react";

<ZkConnectButton 
		//You will need to register an appId in the Factory
    appId={"0x8f347ca31790557391cec39b06f02dc2"}
		//Request proofs from your users for a groupId
		dataRequest={{
          groupId: "0x42c768bb8ae79e4c5c05d3b51a4ec74a"
    }}
		//After user redirection get a response containing his proofs 
    onResponse={async (response) => {
      //Send the response to your server to verify proofs
			//thanks to the @sismo-core/zk-connect-server package
    }}
/>
```

To get the response of your user after his redirection without using the `ZkConnectButton` you can use the `useZkConnect` hook. This allows you to redirect your user to a page other than the page with the button integrated.

```typescript
import { useZkConnect, ZkConnectClientConfig } from "@sismo-core/zk-connect-react";

const config: ZkConnectClientConfig = { appId: "0x8f347ca31790557391cec39b06f02dc2" };

const { response } = useZkConnect({ config });
```

The `useZkConnect` hook also exposes the zkConnect variable which provides access to all the features available in the zkConnect Client package.

```typescript
const { zkConnect } = useZkConnect({ config });
```

Please see the [`zkConnect Client documentation`](https://docs.sismo.io/sismo-docs/technical-documentation/zkconnect/zkconnect-client-request) to see all the possibilities around the zkCOnnect instance.

### Documentation

This package is a wrapper of the zkConnect Client package, which means that all the core concepts such as config, dataRequest, and response are the same as in the client package. If you need to explore these concepts in detail, we advise you to refer to the [@sismo-core/zk-connect-client](https://docs.sismo.io/sismo-docs/technical-documentation/zkconnect/zkconnect-client-request) page, which provides more detailed information.

#### ZkConnectButton

The ZkConnectButton component will allow you to easily request proofs from your user for a groupId he should be a member of.

```typescript
import { ZkConnectButton, ZkConnectClientConfig, ZkConnectResponse } from "@sismo-core/zk-connect-react";

const config: ZkConnectClientConfig = {
	appId: "0x8f347ca31790557391cec39b06f02dc2", 
	devMode: {
		// will use the Dev Sismo Data Vault https://dev.vault-beta.sismo.io
		// active the devMode only in your dev environment 
		enabled: env.name === "DEV", 
		// overrides any group with these addresses
		devAddresses?: [ 
		      "0x123...abc", 
		      "0x456...efa"
		],
		// you can also customize values
		// devAddresses?: {
		//   "0x123...abc": 2,
		//   "0x456...efa": 3
		// },
	}
}

<ZkConnectButton 
	//Request proofs for this groupId
	dataRequest:{{
		groupId: "0x42c768bb8ae79e4c5c05d3b51a4ec74a",
	}}
    	config={config}
	onResponse={async (response: ZkConnectResponse) => {
		//Send the response to your server to verify it
		//thanks to the @sismo-core/zk-connect-server package
	}}
	//boolean to trigger the loading version of the button
	//put it to true while your server verification
	verifying={verifying}
/>
```

**Props**

| Prop                                                                  | Type                                                                                                                                            | Description                                                                                                                                                                             |
| --------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| appId _(optional but must at least provide this appId or a config)_   | string                                                                                                                                          | appId of your zkConnect app. Go on the [Sismo Factory](https://factory.sismo.io/apps-explorer) to register your appId.                                                                  |
| dataRequest _(optional)_                                              | [DataRequestType](https://docs.sismo.io/sismo-docs/technical-documentation/zkconnect/zkconnect-client-request#datarequest)                      | The DataRequest object holds all the information needed to generate the proofs.                                                                                                         |
| config _(optional but must at least provide this config or an appId)_ | [ZkConnectClientConfig](https://docs.sismo.io/sismo-docs/technical-documentation/zkconnect/zkconnect-client-request#zkconnectclientconfig)      | The config allows you to fully customize your zkConnect integration.                                                                                                                    |
| verifying _(optional)_                                                | boolean                                                                                                                                         | This prop allows you to trigger the loading of the button.                                                                                                                              |
| onResponse _(optional)_                                               | (response: [ZkConnectResponse](https://docs.sismo.io/sismo-docs/technical-documentation/zkconnect/zkconnect-client-request#getresponse)) ⇒ void | Return a response that contains the proofs generated by your users that must be sent to your backend and verified thanks to the @sismo-core/zk-connect-server package.                  |
| callbackPath _(optional)_                                             | string                                                                                                                                          | By default, the redirection will send you back to the same page as your button. The callback path props will send your user to another path. See useZkConnect to get the user response. |
| overrideStyle                                                         | React.CSSProperties                                                                                                                             | Override style of the zkConnect                                                                                                                                                         |

**Customization**

If you want to customize the style of your button you can use 3 CSS classes `zkConnectButton`, `zkConnectButtonText`, and `zkConnectButtonLogo`.

An overrideStyle prop is also available on the component which allows you to override the same style as the class zkConnectButton.

Here is an example of a custom button:

```css
//Added in the index.css of a React app

.zkConnectButton {
	background: red !important;
}
.zkConnectButtonText {
	color: blue !important;
}
.zkConnectButtonLogo {
	display: none
}
```

#### useZkConnect

The `useZkConnect` hook makes it easy for you to obtain the response containing the user proof, and it provides access to all functions exposed by the ZkConnect client instance.

```typescript
import { useZkConnect } from "@sismo-core/zk-connect-react";

const { zkConnect, response } = useZkConnect({ config });
```

**Params**

| Param  | Type                                                                                                                                       | Description                                                                          |
| ------ | ------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------ |
| config | [ZkConnectClientConfig](https://docs.sismo.io/sismo-docs/technical-documentation/zkconnect/zkconnect-client-request#zkconnectclientconfig) | The ZkConnectClientConfig allows you to fully customize your zkConnect integration.  |

**States returned**

| State     | Type                                                                                                                                 | Description                                                                                                                                                  |
| --------- | ------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| response  | [ZkConnectResponse](https://docs.sismo.io/sismo-docs/technical-documentation/zkconnect/zkconnect-client-request#getresponse) \| null | The response contains the proofs generated by your users that must be sent to your backend and verified thanks to the @sismo-core/zk-connect-server package. |
| zkConnect | [ZkConnectClient](https://docs.sismo.io/sismo-docs/technical-documentation/zkconnect/zkconnect-client-request)                       | ZkConnect instance with the client configuration.                                                                                                            |

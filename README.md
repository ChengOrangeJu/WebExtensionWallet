# WebExtensionWallet

### 1. Install
This extension is now published on [Chrome web store](https://chrome.google.com/webstore/detail/nasextwallet/gehjkhmhclgnkkhpfamakecfgakkfkco). Wellcome to install and take a try!

We will keep improving this extension, and any suggestions are welcome! 

**Note:** If you need to test local html files, you need to turn on the "Allow access to file URLs" option at extension management page:

![](resources/extension_options.png)


### 2. Brief introduction of using our ExtensionWallet
```
(1) In tab `New-Wallet`, you can create your own wallet, and download the keystore files.
(2) In tab `Send-TX`, you can import your keystore file, and then your account will be stored within the extension.
(3) After your account keyfile is imported, you can send NAS to other account address.
(4) After a transaction is sent, you got the transaction hash shown at the bottom of extension page.
(5) Click the transaction hash in tab `Send-TX` to check transaction status
(6) Another way to check your transaction status is to copy your transaction hash to `check-TX` to view the result.
```

### 3. Instructions on how to use NasExtWallet in your webapp

Now NasExtWallet supports two different ways to communicate with Dapp page.


#### 3.1 Using NebPay SDK

Please refer to Dapp Example [SuperDictionary](https://github.com/15010159959/super-dictionary) to learn how to use this extension.
 
When developing your Dapp page, you can use [NebPay SDK](https://github.com/nebulasio/nebPay) to communicate with ExtensionWallet. Just as the example below.

To call a SmartContract through extensionWallet, you should use [`nebpay.call`](https://github.com/nebulasio/nebPay/blob/master/doc/NebPay_Introduction.md#call) or [`nebpay.simulateCall`](https://github.com/nebulasio/nebPay/blob/master/doc/NebPay_Introduction.md#simulatecall) to send a transaction as below:
```js
nebPay.call(to, value, callFunction, callArgs, {
    qrcode: {
        showQRCode: true
    },
    listener: cbCallDapp //specify a listener to handle the transaction result
});

function cbCallDapp(resp){
    console.log("response: " + JSON.stringify(resp))
}
    
```


#### 3.2 Using `postMessage`

When developing your Dapp page, you can use `postMessage` API to communicate with ExtensionWallet, and use `window.addEventListener` to listen the message answer. Just as the example below.

To call a smart contract function with extensionWallet, you should use `postMessage` to send a message as below:
```js
window.postMessage({
    "target": "contentscript",
    "data":{
        "to": to,
        "value": "0",
        "contract":{  //"contract" is a parameter used to deploy a contract or call a smart contract function
            "function": func,
            "args": para
        }
    },
    "method": "neb_sendTransaction",
}, "*");
```

And then you need to add an `eventlistener` to listen the returned message.
```js
window.addEventListener('message', function(e) {
     console.log("message received, msg.data: " + JSON.stringify(e.data));
     if(!!e.data.data.txhash){
         console.log("Transaction hash:\n" + JSON.stringify(e.data.data.txhash, null, '\t'));
     }
})
```

### 4 how to get account address

It is useful for Dapp to get the current account address in the extension. Here is the explanation on how to achieve this.

#### Method 1:
```js
var userAddress;

function getUserAddress() {
    console.log("********* get account ************")
    window.postMessage({
        "target": "contentscript",
        "data":{
        },
        "method": "getAccount",
    }, "*");
}
// listen message from contentscript
window.addEventListener('message', function(e) {
    // e.detail contains the transferred data (can
    console.log("received by page:" + e + ", e.data:" + JSON.stringify(e.data));
    if (!!e.data.data && !!e.data.data.account) {
        userAddrerss = e.data.data.account;
    }
})

```

#### Method 2:
A module `NasExtWallet` is injected to your page if NasExtWallet is installed, then you can use the code below to get user account: 
```js
var userAddress;

NasExtWallet.getUserAddress(function(addr){
    userAddress = addr;
    console.log("user address is : " + addr)
})

```


### example page
And you can use `example/TestPage.html` to take a test.



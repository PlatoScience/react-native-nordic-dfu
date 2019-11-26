# react-native-nordic-dfu [![npm version](https://badge.fury.io/js/react-native-nordic-dfu.svg)](https://badge.fury.io/js/react-native-nordic-dfu) [![CircleCI](https://circleci.com/gh/Pilloxa/react-native-nordic-dfu.svg?style=svg)](https://circleci.com/gh/Pilloxa/react-native-nordic-dfu) [![Known Vulnerabilities](https://snyk.io/test/github/pilloxa/react-native-nordic-dfu/badge.svg)](https://snyk.io/test/github/pilloxa/react-native-nordic-dfu)

This library allows you to do a Device Firmware Update (DFU) of your nrf51 or
nrf52 chip from Nordic Semiconductor. It works for both iOS and Android.

For more info about the DFU process, see: [Resources](#resources)

## Installation

Add the following to your package.json dependencies:

"react-native-nordic-dfu": "rodrigosimoesrosa/react-native-nordic-dfu"

```bash
npm install --save react-native-nordic-dfu
react-native link react-native-nordic-dfu
```

### Minimum requirements

This project has been verified to work with the following dependencies, though other versions may work as well.

| Dependency | Version |
|-|-|
| React Native | 0.59.4 |  
| XCode | 10.2 |
| Swift | 5.0 |
| CocoaPods | 1.6.1 |
| Gradle | 5.3.1 |

### iOS

The iOS version of this library has native dependencies that need to be installed via `CocoaPods`, which is currently the only supported method for installing this library. (PR's for alternative installation methods are welcome!)

Previous versions supported manual linking, but this was prone to errors every time a new version of XCode and/or Swift was released, which is why this support was dropped. If you've previously installed this library manually, you'll want to remove the old installation and replace it with CocoaPods.

#### CocoaPods

Add the following to your `Podfile`

```ruby
target "YourApp" do
  ...
  
  use_frameworks!
  pod "react-native-nordic-dfu", path: "../node_modules/react-native-nordic-dfu"

  ...
end
```

and in the same folder as the Podfile run

```bash
pod install
```

Since there's native Swift dependencies you need to set which Swift version your project complies with. If you haven't already done this, open up your project with XCode and add a User-Defined setting under Build Settings: `SWIFT_VERSION = <your-swift-version>`.

#### Bluetooth integration

This library needs access to an instance of `CBCentralManager`, which you most likely will have instantiated already if you're using Bluetooth for other purposes than DFU in your project.

To integrate with your existing Bluetooth setup, call `[RNNordicDfu setCentralManagerGetter:<...>]` with a block argument that returns your `CBCentralManager` instance.

If you want control over the `CBCentralManager` instance after the DFU process is done you might need to provide the `onDFUComplete` and `onDFUError` callbacks to transfer back delegate control.

The example project shows how this may be done.

## API

<!-- Generated by documentation.js. Update this documentation by updating the source code. -->

### startDFU

Starts the DFU process

Observe: The peripheral must have been discovered by the native BLE side so that the
bluetooth stack knows about it. This library will not do a scan but only
the actual connect and then the transfer. See the example project to see how it can be
done in React Native.

**Parameters**

-   `obj` **[Object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object)**
    -   `obj.deviceAddress` **[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)** The `identifier`\* of the device that should be updated
    -   `obj.deviceName` **[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)** The name of the device in the update notification (optional, default `null`)
    -   `obj.filePath` **[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)** The file system path to the zip-file used for updating

\* `identifier` — MAC address (Android) / UUID (iOS)

**Examples**

```javascript
import { NordicDFU, DFUEmitter } from "react-native-nordic-dfu";

NordicDFU.startDFU({
  deviceAddress: "C3:53:C0:39:2F:99",
  name: "Pilloxa Pillbox",
  filePath: "/data/user/0/com.nordicdfuexample/files/RNFetchBlobTmp4of.zip"
})
  .then(res => console.log("Transfer done:", res))
  .catch(console.log);
```

Returns **[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)** A promise that resolves or rejects with the `deviceAddress` in the return value

### DFUEmitter

Event emitter for DFU state and progress events

**Examples**

```javascript
import { NordicDFU, DFUEmitter } from "react-native-nordic-dfu";

DFUEmitter.addlistener("DFUProgress",({percent, currentPart, partsTotal, avgSpeed, speed}) => {
  console.log("DFU progress: " + percent +"%");
});

DFUEmitter.addListener("DFUStateChanged", ({state}) => {
  console.log("DFU State:", state);
})
```

## Example project

Navigate to `example/` and run

```bash
npm install
```

Run the iOS project with

```bash
react-native run-ios
```

and the Android project with

```bash
react-native run-android
```

## Development

PR's are always welcome!

## Resources

-   [DFU Introduction](http://infocenter.nordicsemi.com/topic/com.nordic.infocenter.sdk5.v11.0.0/examples_ble_dfu.html?cp=6_0_0_4_3_1 "BLE Bootloader/DFU")
-   [Secure DFU Introduction](http://infocenter.nordicsemi.com/topic/com.nordic.infocenter.sdk5.v12.0.0/ble_sdk_app_dfu_bootloader.html?cp=4_0_0_4_3_1 "BLE Secure DFU Bootloader")
-   [How to create init packet](https://github.com/NordicSemiconductor/Android-nRF-Connect/tree/master/init%20packet%20handling "Init packet handling")
-   [nRF51 Development Kit (DK)](http://www.nordicsemi.com/eng/Products/nRF51-DK "nRF51 DK") (compatible with Arduino Uno Revision 3)
-   [nRF52 Development Kit (DK)](http://www.nordicsemi.com/eng/Products/Bluetooth-Smart-Bluetooth-low-energy/nRF52-DK "nRF52 DK") (compatible with Arduino Uno Revision 3)

## Sponsored by

[![pilloxa](https://pilloxa.com/images/pilloxa-round-logo.svg)](https://pilloxa.com)

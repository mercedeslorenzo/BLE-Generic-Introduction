← [Back to Menu](https://telinkgithub.github.io/Telink/ "Menu")
![header-telink](https://i.imgur.com/5kRG6CF.jpg)

826x and 825x family chipset are both SIG certified BLE device, you can build most of the BLE application on top of Telink BLE stack and SDK. In our SDK we've created a feature_test, driver_test and RemoteControlUnit (RCU) to demonstrate the capability of Telink BLE STACK and on top of the hardware, you can use it as the baseline to create your own application.

## HCI testing

### Feature_test

You can refer to 826x_feature_test/app_config.h TEST_XXX to understand what features demonstration we've supported and you could expand your testing/feature requirement based on this.

Here explains some commonly used features and APIs for reference, you can always refer to [Developer Handbook](http://wiki.telink-semi.cn/tools_and_sdk/BLE_SDK/826x_SDK/Handbook.zip) for further detail of each features and interface mentioned.

__TEST_ADVERTISING_ONLY__

* This enables the device as advertiser and sends the NON_CONNECT and INDIRECT ADV packets
* You'll need to setup the advertising parameters and then enable the advertising function

```
bls_ll_setAdvParam_setAdvParam(...)
bls_ll_setAdvEnable(1)
```

__TEST_SCANNING_ONLY__

* This enables the device as scanner
* If the print function is enabled, it prints the advertising report, otherwise i'll dump to - AA_advRpt[]
* You'll need to setup the scanning parameters and then enable the scan function

blc_ll_setScanParameter(...);
blc_ll_setScanEnable(...);

__TEST_ADVERTISING_SCANNING_IN_CONN_SLAVE_ROLE__

* This a combination feature test that enables the fundamental Slave function and also including the Advertising/Scanning as well, you can use this as the baseline to extend your application
* You'll need to setup the advertising parameters, scanning parameters and then enable the Connection Salve Role to handle the connection request and maintain the connection resource.

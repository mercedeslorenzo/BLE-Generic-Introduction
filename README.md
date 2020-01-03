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

```
blc_ll_setScanParameter(...);
blc_ll_setScanEnable(...);
```

__TEST_ADVERTISING_SCANNING_IN_CONN_SLAVE_ROLE__

* This a combination feature test that enables the fundamental Slave function and also including the Advertising/Scanning as well, you can use this as the baseline to extend your application
* You'll need to setup the advertising parameters, scanning parameters and then enable the Connection Salve Role to handle the connection request and maintain the connection resource.

```
bls_ll_setAdvParam(...);
bls_ll_setAdvEnable();
blc_ll_addAdvertisingInConnSlaveRole();
blc_ll_setAdvParamInConnSlaveRole(…);
```

* And then you can register to handle the connection/disconnect event

```
bls_app_registerEventCallback(BLT_EV_FLAG_CONNECT, &task_connect);
bls_app_registerEventCallback(BLT_EV_FLAG_TERMINATE, &task_terminate);
```


### Driver_test

__HW_TIMER__

This demonstrates all 3 hardware timers and configure them into different timing, once the timer expired, the irq_handler() will callback your ISR function.

```
reg_irq_mask |= FLD_IRQ_TMR0_EN;
reg_tmr0_tick = 0; //clear counter
reg_tmr0_capt = 10 * CLOCK_SYS_CLOCK_1MS;
reg_tmr_sta = FLD_TMR_STA_TMR0; //clear irq status
reg_tmr_ctrl |= FLD_TMR0_EN;  //start timer

irq_enable();
```

Below is the ISR handler sample, to recognize the timer source, and record the counter.

```
if(reg_tmr_sta & FLD_TMR_STA_TMR0){
	reg_tmr_sta = FLD_TMR_STA_TMR0; //clear irq status
	timer0_irq_cnt ++;
	DBG_CHN0_TOGGLE;
}
```

From the sample test, you can see the hardware timer configuration and how do you recognize the timer interrupt source so that you can handle it accordingly.


__GPIO_IRQ__

This demonstrates hardware gpio interrupt function. You can see the ISR callback when the interrupt criteria is met.

```
TBD
```

Below is the ISR handler sample, to recognize the gpio interrupt source, and record the counter and toggle the GPIO so that you can probe the signal as well.

```
if(reg_irq_src & FLD_IRQ_GPIO_EN){
	reg_irq_src = FLD_IRQ_GPIO_EN;

	gpio_irq_cnt ++;
	DBG_CHN0_TOGGLE;
	gpio_toggle(GPIO_LED1_PIN);
}
```


__UART__

This driver test could demonstrate either the UART DMA or UART GPIO functionality depends on the flag, you can run the testing to see how do we transmit and receive the char through the UART interface.

```
#define UART_DMA_MODE_EN     1  // 1:dma mode, 0: not dma mode
```

By default, the test is running in UART DMA mode, this configures the baudrate, tx/rx buffer, and enables the GPIO pin as UART function.

For Not DMA mode, it will configure the controller, but not instead of using DMA to tx/rx data, it'll be a polling base for each char tx/rx.

```
For DMA:
	CLK16M_UART115200;
	uart_RecBuffInit();
	uart_txBuffInit();
	UART_GPIO_CFG_PC2_PC3();

For Not-DMA:
	uart_Init();
	uart_notDmaModeInit();
	UART_GPIO_CFG_PC2_PC3();

irq_enable();
```


And once the irq enabled, and the test main_loop will start sending the data and wait for receiving as well, you can refer to app_uart.c/app_uart_test_irq_proc() for further details.

__I2C__

__SPI__

__ADC__

__PWM__


← [View the Project on GitHub](https://github.com/TelinkGithub/BLE-Generic-Introduction)


![footer-telink](https://telinkgithub.github.io/Assets/General/footer.jpg)

# bootloader\_secure\_ble example in Nordic nRF5 SDK 14.2.0, with support on softdevice s140\_nrf52840\_6.0.0-6.alpha

This example is based on the bootloader example bootloader\_secure\_ble of Nordic nRF5 SDK 14.2.0, which is ported to softdevice s140\_nrf52840\_6.0.0-6.alpha. Functionalities are identical to the original bootloader example from SDK 14.2.0.

### Features

  - Support both nRF52832 and nRF52840
  - Support SES, Keil5, GCC and IAR compilers

You can also:
  - Add bonded link support by adding NRF\_DFU\_BLE\_REQUIRES\_BONDS=1 to compiler options. (Note: In ble\_app\_buttonless\_dfu, you need to change NRF\_DFU\_BLE\_BUTTONLESS\_SUPPORTS\_BONDS=1 in sdk\_config.h.)
  - ...etc.

### Motivations
> Recently, softdevice s140\_nrf52840\_6.0.0-6.alpha has been received, but there
> has been no support in SDK 14.2.0. The GitHub example [nrf52-ble-app-uart-long-range](https://github.com/NordicPlayground/nrf52-ble-app-uart-long-range)
> in [Nordic Playground](https://github.com/NordicPlayground) allows for development using this version of softdevice. This
> project allows for update to newer versions of softdevice, bootloader and applications.
> This way, when the official version of s140 is released, you can upgrade your field-trial
> products with this full feature protocol stack.



### Compilation
This example requires [SDK 14.2.0](http://developer.nordicsemi.com/nRF5_SDK/nRF5_SDK_v14.x.x/) to compile.

In the example directory of SDK 14.2.0, clone the repository.

```sh
$ git clone https://github.com/pkchan/SDK14.2.0_s140_nrf52840_6.0.0-6.alpha_bootloader_secure_ble.git
$ nrfutil keys generate priv_key.pem
$ nrfutil keys display --key pk --format code priv_key.pem > dfu_req_handling/dfu_public_key.c
```

In the project directory, generate signing key.

```sh
$ cd SDK14.2.0_s140_nrf52840_6.0.0-6.alpha_bootloader_secure_ble
$ nrfutil keys generate priv_key.pem
$ nrfutil keys display --key pk --format code priv_key.pem > dfu_req_handling/dfu_public_key.c
```

Choose your compiler. For example, Keil5

```sh
$ uv4 -r -j0 bootloader_secure_ble/pca10056/arm5_no_packs/secure_dfu_ble_s140_pca10056.uvprojx
$ cp bootloader_secure_ble/pca10056/arm5_no_packs/_build/nrf52840_xxaa_s140.hex bootloader.hex
```

### Generate bootloader settings
Suppose you have checked out the GitHub example [nrf52-ble-app-uart-long-range](https://github.com/NordicPlayground/nrf52-ble-app-uart-long-range) in [Nordic Playground](https://github.com/NordicPlayground) and have compiled it. You can generate bootloader settings so that softdevice, bootloader and application can work as a system. Once it is generated, you need to shift the start address so that it fits nRF52840.

```sh
$ nrfutil settings generate --family NRF52 --application app.hex --application-version 1 --bootloader-version 1 --bl-settings-version 1 bootloader_settings.hex
$ objcopy -I ihex -O binary bootloader_settings.hex bootloader_settings.bin
$ objcopy -I binary -O ihex --change-address=0xFF000 bootloader_settings.bin bootloader_settings.hex
$ rm bootloader_settings.bin
```

where app.hex is the application HEX file, and bootloader\_settings.bin is the generated settings file.

### Running as a system
The followings commands will program softdevice, bootloader, bootloader settings and application to nRF52840.

```sh
$ nrfjprog -f NRF52 --eraseall
$ nrfjprog -f NRF52 --program s140_nrf52840_6.0.0-6.alpha_softdevice.hex
$ nrfjprog -f NRF52 --program bootloader.hex
$ nrfjprog -f NRF52 --program bootloader_settings.hex
$ nrfjprog -f NRF52 --program app.hex
$ nrfjprog -f NRF52 --reset
```

### Generate DFU package
You can follow the instructions in [Nordic Infocenter](http://infocenter.nordicsemi.com/topic/com.nordic.infocenter.sdk5.v14.2.0/ble_sdk_app_dfu_bootloader.html?cp=4_0_0_4_4_0_3#lib_dfu_image) to generate DFU package.Regarding the command line parameters --sd-req and --sd-id, the value for s140\_nrf52840\_6.0.0-6.alpha is 0xFFFE.

As example of command is:

```sh
$ nrfutil pkg generate --hw-version 52 --application-version 0x02 --application app_new.hex --sd-id 0xFFFE --key-file priv_key.pem dfu_package.zip
```

Note: application version must be larger than the current one. Before, 0x01. After 0x02.

### Others

| Tools | Link |
| ------ | ------ |
| nRF52840 Tools | [Available on Nordic 52840 download page.](http://www.nordicsemi.com/eng/Products/nRF52840#Downloads) |
| nRF52832 Tools | [Available on Nordic 52832 download page.](http://www.nordicsemi.com/eng/Products/Bluetooth-low-energy/nRF52832#Downloads) |
| nRF Util | [Available on Nordic Infocenter.](http://infocenter.nordicsemi.com/topic/com.nordic.infocenter.tools/dita/tools/nrfutil/nrfutil_intro.html?cp=5_5) |



### Development

For any contribution, please kindly fork, change and send pull request.

If there are any changes required to SDK files, please:
  - Put all modifications in directory SDK.mod. Create the directory if it does not exist.
  - Make a clean copy of the file from the SDK, commit and then make changes, in order for others to understand your modifications.

License
----

As stated in the header of each c/h file:
 
Copyright (c) 2014 - 2017, Nordic Semiconductor ASA

All rights reserved.

Redistribution and use in source and binary forms, with or without modification,
are permitted provided that the following conditions are met:

1. Redistributions of source code must retain the above copyright notice, this
    list of conditions and the following disclaimer.
 
2. Redistributions in binary form, except as embedded into a Nordic
   Semiconductor ASA integrated circuit in a product or a software update for
   such product, must reproduce the above copyright notice, this list of
   conditions and the following disclaimer in the documentation and/or other
   materials provided with the distribution.

 3. Neither the name of Nordic Semiconductor ASA nor the names of its
    contributors may be used to endorse or promote products derived from this
    software without specific prior written permission.
 
 4. This software, with or without modification, must only be used with a
    Nordic Semiconductor ASA integrated circuit.
 
 5. Any software provided in binary form under this license must not be reverse
    engineered, decompiled, modified and/or disassembled.
 
 THIS SOFTWARE IS PROVIDED BY NORDIC SEMICONDUCTOR ASA "AS IS" AND ANY EXPRESS
 OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES
 OF MERCHANTABILITY, NONINFRINGEMENT, AND FITNESS FOR A PARTICULAR PURPOSE ARE
 DISCLAIMED. IN NO EVENT SHALL NORDIC SEMICONDUCTOR ASA OR CONTRIBUTORS BE
 LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR
 CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE
 GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION)
 HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT
 LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT
 OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.




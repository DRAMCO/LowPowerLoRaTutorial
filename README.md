# Tutorial: Low Power IoT

## Registering to the TTN
The Things Network is a global, open and crowd-sourced Internet of things data network. Wireless communication is provided by the LoRaWAN technology. In this tutorial we will use the existing network provided by TTN users.

https://account.thethingsnetwork.org/register

After registering, you can begin creating your own application.

## Making an Application
Navigate to your applications tab (https://console.thethingsnetwork.org/applications) and add a new application. Every application on the network needs an unique identifier. For this tutorial you can opt to choose the following format for your application ID: `low-power-sensor-tutorial-firstname-lastname`. Now, add your application. Through the application you van connect to The Things Network. One way is to use the low-level APIs via MQTT or HTTP. Another is possibility is to use 'Integrations'. They provide an abstraction from the code. An example of integration is to forward messages to some webhook, i.e. HTTP Integration.  

### Storing your Sensor Data
In this tutorial, we will make use of the database integration. To add an integration, navigate to the Integrations page in your application page. Now, you can add the Data Storage integration. That's it! You have created a database where all your device data is stored.

### Adding Devices to your Application
Before we can store sensor data we need to add devices to our application.
Again, a unique id is required to uniquely identify your node in your application. So choose a device ID. Therafter, the EUI of the device needs to be filled in. Make your life easier and just let the TTN worry about generating that field by clicking on the *'shuffle'* icon on the left. The same applies for the app key. The device EUI allows you devices to be uniquely addressable in the network. The `AppKey` will be used to encrypt the communication between your device and your application.

After adding your device you will see an overview of your device. One of the parameters is the activation method. This method describes how your device will connect to the network. In Over-the-Air Activation (OTAA) mode, devices first perform a join-procedure with the network, during which a dynamic `DevAddr` is assigned and security keys --an application session key `AppSKey` and a network session key `NwSKey`-- are negotiated with the device. With Activation by Personalization (ABP) the `DevAddr` and security keys are hardcoded in the device.

#### More Information on Security Keys
These two session keys (`NwkSKey` and `AppSKey`) are unique per device, per session. If you dynamically activate your device (`OTAA`), these keys are re-generated on every activation. If you statically activate your device (ABP), these keys stay the same until you change them.


The network session key (`NwkSKey`) is used for interaction between the Node and the Network. This key is used to check the validity of messages (MIC check).

The application session key (`AppSKey`) is used for encryption and decryption of the payload. The payload is fully encrypted between the Node and the Handler component of The Things Network (which you will be able to run on your own server). This means that nobody except you is able to read the contents of messages you send or receive.

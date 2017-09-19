# Low power wireless communication technologies for connecting embedded sensors in the IoT -- A journey from fundaments to hands-on.

## Abstract
In this hands-on session the attendees will experience the development of a wireless sensor node. More specifically, how low-power operation can be achieved by clever utilization of the available resources. Such design choices include: selecting the right wireless technology, duty-cycled operation, hardware acceleration, etc.

The participants will experiment with a custom LoRa-based sensor, built with a [Semtec SX1272](http://www.semtech.com/wireless-rf/rf-transceivers/sx1272/) Radio chip and an [EFM32 Cortex M0](https://www.silabs.com/products/mcu/32-bit/efm32-happy-gecko) processor. The way operations are being performed on the node can be customized in the [Silabs Simplicity Studio IDE](https://www.silabs.com/products/development-tools/software/simplicity-studio). The effect of these changes on the energy consumption can be observed through the IDE’s built-in energy profiler.

In the end, the lecturers will have shown that the design of true low-power wireless sensors requires a thoughtful design. Both software and hardware need to work seamlessly together within the boundaries of a specific application.

## Overview

## Registering to the TTN
The Things Network is a global, open and crowd-sourced Internet of things data network. Wireless communication is provided by the LoRaWAN technology. In this tutorial we will use the existing network provided by TTN users.

![The Things Network network](https://www.thethingsnetwork.org/wiki/uploads/TTN-Overview.jpg)

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


### Edit Payload Format
Instead of transmitting our sensor values every measurement, we cumulate the data and transmit it when our buffer is full. In this way, we will optimise the power consumption of our IoT node.

We need to distinguish between different sensor measurements. This can be done through the application port. This field in the LoRaWAN packet is used by the MAC layer to _XXXXXXXXXXXXXXXXXXXX_. The values 0 to 255 can be freely used. Hence, we will make use of the app port to determine which type of data the packet carries.

In our application the following structure is used:

| App Port | Sensor Type | Payload Type | Bytes per Measurement |
|:--------:|:-----------:|:------------:|:---------------------:|
|     1    | temperature | Array        | 2                     |
|          |             |              |                       |
|          |             |              |                       |


Thus, the payload first needs to be decoded before it can be stored in our database. This can be done by using the build-in `Payload Formats` function in your application. We will decode our own `custom` payload format.

The following code will act as the decoder:
```javascript
function Decoder(bytes, port) {
  // make an empty decoded object
  // after decoding the bytes received from the device
  // this object will contain a payload and a type (i.e. sensor type)
  var decoded = {};

  switch(port){
    case 1 :
      // if the app port is 1 we expect to receive an array of temperature data
      decoded.payload = toSignedShortArray(bytes);
      decoded.type = "temp";
      break;
    case 2:
      break;
  }

  return decoded;
}

function toSignedShortArray(bytes){
  var data_size = 2; // bytes per measurement, in our case 2 bytes is one measurement
  var num_byte_el = bytes.length;
  var num_readings = parseInt(num_byte_el/data_size);
  var readings = [];
  var offset = 0;

  for(var reading_id = 0; reading_id<num_readings; reading_id++){
    var value = ( ( (bytes[reading_id+offset] << 8) | bytes[reading_id+offset+1]) << 16) >> 16;
    readings.push(value);
    offset += data_size-1;
  }


  return readings;

}
```

So now we have ensured our data coming from our devices are stored in a database. We also made sure our device data was first decoded based on the payload format described above. We can finally begin with developing our IoT node!


## Developing our IoT Node

Some HW description + beweegredenen.

### Commissioning the Device


### Making it low power

#### Accumulate Sensor Data
#### Hardware Encryption vs Software Encryption
#### Go To Sleep

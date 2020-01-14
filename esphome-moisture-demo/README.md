# ESPHome Moisture Sensor Demo

This lightning talk shows how dead simple it is to build a network connected soil moisture sensor without writing a line of code (just some yaml config).

## Hardware Required

* ESP32 Dev Board (Typically $3-$5 AUD) ([AliExpress](https://www.aliexpress.com/item/32910153161.html?spm=a2g0o.productlist.0.0.554c30e5xlkSQn&algo_pvid=4ebb75f4-06d2-4862-8c4f-66dc551f318c&algo_expid=4ebb75f4-06d2-4862-8c4f-66dc551f318c-0&btsid=a99d049a-1a8f-4e67-9f86-330377177d74&ws_ab_test=searchweb0_0,searchweb201602_8,searchweb201603_55)) OR ESP8266 Dev Board (Typically $4-$8AUD) ([AliExpress](https://www.aliexpress.com/item/32965931916.html?spm=a2g0o.productlist.0.0.554c30e5xlkSQn&algo_pvid=4ebb75f4-06d2-4862-8c4f-66dc551f318c&algo_expid=4ebb75f4-06d2-4862-8c4f-66dc551f318c-1&btsid=a99d049a-1a8f-4e67-9f86-330377177d74&ws_ab_test=searchweb0_0,searchweb201602_8,searchweb201603_55))
* Capacitive Soil Moisture Sensor ([AliExpress](https://www.aliexpress.com/item/32863717622.html?spm=a2g0o.productlist.0.0.5f5f131bMfGXlk&algo_pvid=1db2b2e1-fb1e-40f4-be42-d8464678f3c9&algo_expid=1db2b2e1-fb1e-40f4-be42-d8464678f3c9-2&btsid=98f33d64-bdbd-4f74-be45-fb8fa19d940b&ws_ab_test=searchweb0_0,searchweb201602_8,searchweb201603_55))
* Male to Female Breadboarding Jumper Wires ([AliExpress](https://www.aliexpress.com/item/33046431884.html?spm=a2g0o.productlist.0.0.437679eehYfLvP&algo_pvid=37ff7036-823a-48b5-a63a-d530f55bac94&algo_expid=37ff7036-823a-48b5-a63a-d530f55bac94-4&btsid=46167faa-c253-42cf-b551-6bd9b048bfed&ws_ab_test=searchweb0_0,searchweb201602_8,searchweb201603_55)). To connect moisture sensor to ESP32/ESP8266 dev board

**note**: _AliExpress links provided for reference only - you will probably find better prices by searching again._


## Software Required

* [ESPHome](https://esphome.io/): ESPHome is a system to control your ESP8266/ESP32 by simple yet powerful configuration files and control them remotely through Home Automation systems. [Installation guide](https://esphome.io/guides/getting_started_command_line.html)
* (optional) [Home Assistant](https://www.home-assistant.io/): Open source home automation that puts local control and privacy first. Powered by a worldwide community of tinkerers and DIY enthusiasts. Perfect to run on a Raspberry Pi or a local server. [Installation guide](https://www.home-assistant.io/docs/installation/)

## Provided Configuration

The example [`moisture.yaml`](moisture.yaml) file provides a simple configuration to get you up and running with a soil moisture sensor on the aformentioned ESP8266 development board.

Simply replace the wifi details with that of your own, and hook up the soil moisture sensor to your dev board and you should be good to go.

### Wiring

| Moisture Sensor Wire | Purpose | Pin |
| --- | --- | --- |
| Red | Power to sensor | 3v3 |
| Black | sensor ground | GND |
| Yellow | sensor signal | A0 |


## Calibration

Most moisture sensors will output signal voltage in similar ranges to the provided config, but it can't hurt to calibrate it your self.

Comment out the `lambda` filter in the `filters` section of the config. We want to inspect the raw voltage of the sensor to perform calibration.

```yaml
sensor:
  - platform: adc
    pin: A0
    unit_of_measurement: "%"
    accuracy_decimals: 0
    icon: "mdi:flower-outline"
    filters:
      # Calibrate analog sensor
      # - lambda: |-
      #     if (x > 0.64) {
      #       return 0;
      #     } else if (x < 0.32) {
      #       return 100;
      #     } else {
      #       return (0.64-x) / (0.64-0.32) * 100.0;
      #     }
      # Average/smooth values using moving average filter
      # - sliding_window_moving_average:
      #     window_size: 15
      #     send_every: 15
    name: "Soil Moisture Level"
    update_interval: 20s
```

Ensure your sensor is completely dry and in the air. Obviously air humidity will affect the calibration so try to perform the calibration on a _normal_ day.

Take a reading of the voltage when the sensor is in the air. Then subsequently put the sensor in a glass of water and take note of the voltage reading.

In the above `filter` lambda, replace `0.64` with the value you took for the _dry_ measurement and replace `0.32` with the value you took as the _wet_ measurement. Uncomment the filter, and upload & run.


## Compiling / Running

You will need to perform the initial/first flash using a USB cable connected between your computer and your ESP8266/ESP32. Once the initial flash is done, you can take advantage of OTA (Over the air) flashing.

### Compile & deploy & watch logs

```
esphomeyaml moisture.yaml run
```

### View logs without deploying

```
esphomeyaml moisture.yaml logs
```

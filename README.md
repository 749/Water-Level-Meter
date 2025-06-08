# Plant Water Level Meter

A simple sensor PCB to measure the level of water capacitively.

![Video of the sensor descending into water with the measurement visible](./video/teaser.gif)

Full video at: https://github.com/749/Water-Level-Meter/raw/refs/heads/main/video/teaser.mp4

The sensor is capable of measuring the depth it is inserted into the water with sub-millimeter precision.

## Known limitations

- Due to the way this sensor works it is also affected by humidity in the air. Thus a cross-calibration between air moisture and depth of submersion is needed.
- Your finger or other water-containing substances will also be detected. Essentially this is the same technology as a capacitive touchscreen. Also the reason I hold the entire assembly by the cable and not by the sensor.
- This sensor could measure other liquids, but its efficacy depends on the Dielectric constant of that material. Tap-water is one of the highest commonly found at around 80.

## Example

This is the ESPHome code used in the video:

```yaml
esphome:
  name: plant-water-level-meter-3
  friendly_name: Balkon Blumenkasten

# WiFi and MQTT configuration omitted

status_led:
  pin:
    number: GPIO2
    inverted: True

output:
  - platform: gpio
    pin: GPIO17
    id: sensor_enable

switch:
  - platform: output
    name: "Enable Watersensor"
    output: sensor_enable
    restore_mode: ALWAYS_ON


sensor:
  - platform: pulse_meter
    pin: GPIO16
    name: "Watersensor Pulses"
    internal_filter: 10us
    internal_filter_mode: EDGE
    unit_of_measurement: mm
    filters:
      - calibrate_polynomial:
          degree: 2
          datapoints:
            - 4700000 -> 0.0
            - 3100000 -> 50.0
            - 2400000 -> 90.0
      - sliding_window_moving_average:
          window_size: 100
          send_every: 25
```

# ESPHome Diffuser

A ESPHomified aroma diffuser to work as an controllable humidifier through Home Assistant

https://user-images.githubusercontent.com/30476405/200193576-415818a4-d338-4237-9c56-8fed2da2a92a.mp4

# Problem

My partner, [Amanda Lohne](https://amandalohne.com/), is making an [IKEA Greenhouse](https://www.reddit.com/r/IkeaGreenhouseClub/) with an IKEA FABRIKÖR cabinet. This cabinet will be sealed off to maintain high levels of moisture. To create the moisture, she's bought an aroma diffuser which will be filled with water. We could build a mist maker ourselves (check out the [theory section](https://github.com/petrepa/ESPHome-Diffuser/blob/main/README.md#theory)), but we saved ourselves some work and also got a ready made case and water container with a [store bought diffuser](https://www.ebay.com/itm/134066518332).

The problem is that this diffuser only has two modes:
- One button press: always on
- Hold button for 3 seconds while on: on/off interval of 10 seconds

Since we want to be able to set the humidity at a specific level, this is not sufficient. 

The diffuser also has a warm or cool light which is controlled by a single button with this flow:
```mermaid
  graph LR;
      A[Off] -->|Btn press| B[Cool];
      B -->|Btn press| C[Warm];
      C -->|Btn press| A;
```

# Solution

I therefore frankensteined it and added a Wemos D1 Mini to hijack the circuit board. Soldered a wire to the positive connection point of each button.

| Wemos Pin | Connection point on diffuser PCB       | Comments  |
|-----------|----------------------------------------|-----------|
| D5        | Mist button                            |           |
| D6        | Light button                           |           |
| A0        | Ultrasonic mist maker power connection | To measure the voltage and know if the mist maker actually was on or not | 
| 5V        | Capacitor connection                   | Found this connection point by trial and error. Had the USB voltage and everything seemed to work as it should |
| GND       | Random GND point                       |    | 

<details>
  <summary><h2>Build process</h2></summary>
  Pop the lid by unscrewing some screws.
  
  ![PXL_20221102_200026964](https://user-images.githubusercontent.com/30476405/200193846-74f9d098-9158-4b7a-8cf0-1b1d80fd4d62.jpg)
  
  Finding the connections.
  
  <img width="945" alt="image" src="https://user-images.githubusercontent.com/30476405/200193945-d8479ec4-4186-4e06-bf50-d545f9458afb.png">
  
  <img width="948" alt="image" src="https://user-images.githubusercontent.com/30476405/200194011-20719d0f-058f-49a3-9583-f40283aecfc8.png">

  Put it all together

  <img width="auto" alt="image" src="https://user-images.githubusercontent.com/30476405/200194074-10a59369-907c-4f83-a07a-ccdd6a25d0aa.jpg">
  
  Last bit of circuit isolation 🤪🤪
  <img width="auto" alt="image" src="https://user-images.githubusercontent.com/30476405/200194085-1538ef1a-d53d-45a8-8fbb-b4a727dd2e37.jpg">


</details>

# Home Assistant Integration
Using [ESPHome](https://esphome.io/) to configure the module, we can easily integrate and automate the diffuser in [Home Assistant](https://www.home-assistant.io/). The ESPHome yaml can be found [here in the files](https://github.com/petrepa/ESPHome-Diffuser/blob/main/greenhouse-humidifier.yaml).

This gives us a switch for the misting function. By combining this with a separate humidity sensor (or adding one directly to the diffusor), in our case a [Xiaomi BT Home](https://bthome.io/) enabled multi sensor, we can creating a humidifier entity in Home Assistant with the [generic hygrostat integration](https://www.home-assistant.io/integrations/generic_hygrostat/). 

```yaml
# Add to configuration.yaml and reboot your HA server
generic_hygrostat:
  - name: Living Room Greenhouse
    humidifier: switch.greenhouse_humidifier
    target_sensor: sensor.living_room_multi_sensor_humidity

```
<img width="893" alt="image" src="https://user-images.githubusercontent.com/30476405/200193677-2b8985ea-b138-4d08-bc2b-521003368d3a.png">


# Theory
|   |   |
|---|---|
| [![Make your own Super Simple Ultrasonic Mist Maker](https://img.youtube.com/vi/OOZi3QnnDCo/0.jpg)](https://www.youtube.com/watch?v=OOZi3QnnDCo&ab_channel=GreatScott%21) | [![Ultrasonic Mist Maker – DIY or Buy](https://img.youtube.com/vi/aKhPj7uFD0Y/0.jpg)](https://www.youtube.com/watch?v=aKhPj7uFD0Y&ab_channel=GreatScott%21) |




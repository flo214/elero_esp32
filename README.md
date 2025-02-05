# elero_esp32
Control elero/Nice/Griesser unidirectional shutters using the PCB out of a VarioTel2 and an ESP32

# Why?
Contrary to the bidirectional mode, there seems to be no open-source implementation of the Elero unidirectional protocol for older shutter receivers. The goal here is to enable the control of Elero/Nice/Griesser shutters with unidirectional receivers from Home Assistant.

# Requirements
- Elero VarioTel2 remote (or a Remoto 5 should work the same way)
- ESP32
- Relay board

# Hardware
<img src="https://github.com/user-attachments/assets/448aaded-74fe-45b5-ae4a-cf0c7e925a60" alt="Image 2" width="300">




## Instructions

1) It's recommended to configure your remote with the blind controllers before to start the next point. It's easier to do it when you still have the physical switches on the remote.
3) Remove the PCB from the remote.
<img src="https://github.com/user-attachments/assets/b7f53ff8-f5e1-4ac5-941e-cd547dfd53b7" alt="Image 1" width="300">
<img src="https://github.com/user-attachments/assets/0246f223-c585-4e09-ba09-7c1683e445f1" alt="Image 3" width="300">
<img src="https://github.com/user-attachments/assets/e8e00362-e066-4f13-97f9-e925c29a5636" alt="Image 4" width="300">

4) Un-solder the existing switches from the remote

5) I suggest to attach the remote to a PCB. The pad of the PCB are really small and if you only connect a cable without fixing the PCB you will tear off the metal tab from the PCB

6) Connect each switch to a relay and connect the relay board to the ESP32. 
<img src="https://github.com/user-attachments/assets/67057623-8300-4339-9244-1ee7949ee979" alt="Image 5" width="300">

# Home assistant configuration

1) Claim your ESP32 in ESPHome
2) Edit the configuration file this way
```yaml
esphome:
  name: esphome-web-66be9c
  friendly_name: Relais
  min_version: 2024.11.0
  name_add_mac_suffix: false

esp32:
  board: esp32dev
  framework:
    type: esp-idf

# Enable logging
logger:

# Enable Home Assistant API
api:
  encryption:
    key: "xxxxxxxxxxxxxx"

# Allow Over-The-Air updates
ota:
- platform: esphome

wifi:
  ssid: !secret wifi_ssid
  password: !secret wifi_password

  manual_ip:  # Optionnal
    static_ip: 192.168.x.xx
    gateway: 192.168.x.x
    subnet: 255.255.255.0

  # Mode Point d'accès (optionnel, utilisé en cas de déconnexion)
  ap:                  
    ssid: "ESPHome_AP"
    password: "xxxxxxx"

# Déclaration des sorties GPIO pour les relais
output:
  - platform: gpio
    pin: GPIO13
    id: relais_1_output
    inverted: true
  - platform: gpio
    pin: GPIO12
    id: relais_2_output
    inverted: true
  - platform: gpio
    pin: GPIO5
    id: relais_3_output
    inverted: true
  - platform: gpio
    pin: GPIO18
    id: relais_4_output
    inverted: true
  - platform: gpio
    pin: GPIO19
    id: relais_5_output
  - platform: gpio
    pin: GPIO21
    id: relais_6_output
  - platform: gpio
    pin: GPIO22
    id: relais_7_output
  - platform: gpio
    pin: GPIO23
    id: relais_8_output

# Déclaration des entités switch pour contrôler les relais
switch:
  - platform: output
    name: "Store Haut"
    output: relais_1_output
    id: store_haut
    on_turn_on:
      - delay: 3s
      - switch.turn_off: store_haut
  - platform: output
    name: "Store Stop"
    output: relais_2_output
    id: store_stop
    on_turn_on:
      - delay: 1s
      - switch.turn_off: store_stop
  - platform: output
    name: "Store Bas"
    output: relais_3_output
    id: store_bas
    on_turn_on:
      - delay: 3s
      - switch.turn_off: store_bas
  - platform: output
    name: "Store Selection"
    output: relais_4_output
    id: store_selection
    on_turn_on:
      - delay: 1s
      - switch.turn_off: store_selection
  - platform: output
    name: "Relais 5"
    output: relais_5_output
  - platform: output
    name: "Relais 6"
    output: relais_6_output
  - platform: output
    name: "Relais 7"
    output: relais_7_output
  - platform: output
    name: "Relais 8"
    output: relais_8_output
```

3) Create some script for each


    

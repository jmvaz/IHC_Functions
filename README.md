# IHC_Functions
Custom IHC visual functions

## Blinds function with state reporting
  - in order to work please adjust on settings:
    - Full time needed to open the blind
    - The previous time divided by 9 (sadly IHC only is able to sum and substract, that why we need to insert the time divided manually)
    - Step timer if you want different than 5 sec

config.yaml 
```
- ihc:
  url: !secret ihc_ip
  username: !secret ihc_username
  password: !secret ihc_password
  auto_setup: false
  binary_sensor:
    - id: XXXXXXX
      name: rollershutter_kitchen_door_button_up
      inverting: false
      note: momentary switch
      position: Push button rollershutter kitchen door up  (UP for Homeassistant in IHC visual)
    - id: YYYYYYY
      name: rollershutter_kitchen_door_button_down
      inverting: false
      note: momentary switch
      position: Push button rollershutter kitchen door down (DOWN for Homeassistant in IHC visual)
    - id: ZZZZZZ
      name: rollershutter_kitchen_door_stop
      inverting: false
      note: momentary switch
      position: Push button rollershutter kitchen door stop (STOP for Homeassistant in IHC visual)
switch:
    - id: AAAAA
      name: rollershutter_kitchen_door_calibration
      note: Calibration rollershutter kitchen door.

  sensor:
    - id: BBBBBB
      name: rollershutter_kitchen_door_status
      unit_of_measurement: "%"
      note: Status rollershutter kitchen door.

- platform: template
  covers:
    rollershutter_kitchen_door:
      friendly_name: "Kitchen door rollershutter"
      position_template: "{{ states('sensor.rollershutter_kitchen_door_status')|float }}"
      open_cover:
        service: switch.turn_on
        data:
          entity_id: switch.rollershutter_kitchen_door_up
      close_cover:
        service: switch.turn_on
        data:
          entity_id: switch.rollershutter_kitchen_door_down
      stop_cover:
        service: switch.turn_on
        data:
          entity_id: switch.rollershutter_kitchen_door_stop
      set_cover_position:
        service: mqtt.publish
        data_template:
          payload: "{{position}}"
          topic: "covers/rollershutter_kitchen_door"

```

If you want to set also the position by percentage, you will need to work with nodered in order to listen the topic "covers/rollershutter_kitchen_door" and as soon has it receives the desire percentage, check if you need to open or close the blind and send the command. Afterwards you need to wait, and listen the rollershutter_kitchen_door_status updates and send the stop command as soon as the blind reaches the desired position.

NOTE: the reports are always made on 10 percent scale. ( 0%, 10%, 20%, ...., 100%)

Good luck!

https://www.buymeacoffee.com/jmvaz

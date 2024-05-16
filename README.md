# Sonoff-T0
Guide on how to flash Sonoff T0 light switch with ESPHome. Doing this because I got an alert on my iPhone that my details on the Ewelink app have been found in a data breach (thank you apple) so decided its time to go local only. If you use Ewelink you should assume your details are part of the same leak and should change your password, or just scrap using them entirely (now they are charging a subscription to be able to use timers and stuff, I think all these smart control apps are going downhill, homeassistant all the way!)

# What do I need? 

Soldering iron
50mm or 4 inch piece of wire
USB to serial adapter (I used a red FTDI clone) (It NEEDS to be 3.3V logic or at least switchable from 5V to 3.3v)
ESPhome web
Male to Female jumper wires to go from serial adapter to PCB (going to hold the pins into the PCB by hand until its programmed)

# *Assuming that you know the rough ins and outs of using ESPHome and Homeassistant and are not completely new to this*

# Step 1: 
Turn off the power at the breaker (dont skip, the screws of the live terminals at the back are scarily close to touching the metal back box's that we have in the UK and will lead to a big BANG....and an increased heart rate) 

Remove the front cover of the switch. Youll see a PCB which can be taken off by holding the left and right edge of the PCB and pulling it towards you gently, there are no clips or screws. You shouldnt have to unscrew the whole switch out of the wall.

Take the PCB and flip it over, will look like the image below, you want to locate the test point circled below and solder a wire to it. Strip the other end of the wire so that its ready to touch where we need to later on
![163966240-a060048c-ebe6-47a5-b6ce-d1c9f8225e94](https://github.com/nagup/Sonoff-T0/assets/39876186/0e8f10c2-8be5-4414-97fe-3e5b5304357d)


# Step 2: 
Sorry I didnt get a picture but you want to put the end of the wire that youve soldered to the test point, into the GND hole of the serial header, shown below. 

![157070817-a16ae0e4-2f13-4cb5-a253-c7413532df10](https://github.com/nagup/Sonoff-T0/assets/39876186/cf689853-20cf-40e7-87e2-4d05019af1e8)

# Step 3: 

Connect your serial adapter to the serial header using M-F jumpers or whatever method you like.

# Serial adapter  >   PCB

VCC (3.3V)        >   3V3
GND               >   GND
TX                >   RX
RX                >   TX

You want the loose wire that was soldered to the test point to also go in to the GND hole. 

I just pushed the male ends of the jumper wires into the holes including the loose wire and held them there with firm pressure.

# Step 4: Connect serial adapter to ESPHome Web (keep holding the wires into the serial header, not too long left I promise)

Connect the serial adapter to your computer and follow the steps in ESPHome to connect the adapter, install the basic configuration. 

Once completed, unplug the USB connection to the adapter (to power down the PCB). Pull out the loose wire now and connect the serial adapter again, may need to connect the adapter to ESPHome Web again then click the 3 dots and press configure WiFi. 

After this, the device should appear as online in the ESPhome dashboard. Then you want to press edit and underneath all the basic setup YAML thats already there, you want to paste the following (Copied from https://github.com/esphome/esphome-devices/blob/849b599b812d5b8da85a09d430c224545c647e63/src/docs/devices/Sonoff-T1-T2-T3/index.md)
(the line saying esp8266.... is removed because its already defined in the YAML file at the top)
```
binary_sensor:
  - platform: gpio
    pin:
      number: GPIO0
      mode:
        input: true
        pullup: true
      inverted: true
    id: button_1
    on_press:
      then:
        - light.toggle: light_1

  - platform: status
    name: "T1 Status"

output:
  - platform: gpio
    pin: GPIO12
    id: relay_1

light:
  - platform: binary
    name: "T1"
    id: light_1
    output: relay_1

status_led:
  pin:
    number: GPIO13
    inverted: yes
```

Save that and install it (I know your fingers are aching but keep it together, not long left) 

Now you want to watch the logs, so that we can test the touch button and see if its sending updates over the network. I did it by opening homeassistant and going to the web UI of ESPHome, find the device (may need to press adopt first) then you can press logs to start a logging session, let it do what it needs to until you see the big chunk of pink text then press the white square on the PCB as if you are touching the light switch to turn the light on and you should see some text appear in the log window saying that the state has changed. 

# Step 5: 

Unplug the adapter's usb connection then you can remove the jumper wires from the adapter to the PCB. The loose wire can be cut off or soldered off. 

Finally, you can push the pcb back into place on the light switch, close up the light switch and turn the mains power on. The light will function straight away to turn the light on and off, as well as via the network once connected, also doesnt take long. 


Sorry for the lack of detailed images etc I only thought after I did it that I should write a guide on it since I wasnt able to find one, just the one linked to above that referenced the T0 specifically, other guides show the T1 onwards for which the PCB is different. 



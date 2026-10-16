# Mellow-Fly-dp5
This writeup is my personal implementation of the Mellow Fly DP5 board. I guarantee you it will not be like yours, but you are free to use the information to assist you in implementing it on your printer. My setup is as follows:

    V0.2-ish printer I got used
    Tulip gantry
    Intel Nuc host -> BTT U2C board -> EBB36 CAN setup
    Mellow Fly DP5 controller replacing an SKR Pico that came with the parts

There is nothing wrong with the Pico, I just saw the DP5 and wanted to test it.

The Mellow Fly DP5 is a small, 5 independent driver MCU board comparible to the SKR Pico board used frequently in the Voron V0. It is a bit larger than the Pico but comes with not only CAN on board, but also support for the normal 12874 remote display. The DP5 does not have a dedicated RGB port if you are looking for that. The built in RGB for the Drivers replaces that.  More on that setup later.

# Replacement process
## Important Information
The DP5 has 5 independent drivers, but only 4 of them support Diag pins and can be used with sensorless homing. The default config assumes that  you are using stepper drivers 0, 1, and 2 for X, Y, and Z steppers.

## Replacing the board
When I went to replace the Pico with the DP5 I tried to be as plug and play as possible. I left the Pico in it's place and put plugs in the ports that matched on the DP5.

Before plugging in the drivers in their slots, you will need to plug the motors in because the heat sinks that come with the TC2209-RGB are very large and the plugs can't be plugged in with the drivers in place. 

<img src="pictures/2209front.jpg" width=35% height=35% alt="Front of 2209-RGB">  <img src="pictures/2209back.jpg" width=35% height=35% alt="back of 2209-RGB">

Also I placed the heat sinks on the back side under the LEDs because the tallest part of the driver chip side is the driver itself and the heat sink would be too wobbly in my opinion. I do not think the LEDs will get too hot, and the driver PCB has a heat plane built in so I believe this will work ok.

These are the ports that I used for the various parts of the printer:

<img src="pictures/Ports Used.png" width=75% height=35% alt="Poirts used on my v0">

    B motor is plugged into stepper 0 with Diag1 jumpered
    A motor is plugged into stepper 1 with Diag2 jumpered
    Z motor is plugged into stepper 2 with Diag3 left unjumpered
    Power from PSU is plugged into the Power port
    Bed heater is plugged into the port labelled with PC7
    Bed thermistor is plugged into the BED-Temp port
    The CAN +24v/GND are plugged into the power port indicated by the callout
    The Z-Endstop switch is plugged into the appropriate port
    The BTT 12864 display that I have connected is plugged into the EXP1/EXP2 ports as expected

I have the CAN for the extruder managed separately so I am not using CAN on the DP5.

# Configuration file

The configuration file that I used started as the SKR Pico one. I had settings in place and some macros that I didn't want to recreate so I simply copied the existing working Pico config, and used it as a stepping stone for the DP5 config.

The parts that I changed are listed below.  Bear in mind that your settings and values could and probably will be different in many cases so do not just copy and paste what I have included here.

I included the board pins portion of the mellow config from their documentation:

```[board_pins]
aliases:
    # Stepper drivers
    D0_EN=PC2,   D0_STEP=PC15,  D0_DIR=PC14,  D0_CS=PC13, D0_STOP=PB4,  # X
    D1_EN=PA2,   D1_STEP=PA1,   D1_DIR=PA0,   D1_CS=PC3,  D1_STOP=PB3,  # Y
    D2_EN=PA6,   D2_STEP=PA5,   D2_DIR=PA4,   D2_CS=PA3,  D2_STOP=PD2,  # Z
    D3_EN=PB0,   D3_STEP=PC5,   D3_DIR=PC4,   D3_CS=PA7,  D3_STOP=PB5,  # E0
    D4_EN=PB11,  D4_STEP=PB10,  D4_DIR=PB2,   D4_CS=PB1,                # E1

    # Heaters
    BED_OUT=PC7,
    HEAT0=PC6,

    # Thermisors
    BED_TEMP=PC0,
    HEAT0_TEMP=PC1,
   
    # Fans
    FAN0=PC8, FAN1=PC9,

    # EXP1 header
    EXP1_1=<NC>,  EXP1_3=PC11,  EXP1_5=PC10,  EXP1_7=<NC>,  EXP1_9=<GND>,
    EXP1_2=PA15,  EXP1_4=PA14,  EXP1_6=PA13,  EXP1_8=<NC>,  EXP1_10=<5V>,
    
    # EXP2 header
    EXP2_1=PB14, EXP2_3=PC12,  EXP2_5=PB6,   EXP2_7=PB7,    EXP2_9=<GND>,
    EXP2_2=PB13, EXP2_4=PB12,  EXP2_6=PB15,  EXP2_8=<RST>,  EXP2_10=<NC>,

    # BL Touch
    SERVO=PA8   # BL Touch servo pin
```

This made the rest of the config easier to get correct.  I included the MCU in the config next:

```[mcu]
serial: /dev/serial/by-id/usb-Klipper_stm32f072xb_3B0023000457434630323420-if00
restart_method: command
```
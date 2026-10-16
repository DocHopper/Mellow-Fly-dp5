# Mellow-Fly-dp5
This writeup is my personal implementation of the Mellow Fly DP5 board. I guarantee you it will not be like yours, but you are free to use the information to assist you in implementing it on your printer. My setup is as follows:

    V0.2-ish printer I got used
    Tulip gantry
    Intel Nuc host -> BTT U2C board -> EBB36 CAN setup
    Mellow Fly DP5 controller replacing an SKR Pico that came with the parts

There is nothing wrong with the Pico, I just saw the DP5 and wanted to test it.

The Mellow Fly DP5 is a small, 5 driver MCU board comparible to the SKR Pico board used frequently in the Voron V0.
It is a bit larger than the Pico but comes with not only CAN on board, but also support for the normal 12874 remote display.
The DP5 does not have a dedicated RGB port if you are looking for that. The built in RGB for the Drivers replaces that.  More on that setup later.


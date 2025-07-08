
Assuming you only have the default HS3 cable that gomspace gives you, first we need to make a new cable to go between the Dragon and the A3200 JTAG port.

That port is an 8 pin PicoBlade and we need to make the female side of it. I just bought this connect kit from amazon, which makes things very easy...
[Amazon.com: Yoeruyo PicoBlade 1.25mm Pitch Male Female Connector with Premium 28AWG Pre-Crimped Cables,Compatible with Molex PicoBlade 1.25mm Male Female Connector 2/3/4/5/6/7/8/9/10 Pin Housing (MX1.25-MF) : Electronics](https://amzn.to/4kCVaVW)

Mine looks like this...

![[Picoblade jtag connector.png]]

### Pinout on A3200 side

From `gs-ds-nanomind-a3200.PDF`:
![[programming gomspace A3200 with an AVR Dragon under Windows.png]]

### Pinout on Dragon side

From [ww1.microchip.com/downloads/en/devicedoc/atmel-42723-avr-dragon\_userguide.pdf](https://ww1.microchip.com/downloads/en/devicedoc/atmel-42723-avr-dragon_userguide.pdf):

![[avr DRAGON JTAG PINOUT.png]]

To make things easy, I used a breadboard in between. This will also make it easier to add a logic analyzer onto these lines later in case something doesn't work right. To make this work, I soldered all the `male` ends of the pigtails to a pin header. Be careful to keep those little wires in order!

`VCC_OBC` -> `Vtref`. This is the line that lets the Dragon sense what voltage the target is running at. 
`nSTRST` -> `RESET_N`. I picked pin #5 since this is the one that the gomspace HS3 cable connected to.

The rest are self-evident.

Double check your work. The Dragon is pretty good about how it drives lines, but it would be a shame to blow out that $5000 A3200 because of some crossed wires. 

Here is how mine looks...
![[dragon to a3200.png]]


## Final connections

Next we will need to plug in the cable that goes from USB on PC to debug port on A3200. This provides power and will give us a serial port to talk to the A3200 over. 

We also need a USB cable from the PC to the Dragon. Duh.

All done, should look a little somthing like this...

![[full setup.png]]

## Programming it

To get started, lets use Microchip studio since it is easy to install and has a nice GUI so less for use to screw up. You can download it here...

[Microchip Studio for AVR® and SAM Devices \| Microchip Technology](https://www.microchip.com/en-us/tools-resources/develop/microchip-studio)

Once you have it installed and running, we need to install AVR32 support. 


Finally we can go into the "Tools->Device Programming" menu. 
Pick "AVBR Dragon" for Tool.
Pick "AT32UC3C0512C" for device.
Pick "JTAG" for interface. 
Hit apply. 

![[device programming window.png]]

Click the "Read" button and it should blink some blinks and put the same number I got into the box. 

Yey!

Now lets program some flash!

Grab the `nanomind-bsp.elf` that you created using my other document on how to compile the BSP and put it somewhere in the Windows file system.

Put the path to this file in the Flash image filename text box.

Your settings should be like mine (Erase first, Verify after).

Push Program and watch the bits fly!

## Trust, but verify

Next lets connect to the A3200 over the serial port to see if our image  actually booted up.

Use any terminal program (I use TeraTerm, but you can also use Putty). Set baud to 500000. You will need to figure out which COM port the serial port ended up on on your PC. 

![[COM settings.png]]


Connect and start typing!

![[a3200 debug console bring up.png]]

Your first command should be "HELP".
After that, try "CHECKOUT ALL".

## Next steps.

If you want to use the command line to program new flash images, google "Atprogram" (the executable was installed with Microchip Studio).

But I personally like the Microchip Studio IDE and experience, so would maybe bring the BSP files into it and then use it as my development environment. 100% windows and lots of support. This does use a different build chain than the gomspace supplied stuff, but -um- after everything we have just been through, that might be a feature. 


extract all the files from `gs-avr32_hs3_toolchain.GZ` into a windows folder. I used `D:\Documents\Projects\gomspace\Windows JTAG-HS3\gs-avr32_hs3_toolchain\hs3`.

Make sure you have python3 install and in windows executable path.

Install the dependencies. This includes `pyftdi` which is how the software talks to the HS3 over its FDTI port.
```
pip install -r requirements.txt
```

Unfortunately `pyftdi` does not use the standard window FTDI drivers so we need to get that HS3 dongle to use `libusbK` which is a pain.

first plug in the HS3 to the usb port. 

Install zadig if you don't have it already..
[Zadig - USB driver installation made easy](https://zadig.akeo.ie/)

run zadig and turn on "options->list all devices"

Use the pulldown to select the diligent USB device and use "replace driver" to switch it to "libusbK".
![[setup HS3 programmer in windows.png]]

Ok now let's test that we can talk to the HS3.  The cable out of the HS3 should be plugged into the A3200 and the A3200 should be getting power (either from the debug port via the FTDI cable or the power pins)
```
python avr32-prog.py --programmer digilent_hs3 --detect
```

You should see something like...
```
Detection completed. Found 1 devices
0 Device: 6200003F - IR Length: 5
```
## getting it to work reliably

Unfortunately the setup I had would consistently fail when I tried to program the flash (and even sometimes fail just setting the IP register)

By default the `avr32-prog.py` software sets the JTAG interface to run at 6Mhz which seems to be much too fast for the janky wiring I got, so the TAG would occasionally get out of sync with the programmer and not be able to recover. I decided to slow that interface *way* down to avoid any signal quality problems and also to give the AVR32 chip plenty of time to process the JTAG commands and thus avoid busy waits. 

To make this change, you need to edit the file `avr32-prog.py` and go to line 61 and change it from...

```
    adapter = get_adapter(programmer, 6e6)
```

to...
```
    adapter = get_adapter(programmer, 10000)
```

This lowers the frequency from 6MHz to 10KHz (very slow). You can use Notepad or any editor to make the change, then save the file. 

## programming

Ok, lets finally get some code into this thing! 

We will load the default BSP elf file `nanomind-bsp.elf` (check my other document for how to build this). For now, copy that file into the current directory (or you can put the full path to it on the command line below).

Enter this command with the A3200 still connected to the JTAG cable and still getting power...

```
python avr32-prog.py --programmer digilent_hs3 -E -R -f nanomind-bsp.elf
```

You should see it _slowly_ program the segments. Then it will try to verify. The verify does not give any feedback while it is reading and can take a long time so be patient. When it is done, you should see...



we get 
```
Detected ID code: 6200003F = AT32UC3C0512C
 Chip Size: 524288. Page size: 512
```
So we know the JTAG TAP is working. 


### lets read a known value from somewhere

x03FC in the SCIF register should be 0x00000101 at the bottom.

SCIF base is xFFFF0800




# gomspace-a3200-missing-manual

If you have a gomspace nanomind A3200 and you can not figure out how to get it to actually work, here are my (rough) notes on how I 
got mine working. 

Note that I do not work for `gomspace` and I have no connection to them other than I spent many hours trying to figure this stuff out. Here I am just tryingto save you from wasting the same time I did. 

Everything here happens on a Windows machine, but Linux people can at least follow along with the toolchain stuff that happens inside a windows-subsystem-for-linux VM. 

# first comment

Unless you really need the flight command and control stuff, you probably do not need this platform. Turn back if it is not too late. 

The chip on the A3200 is very obsolite and gomspace development kit is poorly documented. 

I think it would almost certainly be better/faster/easier/more robust/lower power to use a standard chip and roll your own solution.

# first steps

1. Get your new module flashed and talking to your computer.

    There is no point in being able to compile code if you can not program it, so lets prove out this stuff first. Note that in this proceedure I use only windows software and I use
    and AVR Dragon as the programmer becuase I have one and I trust it. There are several other Atmel JTAG programmers will work if you make the same cable I desribe here...
   
    https://github.com/bigjosh/gomspace-a3200-missing-manual/blob/main/programming%20gomspace%20A3200%20with%20an%20AVR%20Dragon%20under%20Windows.md

    If you absolutely can not use windows or can not get your hands on one of the proper Atmel programmers, here are steps to get it working with the gomspace-supplied
    HS3 JTAG programmer, but I warn you that this tool chain was very flakely for me and I really strongly do not recommend it...

    https://github.com/bigjosh/gomspace-a3200-missing-manual/blob/main/setup%20HS3%20programmer%20in%20windows.md

    Note that if you really really really want to get the HS3 stuff working correctly and you have a really really good reason (I am already skeptical), get in touch becuase I
    have some ideas about what is causing the failures.     

2. Setup your board support package.

    This was mostly complicated by the fact that you can not get one of the dependancies anymore becuase it is obsolite, so we have to get the source and compile it from scratch...

    I used WSL but all the same commands should work on a straight linux box with a simialy distro too. 

    https://github.com/bigjosh/gomspace-a3200-missing-manual/blob/main/setup%20gomspace%20under%20windows.md

3. Start coding for space

    With this setup, you can edit the code (starting with main.cpp) in the board package linux environment, and then compile it also in that same environment using the `waf` command we
    used to make the inial version. Then you need to get that resulting `elf` file artifact over to windows so you can use Microchip Studio to program it. Note that `explorer.exe .` in
    the WSL is your friend here. Or you could put a copy/scp into the build script. Or get `atprogram` working on the linux side. Or just use Microchip Studio for everything. 

Open an issue if you have any problems or questions and I will try to help. Good luck!

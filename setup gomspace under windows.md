
you will need the file `Software-20250703T014355Z-1-001.zip` or equivalent from gomspace. 

## setup an old ubuntu distro under wsl

install wsl subsystem if it is not already installed...

```
wsl.exe --install -d Ubuntu-20.04
```

This version is new enogh that it works ok with WSL, but old enough that it still has python2 in the repository. 

(I now open the new instance, create a text txt file, reboot the windows machine, and then go back in and make sure that file is still there. I do this because there is a known bug where WSL completely looses your VHDX file. Ask me know I know this is a known bug.)

for now on, we can jump into this WSL distro with...
```
wsl -d Ubuntu-20.04
```

It is also good to then jump into your home dir (im not sure why sometimes it does this automatically and sometimes not)...
```
cd ~
```
## setup the gomspace build env inside the ubuntu instance...

Run the Ubuntu using windows start.

next we need the old python2

```
sudo apt update
sudo apt install python2
sudo ln -s /usr/bin/python2 /usr/bin/python
```

reboot get phython2 into our path and make sure everything stuck...
```
sudo reboot now
```

Make a new folder inside the instance. I used `workspace` top match the gomspace instructions. The explorer line will give you a handy windows file finder into that dir. 

```
mkdir ~/workspace
cd ~/workspace
explorer.exe .
```

now copy the contents of `gs-sw-nanomind-a3200-board-support-package.tar.gz.GZ` file into workspace


get back to our dir and do the bootstrap..
```
cd workspace/gs-sw-nanomind-a3200-board-support-package-2.6.3/
./tools/buildtools/gsbuildtools_bootstrap.py
```

if your copy lost the execution permisions on the `.py` files, then you can execute and then try again....
```
find . -name "*.py" -exec chmod +x {} \;
```

install some tools we will need for the next step...
```
sudo apt update
sudo apt install build-essential
sudo apt install unzip
```

copy `gs-avr32-toolchain.tar.gz.GZ` that we got from gomspace into the WSL `workplace` dir
I had to rename it without the GZ at the end to get the linux side open it with...

```
mv gs-avr32-toolchain.tar.gz.GZ gs-avr32-toolchain.tar.gz
```

then untar it...
```
 tar xfz ../gs-avr32-toolchain.tar.gz
```

install avr32 toolchain
```
cd gs-avr32-toolchain-3.4.2_gs1
./install-avr32.sh
```

restart the instance to get the new avg32-gcc into the path...
```
exit
```
..then from windows...
```
wsl -d Ubuntu-20.04
```

Ok, now we need to build the floating point library that is not included in this distro (because it is too old) but is statically linked into the gcc that gomspace gave us...

first we need to install GMP because we the mpfr depends on it (you can ignore "/sbin/ldconfig.real: /usr/lib/wsl/lib/libcuda.so.1 is not a symbolic link")
```
sudo apt update
sudo apt install libgmp-dev
```

next we grab the mpfr lib source code...

```
mkdir ~/workspace/mpfr-build
cd ~/workspace/mpfr-build
wget https://www.mpfr.org/mpfr-3.1.4/mpfr-3.1.4.tar.gz
tar xfz mpfr-3.1.4.tar.gz
cd mpfr-3.1.4
```


now we can finally build and install it (this takes a sec)...
```
./configure --prefix=/opt/mpfr-3.1.4
make
sudo make install
```

Finally we need to add this new lib to the linker path so gcc can find it (you can again ignore the cuda not a symbolic link warning)...

```
echo "/opt/mpfr-3.1.4/lib" | sudo tee /etc/ld.so.conf.d/mpfr.conf
sudo ldconfig
```

lets reboot just to be safe and make sure we had all the changes in the permeant env before we start using stuff......

```
exit
```

and now start the WSL again fresh...
```
wsl -d Ubuntu-20.04
```

and get back to the right dir (you can use tab to autocomplete these)... 
```
cd ~
cd workspace
gs-sw-nanomind-a3200-board-support-package-2.6.3
```

finally we can build the gomspace bsp (add execute permission to the waf executible in case it got lost)...
```
chmod +x waf
./waf distclean configure build
```

make sure that completes with "'build' finished successfully" that means we now have a file in `build` (both `elf` and `bin` formats are built)  that is a ready to go binary compiled from `src/main.c`!

We are finally ready to actually start doing work!!!

Check out my other doc to see how to actually get this executable into the A3200 using the JTAG-HS3 programmer under windows!
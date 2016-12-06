# Smoothie2

A Smoothie firmware port to the Smoothieboard v2 boards.

Current status : Gcode processing, motion planning, and step generation work. Many secondary features need porting, and some major lower level things need implementation ( USB, Ethernet, SDIO ).

# Update, 05/12/2016

Jim Morris ( lead Smoothie1 dev ) and Alan Carvalho de Assis ( NuttX dev ) are working on a port of Smoothie v1 to the LPC43XX by rebuilding Smoothie on top of the NuttX RTOS ( nuttx.org ) and completing the LPC43XX port of NuttX. That work is expected to take several months, but is now the "official" route to a v2 firmware.

This "mBed" version of the v2 firmware currently in this repository is expected to be left aside, even though it has advanced to the point of being capable of moving motors. We want to apologize to those who have put effort into this version of the port so far. Hopefully the many advantages of moving Smoothie to a RTOS will outweigh the wasting of this codebase, and you will be as exited as we are about the new direction.

If you want to help with the NuttX version of the v2 firmware, please note there will be a period of construction during which only Jim and Alan will work on it, but after that help will be extremely welcome.

# Compiling

First, go into gcc4mbed

    cd gcc4mbed/

Then, install the toolchain

    ./linux_install

Finally, setup the build environment

    ./BuildShell

Then go to the Smoothie2 source code

    cd ../src/

And finally compile the code

    make

# Board Specific Programming/Debugging Notes
* [Smoothie2 Pro Proto1 Boards](https://github.com/Smoothieware/Smoothie2/blob/master/notes/Smoothie2Proto1-board.creole#smoothie2-board-programmingdebugging-notes)
* [Bambino210E Boards](https://github.com/Smoothieware/Smoothie2/blob/master/notes/Bambino210E-board.creole#bambino210e-board-programmingdebugging-notes)

# TODO : 

Current major TODOs : 
 * Porting of existing functionality that hasn't been ported yet ( anything in https://github.com/Smoothieware/Smoothieware/tree/edge/src/modules that isn't ported yet )
 * Some modules have been ported, but the underlying low-level/HAL stuff to talk to the peripherals isn't ( like SPI, or ADCs etc ), it's just dummy objects right now. So that needs porting too.
 * USB and Ethernet need to be implemented.
 * Doing the step generation on the M0 co-processor instead of on the M4 main core.
 * This port was forked from Smoothie months ago, it should be updated to incoprorate changes to Smoothie done in the meantime.
 * Explore using http://nuttx.org/ for a RTOS ( major rewrite )

TODO functionality to port from v1 in more detail : 
 * libs/Adc.cpp : Port low level ADC functionality, then re-enable it in TemperatureControl ( note : there is some weird pin configuration surrounding this that makes it more complicated than it seems, but I don't remember what it is. Logxen@gmail.com probably remembers what it is ).
 * libs/md5.cpp : Port and re-enable in SimpleShell
 * libs/Network : Port and enable
 * [@adamgreen](https://github.com/adamgreen) - libs/SDFAT.cpp : Port for SPI SD card access, not SDIO, and re-enable. Then add SDIO support.
 * [@adamgreen](https://github.com/adamgreen) - libs/SPI.cpp : Port and make to use the mBed library instead of registers
 * libs/USBDevice : Port and enable
 * libs/Watchdog : Port ( no mBed libraries available that I could find )
 * libs/Hook.h : Uses doubles instead of ints ( in v1 ) for some reason, should be fixed
 * modules/tools/drillingcycles : Port ( should be fairly simple )
 * modules/tools/extruder : Port
 * modules/tools/filamentdetector : Port
 * modules/tools/filamentdetector : Get PWM to work
 * modules/tools/scaracal : Port
 * modules/tools/spindle : Port and get PWM to work
 * modules/tools/temperaturecontrol : Finish porting, get the ADC to actually work, port the AD8495 temperature input
 * modules/tools/temperatureswitch : Port ( should be fairly easy )
 * modules/tools/toolmanager : Port ( should be trivial, is needed for Extruder )
 * modules/tools/touchprobe : Port ( could be quite easy )
 * modules/tools/zprobe : Port ( could be quite easy )
 * modules/tools/simpleshell : Port

 * The FPGA requires a SGPIO spi library
 * The FPGA requires a way to flash a .bin to it over JTAG from the M4 

Things that are broken during the port and should be added back as things progress ( some things might be missing here. I try to use the TOADDBACK label when commenting things so search for that too ) : 

* In GcodeDispatch.cpp : Removed dependency to Pauser.h
* In Switch.cpp : Commented out all of the set_low_on_debug stuff
* In SlowTicker.h and Hook.h : Using doubles instead of ints for intervals/frequencies and counting down. Done to accomodate mBed, but dirty costly hack
* In SlowTicker.h : Assuming it's fine to use mBed for this, we can remove a lot of code and just rely fully on mBed
* In RingBuffer.h : Removed the irq stuff
* In Laser.cpp : PWM pin is broken, needs to be fixed

Things changed from Smoothie1 : 

* Pin now uses mBed instead of registers
* SlowTicker now uses mBed instead of registers
* StepTicker now uses mBed instead of register
* Various "waits" now use mBed's Timer

Planned refactors ( only to be done when the basic port is done ) : 

* Remove the checksum system for config options
* Try to read config directly from SD card without cache ( as smoothie used to do ) now that we have SDIO
* Queue refactor ( see wiki )
* Adding MTP and removing MSD for USB
* Full rewrite based on a RTOS

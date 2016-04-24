# teensy_builder

A simple `make` like program to build programs for the
[Teensy](https://www.pjrc.com/teensy/) boards by PJRC.

To use it, simply run `teensy_builder` from the root directory of your project.
You also need a couple of configuration files described below.

## Config files

The builder expect two configuration files in `INI` format in the current
directory (the root of your program). One should be named `builder.conf` and
the other `local.conf`.

### The `builder.conf` file

That file should be checked in the same source control system as the rest of the
program. It should contain the following sections and values:

 - `[main]`: this section specify the how the program should behave.
   - `target`: the board for which the program should be built. This can be
     `teensy30`, `teensy31`, `teensy32`, or `teensylc`. This is the only value
     that is mandatory in this section.
   - `name`: the name of the program. This will be used for the name of the
     binary files (if not set, the default is `teensy_prog`).
   - `cpufreq`: the frequency in MHz to which the board should be set. The
     allowed values depend on the target that was selected. The default is `48`.
   - `usbtype`: the type of USB peripheric to emulate. The default is `serial`.
   - `keyboard`: the keyboard layout to emulate. Used only when emulating a HID
     peripheric. The default is `US_ENGLISH`.
   - `optimize`: the optimization mode to use with GCC. This can be one of `0`,
     `1`, `2`, `3`, `s`, `g`, or `fast`. The default is `s`.
 - `[libs]`: this section list the libraries to build in the program. The name
   of each value is arbitrary and is only used to identify the library. The
   associated value is the relative path of that library from the folder of the
   configuration file. It is mendatory that one of the library is named
   `teensy_core` and points to the Teensy 3 library of PJRC (available
   [here](https://github.com/PaulStoffregen/cores/tree/master/teensy3)). Usually
   there will be at least one other library with your source code.
   The order of the libraries matter: they should be given in the order in which
   they should be linked (so usually the `teensy_core` library will be the first
   and our own code will be the last).

Example of a `builder.conf` file:

    [main]
    target=teensy31
    name=teensy_phone
    cpufreq=72
    usbtype=serial
    optimize=s

    [libs]
    teensy_core=third_party/teensy_cores/teensy3
    spi=third_party/SPI
    gfx=third_party/Adafruit_GFX
    ssd1351=third_party/Adafruit_SSD1351
    main=src

### The `local.conf` file

This file provides some value that are local to your computer, hence it should
not be checked-in with the rest of the source code.

 - `[paths]`: this section (the only one currently in this file) gives the paths
   to the tools needed to build the program.
   - `compiler`: the full path to the directory holding the ARM version of GCC
     on your system. This option is mendatory.
   - `compiler_prefix`: the common prefix used for the name of the GNU tools.
     The default is `arm-none-eabi-` and it should be the correct value in most
     case.
   - `tools`: the full path to the directory holding the Teensy tools provided
     by PJRC. This directory should hold the `teensy.exe`,
     `teensy_post_compile.exe`, and some other binaries.

Example of a `local.conf` file:
 
    [paths]
    compiler=C:\Program Files (x86)\Arduino\hardware\tools\arm\bin
    tools=C:\Program Files (x86)\Arduino\hardware\tools

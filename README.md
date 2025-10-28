Ortec provides the Maestro Gui Utility and the Connections programmers toolkit for Windows platforms. There is no provision for Linux platforms and especially not for ARM platforms since no source code is provided. This library was created by third parties to support Linux platforms.

# libdbaserh 0.4
`libdbaserh` is a C library for device control and data acquisition of DigiBase-RH PMT bases. The library is an adaption of `libdbase` 0.3 for DigiBase-RH modules.

The manufacturer's explanation of the difference between DigiBase and DigiBase-RH (April 2012):

>"RH version was designed to be more ruggedized against neutron damage which
> was a problem with its predecessor. When this change was made, much of the
> hardware and its method of communication changed as well."

As tested this version of the library worked with a non RH Digibase indicating that newer non RH Digibases
now use the same communication method as that developed for the RH.

## Development Status
- version 0.4 -- Fixes and enhancements

                 Fixes:
                 Fix actual high voltage readback
                 Fix ability to turn gain stabilization on/off
                 Fix gain setting for values less than 0.5
                 Initialize local variables to prevent random output during errors

                 Enhancements:
                 Add ability to set gain stability channels
                 Add ability to set zero setting
                 Add ability to set zero stability channels
                 Add ability to set lower and upper discriminator values

                 Defaults:
                 Gain stabilization off by default
                 Gain stabilization default channels set for K40 1461.8kev
                 Zero stabilization off by default
                 Zero stabilization default channels set for Ba133 81kev
                 Lower level discriminator default to zero

                 Include:
                 digibase rbf firmware file

- version 0.3 -- set of different zero and gain stabilization during
                 device initialization (file libdbaserhi.c),
                 modification of some of these parameters on the initialized
                 device is not working

- version 0.2 -- the detector status after init process changed
                 (real/live time preset to 0)

- version 0.1 -- initial version

##  TODO
- Write a comprehensive manual.
- Handle status messages (are there any?)

## Dependencies
- `libusb-1.0` for low-level USB communication

## Installation
1. Install `libusb-1.0`.
    - Note: sudo is normally required to use libusb but to use without sudo add to the system the file /etc/udev/rules.d/99-digibase.rules containing the following line
      ACTION=="add", SUBSYSTEMS=="usb", SUBSYSTEM=="usb", ATTR{idVendor}=="0a2d", ATTR{idProduct}=="001f", MODE="0666" 
2. PACK_PATH specifies where the to look for digiBaseRH.rbf.
   The makefile currently sets it to `./fw/`. If it is not set the path will default to `./`
   When calling dbase_init(dev_handle, filename)) filename should be set to the concatenation of `PACK_PATH` and `digiBaseRH.rbf`.
   Edit the Makefile to suit your installation. 
3. Compile the library: ```make```
4. Install the library: ```sudo make install```
5. Run the control program: ```sudo ./dbaserh```


### Directory Structure
------------------------
```bash
    .
    ├── data                        # Data files
    ├── logs                        # Log files
    ├── fw                          # firmware file digiBaseRH.rbf
        ├── digiBaseRH.rbf          # firmware file written to digiBASE RH on first initialization
        ├── SERIAL#                 # a per serial number folder created when the -save or -name options are used 
            ├── status              # file where the digiBase RH settings are stored when the -save option is used
            └── name                # file where friendly name is stored when the -name option is used
    ├── src                         # Source files
        ├── digibase-rh             # Files for Ortec digiBASE rad-hardened (RH) version
            ├── dbaserh.c           # Standalone utility to excercise the digiBASE RH
            ├── dbaserh.h
            ├── digiBaseRH.rbf      # Raw binary file necessary to interface with digiBASE RH
            ├── libdbaserh.c        # public library of functions in libdbaserh
            ├── libdbaserh.h        # 
            ├── libdbaserhi.c       # internal functions used by libdbaserh
            ├── libdbaserhi.h       # 
            └── ...
    ├── Makefile                    # Makefile
    ├── User guide.md               # Basic introduction on how to use the this library with the Digibase RH
    └── README.md
```

## Acknowledgements
`libdbaserh` originally comes from [Jan Kamenik](mailto:jankamenik@gmail.com). It is an extension of `libdbase`, written by [Peder Kock](mailto:peder.kock@med.lu.se) and Jonas Nilsson,
Lund University. See http://libdbase.sourceforge.net for more information on `libdbase`.

## License
`libdbaserh` is licensed under the GPL-3.

### License Note
The firmware included here is currently (June 20, 2025) freely available via download of the "Connections-32" driver install on the Ortec site. 


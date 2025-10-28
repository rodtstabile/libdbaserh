User Guide

Example library usage (with no error checking):

main.c:

    #include <string.h>
    #include <stdio.h>   /* printf(), ... */
    #include <unistd.h>  /* usleep(), ... */
    #include "libdbaserh.h"
    int main(int argc, char **argv) 
    {
      int targetHV=800, zero=0, uld=1023, lld=10;
      double fineGain=1;
      detector *det;
      char str[strlen(PACK_PATH) + 32];
      snprintf(str, sizeof(str), "%s/digiBaseRH.rbf", PACK_PATH);
      det = libdbase_init(-1, str);
      libdbase_set_hv(det, targetHV);
      libdbase_set_fgn(det, fineGain);
      libdbase_set_zero(det, zero);
      libdbase_set_uld(det, uld);
      libdbase_set_lld(det, lld);
      libdbase_set_pha_mode(det);  // or libdbase_set_list_mode(det);
      libdbase_hv_on(det);
      libdbase_start(det);
      do
      {
        sleep(1);
        libdbase_get_spectrum(det);
        libdbase_clear_spectrum(det);
        libdbase_print_spectrum(det);
        //libdbase_print_file_spectrum(det, FILEHANDLE);
        //do_something_with_spectrum(det->spec);
      } while (0);
      libdbase_stop(det);
      libdbase_close(det);
      return 0;
    }

Compile:
gcc ./src/main.c -o example -DPACK_PATH=\"./fw/\" -L. -l:libdbaserh.a -lusb-1.0 


Comments:

The digibase has a fixed set of 1024 channels (0-1023). Voltage, gain and zero (offset) can be used to calibrate the sensor. Enable the auto gain to ensure accuracy over temperature. The circuit requires a reference peak. Potassium K40 is present everywhere and provides a peak at 1460.8kev. Without another reference peak it is best to leave the auto zero circuit disabled. 

Since the auto gain can range from .4 to 1.2 a good way to calibrate is to set the initial gain to 0.8 and then set the voltage to center the 1460.8 peak. To center other energy peaks a quadratic correction should be applied after each acquisition.  

Note if using offset - the positive and negative offsets will cause channels to wrap around.  You can think of the masking as taking affect before the offset takes affect. So channels are masked and then wrapping occurs. This means you should mask any channels that are going to wrap. So for example if you set a negative offset value that causes channel zero to wrap around to channel 1023 you still need to set the lower level discriminator to a value that masks channel zero. If auto zero is used the discriminators should be set to cover the expected range of channel wrapping it causes. The auto zero should only be used when a reference peak will be available during data capture.


Standalone Utility:

      Usage: dbaserh [-d n][-t,i T] [-s n] [-lm [c/a/t]] [-l,q,h,b,cps,diff] [-o file] 
      [-CTRL [on/off]] 
      [-clr [all/spec/pres/cnt]]
      [-set hvt/fgn/zero/lld/uld/pw/ltp/rtp/gsch/zsch [value(s)]]

      Argument desc.:
        -b    Binary output (default ASCII)
        -cps  Print cps instead of spectra
        -d n  Operate on device with device_no n (see -l)
        -diff Print differential spectra rather than cumulative
        -h    Prints this message
        -i    Sampling interval T
              (pha-mode: default 1s, list-mode: default 50ms)
              valid time suffix's are one of: ms,s,m,h,d,y (omitted s is assumed)
        -l    List connected digibase's device_no, serial_no and dev_names
        -lm   Set in list mode, print c=counts,a=amplitudes,t=times
        -o    Output to file (default is stdout)
        -q    Quiet
        -s    Print status every n:th measurement (default off)
        -t    Sample for time T, then exit (-1=infinite)

      CTRL commands:
        -start      Start Measurement
        -stop       Stop Measurement
        -hv on/off  High Voltage on/off
        -gs on/off  Gain stabilization on/off
        -gz on/off  Zero stabilization on/off

      Clear command:
        -clr all/spec/pres/cnt (all three or one of: spectrum/presets/counters)

      Settings commands:
        -set hvt  val  High voltage target, 50-1200 V
        -set fgn  val  Fine gain, 0.5-1.2
        -set zero val  Zero adjust, -65534 to 65535 (~-/+32chns)
        -set lld  val  Lower level discriminator, 0-1023
        -set uld  val  upper level discriminator, 0-1023
        -set pw   val  Pulse width, 0.75-2.0 us
        -set ltp  val  Live time preset (s)
        -set rtp  val  Real time preset (s)
        -set gsch C W  Gain stab center ch. (1-1022) and width (2-1024)
        -set zsch C W  Zero stab center ch. (1-1022) and width (2-1024)

      Print-once commands:
        -stat           Print status
        -spec           Print MCB spectrum
        -roi low high   Print Region of Interest [low:high channels]
        -serial         Print digibase's serial number
        -name DEV_NAME  Give digibase with device_no n (use with -d n) the name DEV_NAME saved in path ./fw/$SERIAL#/name

      Load/Save status functions:
        -save   Save status to file in path ./fw/$SERIAL#/status.txt.
        -load   Load status from file


      Examples:
      dbaserh -q -hv on -start -t 50 
        will enable hv, start measurement, measure for 50 seconds and then exit.
        without printing anything other than ascii spectra to stdout.

      dbaserh -q -hv on -lm cat -t 1.0h -i 0.05 -o outp.txt
        will enable hv, start measurement in list mode, measure for 1h and exit.
        The counts recieved per 50ms by digibase will be printed to 'outp.txt'.

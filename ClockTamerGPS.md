# Introduction #

ClockTamer has ability to lock its clock to GPS, providing much higher stability then you can get from any TCXO or VCTCXO. To lock to GPS it uses 1pps signal, generated by many GPS chips. ClockTamer micro-controller then counts number of clock pulses on one of generator outputs between consecutive 1pps pulses and applies math filtering to get real (VC)TCXO frequency. Knowing real (VC)TCXO frequency we're able to compensate it in software.

There are two options of getting 1pps signal:
  1. Use external GPS unit with 1pps input. ClockTamer have a placeholder for 1pps U.FL connector (**TODO: Picture**). If you already have GPS unit with 1pps output and you don't care about physical size, that's your choice.
  1. Solder GPS ship right on ClockTamer unit (on the back side of PCB). This makes ClockTamer slightly more expensive, but you don't need any external device anymore! You save in physical space and avoid headaches with wiring devices together.

With GPS locking it's not critical to have very high precision XO, so it's possible to use 2.5ppm VCTCXO option instead of standard 0.28ppm TCXO. And that's the configuration we use in our own experiments.

**Note**: When GPS-locking option is installed, you can use only 5 of 6 ClockTamer outputs. One output is routed to micro-controller in this case. Exactly, output number 2 is not available **TODO: Picture**.

# Usage tips #

  1. When GPS lock is acquired, LED starts flashing with 2 sec period.
  1. If you want to detect GPS lock with automatically, use GPS debug register `R01`. Refer to [control protocol documentation](http://code.google.com/p/clock-tamer/wiki/ControlProtocol?ts=1295896247&updated=ControlProtocol#Info_%28%22INF%22%29) for details and to [tamer-gui](http://code.google.com/p/clock-tamer/source/browse/#hg%2Fhost%2Ftamer-gui) for implementation example.

# Frequency stability #

Current implementation first reduces measurement jitter by averaging measurements for 17sec. Then calculated values are used as input for [exponential filter](http://en.wikipedia.org/wiki/Exponential_smoothing) to get fine resolution frequency measurement. Calculations are done with 32-bit precision.

**Current implementation:**
  1. 32-bit precision of calculations imposes maximum achievable stability of 50ppb.
  1. From 11ppm filter converges to 1ppm for to 85sec, to 50ppb for 30min
  1. Filter can tolerate XO frequency drift of no more then 0.2ppm during 85sec.

> See [WishList](WishList.md) for the way to improve this.

# GPS positioning mode #

You could also use ClockTamer as a GPS positioning device. To switch from SPI command mode to GPS positioning mode use ['%%%' command](ControlProtocol#Enter_GPS_mode_(%22%25%25%25%22).md). To leave GPS positioning mode and switch back to SPI command mode use ['%' command](ControlProtocol#Leave_GPS_mode_(%22%25%22).md).

When in the GPS positioning mode, you could use ClockTamer with any application which understands NMEA protocol. Below is a picture of `xgps` application, reading data from a ClockTamer:

![http://wiki.clock-tamer.googlecode.com/hg/images/misc/ClockTamer-xgps.png](http://wiki.clock-tamer.googlecode.com/hg/images/misc/ClockTamer-xgps.png)
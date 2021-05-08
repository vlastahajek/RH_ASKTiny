# RH_ASKTiny
This is [RadioHead library](https://www.airspayce.com/mikem/arduino/RadioHead/) simplified to RH_ASK only and patched to allow
communication with 1MHz oscillator and possibility to save RAM by disabling receiving.

All credits goes to Mike McCauley, the author of the [RadioHead library](https://www.airspayce.com/mikem/arduino/RadioHead/). 

## Running with 1MHz oscillator

There is a comment in the RH_ASK.cpp for the ATtiny part:<BR>
_REVISIT: does not correctly handle 1MHz clock speeds, only works with 8MHz clocks_<BR>
_At 1MHz clock, get 1/8 of the expected baud rate_

It doesn't seem that timer is actually running at 1/8 speed, but it is still a bit off.
Timer is configured to run 8x faster than the required transmission speed.
This is necessary for precise bit sampling during receiving, but it is not important for transmission.

RH_ASK is in this library enhanced with parameter `overSampling`, which controls timer speed/transmission speed ratio. It is `8` by default. Setting it to `1` seems to work around the problem when transmitting on 1MHz speed.  

```c++
RH_ASK(uint16_t speed = 2000, uint8_t rxPin = 11, uint8_t txPin = 12, uint8_t pttPin = 10, bool pttInverted = false,  uint8_t overSampling = 8); 
```

Usage on ATtiny85:
```c++
#include <RH_ASK.h>
// Speed 2000 bit/s, no RX pin, TX pin PB1, no PTT pin, no inversion, oversampling 1
RH_ASK driver(2000, -1, 1, -1, false, 1); 
```

**Receiving will not work correctly with this patch** 

## Save RAM when transmitting only
This library also allows saving RAM, 82 bytes, by disabling code for receiving.

This is achieved by enabling (uncommenting) `RH_ASK_TX_ONLY` at the beginning of `RH_ASK.h`.

## Timer 1 on ATtiny
RH_ASK on ATtiny8x uses Timer 0 to generate interrupts 8 times per bit interval. 
Timer 0 is used by Arduino platform for `millis()`/`micros()` which is in turn used by `delay()`.
Timer 1 is also used by some other libraries, e.g. Servo. Always check usage of Timer 1 before enabling this.

Support for Timer 1 on ATtiny devices is already part of the official [RadioHead library](https://www.airspayce.com/mikem/arduino/RadioHead/) distribution since 1.116, where it must be enabled in the `RH_ASK.cpp`

In this library, enabling Timer 1 is done by uncommenting `RH_ASK_ATTINY_USE_TIMER1` at the beginning of `RH_ASK.h`.








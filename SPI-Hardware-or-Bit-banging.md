One of the things that the FastLED library will do is attempt to use the fastest mechanism available to it for driving your leds.  This stands out most clearly with SPI based chipsets (e.g. the APA102, WS2801, etc...).

When you set up your leds, you give FastLED a data pin and a clock pin:

```
FastLED.addLeds<APA102,7,11>(leds,NUM_LEDS);
```

If the data pin and clock pin that you give FastLED happened to be pins that have hardware SPI available for them, the library will automatically use the hardware SPI system.  If, for some reason, you don't want it to use the hardware SPI system, you can force software spi by putting the following at the top of your code:

```
#define FASTLED_FORCE_SOFTWARE_SPI
#include <FastLED.h>
```

Of course, the question then becomes, "What pins are hardware SPI pins?"  Here's a quick rundown:

* Arduino ATMega328P based board:
  * Hardware SPI - data 11, clock 13
  * USART SPI - data 1, clock 4
* Arduino 32U4 based board:
  * Hardware SPI - data 16, clock 15 (on the ISCP header on arduino provided boards)
* Teensy++ 2:
  * Hardware SPI - data 22, clock 21
  * USART SPI - data 3, clock 5
* Teensy 2:
  * Hardware SPI - data 2, clock 1
  * USART SPI - data 8, clock 23
* Arduino ATMega1280/2560 based boards:
  * Hardware SPI - data 51, clock 53 
* Teensy 3/3.1:
  * Hardware SPI - data 11, clock 13
  * Hardware SPI - data 7, clock 14
* Arduino Due:
  * Hardware SPI - data 75, clock 76

### What about USART SPI? ###

You will notice that on Atmega328p based boards the library supports using the USART in SPI mode as well.  You will also notice that this is not available on the other arduino boards.  This is because the arduino folks, in their infinite wisdom, decided to wire the XCK line to an led, rather than anything that you can get at.  So, no USART based SPI for you there.

It is possible that I can use the USARTs on the Teensy 3/3.1 and Arduino Due for SPI as well.  This will come in future revisions of the library if so.

### What about the other SPI/USART pins ###

On most devices, the SPI hardware wants four pins - data in, data out, clock, and a select line.  While writing led data only uses the data out and clock pins, on some platforms using the SPI hardware disables the use of the other pins:

* Teensy 3/3.1: Will disable the use of the MISO pin while writing led data, but will enable it again afterwards. 
* Hardware SPI on AVR based devices: Will disable the use of the MISO and SELECT pins while writing led data, but will enable it again afterwards
* UART in SPI mode on AVR based devices: The RX pin may or may not be available (needs further testing).  Note that if you use the UART in SPI mode for leds, you will not be able to use the UART as a UART for other purposes.  Note that using Serial for printing uses the UART on the Atmega328p.
* Hardware SPI on the arduino DUE: The MISO pin will be unavailable for use
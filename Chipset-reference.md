Here's a list of the chipsets that are currently supported by the library as well as some notes about them based on our experiences.  Information pulled from either data sheets or experimentation.

### Current, supported hotness

* ws2811/ws2812/ws2812B - (adafruit sells these as "neopixels") super cheap (30 leds/m for $6, 60 leds/m for $11!), very slow data rate (800Kbps - meaning you'd want to investigate parallel output for more than a few hundred leds - see paul's excellent OctoWS2811 if you're using a teensy 3 - and i'm currently working on code for FastLED that will allow grouping/blocking of multiple streams of output in parallel - hopefully that will be out some time this year - I had an installation going in on july 4th that used it :).  Also - many of the strips are 1 led, 1 controller, so you can cut at every led.  Even better, is the ws2812 variant, which is the led and chip in a single package (some people still sell these as ws2811 - but the protocol is the same) - so it can be very very compact.
* lpd8806's - less cheap (closer to $16/meter shipped for 48/m), but super fast (i've pushed them at upwards of 22Mbps!).  Also, they're paired, so it's one controller per 2 rgb pixels.  Note that these only really display 7 bits per channel, not 8, so they can really only display 128 different levels of light for each color channel.  Programming API is still 8 bits, but the low bit is meaningless.

### Older, still supported, lukewarm

* ws2801 - older, cheap(ish) - but slow (1Mbps), i've found it prone to glitching at longer lengths, and higher data rates are right out.  
* tm1809/1804 - similar in protocol to the ws8211, similar cost benefit when it was out, 1 IC per 3 rgb leds, seems to be a lot twiticher re: line interference (the 1809 controls 3 rgb pixels, the 1804 controls 1)
* tm1803 - slower speed version of the tm1809, sold primarily by radio shack 

### Weird shit

* UCS1903 - similar to tm1809/ws2811.  Not sure why this exists, honestly.  Very very slow protocol, closer to 400kbps.
* SM16716 - implemented because a couple people asked for it.  Terrible protocol.  
* DMX controllers - if your'e controlling your leds using DMX from an arduino, this will drive DMX using the rest of the led library

### Upcoming

(as in, soon, depending, timeframe), unclear on hotness, or if they will be made to work.

* P9813 - This is the chipset used in Cool Neon's Total Control Lighting (or Total Lighting Control?  I don't remember which it is).  I have reference code for this but haven't tested it yet.
* TM1829 - similar to the TM1809/WS2811, but also allows setting 32 base current levels for brightness/power usage control - i have a set of these, but the code for them doesn't work yet
* TLS3001 - I get a lot of requests for this one.  Unsure what benefits it provides, but among other things it's 12-bits per color vs. 8-bit for most of the currently supported chipsets.
* APA102 - I don't remember much about this one, other than I have a sample that I was asked to implement support for
* TI TLC5940 - this is a heavy hitter - also 12 bit color, led color correction support, 16 led channels per chip - i've heard of people doing RGBW with setups like this.﻿
* TI TLC5947 like above, but with 24 channels
* LPD1886 - another 12-bit-capable driver chip, which unlike the LPD8806, appears to be internally clocked (i.e., the LPD8806 uses real "SPI", but this chip does not).  Although it doesn't offer software color correction, it does drive the red channel 1.5ma higher than the green and blue channels, which helps compensate.

Here's a chart listing the various chipsets and pieces of data that we know about them at the moment.  Chipset power draw is how much power a single chip draws when the leds are off, but power is connected:

| Chipset | Supported | Wires | Color Bits | Data Rate | PWM Rate | Chipset Power Draw 
|---------|-----------|-------|------------|---------------|----------|--------------------
| WS2811 | √ | 3 | 8 | 800kbps | 400Hz | 5mw / 1ma@5v 
| WS2812B | √ | 3 | 8 | 800kbps | 400Hz | 5mw / 1ma@5v 
| TM1809 | √ | 3 | 8 | 800kbps | 400Hz | 7.2mw / 0.6ma@12v 
| TM1803 | √ | 3 | 8 | 400kbps | 400Hz | 7.2mw / 0.6ma@12v 
| TM1804 | √ | 3 | 8 | 800kbps | 400Hz | 7.2mw / 0.6ma@12v 
| WS2801 | √ | 4 | 8 | 1Mbps | 2.5kHz | 60mw / 5ma@12v 
| UCS1903 | √ | 3 | 8 | 400kbps | unknown | ? 
| LPD8806 | √ | 4 | 7 | 1-20Mbps | 4kHz | ? 
| SM16716 | √ | 4 | 8 | ? | ? | ? 
| TM1829 | X | 3 | 8 |  1.6Mbps/800kbps | 7kHz | 6ma@12v 
| P9813 | X | 4 | 8 | ? | ? | ? 
| TLS3001 | X | ? | 12 | ? | ? | ? 
| APA102 | X | ? | ? | ? | ? | ? 
| TLC5940 | X | 4 | 12 | ? | ? | ? 
| TLC5947 | X | 4 | 12 | ? | ? | ? 
| LPD1886 | X | 3 | 12 | ? | ? | ? 

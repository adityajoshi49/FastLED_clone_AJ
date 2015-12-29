#### 1 I'm losing serial data when I call FastLED.show(), why?

Short version - you're running into problems with interrupts.  Long version - see the [[Interrupt problems]] wiki page.

#### 2 Help!  When I say leds[0] = CRGB::Red it comes out green!

Not all LED chipsets receive their data in RGB order.  See [[RGB Calibration]] for more info on how to adjust the rgb ordering.

#### 3 Help!  I'm getting a compiler error about "avr/io.h" not being found!

This most likely means that you are compiling for a platform that FastLED doesn't yet support.  Please [check the FastLED issues](http://fastled.io/issues) to see if there's already an issue for your platform, and if not, [make a new issue to add your platform](https://github.com/FastLED/FastLED/issues/new).  


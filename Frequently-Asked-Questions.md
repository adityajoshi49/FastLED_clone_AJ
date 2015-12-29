#### 1. I'm losing serial data when I call FastLED.show(), why?

Short version - you're running into problems with interrupts.  Long version - see the [[Interrupt problems]] wiki page.

#### 2. Help!  When I say leds[0] = CRGB::Red it comes out green!

Not all LED chipsets receive their data in RGB order.  See [[RGB Calibration]] for more info on how to adjust the rgb ordering.

#### 3. Help!  I'm getting a compiler error about "avr/io.h" not being found!

This most likely means that you are compiling for a platform that FastLED doesn't yet support.  Please [check the FastLED issues](http://fastled.io/issues) to see if there's already an issue for your platform, and if not, [make a new issue to add your platform](https://github.com/FastLED/FastLED/issues/new).  

#### 4. Help!  I'm getting weird flickering with my leds, and nothing looks right, why?

First thing to check is the wiring.  Make sure that you have power going to the + (or vcc) line on your leds.  Make sure that you have ground from your power going to the - (or gnd) line on your leds.  Also, make sure that you are running ground to ground on your controller as well, especially if you are running your leds off of a separate power supply than your controller.  Next, check your data lines:

Are your data (or data and clock) lines going to DIN (or DIN and CIN) on the leds?  If you connect the data line from your arduino to the DOUT (data out) pin on your leds, nothing is going to come out right.

Likewise, if you are using a 4-wire chipset like the APA102, if you have connected your clock line to your data line and vice versa, then Weird Stuff™ is going to happen.

#### 4b. I've corrected my wiring but now nothing lights up!

For some LED chipsets, if you accidentally wire ground and power backwards, or if you apply power to DIN, you will blow out the led.  _*You have not destroyed your entire strip of leds*_.  Luckily, you've most likely only damaged the first led in the chain, and you can cut it out and re-connect your wiring to the second led and continue using the rest of the leds.
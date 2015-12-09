# **UNDER DEVELOPMENT - EVERYTHING IN HERE SUBJET TO CHANGE AND/OR BEING WRONG**

### Introducing CRGBSet ###

Up until now, everyone has been used to using a standard C array to access their RGB objects.  This lets you do things like the following:

```
for(int i = 0; i < NUM_LEDS; i++) { leds[i] = CRGB::Red; FastLED.delay(33); leds[i] = CRGB::Black; }
```

This moves a simple red dot down the line of leds.  What if you wanted to make the dot two pixels wide?  You used to have to do something like this:

```
for(int i = 0; i < NUM_LEDS-1; i++) { 
  leds[i] = CRGB::Red; 
  leds[i+1] = CRGB::Red;
  FastLED.delay(33); 
  leds[i] = CRGB::Black; 
  leds[i+1] = CRGB::Black;
}
```

Now, though, FastLED is adding a new container class, ```CRGBSet``` which allows you to work on portions of your led array in one shot.  For example, if you want a 2 led wide dot, assuming ```leds``` is a ```CRGBSet``` you can just do:

```
for(int i = 0; i < NUM_LEDS-1; i++) { leds(i,i+1) = CRGB::Red; FastLED.delay(33); leds(i,i+1) = CRGB::Black; }
```

If you could do it to an individual pixel before, you can do it to a set of pixels now.  In addition, ```CRGBSet``` can be passed into most places where you currently pass in ```CRGB*```.  So, starting at the beginning, with creating a CRGBSet - here's a simple demo program:

```
#include <FastLED.h>
#define NUM_LEDS 40
CRGB rawleds[NUM_LEDS];
CRGBSet leds(rawleds, NUM_LEDS);

void setup() { FastLED.addLeds<APA102>(leds, NUM_LEDS); }
void loop() { static uint8_t hue=0; leds.fill_rainbow(hue++); FastLED.delay(30); }
```

This simply fills a rainbow in on the main ```CRGBSet``` called ```leds```.  What if you want a mirrored type effect?  You could replace your loop line with something like this:

```
void loop() { 
  static uint8_t hue=0;
  leds(0,NUM_LEDS/2 - 1).fill_rainbow(hue++);  // fill the first 20 items with a rainbow
  leds(NUM_LEDS/2, NUM_LEDS-1) = leds(NUM_LEDS/2-1,0);
  FastLED.delay(30);
}
```

This is a work in progress - you can see some more ideas on things you do on the g+ post here - https://plus.google.com/102282558639672545743/posts/a3VeZVhRnqG - and I will try to update this page with more documentation and examples as I work on/expand this functionality.
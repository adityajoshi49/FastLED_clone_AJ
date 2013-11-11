# CRGB Reference

<wiki:toc max_depth="3" />

A "CRGB" is an object representing a color in RGB color space.  It contains simply:
 * a one byte value (0-255) representing the amount of red, 
 * a one byte value (0-255) representing the amount of green, 
 * a one byte value (0-255) representing the amount of blue
in a given color.  

Typically, when using this library, each LED strip is represented as an array of CRGB colors, one color for each LED pixel.
```
  #define NUM_LEDS 160

  CRGB leds[ NUM_LEDS ];
```

For more general information on what the RGB color space is, see http://en.wikipedia.org/wiki/RGB_color_model

# Data Members

CRGB has three one-byte data members, each representing one of the three red, green, and blue color channels of the color.  There is more than one way to access the RGB data; each of these following examples does exactly the same thing:
```
  // The three color channel values can be referred to as "red", "green", and "blue"...
  leds[i].red   = 50;
  leds[i].green = 100;
  leds[i].blue  = 150;

  // ...or, using the shorter synonyms "r", "g", and "b"...
  leds[i].r = 50;
  leds[i].g = 100;
  leds[i].b = 150;

  // ...or as members of a three-element array:
  leds[i][0] = 50;  // red
  leds[i][1] = 100; // green
  leds[i][2] = 150; // blue
```

## Direct Access
You are welcome, and invited, to directly access the underlying memory of this object if that suits your needs.  That is to say, there is no "CRGB::setRed( myRedValue )" method; instead you just directly store 'myRedValue' into the ".red" data member on the object. All of the methods on the CRGB class expect this, and will continue to operate normally.  This is a bit unusual for a C++ class, but in a microcontroller environment this can be critical to maintain performance.

The CRGB object "is trivially copyable", meaning that it can be copied from one place in memory to another and still function normally.

# Methods 

In addition to simply providing data storage for the RGB colors of each LED pixel, the CRGB class also provides several useful methods color-manipulation, some of which are implemented in assembly language for speed and compactness.  Often using the class methods described here is faster and smaller than hand-written C/C++ code to achieve the same thing.

## Setting RGB Colors

CRGB colors can be set by assigning values to the individual red, green, and blue channels.  In addition, CRGB colors can be set a number of other ways which are often more convenient and compact.  The two pieces of code below perform the exact same function.
```
  //Example 1: set color from red, green, and blue components individually
  leds[i].red =    50;
  leds[i].green = 100;
  leds[i].blue =  150;

  //Example 2: set color from red, green, and blue components all at once
  leds[i] = CRGB( 50, 100, 150);
```
Some performance-minded programmers may be concerned that using the 'high level', 'object-oriented' code in the second example comes with a penalty in speed or code size.  However, this is simply not the case;  the examples above generate literally identical machine code, taking up exactly the same amount of program memory, and executing in exactly the same amount of time.  Given that, the choice of which way to write the code, then, is entirely a matter of personal taste and style.  All other things being equal, the simpler, higher-level, more object-oriented code is generally recommended.

Here are the other high-level ways to set a CRGB color in one step:
```
  // Example 3: set color via 'hex color code' (0xRRGBB)
  leds[i] = 0xFF007F;

  // Example 4: set color via any named HTML web color
  leds[i] = CRGB::HotPink;

  // Example 5: set color via setRGB
  leds[i].setRGB( 50, 100, 150);
```
Again, for the performance-minded programmer, it's worth noting that all of the examples above compile down into exactly the same number of machine instructions.  Choose the method that makes your code the simplest, most clear, and easiest to read and modify. 

Colors can also be copied from one CRGB to another:
```
  // Copy the CRGB color from one pixel to another
  leds[i] = leds[j];
```
If you are copying a large number of colors from one (part of an) array to another, the standard library function memmove can be used to perform a bulk transfer; the CRGB object "is trivially copyable".
```
  // Copy ten led colors from leds[src .. src+9] to leds[dest .. dest+9]
  memmove( &leds[dest], &leds[src], 10 * sizeof( CRGB) );
```
Performance-minded programmers using AVR/ATmega MCUs to move large number of colors in this way may wish to use the alternative "memmove8" library function, as it is measurably faster than the standard libc "memmove".

## Setting HSV Colors 

### Introduction to HSV 
CRGB color objects use separate red, green, and blue channels internally to represent each composite color, as this is exactly the same way that multicolor LEDs do it: they have one red LED, one green LED, and one blue LED in each 'pixel'.  By mixing different amounts of red, green, and blue, thousands or millions of resultant colors can be displayed. 

However, working with raw RGB values in your code can be awkward in some cases.  For example, it is difficult to work express different tints and shades of a single color using just RGB values, and it can be particular daunting to describe a 'color wash' in RGB that cycles around a rainbow of hues while keeping a constant brightness.

To simplify working with color in these ways, the library provides access to an alternate color model based on three different axes: Hue, Saturation, and Value (or 'Brightness').  For a complete discussion of HSV color, see http://en.wikipedia.org/wiki/HSL_and_HSV , but briefly:
 * Hue is the 'angle' around a color wheel
 * Saturation is how 'rich' (versus pale) the color is
 * Value is how 'bright' (versus dim) the color is

In the library, the "hue" angle is represented as a one-byte value ranging from 0-255.  It runs from red to orange, to yellow, to green, to aqua, to blue, to purple, to pink, and back to red. Here are the eight cardinal points of the hue cycle in the library, and their corresponding hue angle. 
 * Red (0..)
 * Orange (32..)
 * Yellow (64..)
 * Green (96..)
 * Aqua (128..)
 * Blue (160..)
 * Purple (192..)
 * Pink(224..)
Often in other HSV color spaces, hue is represented as an angle from 0-360 degrees.  But for compactness, efficiency, and speed, this library represents hue as a single-byte number from 0-255.

"saturation" is a one-byte value ranging from 0-255, where 255 means "completely saturated, pure color", 128 means "half-saturated, a light, pale color", and 0 means "completely de-saturated: plain white".

"value" is a one-byte value ranging from 0-255 representing brightness, where 255 means "completely bright, fully lit", 128 means "somewhat dimmed, only half-lit", and zero means "completely dark: black."

### The CHSV Object 
In the library, a CHSV object is used to represent a color in HSV color space.  The CHSV object has the three one-byte data members that you might expect:
 * hue (or 'h')
 * saturation (or 'sat', or just 's')
 * value (or 'val', or just 'v')
These can be directly manipulated in the same way that red, green, and blue can be on a CRGB object.  CHSV objects are also "trivially copyable".

```
  // Set up a CHSV color
  CHSV paleBlue( 160, 128, 255);
  
  // Now...
  //   paleBlue.hue == 160
  //   paleBlue.sat == 128
  //   paleBlue.val == 255
```

### Automatic Color Conversion
The library provides fast, efficient methods for converting a CHSV color into a CRGB color. Many of these are automatic and require no explicit code.

For example, to set an led to a color specified in HSV, you can simply assign a CHSV color to a CRGB color:
```
  // Set color from Hue, Saturation, and Value.  
  // Conversion to RGB is automatic. 
  leds[i] = CHSV( 160, 255, 255);
  
  // alternate syntax
  leds[i].setHSV( 160, 255, 255);
  
  // set color to a pure, bright, fully saturated, hue
  leds[i].setHue( 160);
```

There is no conversion back from CRGB to CHSV provided with the library at this point.

### Explicit Color Conversion 

There are two different HSV color spaces: "spectrum" and "rainbow", and they're not exactly the same thing.  Wikipedia has a good discussion here http://en.wikipedia.org/wiki/Rainbow#Number_of_colours_in_spectrum_or_rainbow but for purposes of the library, it can be summed up as follows:
 * "Spectra" have barely any real yellow in them; the yellow band is incredibly narrow.
 * "Rainbows" have a band of yellow approximately as wide as the 'orange' and 'green' bands around it; the yellow range is easy to see.

All of the automatic color conversions in the library use the "HSV Rainbow" color space, but through use of explicit color conversion routines, you can select to use the "HSV Spectrum" color space.

The first explicit color conversion function is hsv2rgb_rainbow, which is used in the automatic color conversions:
```
  // HSV (Rainbow) to RGB color conversion
  CHSV hsv( 160, 255, 255); // pure blue in HSV Rainbow space
  CRGB rgb;
  hsv2rgb_rainbow( hsv, rgb);
  // rgb will now be (0, 0, 255)  -- pure blue as RGB
```

The HSV Spectrum color space has different cardinal points, and only six of them, which are correspondingly spread out further numerically:
 * Red (0..)
 * Yellow (42..)
 * Green (85..)
 * Aqua (128..)
 * Blue (171..)
 * Purple (213..)

The hsv2rgb_spectrum conversion function's API is identical to hsv2rgb_rainbow:
```
  // HSV (Spectrum) to RGB color conversion
  CHSV hsv( 171, 255, 255); // pure blue in HSV Spectrum space
  CRGB rgb;
  hsv2rgb_spectrum( hsv, rgb);
  // rgb will now be (0, 0, 255)  -- pure blue as RGB
```

Why use the Spectrum color space, instead of Rainbow?  The HSV Spectrum color space can be converted to RGB a little faster than the HSV Rainbow color space can be -- but the results are not as good visually; what little yellow there is appears dim, and at lower brightnesses, almost brownish.  So there is a trade-off between a few clock cycles and visual quality.  In general, start with the Rainbow functions (or better yet, the automatic conversions), and drop down to the Spectrum functions only if you completely run out of speed.

Both color space conversion functions can also convert an array of CHSV colors to a corresponding array of CRGB colors:
```
  // Convert ten CHSV rainbow values to ten CRGB values;
  CHSV hsvs[10];
  CRGB leds[10];
  // (set hsv values here)
  hsv2rgb_rainbow( hsvs, leds, 10); // convert all
```
The function "hsv2rgb_spectrum" can also be called this way for bulk conversions.

## Comparing Colors 

CRGB colors can be compared for exact matches using == and !=.

CRGB colors can be compared for relative light levels using <, >, <=, and =>.  Note that this is a simple numeric comparison, and it will not always match the perceived brightness of the colors.  

Often it is useful to check if a color is completely 'black', or if it is 'lit' at all.  You can do this by testing the color directly with 'if', or using it in any other boolean context.
```
  // Test if a color is lit at all (versus pure black)
  if( leds[i] ) { 
    /* it is somewhat lit (not pure black) */ 
  } else {
    /* it is completely black */
  }
```

## Color Math 

The library supports a rich set of 'color math' operations that you can perform on one or more colors.  For example, if you wanted to add a little bit of red to an existing LED color, you could do this:
```
  // Here's all that's needed to add "a little red" to an existing LED color:
  leds[i] += CRGB( 20, 0, 0);
```

That's it.  

If you've ever done this sort of thing by hand before, you may notice something missing: the check for the red channel overflowing past 255.  Traditionally, you've probably had to do something like this:
```
  // Add a little red, the old way.
  uint16_t newRed;
  newRed = leds[i].r + 20;
  if( newRed > 255) newRed = 255; // prevent wrap-around
  leds[i].r = newRed;
```
This kind of add-and-then-check-and-then-adjust-if-needed logic is taken care of for you inside the library code for adding two CRGB colors, inside operator+ and operator+=.  Furthermore, much of this logic is implemented directly in assembly language and is substantially smaller and faster than the corresponding C/C++ code.  The net result is that you no longer have to do all the checking yourself, and your program runs faster, too.  

These 'color math' operations are part of what makes the library fast: it lets you develop your code faster, as well as executing it faster. 

All of the math operations defined on the CRGB colors are automatically protected from wrap-around, overflow, and underflow.

### Adding and Subtracting Colors
```
  // Add one CRGB color to another.
  leds[i] += CRGB( 20, 0, 0);
    
  // Add a constant amount of brightness to all three (RGB) channels.
  leds[i] += 20;
  
  // Add a constant "1" to the brightness of all three (RGB) channels.
  leds[i]++;
  
  
  // Subtract one color from another.
  leds[i] -= CRGB( 20, 0, 0);
  
  // Subtract a contsant amount of brightness from all three (RGB) channels.
  leds[i] -= 20;
  
  // Subtract a constant "1" from the brightness of all three (RGB) channels.
  leds[i]--;
```
### Dimming and Brightening Colors

There are two different methods for dimming a color: "video" style and "raw math" style.  Video style is the default, and is explicitly designed to never accidentally dim any of the RGB channels down from a lit LED (no matter how dim) to an UNlit LED -- because that often comes out looking wrong at low brightness levels.  The "raw math" style will eventually fade to black.

Colors are always dimmed down by a fraction.  The dimming fraction is expressed in 256ths, so if you wanted to dim a color down by 25% of its current brightness, you first have to express that in 256ths.  In this case, 25% = 64/256.

```
  // Dim a color by 25% (64/256ths)
  // using "video" scaling, meaning: never fading to full black
  leds[i].fadeLightBy( 64 ); 
```

You can also express this the other way: that you want to dim the pixel to 75% of its current brightness.   75% = 192/256.  There are two ways to write this, both of which will do the same thing.  The first uses the %= operator; the rationale here is that you're setting the new color to "a percentage" of its previous value:
```
  // Reduce color to 75% (192/256ths) of its previous value
  // using "video" scaling, meaning: never fading to full black
  leds[i] %= 192;
```
The other way is to call the underlying scaling function directly.  Note the "video" suffix.
```
  // Reduce color to 75% (192/256ths) of its previous value
  // using "video" scaling, meaning: never fading to full black
  leds[i].nscale8_video( 192);
```

If you want the color to eventually fade all the way to black, use one of these functions:
```
  // Dim a color by 25% (64/256ths)
  // eventually fading to full black
  leds[i].fadeToBlackBy( 64 ); 

  // Reduce color to 75% (192/256ths) of its previous value
  // eventually fading to full black
  leds[i].nscale8( 192);
```

A function is also provided to boost a given color to maximum brightness while keeping the same hue:
```
  // Adjust brightness to maximum possible while keeping the same hue.
  leds[i].maximizeBrightness();
```

Finally, colors can also be scaled up or down using multiplication and division.
```
  // Divide each channel by a single value
  leds[i] /= 2;
    
  // Multiply each channel by a single value
  leds[i] *= 2;
```

### Constraining Colors Within Limits 
The library provides a function that lets you 'clamp' each of the RGB channels to be within given minimums and maximums.  You can force all of the color channels to be at least a given value, or at most a given value.  These can then be combined to limit both minimum and maximum. 
```
  // Bring each channel up to at least a minimum value.  If any channel's
  // value is lower than the given minimum for that channel, it is
  // raised to the given minimum.  The minimum can be specified separately
  // for each channel (as a CRGB), or as a single value.
  leds[i] |= CRGB( 32, 48, 64);
  leds[i] |= 96;
  
  
  // Clamp each channel down to a maximum value.  If any channel's
  // value is higher than the given maximum for that channel, it is
  // reduced to the given maximum.  The minimum can be specified separately
  // for each channel (as a CRGB), or as a single value.
  leds[i] &= CRGB( 192, 128, 192);
  leds[i] &= 160;
  
```
### Misc Color Functions 
The library provides a function that 'inverts' each RGB channel.  Performing this operation twice results in the same color you started with.
```
  // Invert each channel
  leds[i] = -leds[i];
```
The library also provides functions for looking up the apparent (or mathematical) brightness of a color.
```
  // Get brightness, or luma (brightness, adjusted for eye's sensitivity to 
  // different light colors.   See http://en.wikipedia.org/wiki/Luma_(video) )
  uint8_t luma = leds[i].getLuma();
  uint8_t avgLight = leds[i].getAverageLight();
```

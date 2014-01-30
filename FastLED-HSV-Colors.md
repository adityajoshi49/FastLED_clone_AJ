The FastLED Hue-Saturation-Value color model differs from 'traditional' computer HSV color models in two important respects: first is differences in the numeric range of values used to represent colors (everything here is a one-byte value from 0-255), and second is in the distribution of colors themselves (FastLED defaults to using a 'rainbow' color map, instead of a traditional 'spectrum' color map).

### Numeric range differences: everything here is exactly one byte
In 'traditional' computer HSV color models, hue is represented as a number (of degrees) from 0-360.  Saturation and value are often represented as numbers (percentages) from 0-100.  But neither "360" nor "100" is a particularly computer-native number, and there's no strong reason to use 'degrees' to represent hue, nor 'percentages' to represent saturation or value; they're all pretty much arbitrary scales.  Accordingly, to make your code smaller, faster, and more efficient, the FastLED library uses simple one-byte values (from 0-255) for hue, and for saturation, and for value.  The performance implications are discussed further below, but suffice it to say that it's faster this way.

### Color distribution
Traditional computer HSV color models use a 'spectrum' color map, and FastLED does offer an "hsv2rgb_spectrum" function.  However, by default FastLED uses a 'rainbow' color map instead of a spectrum. The 'rainbow' color map provides more evenly-spaced color bands, including a band of 'yellow' which is the same width as other colors, and has an appropriately high inherent brightness.  Traditional 'spectrum' HSV color maps have much narrower bands of yellow, and the yellow can also appear muddy.

### Why one-byte FastLED hues are so much faster
Animations using FastLED HSV colors are often be much, _much_ faster than traditional HSV code, because FastLED HSV code has been designed explicitly for microcontroller environments where every byte and every cycle counts.  One of the big design decisions was to represent hue as a number from 0-255, rather than from 0-359; here's a code example of how the FastLED hue range design (from 0-255, vs 0-359) makes your animation code faster and more compact, just by keeping 'hue' down to a single one-byte number.

If 'hue' were to run from 0-359, as with traditional HSV ranges, here's what you'd have to do have a variable that cycles to a new hue each time through your main loop, stepping by an arbitrary amount each time, and wrapping around back to the start.  

    uint16_t gHue = 0;
    uint8_t  gHueDelta = 3;

    void loop() {
      gHue += gHueDelta; // compute new hue
      gHue = gHue % 360; // bring hue back in range
      ...
    }

This code (above) takes up about 84 bytes of program space, and can execute the "hue calculation" about 75,000 times per second.  The use of "%" could be replaced with some IF statements and subtraction, but if you want to be able to add anything you like to gHue and have it still fall in the legal range of 0-359, this is basically what you'd wind up doing.  (Using a hue value of 0-95, as shows up in some 'color wheel' HSV functions winds up with similar constraints; you have to keep checking to keep the hue 'in range' from zero to 95.)

Contrast this: the same functionality using FastLED's 0-255 range for HSV hues:

    uint8_t gHue = 0;
    uint8_t gHueDelta = 3;

    void loop() {
      gHue += gHueDelta; // compute new hue value, automatically in range
      ...
    }

This code, using FastLED's hue range of 0-255 takes up only less than half the program space (34 bytes), and can execute the "hue calculation" about 1.5 _million_ times per second: that's **twenty times faster**.

Of course, there's _far_ more to animation performance than just incrementing one variable.  But by keeping the range of 'hue' to a single, one-byte value with the full range of 0-255 is a design decision we've made that leads to the rest of _your_ animation code becoming more compact, fast, and efficient as well.
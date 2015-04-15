A collection of known platform limitations/errata (a work in progress):

* On the Arduino Megas, you can't use pins 42,43,44,45,46,47,48, and 49 with 3-wire chipsets.  This is because the timing for accessing PORTL is more expensive than the other ports, and it throws all the timing off.
* In order to keep scale8 _fast_ on 8-bit platforms, it is quite literally ((x*s)>>8) (but done in asm) to minimize the cycle count.  This has the side effect of meaning that when the scaling value is set to the max, at 255, it will still scale down the value (e.g. 1 to 0, 255 becomes 254)
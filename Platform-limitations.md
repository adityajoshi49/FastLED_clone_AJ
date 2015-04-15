A collection of known platform limitations/errata (a work in progress):

* On the Arduino Megas, you can't use pins 42,43,44,45,46,47,48, and 49 with 3-wire chipsets.  This is because the timing for accessing PORTL is more expensive than the other ports, and it throws all the timing off.
* In order to keep scale8 _fast_ on 8-bit platforms, it is quite literally ((x*s)>>8) (but done in asm) to minimize the cycle count.  This has the side effect of meaning that values of 1 scale to 0, even when the scaling factor is 255 and 255 will scale to 254.
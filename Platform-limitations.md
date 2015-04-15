A collection of known platform limitations (a work in progress):

* On the Arduino Megas, you can't use pins 42,43,44,45,46,47,48, and 49 with 3-wire chipsets.  This is because the timing for accessing PORTL is more expensive than the other ports, and it throws all the timing off.

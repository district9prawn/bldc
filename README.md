# Vesc test firmware with field weakening support

This fork adds field weakening for SPM motors (not MTPA for IPM motors) using a simple control loop.

This will allow the motor to spin quite a bit faster than normal. 
If the throttle is released suddenly at high speed, field weakening current is maintained until a safe speed is reached. However, manually pushing the motor until bemf is equivalent to supply voltage will not trigger field weakening unless the throttle or brake is used.
Yes, it will also work during braking, or in reverse, if the speed is high enough.

At the moment, this should only be used in current mode, not in duty or speed mode. 

## Setup
I've set the firmware to use the Lq Ld diff value to enable field weakening. If the value is zero, field weakening is disabled. I've set the "gear ratio" parameter to set the maximum field weakening current relative to the maximum phase current. So if the maximum phase current is set to 100A, setting gear ratio to 0.5 will give at most -50A of field weakening current.

![alt text](https://imgur.com/mCgpp1r)

For safety, I highly recommend setting erpm limits so the stator voltage cannot exceed the battery voltage and cause loss of control or hardware damage!

### License

The software is released under the GNU General Public License version 3.0

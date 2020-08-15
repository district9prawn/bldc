# Vesc test firmware with field weakening support

This fork adds field weakening for SPM motors (not MTPA for IPM motors) based on a constant voltage constant power strategy described here. https://au.mathworks.com/help/mcb/ref/mtpacontrolreference.html

This will allow the motor to spin quite a bit faster than normal. 
If the throttle is released suddenly at high speed, field weakening current is maintained until a safe speed is reached. However, manually pushing the motor until bemf is equivalent to supply voltage will not trigger field weakening unless the throttle or brake is used.
Yes, it will also work during braking, or in reverse, if the speed is high enough.

At the moment, this should only be used in current mode, not in duty or speed mode. 

## Setup
Compile the firmware for your hardware. At the time of writing, the latest development version of vesc tool (2.07) is the one to use. You need a version that has the field for the Ld Lq difference, as this is used to calculate the base speed. 

For initial testing, I highly recommend running off a current limited power supply limited to a modest current.

I've set the firmware to use the Lq Ld diff value to enable field weakening. If the value is zero, field weakening is disabled. In vesc tool terminal, you can use measure_ind 0.1 to get the Lq Ld diff. Unfortunately, the inductance value vesc tool detects and uses does not work well for field weakening and needs to be adjusted with the gear ratio paramter. The Lq Ld diff value seems to work well enough so this can be entered into vesc tool. If you use the measured inductance value as is (gear ratio = 1), field weakening will be way too aggressive. Start with a higher gear ratio of 6 (calculate fw with 6x higher inductance value), and the field weakening will be weaker. Play around with this ratio and tabulate the resulting erpm at 95% duty and various d axis currents (use real time plots in vesc tool). 

I recommend plotting your experimentally obtained field weakening curve against calculated field weakening currents to help find the correct ratio. The formula used is:
Id = (wbase - w) * lambda / (w * Ld)
Where wbase is the unloaded speed of your motor with no field weakening, w is your speed variable, lambda is flux linkage measured by the vesc and Ld is D axis inductance. Note speeds are in radians. Ld = ratio * L - L_diff/2
For my 6374 149kv with a "ratio" of 1, the curve looks like this. The calculated curve in orange is way too much. 
![alt text](https://i.imgur.com/4OCBX4E.png)

The correct ratio is 4.5x
![alt text](https://i.imgur.com/o1oT06Y.png)

At the moment fw current is hard limited to 70 % of max motor current. Since fw current is based on speed, fw currents can be limited using erpm limits. On that note, I highly recommend settings sane erpm limits for safety. 

### License

The software is released under the GNU General Public License version 3.0

# FastTomo

FastTomo is a SerialEM script for faster tilt series collection. It achieves speedups by minimizing the use of focusing and tracking shots, and does tracking using correlation between successive Record images of the tilt series data itself. Z compensation is done by pre-calibration before every tilt series.

Supports unidirectional, bidirectional, and dose-symmetric schemes.


# Prerequisites

- Focus and tracking positions should be on tilt-axis
- **Center image shift on tilt axis** should be checked. See [Set Tilt Axis Offset](https://bio3d.colorado.edu/SerialEM/hlp/html/menu_tasks.htm#hid_tasks_settiltaxisoffset)
- Make sure target is centered in View mode before starting (not necessary if using runOnNavItem)


# Usage

This script can be used in 2 main ways.
1. Standalone. Go to a location in View mode and run this script to collect a tilt series there.
    - You can also set runOnNavItem to 1, select a point from the Navigator, and then run this script. SerialEM will realign to this point and collect a tilt series.
2. From the Navigator. You can run this script from the Acquire at Points routine in the Navigator menubar. You can also call this script from your own script.

## Parameters
- scheme - tilting scheme to use
    - 0 = bidirectional, 1 = dose-symmetric, 2 = unidirectional
- runOnNavItem - if set to 1, does an initial realign to the selected navigator item
- Debug - if set to 1, gives verbose output and does not suppress reports
- shot - beam to use for tilt series data
- usePrevCalib - skip calibration and use most recent parameters if they exist
    - Calibration parameters are stored as persistent variables. You can periodically clear all persistent variables to do calibrations once in a while.
    - Saves about 70 sec per tilt series
- tolerance - retakes a shot if the current frame is off target by a fraction of the screen
    - E.g. - 0.5 means the target is off by at least 50% of the shorter image dimension
- multiStepTilt_StepSize (probably do not need to edit)
    - Step size for multistep TiltTo function. Some stages cannot tilt by very large angles at once (e.g. -60 to +60). Therefore a multi-step TiltTo function is used in the Dose-Symmetric scheme. The step size can be set to a big value (like 200) to disable multi-step, which saves a few seconds when switching sides.

## Bidirectional Parameters
- startAngleBi - starting angle
    - for asymmetric bidirectional scheme, set to a nonzero angle (e.g. -20)
- firstSideEnd - end tilt angle for the first side
- secondSideEnd = end tilt angle for the second side
- stepSizeBi - step size

## Dose-Symmetric Parameters
The dose-symmetric scheme in this implementation stays on the same side for the next pair of groups. For example, for 1 deg step and a group size of 1, the scheme would be 0, 1, -1, -2, 2, 3, -3, -4, ...
- endAngleDS - max tilt (should be positive)
- stepSizeDS - step size
- groupSizeDS - number of tilt steps per group
- trackingShot - beam to use for tracking
    - You can use V for a larger field of view
- doExtraTrackingShot - do tracking for smallest non-zero tilts, e.g. +/- 3 deg
    - 0 = off, 1 = on
    - For some stages there is a jump at the beginnning of the tilt series between 0 degrees and a small tilt angle. This option will take extra tracking shots for the smallest positive and negative angles.

This implementation also supports switching to bidirectional scheme for higher tilts, and will also increase the exposure time for these higher tilts. End angle and step size will still be specified by endAngleDS and stepSizeDS.
- angleSwitchToBidirectional - switches to bidirectional scheme after this angle (-1 to disable)
- exposureTimeIncreaseFactor - multiplies the current exposure time by this factor

## Unidirectional Parameters
- startAngleUni - starting angle
- endAngleUni - end angle
- stepSizeUni - step size
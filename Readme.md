# FastTomo

FastTomo is a SerialEM script for faster tilt series collection. It achieves speedups by minimizing the use of focusing and tracking shots, and does tracking using correlation between successive Record images of the tilt series data itself. Z compensation is done by pre-calibration before every tilt series.

Supports unidirectional, bidirectional, and dose-symmetric schemes.


# Prerequisites

- Focus and tracking positions should be on tilt-axis
- **Center image shift on tilt axis** should be checked. See [Set Tilt Axis Offset](https://bio3d.colorado.edu/SerialEM/hlp/html/menu_tasks.htm#hid_tasks_settiltaxisoffset)


# Usage
## Parameters
- scheme - tilting scheme to use
    - 0 = bidirectional, 1 = dose-symmetric, 2 = unidirectional
- runOnNavItem - if set to 1, does an initial realign to the selected navigator item
- Debug - if set to 1, gives verbose output and does not suppress reports
- shot - beam to use for tilt series data

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

This implementation also supports switching to bidirectional scheme for higher tilts, and will also increase the exposure time for these higher tilts. End angle and step size will still be specified by endAngleDS and stepSizeDS.
- angleSwitchToBidirectional - switches to bidirectional scheme after this angle (-1 to disable)
- exposureTimeIncreaseFactor - multiplies the current exposure time by this factor

## Unidirectional Parameters
- startAngleUni - starting angle
- endAngleUni - end angle
- stepSizeUni - step size
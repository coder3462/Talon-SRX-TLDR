[Link to Motion Profiling Manual TLDR](#talonsrx-motion-profiling-manual)

[Link to User Guide TLDR](#talonsrx-users-guide)

# TalonSRX-Motion Profiling Manual
[Official Motion Profiling Manual](https://www.ctr-electronics.com/downloads/pdf/Talon%20SRX%20Motion%20Profile%20Reference%20Manual.pdf)

TL;DR

- Control Modes
	- Percent Voltage
	- Voltage Compensation
	- Position Closed-Loop
	- Velocity (Speed) Closed-Loop
	- Current (Draw) Closed-Loop
- The Talon SRX additionally supports a mode where the robot controller can stream a sequence of trajectory points to express an entire motion profile. 
	- Each trajectory point holds the desired velocity, position, and time duration to honor said point
until moving on to the next point. These points can be sent to the Talon before executing the
motion profile ensuring that enough points are buffered for smooth transitions.
	- Alternatively, the trajectory points can be streamed into the Talon as the Talon is executing the
profile, so long as the robot controller sends the trajectory points faster than the Talon
consumes them. 

### Definitions
	- Control Mode: allow a “Robot Controller” to specify/select a target value to meet
	- Motion Profile Buffer (MPB)
The firmware buffer in the Talon SRX. The MPB can hold up to 128 trajectory points at once
(with the current firmware, see Section 9.2 for requirements).
	- Motion Profile Executer (MPE)
The Motion Profile Executer is the firmware that calculates the motor-output based on the active
trajectory point. It updates the motor update every 1ms by summing the velocity feed-forward
term with the output of a position servo. The MPE holds one trajectory point at a time. Should
the Talon Motion Control features be activated when there is no trajectory point buffered into the
MPE, the motor output will be neutral.
	- Trajectory Point
This term refers to the trajectory point that is being sent to the Talon SRX buffering. This is not
the point that is currently be used for calculating the throttle output.
	- The Last Trajectory Point
When generating the trajectory points, the final trajectory point in the profile is referred to as the
“Last Trajectory Point”. This point typically will have the following properties…
- The velocity to feed-forward is set to zero.
- The “Is Last” flag is set to “true”.
This ensures that when the MPE processes the last point, it will hold position indefinitely (or until
the robot controller decides the next course of action).
	- Trajectory Point Duration (Milliseconds)
The time to servo this trajectory point in milliseconds. The minimum value is 1ms and the
maximum is 255ms. If ‘0’ is sent, it will be interpreted as 1ms.
Talon SRX Motion Profile Reference Manual 1/19/2016
Cross The Road Electronics Page 8 1/19/2016
	- Trajectory Point Velocity
This is the velocity to feed-forward when this trajectory point is loaded into the MPE.
	- Trajectory Point Position
This is the position to target for the PID closed loop portion of the MPE inner loop when this
trajectory point is loaded into the MPE.
	- Trajectory Point Profile Slot Select
Selects which slot to pull the closed-loop gains when this trajectory point is processed by the
MPE. Talon persistently stores two sets of closed-loop parameters. This allows for atomic
switching between two unique sets of gain-constants, ramping, and I-Zone.
	- Trajectory Point Is Last
Set to “true” if trajectory point is the final point of the motion profile. This signals the MPE to
continue to servo this point even after it’s time duration expires. Be sure to zero the position of
this trajectory point so that MPE will closed-loop to the final intended position.
	- Trajectory Point Vel (Velocity) Only
If a trajectory point has this flag set, then the target position is ignored, and only the velocity
term is used. This can be used to conveniently “turn off” the position PID portion.
For example, a sequence of points that are Velocity-only provide a way to stream a customthrottle-waveform,
without the need of a sensor.
Additionally, turning off the position PID port can be diagnostically useful in seeing how well the
system responds with just feed-forward. Note that if the “last” point is vel-only, then it will not
servo position.
	- Trajectory Point Zero Position
If a trajectory point has this flag set, the MPE will zero the position value of the selected sensor
when this trajectory point is processed. Typically, this would be done only with the first
trajectory point of the motion profile. The goal is to clear the position so that the position values
of the following trajectory points are relative to the start position of the mechanism.
The generated trajectory points could be calculated to not require re-zeroing the sensor. This
flag merely provides the option to express a profile relative to the current physical position of the
mechanism
	- Motion Profile Set Value (Set Output, or Output Type)
The set-value is interpreted as follows:
0 - Motor output is neutral.
1 - Motor output is driven by the MPE. MPE will pop the “first” point from the MPB. If the
MPE does not have a point (is empty) Motor output is neutral and “Is Underrun” is set.
2 - Motor output is in “hold”. The active trajectory point inside the MPE will be driven
indefinitely (while set-value is ‘2’ and control mode is “Motion Profile”). This is useful
when a profile has finished and the user needs to disconnect the MPE from the MPB to
begin buffering the next action, meanwhile the MPE will continue to servo/drive the
loaded point.
This set-value is typically only useful when the “Last” trajectory point has been reached,
which typically has a target velocity of ‘0’, and simply will servo/maintain the final
position.
	- Active Trajectory
The active trajectory is the trajectory point loaded into the MPE. This holds the velocity,
position, and flags used by the MPE to calculated the motor output.
	- Active Trajectory Is Valid
Since it is possible for the MPE to be “empty”, there must be a flag to instrument this. For
example, if the robot controller sends a nonzero Motion Profile Set Value when there are no
trajectory points buffered, this will cause the MPE to be active when no trajectory point is
available to shift into the MPE. When this happens, this flag will be false, and motor output will
be neutral.
When reading the other member signals of Active Trajectory, this flag should be checked first.
	- Active Trajectory Velocity
The velocity the MPE will feed-forward if activated.
Only valid if “Active Trajectory Is Valid” is true.
	- Active Trajectory Position
The position the MPE will target if activated.
Only valid if “Active Trajectory Is Valid” is true.
	- Active Trajectory Profile Slot Select
The selected slot that the MPE will pull closed-loop parameters from if activated.
Only valid if “Active Trajectory Is Valid” is true.
Talon SRX Motion Profile Reference Manual 1/19/2016
Cross The Road Electronics Page 10 1/19/2016
	- Active Trajectory Is Last
The “Is Last” signal of the active trajectory point currently in the MPE. MPE will continue to
servo this point indefinitely, allowing robot application to prepare next profile or change modes.
Only valid if “Active Trajectory Is Valid” is true.
3.5.4. Active Trajectory Vel (Velocity) Only
The “Vel Only” signal of the active trajectory point currently in the MPE.
Only valid if “Active Trajectory Is Valid” is true.
3.5.5. Active Trajectory Zero Position
The “Zero Pos” signal of the active trajectory point currently in the MPE.
Only valid if “Active Trajectory Is Valid” is true.
3.6. Is Underrun
If the MPE is ready to process a new active trajectory point, but one is not available, this flag is
set. The underrun behavior of the Talon depends on which three situations caused the
underrun…
 Robot application has signaled MPE to start, but there is no buffered trajectory point to
start with. Motor-output is neutral until a point is available.
 Robot application has signaled MPE to hold, but there is no active trajectory point in the
MPE. Motor-output is neutral until a point is available.
 During the execution of a profile, the MPE timed out the active trajectory point, and was
ready for the next point, but one was not available (MPB was empty). When this
happens MPE will continue to use the active trajectory point until a new point is available
in the buffer. In other words, MPE will not release the active trajectory point if the next
point is not available.
All three situations are caused by the robot controller not providing trajectory points to keep
pace with the execution, or enabling the MPE before enough trajectory points are buffered.
This flag is automatically cleared when the problem condition is resolved.
3.7. Has Underrun
When Is Underrun is set, Has Underrun is also set. However, this flag only is cleared by the
robot application using the robot API. This ensures the robot application can poll for underrun
behavior infrequently without risking missing intermittent buffer underruns.
3.8. (Bottom, Firmware-level) Buffer Count
The number of trajectory points buffered in the MPB.
3.9. (Bottom, Firmware-level) Buffer Is Full
Flag indicating the firmware trajectory buffer is MPB.
Talon SRX Motion Profile Reference Manual 1/19/2016
Cross The Road Electronics Page 11 1/19/2016
3.10. Feed-Forward Gain
When used in Motion Control Mode, this value is used to feed-forward the target velocity gain.
In other words, this is the KV value in the Motion Profile inner loop equation.
3.11. Proportional Gain
When used in Motion Control Mode, this value is used as the proportional gain for the position
closed-loop portion of the Motion Profile inner loop equation.
3.12. Integral Gain
When used in Motion Control Mode, this value is used as the integral gain for the position
closed-loop portion of the Motion Profile inner loop equation.
3.13. Derivative Gain
When used in Motion Control Mode, this value is used as the derivative gain for the position
closed-loop portion of the Motion Profile inner loop equation.
3.14. (Top, API-level) Buffer Count
The language-based robot APIs (C++, Java, HERO C#) utilize a “top level” buffer to hold
trajectory points as they funnel into the Talon SRX’s MPB over CAN Bus. This helps simplify
the robot application by allowing the program to one-shot generate the entire motion, buffer it at
once into the robot API, and resume to other tasks while the Talon’s MPB fills.

# TalonSRX-User's Guide

[Official User Guide](http://www.ctr-electronics.com/Talon%20SRX%20User%27s%20Guide.pdf)

TL;DR

- Talonsrx is a speed controller which handles high current loads with low voltage drops and minimal heat generation
	- Has a data port allows quadrature encoders, limit switches and analog sensors
	
- Installing Talon SRX(voltage should never exceed 24V and 40A or smaller breaker should be used in series with TalonSRX positive input):
	- White Wire goes to M+ side of motor
	- Green wire goes to M- side of motor
	- Connect positive input (red wire) to positive terminal on PDP
	- Connect input ground (black wire) to corresponding ground terminal on PDP
	- Make sure firmware always updated before PWM use
	
- Talon SRX has two modes:
	- Break
		- Doesn't have any affect on when motor isn't rotating
		- High influence on robot behavior when used on a high reduction gearbox. Don't worry about it.
	- Coast
		- Black-EMF will not be generated. Don't worry about it.
		- Motor rotation will not be affected by Talon SRX.
	- Talon SRX can switch between both modes during operation.
		- To do so, push B/C CAL button.
- To calibrate PWM(to determine how to connect input signal to output voltage):
	- Hold B/C CAL button until LEDs turn on.
	- While holding button, move joystick/input signal to full forward, then full reverse.

### Definitions:
- Back-EMF: Back Electromotive First
- CAN: Controller Area Network
- Limit switches: a switch for a circuit
- PDP: power distribution panel
- Quadrature encoders: An encoder which indicates both position and direction of rotation

### Wires
- Positive Input(V+): Red wire
- Input Ground(GND): Black wire
- Postitve Output(M+): White wire
- Output Ground(M-): Green wire
- CAN-High/PWM signal(pulse-width modulation): Yellow wire
- CAN-Low/PWM signal(pulse-width modulation): Green wire

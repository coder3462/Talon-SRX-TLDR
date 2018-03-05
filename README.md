[User Guide](#talonsrx-users-guide)

# TalonSRX-User's Guide

[User Guide](http://www.ctr-electronics.com/Talon%20SRX%20User%27s%20Guide.pdf)

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
- CAN stands for Controller Area Network
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

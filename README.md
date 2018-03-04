[http://www.ctr-electronics.com/Talon%20SRX%20User%27s%20Guide.pdf](userguide)

TLDR
# Talon SRX - User's Guide

- Talonsrx is a speed controller which handles high current loads with low voltage drops and minimal heat generation
	- Has a data port allows quadrature encoders, limit switches and analog sensors
- quadrature encoders:
- limit switches: a switch for a circuit
-CAN stands for Controller Area Network
-Positive Input(V+): Red wire
-Input Ground(GND): Black wire
-Postitve Output(M+): White wire
-Output Ground(M-): Green wire
-CAN-High/PWM signal(pulse-width modulation): Yellow wire
-CAN-Low/PWM signal(pulse-width modulation): Green wire
-Installing Talon SRX:
	-White Wire goes to M+ side of motor
	-Green wire goes to M- side of motor
PDP means power distribution panel
-Talon SRX voltage should never exceed 24 V and 40A or smaller breaker should be used in series with TalonSRX positive input
-Connect positive input (red wire) to positive terminal on PDP
-Connect input ground (black wire) to corresponding ground terminal on PDP
-Make sure firmware always updated before PWM use		
-Talon SRX has two modes:
Back Electromotive First == Back-EMF
	-Break
		-Doesn't have any affect on when motor isn't rotating
		-High influence on robot behavior when used on a high reduction gearbox. Don't worry about it.
	-Coast
		-Black-EMF will not be generated. Don't worry about it.
		-motor rotation will not be affected by Talon SRX.
	-Talon SRX can switch between both modes during operation.
		-To do so, push B/C CAL button. Don't worry about it.
-To calibrate PWM to determine how to connect input signal to output voltage.
-Hold B/C CAL button until LEDs turn on.
-While holding button, move joystick/input signal to full forward, then full reverse.
-
		

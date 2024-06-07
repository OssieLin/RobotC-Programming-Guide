# **RobotC Programming Guide**
### By Shahir Latif and Osmond Lin
### Last updated: January 2024
[View Online](https://tinyurl.com/robotcguide)

----

## Introduction
Any program’s job is to take some input, do some calculations, and provide some output. For robotics, the input mainly comes from the controller joysticks and buttons. The processing is done in the VEX Cortex, and the output is moving motors and servos.

RobotC is a programming language, based on the C language, that can be used to write programs for the VEX Cortex. The RobotC environment can be installed at [ROBOTC](https://www.robotc.net/).

In RobotC, most lines of code need to end with a semicolon; if statements and loops are exceptions. Single-line comments are made by typing `//` at the start of a line. Multi-line comments are surrounded by `/* and */`. Variable names cannot have spaces or special characters (except `_`). Variables also cannot start with a number.

### Prerequisite
• Robotc installed 
• Vex Cortex
• Vex controller
• 2 motors (for the basic arcade dirve)
• Wires and cables
Enjoy the guide and your robot!!!

## Setup
1. Open and save a new C file in the RobotC editor. In the menu bar, go to `Robot > Platform Type > VEX Robotics`, and select VEX 2.0 Cortex.
![image1](https://hackmd.io/_uploads/HJ-8hZPKa.png)

2. Click the `Motor and Sensor Setup` button at the top of the RobotC window. In the “Motors” tab, you can specify what motors your program will use. The ports correspond to motor ports on the VEX Cortex.

3. In any two ports **(except for 1 and 10)**, set the type to `3-wire Motor`. Give each motor name to use in code, such as “motorLeft” and “motorRight”

4. Reverse one of the motors. It is difficult to determine which one to reverse during programming, but if you run your code and find that your robot is moving backwards, change which motor you reversed.

![image3](https://hackmd.io/_uploads/ryIqsB2Kp.png)

*When you edit the program’s motor settings, RobotC will automatically insert configuration code at the top of your program. You do not need to directly edit this.*

## Drive Controls
After the robot starts, the program will start executing at `task main()`. Most programs create an infinite loop here which constantly checks for joystick inputs and takes action. An infinite loop can be created with a `while (true) { }` statement.


```c
task main()
{
	while (true)
	{
		// this code will loop
	}
}

```
You can read the value of a joystick with the `VexRT[]` command, with the corresponding channel indicated on the controller, as shown below. For example `VexRT[Ch1]` retrieves the horizontal position of the right joystick. These values range from -127 to 128, with 0 being the joystick at its base position.
![controller1](https://hackmd.io/_uploads/BJ0TBrl9a.png)
![controller2](https://hackmd.io/_uploads/rJlfRrSlqT.png)


Motors can be controlled with the `motor[]` command. The name declared in the “Motor and Sensor Setup” menu goes between the brackets. For example, `motor[motorLeft] = 0;` stops the left motor, and `motor[motorRight] = -127;` moves the right motor backwards at full power.

### Tank Drive
The simplest way to control the robot is tank drive. The left joystick controls the speed of the left wheel, and the right joystick controls the speed of the right wheel. Within the main loop, tank drive can be implemented like this:
```c
motor[motorLeft] = vexRT[Ch3];
motor[motorRight] = vexRT[Ch2];
```

### Arcade Drive
Arcade drive is a more convenient way to control a robot. One joystick controls both the robot’s forward-backward movement and turning. The following formula implements arcade drive on the right joystick:
```c
motor[motorLeft] = (vexRT[Ch2] + vexRT[Ch1]);
motor[motorRight] = (vexRT[Ch2] - vexRT[Ch1]);
```

### Deadbands
Sometimes, a joystick will not reach 0 when it is released, instead settling on a low value. This provides unnecessary low-power signals to the motors, which can cause the robot to drift. Deadbands use an if statement to prevent joystick inputs from registering until the joystick is moved past a small threshold. A full arcade drive implemented with a deadband threshold of 12 would look like this:
```c
task main() // Program entry point
{
    while(true) // Event loop
    {
        if ((abs(vexRT[Ch1]) + abs(vexRT[Ch2])) > 12) // Dead zone
        {
            motor[motorLeft]  = (vexRT[Ch2] + vexRT[Ch1]);
            motor[motorRight] = (vexRT[Ch2] - vexRT[Ch1]);
        }
        else // Turn off motors if joysticks do not exceed dead zone
        {
            motor[motorLeft]  = 0;
            motor[motorRight] = 0;
        }
    } // End event loop
}
```
*The threshold (12 in this example) can be adjusted as needed.*
	
If your robot uses any other 3-wire motors, you can similarly control them using the `motor[]` command. Make sure to set them up in the `Motor and Sensor Setup` menu, and use the name given to the specific motor.

## Uploading Code

### Uploading
You can press the “Compile Program” button at the top of the RobotC window at any time to check your code for errors, without needing to connect a Cortex. When you are ready, you can upload your program to your robot’s Cortex with the following steps:
1. Connect a 7.2V battery to the VEX Cortex.
2. Connect the Cortex to the computer with the orange USB cable
3. Turn on the Cortex
4. Press the `Download to Robot` button in RobotC (pressing the Compile Program button is not necessary)

If you get an error message when uploading, and your code is correct, you may have to update your Cortex firmware. You will have to do this if the Cortex was previously running Simulink code. Connect the Cortex to the computer with a battery as described above. Then, in the RobotC menu bar, go to `Robot > Download Firmware > Automatically Update VEX Cortex`.
![image2](https://hackmd.io/_uploads/BkoTCS3Ka.png)

### Wiring
When connecting motors to the Cortex, each wire should correspond to the port selected in the `Motor and Sensor Setup` menu. 3-wire motors will require an extra motor controller wire. On the Cortex, the wires should be plugged so that the white wire is towards the center of the Cortex. You may have to run the robot with the Cortex connected to the Joystick with the orange USB cable before using a wireless key.


## Servo Motors
While 3-wire motors (the ones we have used so far) are controlled by speed or power, you control the rotational position of a servo motor. Servos are useful for components that require rotation to precise angles, such as claws. This is different from something like a drive motor, where the exact angle of rotation is not crucial. However, the main limitation of servos is they can only rotate in a range of 120 degrees.

To set up a servo in your RobotC program, go to the Motor and Sensor Setup menu. Choose an empty port, give it a name, and select the type `3-wire Servo`. For example, this is how a servo called `clawServo` would be set up on Port 5:
![ss_4](https://hackmd.io/_uploads/HkGEkUhK6.png)
*In RobotC, servos are controlled just like 3-wire motors, with the `motor[]` command. A value of -127 is -60 degrees, 128 is 60 degrees, and 0 is in the middle of the rotation range.*
            

### Toggling a Servo with a Button
Your program can take input from buttons on the controller using the `vexRT[]` command in an if-statement. When you start typing `Btn` inside the brackets, the RobotC editor will show the available button commands, as shown below. These names correspond to the buttons labeled on the controller.
![ss_5](https://hackmd.io/_uploads/ryavNUhYp.png)

The following code toggles a servo between two states when a button on the controller is pressed. This can be used for something like a claw.
Before the while-loop, declare a state variable as a boolean (true/false):
```c
bool isRetracted = false;
```
In the while loop, invert the value of the variable when the button is pressed. Then, set the servo position depending on the value of the variable.
```c
if (vexRT[Btn5D]) // When button is pressed
{
    isRetracted = !isRetracted; // Invert position variable
}		

if (isRetracted)
{
    motor[clawServo] = -20;
}
else
{
    motor[clawServo] = 100;
}
```
*You will have to determine the exact servo position values through experimentation with your robot.*


## Autonomy
Autonomy allows your robot to complete a series of moves on its own, without the driver directly controlling it. A common example is moving in a predetermined path for set times. Usually, a button activates the autonomous sequence when pressed.
	
It is useful to add a timed delay in your code for autonomous programs. This can be done in RobotC with the `wait1Msec()` function. When the program reaches this command, it will wait for the number of milliseconds entered between the parentheses.

You can take input for the autonomous activation button in the main loop. The following example makes the robot complete a certain set of moves when the 7R button on the controller is pressed.

```c
while (true)
{
    if (vexRT[Btn7R])
	{
	    // Move forward for 1 second
	    motor[motorLeft] = 127;
	    motor[motorRight] = 127;
	    wait1Msec(1000);
	    // Turn right for 0.5 seconds
	    motor[motorLeft] = 100;
	    motor[motorRight] = -100;
	    wait1Msec(500);
	    // Move forward slowly for 1 second
	    motor[motorLeft] = 50;
	    motor[motorRight] = 50;
	    wait1Msec(1000);
	}
}
```

You will have to determine specific values for autonomous motor powers and delays through experimentation with your robot.

## Full Sample Program
```c
#pragma config(Motor,  port2,           motorLeft,     tmotorServoContinuousRotation, openLoop)
#pragma config(Motor,  port5,           clawServo,     tmotorServoStandard, openLoop)
#pragma config(Motor,  port9,           motorRight,    tmotorServoContinuousRotation, openLoop)
//*!!Code automatically generated by 'ROBOTC' configuration wizard               !!*//

task main()
{

    bool isRetracted = false;

    while (true) // Main loop
    {
		
        // Drive Controls
        if ((abs(vexRT[Ch1]) + abs(vexRT[Ch2])) > 12) // Dead zone
        {
            motor[motorLeft]  = (vexRT[Ch2] + vexRT[Ch1]);
            motor[motorRight] = (vexRT[Ch2] - vexRT[Ch1]);
        }
        else // Turn off motors if joysticks do not reach dead zone
        {
            motor[motorLeft]  = 0;
            motor[motorRight] = 0;
        }

        // Claw Control (servo)
        if (vexRT[Btn5D]) // When button 5D is pressed
        {
            isRetracted = !isRetracted; // Invert position variable
        }

        if (isRetracted)
        {
            motor[clawServo] = -20;
        }
        else
        {
            motor[clawServo] = 100;
        }
		
        // Autonomous mode
        if (vexRT[Btn7R]) // When button 7R is pressed
        {
            // Move forward for 1 second
            motor[motorLeft] = 127;
            motor[motorRight] = 127;
            wait1Msec(1000);
            // Turn right for 0.5 seconds
            motor[motorLeft] = 100;
            motor[motorRight] = -100;
            wait1Msec(500);
            // Move forward slowly for 1 second
            motor[motorLeft] = 50;
            motor[motorRight] = 50;
            wait1Msec(1000);
            }

    } // End main loop
}
```


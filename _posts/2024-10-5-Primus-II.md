---
layout: post
title: PRIMUS_Blog-II
subtitle: Omni-drive formulation
header-img: img/BlogPrimus/images/blog2/thumbnail_Blog2a.jpeg
---



# PRIMUS-II

Yo! 
In the last blog we went through the whole design and hardware part of primus. Now we'll be going through the math behind the drive and some code. To get a proper idea of the math working here you should know some basics of trignometry and matrices also a little vector algebra. This one can be a little difficult to understand. but don't worry we'll go through this together. I've included the necessary formulas below, which we'll use for the derivation.
```
#Trignometric formulae:
sin(𝜃) = opposite / hypotenuse        
cos(𝜃) = adjacent / hypotenuse

#Matrix multiplication:
Given 2 matrices, 
A = | a11  a12  a13 |       B = | b1 |  
    | a21  a22  a23 |           | b2 |
    | a31  a32  a33 |           | b3 |

The resulting matrix C=A×B is:
        C = | c1 |      c1 = a11*b1 + a12*b2 + a13*b3
            | c2 |      c2 = a21*b1 + a22*b2 + a23*b3
            | c3 |      c3 = a31*b1 + a32*b2 + a33*b3

#Determinant of a 3x3 matrix: 
For matrix A, the determinant is:
        det(A) =  a11 * (a22 * a33 - a23 * a32) 
                  - a12 * (a21 * a33 - a23 * a31) 
                  + a13 * (a21 * a32 - a22 * a31)

#Matrix inverse:
The inverse of matrix A is:
          | a11_inv  a12_inv  a13_inv |
 |A|^-1 = | a21_inv  a22_inv  a23_inv |
          | a31_inv  a32_inv  a33_inv |
        
                | (a22*a33 - a23*a32)  -(a12*a33 - a13*a32)   (a12*a23 - a13*a22) |  
   = 1/det(A) * | -(a21*a33 - a23*a31) -(a11*a33 - a13*a31)  -(a11*a23 - a13*a21) |
                | (a21*a32 - a22*a31)  -(a11*a32 - a12*a31)   (a11*a22 - a12*a21) |
```

Before diving into the scary math, let’s start with a simple example of code to run the motor and make the wheels turn at variable speeds.

At some point in your childhood, I’m sure you attached a fan to a motor shaft and connected a battery, just to enjoy a few minutes of cool breeze. As the fan spun, you probably noticed a few interesting things about how the motor behaved. One of the key characteristics was that if you switched the polarity of the battery (i.e., switched the positive and negative terminals), the fan started spinning in the opposite direction, and as the battery drained (i.e., the voltage level dropped), the motor speed decreased. In the previous blog, I mentioned something about motor drivers—these do similar things to control the motor. I’ll briefly explain it here. The direction control part is simple: just like with the battery, you just have to change the state of the direction pins in the motor driver.
For example, consider the DIR_A and DIR_B pins. To achieve clockwise rotation of the motor, set DIR_A to HIGH (1) and DIR_B to LOW (0). Conversely, for anticlockwise rotation, the pin states should be DIR_A set to LOW (0) and DIR_B set to HIGH (1), it will become clearer in the code. This control is accomplished using an H-bridge. I won't go into detail about it here, but if you're interested, refer to this [link here](https://lastminuteengineers.com/l298n-dc-stepper-driver-arduino-tutorial/)

As mentioned in the previous example, the speed of a DC motor can be controlled by changing its input voltage. **PWM, or Pulse Width Modulation**, is a technique that adjusts the average value of the input voltage by sending a series of ON-OFF pulses.
You can think of it like a light switch that you can turn on and off very quickly. Imagine you have a flashlight that you can rapidly toggle: 
- if you leave it on for a long time and then turn it off for a short time, it will appear bright. 
- Conversely, if you turn it on and off very quickly but keep it off for a longer duration, it will appear dim. 

![PWM average output voltage.](/img/BlogPrimus/images/blog2/PWM_Blog2.jpg)

By varying the duration that the flashlight remains on compared to when it is off, you can control its brightness. The average voltage produced through PWM is proportional to the width of the pulses, which is known as the **Duty Cycle**. The Duty cycle is calculated as 
```
Duty_Cycle(%) = ( Time_ON / Time_ON + Time_OFF ) × 100
```
A higher duty cycle results in a higher average voltage applied to the DC motor, leading to an increase in motor speed. Conversely, a shorter duty cycle means a lower average voltage applied to the DC motor, resulting in a decrease in motor speed.

![PMW parameters](/img/BlogPrimus/images/blog2/PWM_charBlog2.png)


The following code demonstrates an implementation of PWM (Pulse Width Modulation) to control both the speed and direction of a DC motor using an Arduino with an L298 motor driver. By adjusting the duty cycle through the PWM signal, we can gradually increase and decrease the motor's speed while also managing its rotation direction.
```
int pwmPin = 12;          // Pin connected to the PWM signal for motor speed control
int motorDir1 = 13;      // Pin for the first direction control of the motor
int motorDir2 = 15;      // Pin for the second direction control of the motor

void setup() {
    pinMode(motorDir1, OUTPUT);  // Set motor direction pin 1 as an output
    pinMode(motorDir2, OUTPUT);  // Set motor direction pin 2 as an output
    pinMode(pwmPin, OUTPUT);      // Set PWM pin as an output
}

void loop() {
    // Gradually increase the motor speed
    for (int i = 0; i < 255; i++) {
        digitalWrite(motorDir1, HIGH);  // Set direction to clockwise
        digitalWrite(motorDir2, LOW);    // Ensure the opposite direction pin is low
        analogWrite(pwmPin, i);          // Apply PWM signal to control speed (0 to 255)
        delay(10);                       // Small delay to allow the speed to increase gradually
    }

    // Gradually decrease the motor speed
    for (int i = 255; i >= 0; i--) {
        digitalWrite(motorDir1, LOW);   // Set direction to counterclockwise
        digitalWrite(motorDir2, HIGH);   // Ensure the opposite direction pin is high
        analogWrite(pwmPin, i);          // Apply PWM signal to control speed (255 to 0)
        delay(10);                       // Small delay to allow the speed to decrease gradually
    }
}
```

Now, let’s dive into everyone’s favorite topic: MATH! If you’ve seen the formulas mentioned above at least once in your life, trust me, it’s not going to be scary. In our case, we are using a left-hand coordinate system, you can visualize it by extending your left hand, as shown in the picture.

![co-ordinate system photo](/img/BlogPrimus/images/blog2/PRIMUS_cordBlog2.png)

Let’s start with what are degrees of freedom. The degrees of freedom for a system refers to the number of independent parameters or ways it can move. Essentially, it measures how many different directions or types of motion the robot's joints or components can take.
Consider a door hinge: it has one degree of freedom because it can rotate along a single axis, allowing the door to swing open or closed. Similarly, a ball in three-dimensional space has six degrees of freedom: three translational (movement along the three perpendicular axes: x, y, and z) and three rotational (rotation around each of the three axes).
Primus has three degrees of freedom; it can move along the x-axis and y-axis, and it can also rotate around the z-axis. Thus, we will be programming it to move in combinations of these movements.

What determines the direction in which the robot moves? The direction is dictated by the resultant forces acting on the robot. The resultant force is the vector sum of all the individual forces applied to it. For our robot, this resultant force is the sum of all force vectors generated by the wheels.
The wheels make an angle of 90 degrees to the Z axis, the rotation of the robot is simply the sum of the force provided by the wheels.Thus,the rotational force **Fw**(around the z-axis) is the sum of all the forces causing the robot to twist can be expressed as:
**Fw = Fm1 + Fm2 + Fm3** 

> The motor values in these equations represent the motor speeds, which are controlled using PWM (Pulse Width Modulation) as demonstrated in the code above.

For the remaining two degrees of freedom, which involve translation along the x and y axes, we break down the resultant force acting on the robot into its x and y components. The x-component of the resultant force is the sum of the x-components of the forces generated by motors m1,m2 and m3:
**Fx = Fx1 + Fx2 + Fx3** 
Similarly for the y-component,
**Fy = Fy1 + Fy2 + Fy3**   

### How are x and y components calculated?

[rigth angles triangle image]
To calculate the x and y components of the force, consider a right-angled triangle where the hypotenuse represents the motor's force. Using trigonometric ratios, we can resolve this force into its components.
```
sinθ = Opp / hyp = y_component / motor_force 
y_component = motor_force*sinθ

Similarly,
cosθ = Adj / hyp = x_component / motor_force
x_component = motor_force*cosθ

Now, consider that the wheel shafts are positioned at an angle of 120 degrees from each other in the x-y plane:
- motor1 = 0° 
- motor2 = 120° 
- motor3 = 240° 

Using these angles, we can express the x and y components of the resultant force as:
Fx = cos(0°)*Fm1 + cos(120°)*Fm2 + cos(240°)*Fm3 
Fy = sin(0°)*Fm1 + sin(120°)*Fm2 + sin(240°)*Fm3

Simplifying these equations :
Fx = (1)*Fm1 + (-0.5)*Fm2 + (-0.5)*Fm3
Fy = (0)*Fm1 + (0.86)*Fm2 + sin(-0.86)*Fm3

```
This leaves us with three simultaneous equations:
- Fx = (1)*Fm1 + (-0.5)*Fm2 + (-0.5)*Fm3 
- Fy = (0)*Fm1 + (0.86)*Fm2 + (-0.86)*Fm3
- Fw = Fm1 + Fm2 + Fm3 

### What are we solving for?
The final goal is to control the robot's movement using a joystick. The robot should move in the direction indicated by the joystick. The resultant forces **Fx** and **Fy** represent the vector indicated by the joystick, and we need to use that vector to calculate the motor values **Fm1,Fm2 and Fm3**.

There are two methods to solve these simultaneous equations: algebraically, which is possible but complicated, or using matrix methods, which is much more efficient for larger systems of equations.
```
Representing the simultaneous equations in matrix form: 
    | Fx |   | 1  -0.5  -0.5  |  | Fm1 |
    | Fy | = | 0  0.86  -0.86 |  | Fm2 |
    | Fw |   | 1   1      1   |  | Fm3 |

For simplicity let:
          | 1  -0.5  -0.5  |
    |A| = | 0  0.86  -0.86 |
          | 1   1      1   | 

Therefore, the matrix equation is:
    | Fx |        | Fm1 |
    | Fy | = |A|  | Fm2 |
    | Fz |        | Fm3 |
    
Multiplying both sides of the above equation with |A|^-1:
             | Fx |               | Fm1 |
    |A|^-1 * | Fy | = |A|*|A|^-1  | Fm2 |
             | Fz |               | Fm3 |

Since |A|^-1 * |A| equals an identity matrix we get:
             | Fx |   | Fm1 |
    |A|^-1 * | Fy | = | Fm2 | 
             | Fz |   | Fm3 |

After calculating |A|^-1 (using the formula provided earlier) we get:
              |  0.66    0    0.33 | 
    |A|^-1 =  | -0.33   0.58  0.33 | 
              | -0.33  -0.58  0.33 | 

Substituting the value of |A|^-1:
    |  0.66    0    0.33 |   | Fx |   | Fm1 |  
    | -0.33   0.58  0.33 | * | Fy | = | Fm2 |    
    | -0.33  -0.58  0.33 |   | Fz |   | Fm3 |

By matrix multiplication (also stated above) we get:
    | Fm1 | = | ( 0.66*Fx)   ( 0*Fy)    (0.33*Fw) |
    | Fm2 | = | (-0.33*Fx)  ( 0.58*Fy)  (0.33*Fw) |
    | Fm3 | = | (-0.33*Fx)  (-0.58*Fy)  (0.33*Fw) |

In simplified form, the equations for the motor forces are:
   Fm1 = (0.33*Fx) + (0.58*Fy) + (0.33*Fw)
   Fm2 = (0.33*Fx) + (0.58*Fy) + (0.33*Fw)
   Fm3 = (0.33*Fx) + (0.58*Fy) + (0.33*Fw)

```
At last, we've completed the derivation for the equations—what a challenging journey it was! But now, we have the formula we need to calculate motor values from the joystick inputs, and with that, we’ve successfully wrapped up this important step.

In this blog, we've derived the equations for our omni-wheel robot **PRIMUS** and understood how motors are controlled using a motor driver. In the final blog on PRIMUS, we’ll dive into coding for the web server-based remote control. So stay tuned!

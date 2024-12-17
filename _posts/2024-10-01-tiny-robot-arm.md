---
layout: post
title:  "SG90 Servo Arm"
date:   2024-10-01 15:13:32 +0000
categories: jekyll update
---
I have always been fascinated by the robot arm. Its speed, 
precision, and appearance of autonomy have captivated me from my youth.
To make one myself has been a dream come true. Read about my robot arm's 
first iteration below.

## Bill of Materials

| Item                 | Quantity | Cost  | Notes        |
|----------------------|----------|-------|--------------|
| Arduino Nano         | 1        | $4.00 |              |
| SG90 Servo           | 4        | $0.97 |              |
| L7805                | 4        | $0.34 | 5V Regulator |
| 9V Battery           | 1        | $1.25 |              |
| 9V Battery Connector | 1        | $0.12 |              |
| Breadboard           | 1        | $1.70 |              |
| M\M Jumper Wire      | 20       | $0.11 |              |
| 3D Printed Parts     | 4        | $1.04 |              |

**Total Cost:** $15.51

## 3D Printed Parts
Modelling these wasn't too difficult. I made the rectangular bracket first, and then modelled different parts starting with a copy of the bracket each time.

<div style="display: grid; grid-template-columns: repeat(2, 1fr); gap: 15px; padding: 20px;">
  <div style="border: 2px solid #ccc; border-radius: 10px; box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1); overflow: hidden;">
    <img src="{{ jhk35.github.io }}/assets/media/tiny-robot-arm/foot_rev4.png" alt="Robot Arm Base" style="width: 100%; height: 100%; object-fit: cover;">
    <p style="text-align: center; padding: 10px;">Robot Arm Base</p>
  </div>
  <div style="border: 2px solid #ccc; border-radius: 10px; box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1); overflow: hidden;">
    <img src="{{ jhk35.github.io }}/assets/media/tiny-robot-arm/long_arm_rev4.png" alt="Robot Arm Link 1" style="width: 100%; height: 100%; object-fit: cover;">
    <p style="text-align: center; padding: 10px;">Robot Arm Link 1 (Long Segment)</p>
  </div>
  <div style="border: 2px solid #ccc; border-radius: 10px; box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1); overflow: hidden;">
    <img src="{{ jhk35.github.io }}/assets/media/tiny-robot-arm/short_arm_rev4.png" alt="Robot Arm Link 2" style="width: 100%; height: 100%; object-fit: cover;">
    <p style="text-align: center; padding: 10px;">Robot Arm Link 2 (Short Segment)</p>
  </div>
  <div style="border: 2px solid #ccc; border-radius: 10px; box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1); overflow: hidden;">
    <img src="{{ jhk35.github.io }}/assets/media/tiny-robot-arm/wrist_rev4.png" alt="Robot Arm Wrist" style="width: 100%; height: 100%; object-fit: cover;">
    <p style="text-align: center; padding: 10px;">Robot Arm Wrist</p>
  </div>
</div>

## Assembly
Assembly was just dropping the servos into their brackets, inserting screws, and connecting jumper wires. 


### Electronic Diagram
![Simple Electronic Diagram, made in Tinkercad, using Arduino Nano, 9 Volt Battery, 4x L7805, and 4x SG90 Servo.](/assets/media/tiny-robot-arm/TinyRobotArmCircuit.png "Robot Arm Circuit")

### All Together
![3D models mentioned earlier were assembled into a small blue robot arm posed in a working position.](/assets/media/tiny-robot-arm/TinyRobotArm.png "Tiny Robot Arm")
Everything went together nicely on this one. The 3D printed parts exceeded my expectations!


## Code
{% highlight ruby %}
#include <Arduino.h>
#include <Servo.h>
#include <math.h>

// void s_moves (int num_moves, float *s_percentages);
// void s_curve(int start, int end, int num_moves, float *servo_positions);

// name servos
Servo base;
Servo elbow;
Servo wrist;
Servo rotate;

// inital positions
int p_base = 1000;
int p_elbow = 1000;
int p_wrist = 2000;
int p_rotate = 1000;

void setup() {
  // attach servos
  base.attach(6);
  elbow.attach(7);
  wrist.attach(8);
  rotate.attach(9);

  // set initial position
  base.writeMicroseconds(p_base);
  elbow.writeMicroseconds(p_elbow);
  wrist.writeMicroseconds(p_wrist);
  rotate.writeMicroseconds(p_rotate);
  delay(1000);
}

void loop() {
  for(int i=1000; i<2000; i++) {
    base.writeMicroseconds(i);
    elbow.writeMicroseconds(i);
    wrist.writeMicroseconds(3000 - i);
    rotate.writeMicroseconds(i);
    delay(50);
  }
  delay(500);
  for(int i=2000; i>1000; i--) {
    base.writeMicroseconds(i);
    elbow.writeMicroseconds(i);
    wrist.writeMicroseconds(3000 - i);
    rotate.writeMicroseconds(i);
    delay(50);
  }
  delay(500);
}
{% endhighlight %}



Check out this video:
<video src="{{jhk35.github.io}}/assets/media/tiny-robot-arm/TinyRobotArm.mov" width="800" height="600" controls></video>

## Reflection
While I was able to get the arm to move, it was weak and imprecise.
Here are the issues I've noticed:
1. The 9V battery does not supply enough current for these servos
and dies after just a few minutes of use.
2. The servo movements are shaky and imprecise, I'm going to look 
into s-curve acceleration options for future iterations.
3. After the 9V battery died, I tried using my 20V 3.25A USB-C power 
supply. L7805 chips were not able to drop the voltage without 
overheating quickly. I believe that I activated their thermal 
shutdown procedure. I'm looking into a buck converter or using a 
lower voltage USB-C power supply for future applications.

### Plans for next time
1. Solve the power supply issue, using ideas mentioned above.
2. Finish modelling some kind of gripper for the end of the arm.
3. Research and apply s-curve accelerations to the movement commands.
4. Use brass gear servos. (I'm currently considering the MG90S servo.)
5. Reasrch the use of a gear reduction at each joint, like a cycloidal 
or planetary gearbox.
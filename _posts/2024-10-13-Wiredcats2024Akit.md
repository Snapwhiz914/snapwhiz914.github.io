---
layout: post
title:  "Initial Commit: FRC team 2415's code ported to the AdvantageKit framework"
date:   2024-08-12 12:00:00 -0400
author: Snapwhiz914
tags: robotics frc structure
---

# Background

Next year marks the last season I can participate on my school's FRC team. When I joined, software was not our team's priority, to say the least. Thankfully, as the seasons went by, the software sub-team learned from past mistakes to iterate robot code to improve reliability and functionality. Now that I'm the software lead, it's my turn to continue our progression, and I think the best direction to go is towards the code of [Team 6328 Mechanical Advantage](https://github.com/mechanical-advantage). This post explains what Mechanical Advantage's framework is and my process of completely refactoring our 2024 season code to match it.

## Quick Intro to FRC robot code

FRC is a high school robotics league run by [FIRST](https://www.firstinspires.org/robotics/frc). Most teams program thier robots in Java within the [WPILib](https://docs.wpilib.org/en/stable/index.html) ecosystem, a toolsuite and API for running code on the robot's computer.

WPILib heavily encorages teams to use thier command-based framework, which organized mechanisms on the robot into [Subsystems](https://docs.wpilib.org/en/stable/docs/software/commandbased/subsystems.html#subsystems) and actions on the subsystems into [Commands](https://docs.wpilib.org/en/stable/docs/software/commandbased/commands.html). In my expirience it is an easy way of thinking about robot control, especially for robots with many mechanisms and movements. Subsystems expose commands to move themselves, and driver inputs can trigger chains of these commands to perform complex actions quickly, without relying on timing or threading.

# Why use Mechanical Advantage's framework

What got me interested in the Mechanical Advantage framework (which is built on top of the command-based framework) is the seperation between subsystem control logic and motor control, the latter of which is called the "IO" layer. This has three signficant advantages:

1. From a project structure standpoint, it is cleaner. Instead of one class that contains subsystem logic and IO objects, the ownership is split into multiple classes with less lines.
2. There is no limit on the number of IO classes you can have. This means that you can switch out motors on the physical robot and only change one line, provided that there is an IO class for it.
3. Last, but definetly the most important advantage, is simulation ability. Instead of handling simulation logic within the subsystem class, all of it can be moved into its own IO class.

The second main benefit of the Mechanical Advantage framework is thier logging system [AdvantageKit](https://docs.advantagekit.org/what-is-advantagekit). I'm not going to go into details here because of how elegantly they put it on their website, so I'll give a quick summary: a logging system that lies within the subsystem and records all inputs to it, so that inputs can be replayed from a log to diagnose problems quickly and simuluate robot code without a physical robot available.

Simulation is extremely important early in the build season. While the physical robot is not available, a simulated model allows teams to test thier code's logic before deployment. Mechanical Advantage provides a log viewer called [AdvantageScope](https://github.com/Mechanical-Advantage/AdvantageScope). Again, I'll summarize: an incredibly feautre-rich log viewer that makes problems very easy to spot.

# The porting process

## Before: WiredCats2024

In all honesty I was very happy with our code this year. We finally used the Command Based framework, which organizes motor outputs into thier own self-contained commands that are linked to inputs from the driver. I was pleased with its readability, reliability and the code structure that came with it. Compared to the 2023 season it was very easy to add a new button bind for the driver or a new command for an autonomous routine.

During the season and especially after reading other team code on Github, I found some room for improvement:

1. Following is the constructor for one of our subsystems:
```java
    motor = new CANSparkMax(RobotMap.Finger.FINGER_MOTOR, CANSparkMax.MotorType.kBrushless); // initialize motor
    configureMotor();
    configureMechansim2dWidget();
    relativeEncoder.setPosition(0);
    offset = 0; 
    pidController.setReference(0, ControlType.kPosition);
}
```
There is such a thing as too much abstraction, and such a thing as too little abstraction. The above code falls on the too little side. From the constructor alone, this class clearly has more than one responsibility: controlling its motor, a visualization widget and offsetting the motor's set position. Ideally, this subsystem class would only be concerned with setting the motor's position. Visualization could be handled in this class, but motor control takes lots of screen space and is very distracting when trying to code in a hurry. Instead motor control should be handled in its own class.

2. 

## After: WiredCats2024Akit


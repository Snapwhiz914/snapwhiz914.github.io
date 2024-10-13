---
layout: post
title:  "Initial Commit: My FRC team's code ported to the AdvantageKit framework"
date:   2024-08-12 12:00:00 -0400
author: Snapwhiz914
tags: robotics frc structure
---

# Background

Next year marks the last season I can participate on my school's FRC team [2415](https://www.thebluealliance.com/team/2415). When I joined, software was not our team's priority, to say the least. Thankfully, as the seasons went by, the software sub-team learned from past mistakes to iterate robot code to improve reliability and functionality. Now that I'm the software lead, it's my turn to continue our progression, and I think the best direction to take that in is the [AdvantageKit framework](https://github.com/Mechanical-Advantage/AdvantageKit/blob/main/docs/docs/what-is-advantagekit/index.md). This post explains what AdvantageKit is and my process of porting our competition code from this year to the new framework.

# What is AdvantageKit?

Broadly speaking, the code that runs on the robot's computer is responsible for recieving inputs from the driver and sensors to control motors and solenoids. Indivudal mechanisms on the robot, like a drivebase or an arm, are organized into subsystems. AdvantageKit itself is a logging utility that lies within the subsystem to record its inputs and outputs. Logging the robot software activity allows for quick problem response, a must-have in fast moving competion environments.

The logging system alone is not the only appeal of using it. AdvantageKit works hand-in-hand with the "IO" methodology. In this implementation, the subsystem logic   is seperate from the actual control over the motor or reception of sensor information. This separation is essnetial for logging, but it also allows for well organized robot simulation.

Simulation is extremely important early in the build season. While the physical robot is not available, a simulated model allows  teams to test thier code's logic before deployment.

# The porting process



## Before: WiredCats2024

In all honesty I was very happy with our code this year. We finally used the Command Based framework, which organizes motor outputs into thier own self-contained commands that are linked to inputs from the driver. I was pleased with its readability, reliability and the code structure that came with it.

## After: WiredCats2024Akit


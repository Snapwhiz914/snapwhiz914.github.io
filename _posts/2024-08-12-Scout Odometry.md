---
layout: post
title:  "Initial Commit: ScoutOdometry"
date:   2024-08-12 12:00:00 -0400
author: Snapwhiz914
tags: robotics slam vision
---

# Background

Some projects I undertake are the result of a single thought that explodes into a fully formed project (such is the case with PN-API). Others result from multiple thoughts and other sources in my life and converge into a fully formed project. This one is an example of the later, except at the time of this writing it is a folder of experimental scripts.

Anyways, this project resulted from experimenting with a [Moorebot Scout](https://www.moorebot.com/products/moorebot-scout?). Now I already knew plenty about wheel spin, IMU and visual [fiducial](https://en.wikipedia.org/wiki/Fiducial_marker) odometry from coding an FRC robot, but the 120 degree fisheye camera in a domestic fiducial-less environment presented its own interesting problem: Visual Odometry. Was it possible to obtain accurate measurements from a camera alone? This quickly led me down the rabbit hole of Visual SLAM (Simultaneous Localization and Mapping). Before I knew it, I had a folder full of Python scripts, an ROS installation on server1 and multiple youtube tabs to learn about this interesting field.

## Basic Odometry

Odometry is the process of figuring out how far something has moved, relative to itself. A car knows how many miles it has on it because it records when the wheel does a full rotation. By multiplying this number times the circumference of the tire and doing appropriate distance conversions, it can display this number on a screen. FRC robots use the same concept, however instead of just recording the total distance, it is far more helpful to know what position the robot is in relative to where it started. By using the spin of each wheel combined with gyroscope measurements, you can get a solid pose estimation.

But what if the robot collides with something, and the wheels move without the robot physically moving? What if it rotates so fast the gyroscope doesn't catch it? For that and various other imperfections, many FRC teams will include a camera system that measures their relative position from [AprilTags](https://april.eecs.umich.edu/software/apriltag), and use a Kalman Filter to integrate these measurements together.

## SLAM

Going a step further than Odometry, a SLAM software will attempt to make a map of its environment while also measuring where it is relative to where it started. SLAM has a surprising amount of applications (mobile phone measurements and VR/AR being on that list), including the application to a domestic robot such as the Moorebot. It is advertised to be a security device: it can build a map of your home and autonomously move through it and send alerts if necessary. 

## ScoutOdometry

Since I only had about a week of time with the Scout, I decided to learn as much as I could through experience. Before I went down the rabbit hole of SLAM, I tried to create my own mapping system, using the ToF sensor, wheel spin and simply integrating time with the onboard gyroscope's velocity measurements. I got a surprisingly accurate map for about thirty seconds, then the scale became noticeably off.

After looking into Visual SLAM I realized I needed to calibrate the fisheye camera to try to approximate the pinhole camera model, so there is a folder for that. In the experiments folder are scripts that display the camera feed annotated with image features, namely Harris Corner detection and image moments.

# Future

My school offers an Independent Study class, and I signed up for it with this topic. My final project will be an open source vSLAM library, tested and tuned specifically for domestic robots with just a monocular camera. As soon as I finish it I will put it on GitHub and write a post about it.
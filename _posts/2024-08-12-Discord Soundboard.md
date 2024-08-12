---
layout: post
title:  "Initial Commit: VSEB"
date:   2024-08-12 12:00:00 -0400
author: Snapwhiz914
tags: discord website fastapi
---

# Background

Last summer, I was exploring discord and discovered they had a soundboard feature. I joined a call with some friends to test it out, and after uploading 5 sounds I was met with an all too familiar roadblock: the paywall. So I decided to take matters into my own hands add my own spin to it.

# VSEB (Virtual Sound Effect Board)

VSEB is a sound effect board software that doesn't use the keyboard, an app or proprietary hardware to trigger sounds. Instead, it hosts a minimalistic website with buttons to play the sounds with. Since it was made for discord, it can automatically change to Push-To-Talk mode for better sound quality over calls.

## Code overview

### The process

This project started out as a single file, as I was only using one sound. In this stage I didn't use the website, I just used buttons on my keyboard that I didn't need to use for anything else. I discovered an old kindle and realized that it would be perfect to use that instead of my keyboard to play sounds with, so I split the project into multiple files and organized it into what it is now.

### Files

 - player.py: Manages the sound playing thread, defined as `play_sound_t`. Uses the `wave` library to load sounds and uses `pyaudio` to play sounds over selected outputs.
 - key_presser.py: Defines functions to single press or hold down keys, used to interface with discord. Uses `pynput` to accomplish this.
 - templates/soundboard.html: The main page of the website, provides css styling and the function to send a request back to the server to play a sound
 - main.py: Unifies the above and serves the website using `fastapi` and `uvicorn`.
 - conf_ui.py: Provides a simple GUI to add sounds, select sound outputs and configure discord keybinds

## Possible future additions

 - Support other types of sound files OR automatically convert them to .wav and copy them to a specific folder
 - integrated installation of virtual microphone software per platform
 - website improvements, such as custom styling, automatic reload when a new sound is added, notifying the page when the sound has stopped playing, etc.

[Link to the code (Containing installation instructions)](https://github.com/Snapwhiz914/VirtualSoundboard).
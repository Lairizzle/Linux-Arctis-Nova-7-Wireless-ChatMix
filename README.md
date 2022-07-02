# Linux-Arctis-7-Plus-ChatMix

## Overview
<br>
The SteelSeries Arctis series of headsets include a hardware modulation knob for 'chatmix' on the headset.
This allows the user to 'mix' the volume of two different devices on their system, named "Game" and "Chat".

On older Arctis models (e.g. Arctis 7), the headset would be detected as two individual hardware devices by
the host operating system and would assign them as such accordingly, allowing the user to specify which device to
use and where. 

**Typical use case:** "Chat" for voicechat in games and VOIP/comms software, and "Game" for system / music etc

On the Arctis 7 model, this two-device differentiation no longer exists, and the host OS will only recognize a single device.
If the user wishes to utilize the chatmix modulation knob, they *must* install the SteelSeries proprietary GG software. This
software is not currently supported for Linux.

This script provides a basic workaround for this problem for Linux users. It creates a Virtual Audio Cable (VAC) pair called "Arctis 7+ Chat"
and "Arctis 7+ Game" respectively, which the user can then assign accordingly as they would have done with an older Arctis model. 
The script then listens to the headset's USB dongle interrupt signals and interprets this in a way that can be meaningfully converted 
to adjust the audio accordingly when the user moves the dial on the headset.


## Requirements

The service itself depends on the [PyUSB]https://github.com/walac/pyusb package. 

In order for the VAC to be initialized and for the volumes to be controlled, the system requires **Pipewire** (and the underlying **PulseAudio**)
which are both fairly common on Linux systems out of the box.


## Implementation - How it works

The service first initializes the VAC by making direct calls to PulseWire `pw-cli` to create a `nodes` and pipe them to the default audio device.

The service relies on the [PyUSB]https://github.com/walac/pyusb package to read the interrupts from the headset's usb dongle.

The headset sends three bits, the second and third of which are the volume values for the dial's two directions (toward 'Chat' / down, toward 'Game' up).

These are then issued to the audio system via `pactl`. 

The service will automatically set "Arctis 7+ Game" as the default device on startup.



# Acknowledgements

With great thanks to:
- [awth13]https://github.com/awth13, especially for contributions in creation of our rules.d and systemd configuration and for wrestling with ALSA in our early attempts
- [Alexandra Zaharia's]https://github.com/alexandra-zaharia excellent [article]https://alexandra-zaharia.github.io/posts/stopping-python-systemd-service-cleanly for the clear advice on good practices for sigterm SIGINT/SIGTERM & logging

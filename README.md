# ofxPiMapper

[![Ubuntu Workflow Badge](https://github.com/kr15h/ofxPiMapper/workflows/linux/badge.svg)](https://github.com/kr15h/ofxPiMapper/actions?query=workflow%3Alinux)
[![Windows Workflow Badge](https://github.com/kr15h/ofxPiMapper/workflows/windows/badge.svg)](https://github.com/kr15h/ofxPiMapper/actions?query=workflow%3Awindows)
[![OSX Workflow Badge](https://github.com/kr15h/ofxPiMapper/workflows/osx/badge.svg)](https://github.com/kr15h/ofxPiMapper/actions?query=workflow%3Aosx)

Projection mapping addon for openFrameworks that runs on the Raspberry Pi. It has been tested against openFrameworks version (0.11.0).

The project started as master's thesis project by [Krisjanis Rijnieks](https://rijnieks.com) at the [Aalto Media Lab](https://www.aalto.fi/en/aalto-media-lab). Currently being developed during free time. Please [donate](https://ofxpimapper.com/) to create more free time.

Note on build status: GitHub Actions ubuntu-latest, windows-latest and osx-latest images with 64bit environments are used. To be reviewed.

## Disk Image

For those not using openFrameworks, there is a precompiled disk image available via [ofxPiMapper website](https://ofxpimapper.com). It works with Raspberry Pi up until version 3B+ (with the older BCM2837 chip). Given the recent growing interest in the project, some effort is being put into figuring out how to make the disk image to work on Pi 4 and Pi 5 (and Pi 3B+ with the more recent BCM2837B0 chip). Please [donate](https://ofxpimapper.com/) to increase the frequency and duration of these efforts.

> **Warning!** It does not work on the latest generation of Raspberry Pi 3B+ with the BCM2837B0 chip. Make sure the Pi has the older BCM2837 chip when purchasing.

## Introduction

This repository contains the addon for [openFrameworks](https://openframeworks.cc). It is expected to be used as an extension for your custom openFrameworks project where you create a custom [FBO source](example_fbo-sources) that is being animated by an advanced process rather than a plain video.

If you are planning to use videos and images only, consider using the precompiled Raspberry Pi image available from the [ofxPiMapper website](https://ofxpimapper.com). It is based on a recent Raspberry Pi OS disk image and contains compiled installation of openFrameworks along with all the scripts and services that make Pi Mapper start automatically.

## Installation

Clone or download the repository to your openFrameworks addons folder. Make sure that default openFrameworks examples compile and run before trying ofxPiMapper examples. There are a few things that you should do depending on if you are compiling on the Raspberry Pi or not.

- Raspberry Pi  
  Please install the [ofxOMXPlayer](https://github.com/jvcleave/ofxOMXPlayer) addon before compiling.
- Windows MSYS2  
  Go to `patches/msys2` directory and run `./patch.sh`
- Other platforms  
  Please rename the `addons.make.rpi` file to `addons.make`.

To test, use Terminal to change the directory to `example_basic`, compile and run.

```
cd path/to/openFrameworks/addons/ofxPiMapper/example_basic
make
cd bin
./example_basic
```

If you plan to use `example_remote-server` and `example_remote-client`, make sure that you have added [ofxJSON](https://github.com/jeffcrouse/ofxJSON) dependency to your `openFrameworks/addons` folder prior compilation.

## Usage

Currently a keyboard and a mouse has to be used in order to do the mapping with ofxPiMapper.


### Modes

PiMapper has 4 modes:

1. Presentation mode
2. Texture mapping mode
3. Surface editing mode
4. Source assignment mode

You can access these modes by pressing 1, 2, 3 or 4 respectively.


#### Presentation mode

This mode is activated once the application starts up. It does not show anything else except the final projection mapping as it was saved previously.


#### Texture mapping mode

In this mode you can adjust the texture coordinates of the surface you have selected in the surface editing mode.


#### Surface editing mode

Here you can select, move and distort the surfaces you have created.


#### Source assignment mode

After you select a surface in surface editing mode, activate this mode to be able to choose a source for the surface. Afterwards you might want to go to the texture mapping mode to adjust texture coordinates.


### Other shortcuts

These other shortcuts that you can use while using the example app.

Key | Function
:--- | :---
1 | Presentation mode
2 | Texture editing mode
3 | Projection mapping mode, use this to select a surface first
4 | Source selection mode
i | Show info
t | Add triangle surface
q | Add quad surface
g | Add grid warp surface
c | Add circle surface
d | Duplicate selected surface
\+ | Scale surface up
\- | Scale surface down
p | Toggle perspective warping (quad surfaces only)
F | Expand selected surface to fill the screen (quad surfaces only)
] | Add columns to grid surface (grid warp surfaces only)
[ | Remove columns from grid surface (grid warp surfaces only)
} | Add rows to grid surface (grid warp surfaces only)
{ | Remove rows from grid surface (grid warp surfaces only)
. | Select next surface (projection mapping mode only)
, | Select previous surface (projection mapping mode only)
\> | Select next vertex
\< | Select previous vertex
0 | Move selected surface one layer up
9 | Move selected surface one layer down
s | Save composition
l | Hide/show layer panel
z | Undo
rbt | Reboot (Raspberry Pi only)
sdn | Shutdown (Raspberry Pi only)
new | Clear composition (remove all surfaces)
ext | Exit application and return to command line
BACKSPACE ('\' via SSH) | Delete surface.
SPACE | Toggle pause for video sources (texture and projection mapping modes)
TAB | Select next source (no need to use the source selection interface)
Arrow keys | Move selection. If no surface is selected in the projection mapping mode, all surfaces are moved.
\/ | Toggle 1px/10px steps for keyboard moves on Raspberry Pi

## Notes on Video Encoding

Easiest way to achieve success is to use [HandBrake](https://handbrake.fr/) with the following settings. This will produce a `.mkv` file with 16bit FLAC audio with 22.05KHz sampling rate.

```
Preset: Fast 720p30
Summary / Format: MKV File
Video / Framerate: Same as source
Video / Profile: Baseline
Audio / Codec: FLAC 16-bit
Audio / Samplerate: 22.05
```

If you are familiar with [ffmpeg](http://ffmpeg.org/), use the following. It will produce a `.mov` file. PCM audio codec with 22KHz sampling rate is used.

```
ffmpeg -i input-video.mp4 -s 1280x720 -aspect 16:9 \
  -c:v libx264 -profile:v baseline \
  -c:a pcm_s16le -ar 22000 -ac 2 \
  output-video.mov
```

These two settings have shown the best results so far. Audio problems and video playback at the beginning of longer video files was the main issue in most cases. Please open an issue if you have better suggestions.

## Extended Functionality

Examples represent extended functionality of ofxPiMapper. Enter each example separately to find out more.

- [Basic Example](example_basic).
- [FBO Sources Example](example_fbo-sources).
- [Camera Example](example_camera).
- [Remote Control Server](example_remote-server) example.
- [Remote Control Client](example_remote-client) example.
- Readme's for the rest are cooking...

## Problems with Audio

If you are having problems with audio playback, here are two steps for you. Before you do these, make sure audio of your video file works.


### Step 1

Open example openFrameworks application `ofApp.cpp` file in a text editor.

```
cd /home/pi/openFrameworks/addons/ofxPiMapper/example
nano src/ofApp.cpp
```

Make sure that the following line looks as follows.

```
ofx::piMapper::VideoSource::enableAudio = true;
```

Save the file (CTRL + X, Y and ENTER). Recompile and run the example.

```
make && make run
```

### Step 2

If the sound still does not work, try to use `raspi-config`.

```
sudo raspi-config
```

Select "7 Advanced Options" and "A9 Audio" then "0 Auto". You can use one of the force options if you want to be 100% sure.

Open alsamixer.

```
alsamixer
```

Set the volume to a value between 90 to 100 by using the arrow keys. ESC to exit the mixer.

Launch ofxPiMapper example, select a surface and set a video source with audio. Should work.


## Development

Keeping it simple. Developing ofxPiMapper master branch against the latest stable release version of openFrameworks.


## Licence

ofxPiMapper is distributed under the [MIT License](https://en.wikipedia.org/wiki/MIT_License). See the [LICENSE](LICENSE.md) file for further details. Just add my name somewhere along your project [Krisjanis Rijnieks](https://rijnieks.com) whenever possible.


## Supporters

You can become a supporter by donating BitCoins, via PayPal or becoming a Patreon. Please check https://ofxpimapper.com for details. List of supporters below.

- [Marc-André Gasser](https://www.magdesign.ch/)
- Manuel Meißner


## Dependencies

Before moving on, make sure that you have all the dependencies installed.

 - ofxGui (available in oF by default)
 - ofxXmlSettings (available in oF by default)
 - [ofxOMXPlayer](https://github.com/jvcleave/ofxOMXPlayer) (needed only on Raspberry Pi)

To install dependencies, `cd` into `openFrameworks/addons` directory and execute the following:

```bash
git clone https://github.com/jvcleave/ofxOMXPlayer.git
```

The `ofxOMXPlayer` addon recommends you to use its releases. Currently the latest release is `0.9.0-compatible`. To checkout the code of the relase, go to the `ofxOMXPlayer` addon directory and checkout the relase.

```
cd openFrameworks/addons/ofxOMXPlayer
git checkout 0.9.0-compatible
```

You can check the latest releases on the [ofxOMXPlayer GitHub repository](https://github.com/jvcleave/ofxOMXPlayer/releases).

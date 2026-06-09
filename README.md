# Hand Gesture Recognition — Raspberry Pi 4

A real-time hand gesture recognition system built with OpenCV and MediaPipe, designed to run on a Raspberry Pi 4 with a USB webcam. The project includes a gesture detector, a finger-painting app, and a Rock Paper Scissors game — all controlled entirely by hand gestures.

![Python](https://img.shields.io/badge/Python-3.7+-blue.svg)
![OpenCV](https://img.shields.io/badge/OpenCV-4.4.0-green.svg)
![MediaPipe](https://img.shields.io/badge/MediaPipe-rpi4-orange.svg)
![Platform](https://img.shields.io/badge/Platform-Raspberry%20Pi%204-red.svg)

---

## Table of Contents

- [Demo](#demo)
- [Project Structure](#project-structure)
- [Features](#features)
- [Requirements](#requirements)
- [Setup Guide](#setup-guide)
- [Usage](#usage)
- [Supported Gestures](#supported-gestures)
- [Known Issues & Notes](#known-issues--notes)
- [Credits](#credits)

---

## Project Structure

```
hand-gesture-project/
│
├── module2.py               # Core module: hand landmark detection + TTS
├── module.py                # Legacy support module (used by Simple-Hand-Tracker.py)
│
├── Simple-Hand-Tracker.py   # Basic hand skeleton tracker — start here
├── hnd_rcg_1.py             # Gesture recognizer v1 (console output)
├── hnd_rcg_2_try1.py        # Gesture recognizer v2 (on-screen overlay) ← main project
├── paint2323.py             # Finger painting app using index finger
├── rck_ppr_scr_gme.py       # Rock Paper Scissors game v1
├── rck_ppr_scr_gme2.py      # Rock Paper Scissors game v2 (with countdown overlay)
│
├── requirements.txt
└── README.md
```

---

## Features

- **Real-time hand landmark detection** using MediaPipe (21 landmarks per hand)
- **Gesture recognition** mapped to words, letters, and signs
- **Finger painting** — draw on screen using just your index finger
- **Rock Paper Scissors** — play against the computer using hand gestures
- **Text-to-speech** support via gTTS (optional, requires `mpg321`)
- Designed and tested on **Raspberry Pi 4** with **Raspberry Pi OS Buster**

---

## Requirements

### Hardware
- Raspberry Pi 4 (tested; may work on Pi 5 but untested)
- USB Webcam
- Micro SD Card (16GB+ recommended)
- Ethernet Cable or Wi-Fi
- Monitor / VNC Viewer for display

### Software
- Raspberry Pi OS **Buster** (see note below)
- Python 3.7+
- OpenCV 4.4.0 (built from source on Pi)
- MediaPipe for RPi4
- gTTS + mpg321 (for text-to-speech)

> **⚠️ Important:** This project was tested on **Raspberry Pi OS Buster** (2021-05-28).
> OpenCV has known compatibility issues with newer OS versions (Bullseye/Bookworm).
> Use the Buster image linked below for best results.
>
> Buster OS download: https://downloads.raspberrypi.org/raspios_armhf/images/raspios_armhf-2021-05-28/

---

## Setup Guide

### Step 1 — Flash the OS

Flash the Buster OS image to your SD card using [Raspberry Pi Imager](https://www.raspberrypi.com/software/).

### Step 2 — Install Python Dependencies

Open a terminal on your Raspberry Pi and run the following commands one by one:

```bash
sudo pip3 install mediapipe-rpi4
sudo pip3 install gtts
sudo apt install mpg321
```

If you encounter NumPy errors:
```bash
sudo pip3 install numpy --upgrade --ignore-installed
```

### Step 3 — Build OpenCV from Source

OpenCV must be compiled from source on the Raspberry Pi. This takes **over an hour** — be patient.

```bash
# Update system
sudo apt-get update && sudo apt-get upgrade

# Expand swap file (edit CONF_SWAPSIZE from 100 to 2048)
sudo nano /etc/dphys-swapfile
# Press Ctrl+X, then Y, then Enter to save

# Install build dependencies
sudo apt-get install build-essential cmake pkg-config
sudo apt-get install libjpeg-dev libtiff5-dev libjasper-dev libpng12-dev
sudo apt-get install libavcodec-dev libavformat-dev libswscale-dev libv4l-dev
sudo apt-get install libxvidcore-dev libx264-dev
sudo apt-get install libgtk2.0-dev libgtk-3-dev
sudo apt-get install libatlas-base-dev gfortran
sudo pip3 install numpy

# Download OpenCV source
wget -O opencv.zip https://github.com/opencv/opencv/archive/4.4.0.zip
wget -O opencv_contrib.zip https://github.com/opencv/opencv_contrib/archive/4.4.0.zip
unzip opencv.zip
unzip opencv_contrib.zip

# Build
cd ~/opencv-4.4.0/
mkdir build && cd build
cmake -D CMAKE_BUILD_TYPE=RELEASE \
      -D CMAKE_INSTALL_PREFIX=/usr/local \
      -D INSTALL_PYTHON_EXAMPLES=ON \
      -D OPENCV_EXTRA_MODULES_PATH=~/opencv_contrib-4.4.0/modules \
      -D BUILD_EXAMPLES=ON ..

make -j$(nproc)
sudo make install && sudo ldconfig
sudo reboot
```

### Step 4 — Clone this Repository

```bash
git clone https://github.com/YOUR_USERNAME/hand-gesture-project.git
cd hand-gesture-project
```

### Step 5 — Verify Setup

Run the simple hand tracker first to confirm everything is working:

```bash
python3 Simple-Hand-Tracker.py
```

If you see a live camera feed with hand skeleton overlaid — you're all set!

---

## Usage

### Hand Tracker (Start Here)
```bash
python3 Simple-Hand-Tracker.py
```
Press `Q` to quit.

### Gesture Recognizer
```bash
python3 hnd_rcg_2_try1.py
```
Recognized gestures are displayed on-screen. Press `S` to stop.

### Finger Painting
```bash
python3 paint2323.py
```
Move your index finger to draw. Press `Q` to quit.

### Rock Paper Scissors Game
```bash
python3 rck_ppr_scr_gme2.py
```
- Show **thumbs up** to start / play again
- Show **Rock**, **Paper**, or **Scissors** after the countdown
- Press `S` to stop at any time

---

## Supported Gestures

| Gesture Tuple | Label |
|---|---|
| `(1,1,0,0,0)` | A |
| `(0,1,1,1,1)` | B |
| `(1,1,1,0,0)` | C |
| `(1,0,0,0,0)` | Thumb Up |
| `(1,1,1,1,1)` | Hello |
| `(0,0,0,0,0)` | No fingers up |
| `(0,1,1,0,0)` | Victory ✌️ |
| `(0,0,1,0,0)` | Middle finger up |
| `(1,0,1,0,0)` | Surfer 🤙 |
| `(0,1,0,0,0)` | Index Up |
| `(0,0,0,1,0)` | Ring Up |
| `(0,0,0,0,1)` | Pinky Up |
| `(1,1,0,0,1)` | Wassup |

Gesture tuples represent: `(Thumb, Index, Middle, Ring, Pinky)` — `1` = up, `0` = down.

You can easily extend this by adding new entries to the `gesture_dict` in `hnd_rcg_2_try1.py`.

---

## Known Issues & Notes

- **Single hand only** — multi-hand support is not implemented in the gesture recognizer
- **Thumb detection** uses horizontal (X-axis) comparison, which may be less accurate when the hand is tilted
- **Camera orientation** — if the video appears flipped, uncomment the `cv2.flip` line in the relevant script
- If errors occur during setup, check the [Core Electronics guide](https://core-electronics.com.au/guides/hand-identification-raspberry-pi/) and its comment section — it was invaluable during development

---

## Credits

- MediaPipe by Google — hand landmark detection
- [Core Electronics](https://core-electronics.com.au/guides/hand-identification-raspberry-pi/) — Raspberry Pi OpenCV setup guide
- gTTS — text-to-speech output

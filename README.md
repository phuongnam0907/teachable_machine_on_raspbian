# teachable_machine_on_raspbian
This tutorial follows the instructions from page: https://www.instructables.com/La-Croix-Flavor-Detector-Easy-Object-Detection-on-/
## Prepare
* Raspberry Pi (tested on RPi3 Model B and RPi4 Model B) 
* Username: pi
* Password: raspberry

## Setup environment
Follows these steps to setup environment

### STEP 1
Enable these interface: Camera, SSH, VNC. <br>
There are 2 ways to config this:<br>
1. Using command line:
```
sudo raspi-config 
```
2. Using GUI on Raspberry PI Desktop Configuration
![alt text](https://github.com/phuongnam0907/teachable_machine_on_raspbian/blob/main/image/setting.png?raw=true)

### STEP 2
Install packages:
```
sudo apt-get install git
sudo apt-get install python3
sudo apt-get install fswebcam
```

### STEP 3
Test library version: <br>
1. Python 3 version
```
python3 --version
```
Expect result:
```
Python 3.7.3
```
2. Setup USB camera
```
sudo usermod -a -G video $LOGNAME
fswebcam image.jpg
```
Expect result:
```
--- Opening /dev/video0...
Trying source module v4l2...
/dev/video0 opened.
No input was specified, using the first.
Adjusting resolution from 384x288 to 480x270.
--- Capturing frame...
Corrupt JPEG data: 2 extraneous bytes before marker 0xd4
Captured frame in 0.00 seconds.
--- Processing captured image...
Writing JPEG image to 'image.jpg'.
```
Resolution of USB Camera is <b>480</b>x<b>270</b>

### Clone source and setup environment
Run these commands to setup environment for OpenCV/TensorFlow
```
cd ~
git clone https://github.com/mjdargen/RPi-La-Croix-Flavor-Detector.git
cd RPi-La-Croix-Flavor-Detector/
sudo chmod +x ./rpi_install.sh
./rpi_install.sh
```
It takes about <b>3 hour</b> to setup environment.

## Config 
Change configuration of teachable machine script python
```
cd ~/RPi-La-Croix-Flavor-Detector/
nano tm_obj_det.py 
```
Edit these lines:
```
Line 84: Path of labels.txt file. 
		DEFAULT: f"{DIR_PATH}/la_croix_model/labels.txt"
Line 99: Path of models.h5 file. 
		DEFAULT: f"{DIR_PATH}/la_croix_model/keras_model.h5"
Line 107: Width frame from USB camera. 
		DEFAULT: 1024
Line 108: Height frame from USB camera. 
		DEFAULT: 768
Line 160: Threshold percent. 
		DEFAULT: 90
```
Examples:
```
...<line 84>...
labels_path = f"{DIR_PATH}/la_croix_model/labels.txt"

...<line 99>...
model_path = f"{DIR_PATH}/la_croix_model/keras_model.h5"

...<line 107>...
frameWidth = 480

...<line 108>...
frameHeight = 270

...<line 160>...
conf_threshold = 90
```

If it has error when run python file:
```
source TMenv/bin/activate
sudo pip install numpy --upgrade --ignore-installed
deactivate
```
## Run
Run these commands to start teachable machine on rasberry pi:
```
source TMenv/bin/activate
python3 tm_obj_det.py
deactivate
```
<i>Wait for <b>30 seconds</b> to load models</i>

## Run with another models
1. Go to page [Teachable Machine](https://teachablemachine.withgoogle.com/) to create keras models<br>
2. Download the keras models. Ex: <i>converted_keras.zip</i> <br>
3. Copy .zip file to directory on Raspberry PI
```
/home/pi/RPi-La-Croix-Flavor-Detector/la_croix_model/
```
4. Remove old files of model
```
cd ~/RPi-La-Croix-Flavor-Detector/la_croix_model/
rm -f keras_model.h5 labels.txt
```
5. Unzip file "converted_keras.zip" to create files of model
```
cd ~/RPi-La-Croix-Flavor-Detector/la_croix_model/
unzip converted_keras.zip
```
6. Run these commands to run teachable machine
```
cd ~/RPi-La-Croix-Flavor-Detector/
source TMenv/bin/activate
python3 tm_obj_det.py
deactivate
```

## Note
Before running python script, make sure USB camera is detecting. <br>
Test the USB camera:
```
ls /dev/video*
```
Expect result
```
/dev/video0 /dev/video1 ...
```
If nothings show, re-plugin the USB camera then run the command again. <br>
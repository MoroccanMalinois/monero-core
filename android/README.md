Copyright (c) 2014-2017, The Monero Project


## Current status : ALPHA

 - Minimum Android 5.0 (api level 21)
 - Usage of the password strength meter during the wizard segfaults
 - Modal dialogs can appear in background giving the feeling that the application is frozen (Work around : turn screen off/on or switch to another app and back)
 - No anchors in QLineEdits and no Copy/paste in Qt 5.7 (should be fixed in Qt 5.8)
 - Only tested on debian x86-64

## Build using Docker

# Base environnement

        cd monero/contrib/android
        docker build -t monero-android .

# Build GUI

        cd android/docker
        docker build -t monero-gui-android .
        docker create -it --name monero-gui-android monero-gui-android bash

# Get the apk

        docker cp monero-gui-android:/usr/monero-core/build/debug/bin/bin/QtApp-debug.apk .
       
## Deployment

- Using ADB (Android debugger bridge) : 

  First, see section [Enable adb debugging on your device](https://developer.android.com/studio/command-line/adb.html#Enabling)
  The only place where we are allowed to play is `/data/local/tmp`. So : 

        adb  push /usr/monero-core/build/debug/bin//bin/QtApp-debug.apk /data/local/tmp
        adb  shell pm install -r /data/local/tmp/QtApp-debug.apk
   
  - Troubleshooting: 
  
        adb devices -l
        adb logcat
	
    if using docker, make sure you did "docker run -v /dev/bus/usb:/dev/bus/usb "
	
- Using a web server

        mkdir /usr/tmp
        cp /usr/monero-core/build/debug/bin//bin/QtApp-debug.apk /usr/tmp
        docker run -d -v /usr/tmp:/usr/share/nginx/html:ro -p 8008:80 nginx

  Now it should be accessible through a web browser at 
  
        http://<your.local.ip>:8080/QtApp-debug.apk
  

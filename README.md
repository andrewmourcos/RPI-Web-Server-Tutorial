# Raspberry Pi Camera Server

By Andrew Mourcos

# Setup raspberry pi (RPI)

I assume you already have the Raspberry Pi operating system (Raspian OS) installed. Plug RPI into wall, monitor/TV screen, mouse, keyboard…

Open the terminal application and write the following commands:
```
sudo apt-get update

sudo apt-get upgrade
```
^this is just standard to update your RPI system.

# Enable camera access

Type in terminal:
```
sudo raspi-config
```
Choose &quot;camera&quot; and select &quot;Enable support for Raspberry Pi camera&quot;. Reboot when prompted. This just changes your settings to let u use a camera. You can test that the camera works by taking a picture with this command:
```
raspistill -o testpicture.jpg
```
# Install Motion

You are probably going to want to use software called Motion. You will need to install it and activate a few required drivers to make it work:
```
sudo apt-get install motion

sudo modprobe bcm2835-v4l2
```
There&#39;s one problem now, if you turn off the RPI, you will have to reactivate this driver (the bcm2835 thing) every time you turn it back on. You can easily make the driver activate automatically when the RPI boots up by modifying a settings file on the RPI. In terminal type:
```
sudo nano /etc/modules
```
This opens up a text file in the terminal and you will have to add the following line at the end of the file:
```
bcm2835-v4l2
```
To exit the file editor type &#39;CTRL+X&#39;. It will ask you if you want to save, type &#39;Y&#39; (for yes) and hit enter.

You probably also want this camera running constantly, so you can make it into a background process (called a &quot;Daemon&quot;). In terminal, you will need to open a new settings file:
```
sudo nano /etc/default/motion
```
In this file, scroll until you find a line that says &quot;start\_motion\_daemon&quot; and change it to equal &quot;yes&quot;. It should look something like this:
```
start_motion_daemon=yes
```
If you 100% cannot find that line in the file, just add it to the end of the file. Save &amp; exit (CTRL+X, then Y, then enter).

# Configuring Camera Feed

Open the Motion configuration (settings) file:
```
sudo nano /etc/motion/motion.conf
```
Next, find the options below and edit them to look like this (btw, anything I write after a hashtag is my comment to help you see what each line means):
```
# Allow motion to run the daemon we set earlier
daemon on

# Set the logfile (makes a file for you to see errors if system crashes)
logfile /tmp/motion.log

# Allows stream to be accessed outside of RPI&#39;s localhost (on ur computer, etc.)
stream_localhost off

# disable pictures and movies saving (if u just want a stream)
output_pictures off
ffmpeg_output_movies off

# set the framerate of the stream (100 is pretty high quality, if RPI too laggy try playing with this number)
framerate 100

# set the width and height of your video (resolution)
width 640
height 480

# Sets stream to a viewing port
webcontrol_port 8081
```
# Test Camera Server

First start the Motion server in terminal with:
```
sudo service motion start
```
To view the stream, go to [http://localhost:8081](http://localhost:8081) **on the RPI**.

# Test Camera Server from Another Device

Connect RPI to WiFi.

Now you need to know your RPI&#39;s IP Address. Do this by typing:
```
ifconfig
```
In the response, try to find a line with &quot;inet address&quot;, it&#39;ll look something like this:
```
wlan0 Link encap:Ethernet HWaddr 80:1f:02:aa:12:58

      inet addr:__192.168.1.8_ _Bcast:192.168.1.255 Mask:255.255.255.0

      ...
```
The RPI&#39;s IP address is the string of numbers that looks something like: _192.168.1.8_ (an example). Write it down.

Now take your laptop or phone or whatever and go to http://&lt;Your IP Address Here&gt;:8081 .

An alternative would be to go to [http://raspberrypi:8081](http://raspberrypi:8081) . Some older web browsers don&#39;t let you do this, so if it doesn&#39;t work at first, try another newer browser.

If all went well, you are successfully streaming the camera to that site. Keep in mind, this isn&#39;t a website that anyone can visit, only people on your WiFi network should be able to visit this page.

If you want it to be accessible outside of your local WiFi, look into using &quot;DNS&quot;. It gets a bit more complicated if you do and I think having it locally is your best bet.

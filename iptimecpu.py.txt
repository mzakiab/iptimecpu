#!/usr/bin/env python

# Diubahsuai oleh 9W2KEY OJ15dx
# Pada 8 Julai, 2020

#Penulis asal:
#Written By Cyrus Wolf
#Can be modified and is under the Open Source Standard Rules
#Please pay attention to indents and spacing as it matters with python


#import RPi.GPIO as GPIO #Imports RPi for GPIO Use (Comment/Uncomment to use)
import I2C_LCD_driver # Imports Custom LCD Display Driver
import socket # Imports socket function
import struct # Imports structure function
import fcntl # Imports networking function
import time # Imports time function
import os # Imports Operating System function
import re # Imports Reg Ex function
from time import sleep # Imports sleep function from time module

disp = I2C_LCD_driver.lcd() # Initializes LCD Display

# Sends the Temp of the cpu to the lcd display (for 10 seconds)
def tempcpu(): #Defines "tempcpu"
   for _ in range(10): # Sets up timer

        cputemp = os.popen("vcgencmd measure_temp").readline() # Gets temp readi
ng (shows as "temp=xx.x'C")
        celsius = re.sub("[^0123456789\.]", "", cputemp) # Removes everything bu
t numbers and "."
        fahrenheit = int(9.0/5.0*int(float(celsius)+32)) # Math Function Fahrenh
eit (celsius * 9 / 5 + 32) as interger

        disp.lcd_display_string("Suhu : {} c".format(celsius), 1) # Prints Temp
as Celsius to the LCD Display line 1
        disp.lcd_display_string("C P U: {}  f".format(fahrenheit), 2) # Prints T
emp as Fahrenheit to the LCD Display line 2

        sleep(1) # Sleeps for one second before restarting loop

# Sends the Time and Date to the lcd display (for 10 seconds)
def curtime(): # Defines "curtime"
   for _ in range(10): # Sets up timer

        disp.lcd_display_string("Time: {}".format(time.strftime("%H:%M:%S")), 1)
 # Prints time to the LCD Display line 1
        disp.lcd_display_string("Date: {}".format(time.strftime("%d/%m/%Y")), 2)
 # Prints date to the LCD Display line 2

        sleep(1) # Sleeps for one second before restarting loop

# Gets the IP Address
def getaddr(ifname): # Defines "getaddr" as well as ifname arguement later

    s = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
    return socket.inet_ntoa(fcntl.ioctl(
        s.fileno(),
        0x8915,
        struct.pack('256s', ifname[:15])
    )[20:24]) # Not sure how this block works just yet, but it does dig up the i
p address

# Sends the IP Address to the lcd display (for 10 seconds) as wlan0, eth0 can al
so be used
def getip(): # Defines "getip"

    ip = getaddr('eth0') # Grabs the address from "wlan0" and assigns it to "ip"
    for _ in range(10): # Sets up timer

        disp.lcd_display_string("IP Address FR24:", 1) # Prints string to LCD Di
splay line 1
        disp.lcd_display_string(ip, 2) # Prints "ip" to LCD Display line 2

        sleep(1) # Sleeps for one second before restarting loop

# runs a forever loop calling the defs above
try: # Gives way to exception later

    while True: # Forever loop

        tempcpu() # Calls "tempcpu"
        disp.lcd_clear() # Clears the LCD Display

        curtime() # Calls "curtime"
        disp.lcd_clear() # Clears the LCD Display

        getip() # Calls "getip"
        disp.lcd_clear() # Again Clears the LCD Display

# Allows for clean exit
except KeyboardInterrupt: # If interrupted by the keyboard ("Control" + "C")

   disp.clear() #clear the lcd display
   sleep(1) #sleeps 1 second
   disp.backlight(0) #Turn Off Backlight

# Exits the python interperter
exit()


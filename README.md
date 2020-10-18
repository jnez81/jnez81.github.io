# Welcome to James Martinez's ePortfolio!

This ePortfolio contains a professional self-assessment, followed by a code review of several artifacts that will undergo revision and enhancements. 

Each artifact presented in this ePortfolio will also be accompanied by narratives that reflect on the revision and enhancement process.

Thank you kindly for spending your time reviewing my ePortfolio!

## Code Review
[Code Review Video](https://youtu.be/N0ShK56sxoU)


## Artifacts
Software Design/Engineering
```python
# James Martinez 
# Software Design/Engineering program enhancemnents
# Embedded System Device Script
# This software is designed to operate as a weather station
# that records temperature and humidity during daytime only
# The improvements made to this program include easier to understand
# comments and the inclusion of Twitter functionality that will
# tweet the weather data every minute


import time
import twitter
import math
import grovepi
from grovepi import *
from grove_rgb_lcd import *
from time import sleep
from math import isnan

dht_sensor_port = 7 # connect the DHt sensor to port 7
dht_sensor_type = 0 # use 0 for the blue-colored sensor and 1 for the white-colored sensor

# set blue as backlight color
setRGB(0,0,255)

# Connect the Grove Light Sensor to analog port A0
# SIG,NC,VCC,GND
light_sensor = 0

# Connect the Green LED to digital port D4
# SIG,NC,VCC,GND
led_g = 4

# Connect the Blue LED to digital port D3
# SIG,NC,VCC,GND
led_b = 3

# Connect the Red LED to digital port D2
# SIG,NC,VCC,GND
led_r = 2


# Sensor records data once sensor exceeds threshold resistance
threshold = 10

# Connect to Twitter
api = twitter.Api(
    consumer_key='YourKey',
    consumer_secret='YourKey',
    access_token_key='YourKey',
    access_token_secret='YourKey'
    )

grovepi.pinMode(light_sensor,"INPUT")
grovepi.pinMode(led_g,"OUTPUT")
grovepi.pinMode(led_b,"OUTPUT")
grovepi.pinMode(led_r,"OUTPUT")


while True:
    try:
        # Get sensor value
        sensor_value = grovepi.analogRead(light_sensor)

        # Calculate resistance of sensor in K
        if sensor_value <= 0: # Prevents error caused by divide by 0
            resistance = 0
        else: 
            resistance = (float)(1023 - sensor_value) * 10 / sensor_value
	
	if resistance >= threshold:
            # Record temp and humidity data during daytime only
            print("sensor_value = %d resistance = %.2f" %(sensor_value,  resistance))
            time.sleep(1800) # Readings must be taken every 30 mins

            # get the temperature and Humidity from the DHT sensor
            [ temp,hum ] = dht(dht_sensor_port,dht_sensor_type)
            print("temp = ", ((temp * 1.8) + 32) , "humidity = ", hum,"%")

            # check if number is a valid number
            # if so, then raise a type error exception
            if isnan(temp) is True or isnan(hum) is True:
                    raise TypeError('Number is not valid')

            t = str((temp * 1.8) + 32)
            h = str(hum)

            # Output temp and humidity data to LCD screen
            setText_norefresh("Temp: " + t + "F\n" + "Humidity : " + h + "%")

            # Output visual indication of type of data using LEDs
            if t > 60 and t < 85 and h < 80:
                digitalWrite(led_g,1)  # Send HIGH to Green LED
                time.sleep(10)

            elif t > 85 and t < 95 and h < 80:
                digitalWrite(led_b,1)  # Send HIGH to Blue LED
                time.sleep(10)

            elif t > 95:
                digitalWrite(led_r,1)  # Send HIGH to Red LED
                time.sleep(10)

            elif h > 80:
		digitalWrite(led_g,1)  # Send HIGH to Green and Blue LEDs
                time.sleep(10)	       

                digitalWrite(led_b,1)
                time.sleep(10)  

        else:
            # Sensor readings not to be recorded during nighttime
            print("Temperature and Humidity not recorded at night")
            time.sleep(60) # Refresh every minute

        # Post a tweet
        out_str = "%s Temp: %d F, Humidity: %d" % (t, h)
        print(out_str)
        api.PostUpdate(out_str)

    except IOError:
        print ("Error")

    except (IOError, TypeError) as e:
        print(str(e))
	    # if type error received, reset LCD text
        setText("")

    except KeyboardInterrupt as e:
        print(str(e))
        # Exiting the program, leave the LCD blank
        setText("")
        break


	 # wait one minute before refreshing the LCD and twitter feed
    time.sleep(60)
```

[Algorithms and Data Structure](https://github.com/jnez81/algo_data_struct)

[Databases](https://github.com/jnez81/databases)

## Narratives
[Software Design/Engineering](https://github.com/jnez81/design_eng_artifact/blob/main/narrative)

[Algorithms and Data Structure](https://github.com/jnez81/algo_data_struct/blob/main/narrative)

[Databases](https://github.com/jnez81/databases/blob/main/narrative)


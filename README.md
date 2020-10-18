# Welcome to James Martinez's ePortfolio!

This ePortfolio contains a professional self-assessment, followed by a code review of several artifacts that will undergo revision and enhancements. 

Each artifact presented in this ePortfolio will also be accompanied by narratives that reflect on the revision and enhancement process.

Thank you kindly for spending your time reviewing my ePortfolio!


## Code Review
[Code Review Video](https://youtu.be/N0ShK56sxoU)



## Artifacts and Narratives

### Software Design/Engineering
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
 
The name of this artifact is the WetSpec, a weather reporting station that runs on the Raspberry Pi platform which is connected to a GrovePi electronics board which 
has the capability to easily connect a wide range of sensor types.  The software for this project was designed using Python.

This artifact was selected to showcase my ability to write software to interface with embedded systems to control hardware and software components, and display data from an 
embedded device by modifying interfacing software in an emerging systems architecture. The artifact was improved by including Twitter feed functionality that will tweet 
the weather data every minute to a specified Twitter account.  

Several revisions were made to the initial enhancement plan as I had originally planned to revise the code to add color-coded functionality to the temperature and humidity 
readings. However, I later decided to include the ability for the program to tweet the weather data on a specified Twitter account.  

During the enhancement process for this element of the final project I was able to see the theory of having new ideas and perspectives after not seeing a specific code for 
a period of time, play out in a real-world scenario.  The original source code for this project was done about 6 months ago and when I submitted the code for a class final project,
I was very satisfied with the result.  The program functioned exactly the way I intended and was the final product after weeks of revisions and improvements.  However, after 
many months without seeing the code I right away noticed that some of the comments were confusing to understand, and since I hadn’t seen the code for months it took me a 
while to remember what the code was designed to accomplish.




### Algorithms and Data Structure
```python
# Aggregation pipeline statement that determines a user-specified 
# company's market cap value.  Company specific stock information is 
# retrieved from MongoDB database "market" in the collection "stocks" 

import json 
from bson import json_util
from pymongo import MongoClient

client = MongoClient('localhost', 27017)
db = client.market
coll = db['stocks']

def aggregate():
    try: 
        
        # User-input company name to retrieve market cap value
        company = input('Enter company name using quotation marks: ')

        # Aggregation pipeline that identifies company name and multiplies company's
        # share price and shares outstanding value to determine company's market cap value
        pipe1 = [
                {'$match': {"Company" : company}}, 
                {'$project': {"Company": 1, "Market Cap Value": { '$multiply': ["$Price", "$Shares Outstanding"] }}} 
              ]
        
        result = db.stocks.aggregate(pipe1)
        
        for x in result:
            print(x)
          
        
    except Exception as exception:
        print("Exception: {}".format(type(exception).__name__))
        print("Exception message: Use quotation marks and/or check spelling.")
        
aggregate()
```

This artifact is designed to function as a stock market securities information reporting service and utilizes a RESTful API web-based protocol to retrieve specified company data 
in conjunction with a MongoDB database of company stocks information.  

This artifact was selected to showcase my abilities and skills in developing algorithms that search and retrieve data according to customer’s requests, using advanced programming 
techniques in multiple languages that interface within a full stack environment, and using application programming interfaces (APIs) to create a reusable software service.  The 
artifact was previously designed to retrieve a list of companies that that have the highest number of totals shares outstanding and then group the companies by their respective 
industry.  It was later decided that this artifact could be significantly improved if it could provide potential investors with one of the most important values in respect to 
stocks, which is market capitalization.  The function will be significantly more efficient as it will only need to search for one specified company name and then determine the 
company’s respective market capitalization, or market cap.

The design of the new function of determining a company’s market cap value significantly improves its efficiency and provides the user with a more valuable result. Potential 
investors can simply input the company name into the program, and it will return the company’s market cap value which is a great indicator of potential risk that is involved 
while investing in company stocks.  

Modifying this artifact was another example of returning to an old project after a period of time and having fresh ideas that can improve its functionality and efficiency.  These 
moments in software development teach us that innovation is a constant process and extremely relevant in computer science.  During the process of modifying this artifact I was
also able to relate the similarities between continuously learning new skills as a software engineer, and continuously improving and innovating older technology that we have 
developed. 




### Databases
```python
# Method prompts user-input to identify business ID in MongoDB database "city" 
# in the collection "inspections".  User is then prompted to select which value of 
# the document to modify and update in the database. 

import json 
from bson import json_util
from pymongo import MongoClient

#Connect to MongoClient, database and collection
client = MongoClient('localhost', 27017)
db = client.city
coll = db['inspections']

#Method to update a document in MongoDB
def update_one():
    try: 
        businessID = input('Enter business ID without quotation marks: ')
        
        
        db.inspections.find_one(
                {
                    "id" : businessID ,
                   
                }
            )
        print("\nBusiness found successfully!")
        print(coll.find_one({"id": businessID}))
        
        user_input = input('\nChoose the numbered attribute to update: 1. ID, 2. Business Name, 3. Inspection Date,4. Inspection Result ')
        
        # elif statement to update attribute per user request
        if user_input == 1:
          newID = input('Please enter new business ID: ')
          
          db.inspections.insert({"id" : newID})
          
          print("Business ID has been updated!")
          
        elif user_input == 2: 
          newName = input('Please enter new business name in quotation marks: ')
          
          db.inspections.insert({"business_name" : newName})
          
          print("Business name has been updated!")
          
        elif user_input == 3: 
          newDate = input('Please enter new inspection date in quotation marks: ')
          
          db.inspections.insert({"date" : newDate})
          
          print("Inspection date has been updated!")
          
        elif user_input == 4: 
          newResult = input('Please enter new inspection result in quotation marks: ')
          
          db.inspections.insert({"result" : newResult})
          
          print("Inspection result has been updated!")
          
        else: 
          print("Please select options 1-4, please try again.")
        
        
    except Exception as exception:
        print("Exception: {}".format(type(exception).__name__))
        print("Exception message: Use quotation marks and/or check spelling.")
        
update_one()
```

This artifact is designed to function as a stock market securities information reporting service and utilizes a RESTful API web-based protocol to retrieve specified company data 
in conjunction with a MongoDB database of company stocks information.  

This artifact was selected to showcase my abilities and skills with database systems, specifically MongoDB which is a NoSQL document storage system. This artifact incorporates 
CRUD (create, read, update, and delete) operations that are designed to manipulate documents in the NoSQL database according to user-input.  It also demonstrates the ability to 
develop a web service application to implement a RESTful application programming interface for the MongoDB database. The improvements made to this artifact consist of incorporating
MongoDB statements that will update user-defined values for existing documents using the company’s business ID. The original code simply prompted the user to identify which 
company ticker abbreviation to use in order to update the “volume” value in the document.  Each company document has a large number of values associated with the company’s 
respective stock market information and prompting the user to identify several specific values to update adds much more functionality and purpose to the program.  The user inputs 
the business ID into the database and will then prompt the user to select which values to update in the document.  

Several revisions were made to this artifact’s enhancement plan over the previous weeks, all consisting of determining which specific document values to modify in the MongoDB 
system.  The implementation of prompting the user to identify which business value to update required advanced programming concepts that include the else-if statement in the 
‘update_one’ method which is designed to identify the requested value and update it in the MongoDB database.

The enhancement process of this artifact did not present many challenges, however there was some additional research required to refresh knowledge of proper language syntax while 
designing the else-if statement in the ‘updateDocument.py’ function.  A key learning through this process is understanding the importance of documentation and always taking 
advantage of the ability to research online documentation that greatly benefits software development projects. The practice of documenting software development is also used as a 
historical reference to the decisions made during its design, and to have a solid reference point other than a few developers who may lose this valuable information as time passes.   





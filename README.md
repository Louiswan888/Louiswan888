#Add Phidgets Library
from Phidget22.Phidget import *
from Phidget22.Devices.DigitalInput import *

import pgzrun

WIDTH = 500
HEIGHT = 500
SPEED = 5

#Define Actors
red_target = Actor('red_button')
red_target.pos = ((WIDTH/2) - 100), HEIGHT - 150

green_target = Actor('green_button')
green_target.pos = ((WIDTH/2) + 100), HEIGHT - 150

red_circle = Actor('red_circle')
red_circle.pos = (red_target.x,0)

#Used for PERFECT, GOOD, MISS text
status_string = ""

def draw():
    screen.clear()
    screen.fill((255,255,255))
    red_target.draw()
    green_target.draw()
    red_circle.draw()
    screen.draw.text(status_string, center=(red_target.x, red_target.y + 100), color="red",fontsize=50)    
    
def update():
    #move red circle down the screen
    red_circle.y += SPEED
    
    #if circle has reached bottom, move it to top
    if(red_circle.y == HEIGHT + red_circle.height):
        red_circle.y = 0    
    
#Phidgets Code Start
#Button Event
def onRedButton_StateChange(self, state):
    global status_string
    
    #When button is pressed
    if(state):
        #Compare y-position to judge accuracy 
        accuracy = abs(red_target.y - red_circle.y)
        if(accuracy < 10):
            status_string = "PERFECT"
        elif(accuracy < 30):
            status_string = "GOOD"
        else:
            status_string = "MISS"
        
#Create, Address, Subscribe to Events and Open
redButton = DigitalInput()
redButton.setIsHubPortDevice(True)
redButton.setHubPort(0)
redButton.setOnStateChangeHandler(onRedButton_StateChange)
redButton.openWaitForAttachment(1000)
#Phidgets Code End    
    
pgzrun.go()
  

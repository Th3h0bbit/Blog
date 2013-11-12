Experiments
===========
/*
HC-SR04 Ping distance sensor]
VCC to arduino 5v GND to arduino GND
Echo to Arduino pin 13 Trig to Arduino pin 12
Red POS to Arduino pin 11
Green POS to Arduino pin 10
560 ohm resistor to both LED NEG and GRD power rail
More info at: http://goo.gl/kJ8Gl
Original code improvements to the Ping sketch sourced from Trollmaker.com
Some code and wiring inspired by http://en.wikiversity.org/wiki/User:Dstaub/robotcar
*/



#define trigPin 13
#define echoPin 12
#define led 11
#define led2 10

#include <Servo.h> 
Servo servoxAxis;
int valxAxis;    // variable to read the value from the analog pin 


const int numReadings = 10;

int readings[numReadings];      // the readings from the analog input
int index = 0;                  // the index of the current reading
int total = 0;                  // the running total
int average = 0;                // the average





void setup() {
  Serial.begin (9600);
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);
  pinMode(led, OUTPUT);
  pinMode(led2, OUTPUT);
  
  servoxAxis.attach(8);  // attaches the servo on pin 9 to the servo object 
}

void loop() {
  long duration, distance;
  digitalWrite(trigPin, LOW);  // Added this line
  delayMicroseconds(2); // Added this line
  digitalWrite(trigPin, HIGH);
  //delayMicroseconds(1000); //- Removed this line
  delayMicroseconds(10); // Added this line
  digitalWrite(trigPin, LOW);
  duration = pulseIn(echoPin, HIGH);
  distance = (duration/2) / 29.1;
  
  
   // subtract the last reading:
  total= total - readings[index];         
  // read from the sensor:  
  readings[index] = distance;
  // add the reading to the total:
  total= total + readings[index];       
  // advance to the next position in the array:  
  index = index + 1;                    

  // if we're at the end of the array...
  if (index >= numReadings)              
    // ...wrap around to the beginning: 
    index = 0;                           

  // calculate the average:
  average = total / numReadings;         
  // send it to the computer as ASCII digits
  
  
  
  if (distance < 4) {  // This is where the LED On/Off happens
    digitalWrite(led,HIGH); // When the Red condition is met, the Green LED should turn off
  digitalWrite(led2,LOW);
}
  else {
    digitalWrite(led,LOW);
    digitalWrite(led2,HIGH);
  }
  if (distance >= 200 || distance <= 0){
    Serial.println("Out of range");
  }
  else {
    if (distance <= 30){
      
       valxAxis = average*1000;            // reads the value of the potentiometer (value between 0 and 1023) 
  valxAxis = map(valxAxis, 0, 50000, 0, 179);     // scale it to use it with the servo (value between 0 and 180) 
  servoxAxis.write(valxAxis);                  // sets the servo position according to the scaled value 
  delay(15);                           // waits for the servo to get there 
    }
  
    
    Serial.print(average);   
    Serial.println(" cm");
  }
  delay(50);
  
}

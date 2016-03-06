/*
 HC-SR04 Ping distance sensor:
 VCC to arduino 5v 
 GND to arduino GND
 Echo to Arduino pin 7 
 Trig to Arduino pin 8
 */
 This sketch originates from Virtualmix: http://goo.gl/kJ8Gl
 Has been modified by Winkle ink here: http://winkleink.blogspot.com.au/2012/05/arduino-hc-sr04-ultrasonic-distance.html
 And modified further by ScottC here: http://arduinobasics.blogspot.com.au/2012/11/arduinobasics-hc-sr04-ultrasonic-sensor.html
 on 10 Nov 2012.
*/


#define trigPin 2      // Trigger Pin
#define echoPin 3    // Echo Pin
#define yellowLED 7 // warning
#define redLED 6 // red
#define IRoutputPin 4 // IR output
#define LEDsignalPin 5 // led input signal

int maximumRange = 100; // Maximum range needed
int minimumRange = 0; // Minimum range needed
long duration, distance; // Duration used to calculate distance

void setup() {
 Serial.begin (9600);
 pinMode(trigPin, OUTPUT); // 3 is output
 pinMode(echoPin, INPUT);  // 2 is input
 pinMode(yellowLED, OUTPUT);   // 7 output yellow
 pinMode(redLED, OUTPUT);   // 6 output white led (red)
  pinMode(IRoutputPin,OUTPUT);
  digitalWrite(IRoutputPin,HIGH);
  pinMode(LEDsignalPin, OUTPUT);
  Serial.begin(9600);
}

  long consistency[3] = {500, 500, 500};
  
// sonar returns distance and reads slower if object is further than max range
int read (void){

/* The following trigPin/echoPin cycle is used to determine the
 distance of the nearest object by bouncing soundwaves off of it. */ 
 digitalWrite(trigPin, LOW); 
 delayMicroseconds(2); 

 digitalWrite(trigPin, HIGH);
 delayMicroseconds(10); 
 
 digitalWrite(trigPin, LOW);
 duration = pulseIn(echoPin, HIGH);
 
 //Calculate the distance (in cm) based on the speed of sound.
 distance = duration/58.2;

 // if distance less than max range
 Serial.println(distance);
 return (distance);   // return distance to main
 }

void loop() {

while(true){
  long greenDistance = 150;
  long yellowDistance = 100; //if less than 100cm distance turn on WARNING
  long redDistance = 50;    // if less than 50cm distance STOP/SLOWDOW
  
  consistency[0] = read();
// check if 1st reading is warning
while (consistency[0] <= greenDistance)
{
  consistency[1] = read();

  // check if 2nd reading is also warning
  while (consistency[1] <= greenDistance)
  {
     consistency[2] = read();

     // while 3rd reading is also warning, blink yellow slowly
     while (consistency[2] <= greenDistance)
     {
      if ((consistency[0] <= redDistance) && (consistency[1] <= redDistance) && (consistency[2] <= redDistance))
        {
          digitalWrite(redLED, HIGH);   // blinks
          digitalWrite(redLED, LOW);
          digitalWrite(redLED, HIGH);
          digitalWrite(yellowLED, HIGH);
          delay(50);
          digitalWrite(yellowLED, LOW);
          delay(50);
          digitalWrite(redLED, LOW);
        } 
        
        else if ((consistency[0] <= yellowDistance) && (consistency[1] <= yellowDistance) && (consistency[2] <= yellowDistance))
        {
          digitalWrite(redLED, LOW);
          digitalWrite(yellowLED, HIGH);
          delay(50);
          digitalWrite(yellowLED, LOW);
          delay(50);
        }
        
        // check is all 3 readings are less than green warning
        else if ((consistency[0] <= greenDistance) && (consistency[1] <= greenDistance) && (consistency[2] <= greenDistance))
        {
          digitalWrite(redLED,LOW);
          digitalWrite(yellowLED, HIGH);
          delay(150);
          digitalWrite(yellowLED, LOW);
          delay(150);
        }
         
        // move 2nd reading to 1st, and 3rd reading to 2nd, now read 3rd again
        consistency[0] = consistency[1];
        consistency[1] = consistency[2];
        consistency[2] = read();
     }
    
  }
}  
}
}\

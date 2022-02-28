Heart pulse :

#define USE_ARDUINO_INTERRUPTS true    // Set-up low-level interrupts for most acurate BPM math.
#include <PulseSensorPlayground.h>// Includes the PulseSensorPlayground Library.   

//  Variables
const int PulseWire = 0;       // PulseSensor PURPLE WIRE connected to ANALOG PIN 0
const int LED13 = 13;          // The on-board Arduino LED, close to PIN 13.
int Threshold = 550;           // Determine which Signal to "count as a beat" and which to ignore.
                               // Use the "Gettting Started Project" to fine-tune Threshold Value beyond default setting.
                               // Otherwise leave the default "550" value. 
                               
PulseSensorPlayground pulseSensor;  // Creates an instance of the PulseSensorPlayground object called "pulseSensor"


void setup() {   

  Serial.begin(9600);          // For Serial Monitor

  // Configure the PulseSensor object, by assigning our variables to it. 
  pulseSensor.analogInput(PulseWire);   
  pulseSensor.blinkOnPulse(LED13);       //auto-magically blink Arduino's LED with heartbeat.
  pulseSensor.setThreshold(Threshold);   

  // Double-check the "pulseSensor" object was created and "began" seeing a signal. 
   if (pulseSensor.begin()) {
    Serial.println("We created a pulseSensor Object !");  //This prints one time at Arduino power-up,  or on Arduino reset.  
  }
}



void loop() {

 int myBPM = pulseSensor.getBeatsPerMinute();  // Calls function on our pulseSensor object that returns BPM as an "int".
                                               // "myBPM" hold this BPM value now. 

if (pulseSensor.sawStartOfBeat()) {            // Constantly test to see if "a beat happened". 
 Serial.println("♥  A HeartBeat Happened ! "); // If test is "true", print a message "a heartbeat happened".
 Serial.print("BPM: ");                        // Print phrase "BPM: " 
 Serial.println(myBPM);                        // Print the value inside of myBPM. 
}

  delay(20);                    // considered best practice in a simple sketch.

}





GPS:

#include <SoftwareSerial.h>

static const int RXPin = 2, TXPin = 3;
static const uint32_t GPSBaud = 9600;
int  m = 9740;
int y = 71;

SoftwareSerial ss(RXPin, TXPin); 
SoftwareSerial SIM900(7, 8);
int Buzzer = 4; 
String textForSMS;
int Switch = 5; 

String datareal;
String dataimaginary;
String combined;
int raw = 1000000;

String datareal2;
String dataimaginary2;
String combined2;

double longitude;
double latitude;

void setup()
{
  SIM900.begin(19200);
  Serial.begin(9600);
  ss.begin(GPSBaud);
  delay(10000); 
  Serial.println(" logging time completed!");
  randomSeed(analogRead(0));
  pinMode(Switch, INPUT);
  digitalWrite(Switch, HIGH);
  pinMode(Buzzer, OUTPUT);
  digitalWrite(Buzzer, LOW);

  Serial.println(F("DeviceExample.ino"));
  Serial.print(F("Testing TinyGPS++ library v. "));
 

  Serial.println();
}



void sendSMS(String message)
{
  SIM900.print("AT+CMGF=1\r");                     
  delay(100);
  SIM900.println("AT + CMGS = \"+918830584864\"");  
  delay(100);
  SIM900.println(message);                         
  delay(100);
  SIM900.println((char)26);                        
  delay(100);
  SIM900.println();
  delay(5000);                                     

}

void loop()
{
  int reading;
  
  while (ss.available() > 0)
    if (gps.encode(ss.read()))
      displayInfo();

  if (millis() > 5000 && gps.charsProcessed() < 10)
  {
    Serial.println(F("No GPS detected: check wiring."));
    while (true);
  }
  
  if (digitalRead(Switch) == LOW)
  {
    displayInfo();
    latitude = gps.location.lat(), 6 ;
    longitude = gps.location.lng(), 6 ;
    long datareal = int(latitude);
    int fahad = ( latitude - datareal) * 100000;
    long datareal2 = int(longitude);
    int fahad2 = (longitude - datareal2 ) * 100000;
    textForSMS.concat(fahad);
    //textForSMS = "Longitude:  ";
    textForSMS.concat(datareal2);
    textForSMS = textForSMS + ".";
    textForSMS.concat(fahad2);
    //textForSMS = textForSMS + " Latitude: ";
    textForSMS.concat(datareal);
    textForSMS = textForSMS + ".";
    sendSMS(textForSMS);
    Serial.println(textForSMS);
    Serial.println("message sent.");
    delay(5000);
  }
  else
    digitalWrite(Switch, HIGH);
  digitalWrite(Buzzer, LOW);
}


void displayInfo()
{
  Serial.print(F("Location: "));
  if (gps.location.isValid())
  {
    Serial.print(gps.location.lat(), 6);
    Serial.print(F(","));
    Serial.print(gps.location.lng(), 6);
    Serial.print(" ");
    Serial.print(F("Speed:"));
    Serial.print(gps.speed.kmph());
  }
  else
  {
    Serial.print(F("INVALID"));
  }

  Serial.print(F("  Date/Time: "));
  if (gps.date.isValid())
  {
    Serial.print(gps.date.month());
    Serial.print(F("/"));
    Serial.print(gps.date.day());
    Serial.print(F("/"));
    Serial.print(gps.date.year());
  }
  else
  {
    Serial.print(F("INVALID"));
  }

  Serial.print(F(" "));
  if (gps.time.isValid())
  {
    if (gps.time.hour() < 10) Serial.print(F("0"));
    Serial.print(gps.time.hour());
    Serial.print(F(":"));
    if (gps.time.minute() < 10) Serial.print(F("0"));
    Serial.print(gps.time.minute());
    Serial.print(F(":"));
    if (gps.time.second() < 10) Serial.print(F("0"));
    Serial.print(gps.time.second());
    Serial.print(F("."));
    if (gps.time.centisecond() < 10) Serial.print(F("0"));
    Serial.print(gps.time.centisecond());
  }
  else
  {
    Serial.print(F("INVALID"));
  }

  Serial.println();
}

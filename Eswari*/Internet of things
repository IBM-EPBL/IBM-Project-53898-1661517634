#include <Keypad.h>                // library for keyboard
#include <Password.h>              // library for password

Password password = Password( "1234" );  // password

const byte rows = 4;                     // four rows       
const byte cols = 4;                     // three columns
char keys[rows][cols] = {                // keys on keypad

{'1','2','3','A'},
{'4','5','6','B'},
{'7','8','9','C'},
{'*','0','#','D'},

};

byte rowPins[rows] = {9,8,7,6};
byte colPins[cols] = {5,4,3,2};
Keypad keypad = Keypad(makeKeymap(keys), rowPins, colPins, rows, cols);



#define sensorz A3      // pin for PIR sensor data
//#define contact 0     // pin for on/off alarm
#define alrm 12      // pin for siren, buzzer
#define redLed A2        //  pin for red led
#define greenLed A0        // pin for green led
#define yellowLed A1    // pin for blue led
int contact = 10;       //used to immediately on/off alarm
int val;
int ledBlink;

int sensorzData;
unsigned long ceas, timpmemorat;

int intarziereactivare = 20;    // To delay for standby to armed
int intarzieredezactivare = 10; // To delay for triggered to alarm activated
int timpurlat = 10;             // Time of alarm is on

// This is the variable for states "0"
char caz = 0;

int sistem = 0;      // system is 0 for off and 1 for on


/*
States for 

  0. - off
  1. - stand-by
  2. - waitting
  3. - countdown
  4. - alarm
  
*/

void setup()
  {
  keypad.addEventListener(keypadEvent); // an object is created for tracking keystrokes
  
  Serial.begin(9600);  //Used for troubleshooting
  pinMode(alrm, OUTPUT);
  pinMode(sensorz, INPUT);
  pinMode(contact, INPUT);
  pinMode(redLed, OUTPUT);
  pinMode(yellowLed, OUTPUT);
  pinMode(greenLed, OUTPUT);
  digitalRead(contact);
  Serial.println("System startup"); //Used for troubleshooting
  Serial.println("Alarm button status:"); //used for troubleshooting
  Serial.println(digitalRead(contact)); //used for troubleshooting
  }

void loop()
  
  {
     
  ceas = millis();    // read the internal clock
  val = digitalRead(contact);
  
keypad.getKey();
 
     
    if (sistem%2 == 0)
    {
    // alarm is off
    digitalWrite(greenLed, LOW);
    digitalWrite(redLed, LOW);
    digitalWrite(yellowLed, HIGH);
    //Serial.println(contact); //Used for troubleshooting

    digitalWrite(alrm, LOW);
    caz = 0;
   // Serial.println("System is OFF !"); // Used for troubleshooting

    }

  else
    {
    // alarm is on
    if(caz == 0) 
     {
     caz = 1;
     timpmemorat = ceas;
     digitalWrite(yellowLed, HIGH);
     }

    if(caz == 1)              // system waiting
      {

      if ((ceas%1000)<500) digitalWrite(greenLed, HIGH);
      else digitalWrite(greenLed, LOW);
 keypad.getKey();
      if(ceas >= timpmemorat + intarziereactivare * 1000) {caz = 2;}
      //Serial.println("System is arming !"); // Used for troubleshooting
      }
      
    if(caz == 2)              // system is armed
      {
      digitalWrite(greenLed, HIGH); 
 keypad.getKey();
   
      sensorzData = digitalRead(sensorz);  
      //Serial.print("sensorzdData = "); //Used for troubleshooting
      //Serial.println(sensorzData); //Used for troubleshooting
   
  //    if(sensorzData > 600) {caz = 3; timpmemorat = ceas;}
     if(sensorzData == HIGH)
       {
       caz = 3;
       timpmemorat = ceas;
       digitalWrite(greenLed, LOW);
       }
      Serial.println("System is armed !"); // Used for Troubleshooting
      }

    if(caz == 3)              // system is triggered and countdown
      {             

      if ((ceas%500)<100) digitalWrite(redLed, HIGH);
      else digitalWrite(redLed, LOW);
 keypad.getKey();
      if(ceas >= timpmemorat + intarzieredezactivare * 10) {caz = 4; timpmemorat = ceas;}
      Serial.println("System is triggered and is countdown !"); //Used for troubleshooting
      }

    if(caz == 4)              // siren (buzzer) is active
      {
      //digitalWrite(alrm, HIGH);
      digitalWrite(redLed, HIGH);
      Serial.println("Siren is active !"); //Used for troubleshooting


// For siren

    //tone( 10, 10000, 100);  // Simple Alarm Tone
    for(double x = 0; x < 0.92; x += 0.01){  // Elegant Alarm Tone
        tone(10, sinh(x+8.294), 10);
        delay(1);
        }   
    
      
 keypad.getKey();      
      if(ceas >= timpmemorat + timpurlat * 1000) {caz = 2; digitalWrite(alrm, LOW); digitalWrite(redLed, LOW);}
      }
    }
  }
  
  //take care of some special events
void keypadEvent(KeypadEvent eKey){
  switch (keypad.getState()){
    case PRESSED:
  Serial.print("Pressed: ");
  Serial.println(eKey);
  switch (eKey){
    case '*': checkPassword(); break;
    case '#': password.reset(); break;
    default: password.append(eKey);
     }
  }
}

  
  void checkPassword(){
  if (password.evaluate()){
    Serial.println("Success"); //Used for troubleshooting
  sistem++;
  password.reset();
    Serial.println("Disarmed");//Add code to run if it works
  }else{
    Serial.println("Wrong"); //Used for troubleshooting
    //add code to run if it did not work
    ledBlink = 0;
    while (ledBlink <= 5){
    digitalWrite(redLed, HIGH);
    delay(100);
    digitalWrite(redLed, LOW);
    delay(100);
    ledBlink++;
    }
    password.reset();
  }
}

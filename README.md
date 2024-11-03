# Embedded - security-door-lock- system -
#include <Keypad.h>

// Constants
const String correctPassword = "1234";  
const int lockPin = 7;                  
const int buzzerPin = 8;            
const int timeout = 5000;              

String enteredPassword = "";        
bool isUnlocked = false;

const byte ROWS = 4;                    
const byte COLS = 4;                    
char keys[ROWS][COLS] =           {               
  {'1', '2', '3', 'A'},
  {'4', '5', '6', 'B'},
  {'7', '8', '9', 'C'},
  {'*', '0', '#', 'D'}
};
byte rowPins[ROWS] = {9,10,11,12};   
byte colPins[COLS] = {5, 4,3, 2};      
Keypad keypad = Keypad(makeKeymap(keys), rowPins, colPins, ROWS, COLS);

void setup()                      {
  pinMode(lockPin, OUTPUT);        
  pinMode(buzzerPin, OUTPUT);       
  digitalWrite(lockPin, LOW);          
  Serial.begin(9600);
}

void loop()                       {
  char key = keypad.getKey();


  if (key) {
    
    tone(buzzerPin, 1000, 100);    
    delay(100);

    
    if (key == '*')                {
      enteredPassword = "";         
      Serial.println("Cleared");   
    } else if (key == '#')        {
      checkPassword();      
    } else                         {
      enteredPassword += key;       
      Serial.print("*");        
    }
  }


  if (isUnlocked && (millis() - unlockTime > timeout)) {
    lockDoor();
  }
}
void checkPassword() {
  if (enteredPassword == correctPassword) {
    Serial.println("\nAccess Granted");
    unlockDoor();
  } else {
    Serial.println("\nAccess Denied");
    tone(buzzerPin, 200, 500);  
    delay(500);
  }
  enteredPassword = "";  // Clear entered password after checking
}


void unlockDoor()                 {
  digitalWrite(lockPin, HIGH);     
  isUnlocked = true;               
  unlockTime = millis();               
  Serial.println("Door Unlocked");
}

void lockDoor()                   {
  digitalWrite(lockPin, LOW);    
  isUnlocked = false;
  Serial.println("Door Locked");
}

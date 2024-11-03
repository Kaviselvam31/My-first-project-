# Embedded - security-door-lock- system -
#include <Keypad.h>

// Constants
const String correctPassword = "1234";  // Set your password here
const int lockPin = 7;                  // Pin to control relay or servo
const int buzzerPin = 8;                // Pin for the buzzer
const int timeout = 5000;               // Timeout after correct password (in ms)

// Variables
String enteredPassword = "";
bool isUnlocked = false;

// Define keypad setup
const byte ROWS = 4;                    // Four rows
const byte COLS = 4;                    // Four columns
char keys[ROWS][COLS] = {               // Keypad layout
  {'1', '2', '3', 'A'},
  {'4', '5', '6', 'B'},
  {'7', '8', '9', 'C'},
  {'*', '0', '#', 'D'}
};
byte rowPins[ROWS] = {9, 10, 11, 12};   // Row pins for keypad
byte colPins[COLS] = {5, 4, 3, 2};      // Column pins for keypad
Keypad keypad = Keypad(makeKeymap(keys), rowPins, colPins, ROWS, COLS);

void setup() {
  pinMode(lockPin, OUTPUT);
  pinMode(buzzerPin, OUTPUT);
  digitalWrite(lockPin, LOW);           // Lock is initially locked
  Serial.begin(9600);
}

void loop() {
  char key = keypad.getKey();

  // Check if a key is pressed
  if (key) {
    // Buzzer feedback
    tone(buzzerPin, 1000, 100);
    delay(100);

    // Handle special keys
    if (key == '*') {
      enteredPassword = "";  // Clear password if * is pressed
      Serial.println("Cleared");
    } else if (key == '#') {
      checkPassword();       // Check password if # is pressed
    } else {
      enteredPassword += key;  // Add key to entered password
      Serial.print("*");        // Print * for hidden input
    }
  }

  // Lock the system after timeout
  if (isUnlocked && (millis() - unlockTime > timeout)) {
    lockDoor();
  }
}

// Function to check the entered password
void checkPassword() {
  if (enteredPassword == correctPassword) {
    Serial.println("\nAccess Granted");
    unlockDoor();
  } else {
    Serial.println("\nAccess Denied");
    tone(buzzerPin, 200, 500);  // Long buzzer for error
    delay(500);
  }
  enteredPassword = "";  // Clear entered password after checking
}

// Function to unlock the door
void unlockDoor() {
  digitalWrite(lockPin, HIGH);   // Activate relay or unlock mechanism
  isUnlocked = true;
  unlockTime = millis();         // Set unlock timestamp
  Serial.println("Door Unlocked");
}

// Function to lock the door
void lockDoor() {
  digitalWrite(lockPin, LOW);    // Deactivate relay or lock mechanism
  isUnlocked = false;
  Serial.println("Door Locked");
}

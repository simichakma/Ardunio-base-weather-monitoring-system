#using Arduino Uno.
#include <LiquidCrystal_I2C.h>
// Define LCD
LiquidCrystal_I2C lcd(0x27, 16, 2);

// Custom degree symbol
byte degree_symbol[8] = {
  0b00111,
  0b00101,
  0b00111,
  0b00000,
  0b00000,
  0b00000,
  0b00000,
  0b00000
};

// Pin definitions
int gate = 11;

// Variables for DHT11 sensor data
volatile unsigned long duration = 0;
unsigned char i[5];
unsigned int j[40];
unsigned char value = 0;
unsigned answer = 0;
int z = 0;

void setup() {
  // Initialize the LCD
  lcd.init();
  lcd.backlight();

  // Print initial labels on the LCD
  lcd.print("Temp = ");
  lcd.setCursor(0, 1);
  lcd.print("Humidity = ");
  
  lcd.createChar(1, degree_symbol);
  lcd.setCursor(9, 0);
  lcd.write(1); // Print the degree symbol
  lcd.print("C");
  lcd.setCursor(13, 1);
  lcd.print("%");
}

void loop() {
  delay(1000);

  // Reset variables before each reading
  memset(i, 0, sizeof(i));
  z = 0;

  // Send start signal to the sensor
  pinMode(gate, OUTPUT);
  digitalWrite(gate, LOW);
  delay(18); // 18ms low pulse to wake up the sensor
  digitalWrite(gate, HIGH);
  delayMicroseconds(40);
  pinMode(gate, INPUT);

  // Wait for sensor response
  duration = pulseIn(gate, LOW, 1000); // Timeout of 1ms
  if (duration <= 84 && duration >= 72) {
    while (z < 40) {
      // Read each bit from the sensor
      duration = pulseIn(gate, HIGH, 1000); // Timeout of 1ms
      
      if (duration <= 26 && duration >= 20) {
        value = 0;
      } else if (duration <= 74 && duration >= 65) {
        value = 1;
      } else {
        continue;
      }

      // Store bit in the correct position
      i[z / 8] |= value << (7 - (z % 8));
      j[z] = value;
      z++;
    }
  }

  // Calculate checksum and validate data
  answer = i[0] + i[1] + i[2] + i[3];
  if (answer == i[4] && answer != 0) {
    // Display temperature and humidity on the LCD
    lcd.setCursor(7, 0);
    lcd.print(i[2]); // Temperature
    lcd.setCursor(11, 1);
    lcd.print(i[0]); // Humidity
  } else {
    lcd.setCursor(0, 0);
    lcd.print("Error       ");
  }

  delay(2000); // Wait before taking the next reading
}

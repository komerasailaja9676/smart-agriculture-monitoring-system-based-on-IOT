# smart-agriculture-monitoring-system-based-on-IOT
#include <DHT.h>

#define DHTPIN 2        // DHT11 data pin
#define DHTTYPE DHT11

DHT dht(DHTPIN, DHTTYPE);

int soilPin = A0;
int waterPin = A1;

void setup() {
  Serial.begin(9600);
  dht.begin();
  Serial.println("Smart Agriculture Monitoring System");
}

void loop() {
  // Read temperature
  float temperature = dht.readTemperature();

  // Read soil moisture
  int soilValue = analogRead(soilPin);

  // Read water level
  int waterValue = analogRead(waterPin);

  // Display values
  Serial.println("----------------------------");
  Serial.print("Temperature: ");
  Serial.print(temperature);
  Serial.println(" °C");

  Serial.print("Soil Moisture Value: ");
  Serial.println(soilValue);

  Serial.print("Water Level Value: ");
  Serial.println(waterValue);

  delay(2000);  // 2 seconds delay
}
#include <DHT.h>
#include<WiFi.h>
#include<FirebaseESP32.h>
int soilPin = 34;
#define DHTPIN 5        // Data pin connected to DHT11
#define DHTTYPE DHT11   // Define DHT11
#define relay1 23
#define relay2 12
#define relay3 13
DHT dht(DHTPIN, DHTTYPE);

#define API_KEY "AIzaSyBcWjPOFTfYvvzYkFRvnmQ-RaqKL8kptGU"
#define DATABASE_URL "https://smart-agriculture-eb32c-default-rtdb.firebaseio.com/"

#define NETWORK "IOT"
#define PASSWORD "123456789"
 
#define USER_EMAIL "example@gmail.com"
#define USER_PASSWORD "123456789"

FirebaseData fbdo;
FirebaseData fbdo1;
FirebaseData fbdo2;
FirebaseData fbdo3;

FirebaseAuth auth;
FirebaseConfig config;
int mode;
int fbrelay;
int fbrelay1 ;
int fbrelay2 ;


void connectToWiFi(){
  Serial.print("connecting to WiFi");
  WiFi.begin(NETWORK,PASSWORD);
  while(WiFi.status()!=WL_CONNECTED){
    
    Serial.println(".");
    delay(100);
  }
  if(WiFi.status()!=WL_CONNECTED){
    Serial.print("failed");
  }
  else{
    Serial.println("connected to WiFi");
    Serial.println(WiFi.localIP());
  }
  
}
void setFirebase(){
  auth.user.email = USER_EMAIL;
  auth.user.password = USER_PASSWORD;
  config.api_key=API_KEY;
  config.database_url= DATABASE_URL;
  Firebase.begin(&config, &auth);
}
void setup() {
  Serial.begin(9600);
  connectToWiFi();
  setFirebase();
  dht.begin();
  pinMode(relay1, OUTPUT);
   pinMode(soilPin, INPUT);
  pinMode(relay2, OUTPUT);
  pinMode(relay3, OUTPUT);
}


void loop() {

if (Firebase.ready()) {
  Firebase.getString(fbdo, "/value/load1");
  Firebase.getString(fbdo1, "/value/load2");
  Firebase.getString(fbdo2, "/value/load3");
  Firebase.getString(fbdo3, "/value/mode");

  int fbrelay  = fbdo.stringData().toInt();
  int fbrelay1 = fbdo1.stringData().toInt();
  int fbrelay2 = fbdo2.stringData().toInt();
  int mode = fbdo3.stringData().toInt();

  Serial.println(fbrelay);
  Serial.println(fbrelay1);
  Serial.println(fbrelay2);
  Serial.println(mode);
}

  delay(1000); // DHT11 needs ~2 seconds between readings

  float humidity = dht.readHumidity();
  float temperature = dht.readTemperature(); // Celsius
  int soil = analogRead(soilPin); 
 Serial.println(soil);
 int soilvalue = map(soil,0,4095,0,100);
//  Serial.print("maped value");
//  Serial.println(soilvalue);

 if(mode == 1){
  if(fbrelay == 1){
    digitalWrite(relay1, HIGH); 
  }else{
    digitalWrite(relay1,LOW);
  }
  if(fbrelay1 == 1){
    digitalWrite(relay1, HIGH); 
  }else{
    digitalWrite(relay1,LOW);
  }
  if(fbrelay2 == 1){
    digitalWrite(relay2, HIGH); 
  }else{
    digitalWrite(relay1,LOW);
  }
 }
  else{
    if(humidity < 50 )
    {
      digitalWrite(relay1, HIGH); 
    }else{
      digitalWrite(relay1,LOW);
      } 
if(temperature<=30 )
{
digitalWrite(relay2, HIGH); 

}else{
  digitalWrite(relay2,LOW);
}
  if(soilvalue > 40 )
{
digitalWrite(relay3, HIGH); 

}else{
  digitalWrite(relay3,LOW);
  
} 

}


  Serial.print("Humidity: ");
  Serial.print(humidity);
  Serial.print(" %\t");

  Serial.print("Temperature: ");
  Serial.print(temperature);
  Serial.println(" °C");


  Firebase.setString(fbdo, F("/smart_agri_stm32/temperature"), String(temperature));
  Firebase.setString(fbdo1, F("/smart_agri_stm32/humidity"), String(humidity));
  Firebase.setString(fbdo2, F("/smart_agri_stm32/humidity"), String(soilvalue));
}

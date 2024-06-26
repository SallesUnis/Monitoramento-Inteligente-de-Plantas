# Monitoramento-Inteligente-de-Plantas

Código utilizado para a criação do nosso projeto de Sistemas Embarcados e IOT, nesse projeto estamos criando um monitoramento inteligente de plantas, utilizando dispositivos embarcados e a Internet das Coisas. Com o uso de sensores para coletar dados ambientais e uma plataforma IoT para análise e visualização, os usuários poderão cuidar melhor de suas plantas, garantindo que elas recebam as condições ideais para crescer saudavelmente.


------

Código

#include <ESP8266WiFi.h>
#include <DHT.h>
#include <ThingSpeak.h>
#include <ESP8266HTTPClient.h>

// Definições dos pinos e sensores
#define DHTPIN D4
#define DHTTYPE DHT22
#define SOIL_MOISTURE_PIN A0
#define LDR_PIN A1

DHT dht(DHTPIN, DHTTYPE);

// Credenciais WiFi e ThingSpeak
const char* ssid = "YOUR_SSID";
const char* password = "YOUR_PASSWORD";
unsigned long channelID = YOUR_CHANNEL_ID;
const char* apiKey = "YOUR_API_KEY";

WiFiClient client;

void setup() {
  Serial.begin(115200);
  dht.begin();
  WiFi.begin(ssid, password);

  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.print("Connecting...");
  }
  Serial.println("Connected to WiFi");
  
  ThingSpeak.begin(client);
}

void loop() {
  float humidity = dht.readHumidity();
  float temperature = dht.readTemperature();
  int soilMoisture = analogRead(SOIL_MOISTURE_PIN);
  int lightLevel = analogRead(LDR_PIN);

  Serial.print("Humidity: ");
  Serial.print(humidity);
  Serial.print(" %\t");
  Serial.print("Temperature: ");
  Serial.print(temperature);
  Serial.print(" *C\t");
  Serial.print("Soil Moisture: ");
  Serial.print(soilMoisture);
  Serial.print("\t");
  Serial.print("Light Level: ");
  Serial.println(lightLevel);

  ThingSpeak.setField(1, temperature);
  ThingSpeak.setField(2, humidity);
  ThingSpeak.setField(3, soilMoisture);
  ThingSpeak.setField(4, lightLevel);

  int x = ThingSpeak.writeFields(channelID, apiKey);

  if(x == 200){
    Serial.println("Channel update successful.");
  }
  else{
    Serial.println("Problem updating channel. HTTP error code " + String(x));
  }

  delay(20000); // Envia dados a cada 20 segundos
}


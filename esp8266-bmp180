#include <ESP8266WiFi.h>
#include <NTPClient.h>
#include <WiFiUdp.h>
#include <Wire.h>
#include <Adafruit_BMP085.h>

Adafruit_BMP085 bmp180;
 
uint32_t sleep_time_s = 60 * 1000000;
 
ADC_MODE(ADC_VCC);                                           //vcc read
 
const char* ssid = "WIFI";                             //Definir o SSID da rede WiFi
const char* password = "SENHA";                     //Definir a senha da rede WiFi
 
WiFiUDP ntpUDP;
 
int16_t utc = -3;                                                         //UTC -3:00 Brazil
NTPClient timeClient(ntpUDP, "a.st1.ntp.br", utc*3600, 60000);
 
String apiKey = "CHAVE";                                      //Colocar a API Key para escrita neste campo
const char* server = "api.thingspeak.com";                            //Ela é fornecida no canal que foi criado na aba API Keys
 
WiFiClient client;
 
void setup()
{
  Serial.begin(115200);                       //Configuração da UART
  delay(10);
  Serial.print("Conectando na rede ");
  Serial.println(ssid);
  WiFi.mode(WIFI_STA);           
  WiFi.begin(ssid, password);                  //Inicia o WiFi
 
 while (WiFi.status() != WL_CONNECTED)        //Loop até conectar no WiFi
  {
    delay(500);
    Serial.print(".");
  }
 
  timeClient.begin();
  timeClient.update();
 
  Serial.println("");                              //Logs na porta serial
  Serial.println("WiFi conectado!");
  Serial.print("Conectado na rede ");
  Serial.println(ssid);
  Serial.print("IP: ");
  Serial.println(WiFi.localIP());
}
 
void envia_dados(void)
{ 
  if (client.connect(server,80))                                //Inicia um client TCP para o envio dos dados
  {
    float vdd = ESP.getVcc() / 1024.0;
    vdd=vdd+0.5;

    String postStr = apiKey;
           postStr +="&field1=";
           postStr += String(bmp180.readPressure());
           postStr +="&field2=";
           postStr += String(bmp180.readTemperature());
           postStr +="&field3=";
           postStr += String(vdd);
           postStr += "\r\n\r\n";
 
     client.print("POST /update HTTP/1.1\n");
     client.print("Host: api.thingspeak.com\n");
     client.print("Connection: close\n");
     client.print("X-THINGSPEAKAPIKEY: "+apiKey+"\n");
     client.print("Content-Type: application/x-www-form-urlencoded\n");
     client.print("Content-Length: ");
     client.print(postStr.length());
     client.print("\n\n");
     client.print(postStr);
 
  }
  client.stop();
}
 
void loop() 
{
  Wire.begin(4, 5);
  Wire.setClock(400000);

  if (!bmp180.begin()) 
  {
      Serial.println("O sensor BMP180 não foi encontrado!");
      while (1) {}  //enquanto o sensor não for encontrado executa este ciclo
  }

  Serial.print("TEMP: ");
  Serial.println(bmp180.readTemperature());
  Serial.print("PRESSAO: ");
  Serial.println(bmp180.readPressure());
  
    envia_dados();
    
    ESP.deepSleep(sleep_time_s);
}

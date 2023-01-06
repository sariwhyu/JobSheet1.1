# SistemEmbedded
### Disusun Oleh
- Sari Wahyuningsih 4.31.20.0.23

# Daftar Laporan JobSheet Sistem Embedded

- [JobSheet 1 - Dasar Pemrograman ESP32 Untuk Pemrosesan Data Input/Output Analog dan Digital](https://github.com/sariwhyu/JobSheet1)
- [JobSheet 1.1 - Jaringan Sensor Nirkabel Menggunakan ESP-NOW](https://github.com/sariwhyu/JobSheet1.1)
- [JobSheet 2 - Protokol Komunikasi dan Sensor](https://github.com/sariwhyu/JobSheet2)
- [JobSheet 3 - Topologi Jaringan Lokal dan WiFi](https://github.com/sariwhyu/JobSheet3)
- [Jobsheet4.1 - Cayenne(MQTT)+Sensor+Button+Website Monitoring](https://github.com/sariwhyu/TugasNo1)
- [Jobsheet4.2 - Adafruit.IO+Sensor+LED+Suara](https://github.com/sariwhyu/TugasNo2)
- [Jobsheet4.3 - ThingSpeak+Sensor](https://github.com/sariwhyu/TugasNo3)
- [Jobsheet4.4 - ESP Now+IOT](https://github.com/sariwhyu/TugasNo4)


# JobSheet 1.1 - Jaringan Sensor Nirkabel Menggunakan ESP-NOW

## Alat & Bahan

- ESP32
- Breadboard 
- Kabel jumper
- Resistor 10K Ohm

## Coding

### ESP-NOW One-Way Point-to-Point Communication

```bash
  #include <esp_now.h>
#include <WiFi.h>
// Ganti dengan Mac Address ESP32 Receiver
uint8_t broadcastAddress[] = {0x94, 0xB5, 0x55, 0x2C, 0x47, 0xB4};
// Struktur pesan sender dan receiver harus sama
typedef struct struct_message {
 char a[32];
 int b;
 float c;
 bool d;
} struct_message;
// Driver untuk struktur pesan
struct_message myData;
esp_now_peer_info_t peerInfo;
// fungsi callback
void OnDataSent(const uint8_t *mac_addr, esp_now_send_status_t status) {
 Serial.print("\r\nStatus Paket Terakhir :\t");
 Serial.println(status == ESP_NOW_SEND_SUCCESS ? "Sukses Terkirim" : "Gagal Terkirim");
}
void setup() {
 // Init Serial Monitor
 Serial.begin(115200);
 // Set ESP32 sebagai station
 WiFi.mode(WIFI_STA);
 // Init ESP-NOW
 if (esp_now_init() != ESP_OK) {
 Serial.println("Gagal menginisialisasi ESP-NOW");
 return;
 }
 // Fungsi akses register cb untuk perintah mengirim data
 esp_now_register_send_cb(OnDataSent);

 // Register peer
 memcpy(peerInfo.peer_addr, broadcastAddress, 6);
 peerInfo.channel = 0;
 peerInfo.encrypt = false;

 // Add peer
 if (esp_now_add_peer(&peerInfo) != ESP_OK){
 Serial.println("Gagal menambahkan peer");
 return;
 }
}
void loop() {
 // Set values to send
 strcpy(myData.a, "INI ADALAH CHAR");
 myData.b = random(1,250);
 myData.c = 1.2;
 myData.d = false;

 // Send message via ESP-NOW
 esp_err_t result = esp_now_send(broadcastAddress, (uint8_t *) &myData,
sizeof(myData));

 if (result == ESP_OK) {
 Serial.println("Data berhasil terkirim");
 }
 else {
 Serial.println("Gagal mengirim data");
 }
 delay(2000);
 }

```

### ESP-NOW One-Way, One-to-Many Communication

```bash
  #include <esp_now.h>
#include <WiFi.h>
// REPLACE WITH YOUR ESP RECEIVER'S MAC ADDRESS
uint8_t broadcastAddress1[] = {0x94, 0xB5, 0x55, 0x2C, 0x47, 0xB4};
uint8_t broadcastAddress2[] = {0x24, 0x6F, 0x28, 0x95, 0xD5, 0x80};
uint8_t broadcastAddress3[] = {0x24, 0x0A, 0xC4, 0xC6, 0x09, 0x94};
uint8_t broadcastAddress4[] = {0x3C, 0x71, 0xBF, 0xF1, 0x4B, 0x08};
typedef struct test_struct {
 int x;
 int y;
} test_struct;
test_struct test;
esp_now_peer_info_t peerInfo;
// callback when data is sent
void OnDataSent(const uint8_t *mac_addr, esp_now_send_status_t status) {
 char macStr[18];
 Serial.print("Packet to: ");
 // Copies the sender mac address to a string
 snprintf(macStr, sizeof(macStr), "%02x:%02x:%02x:%02x:%02x:%02x",
 mac_addr[0], mac_addr[1], mac_addr[2], mac_addr[3], mac_addr[4],
mac_addr[5]);
 Serial.print(macStr);
 Serial.print(" send status:\t");
 Serial.println(status == ESP_NOW_SEND_SUCCESS ? "Delivery Success" : "Delivery Fail");
}
void setup() {
 Serial.begin(115200);
 WiFi.mode(WIFI_STA);
 if (esp_now_init() != ESP_OK) {
 Serial.println("Error initializing ESP-NOW");
 return;
 }

 esp_now_register_send_cb(OnDataSent);

 // register peer
 peerInfo.channel = 0;
 peerInfo.encrypt = false;
 // register first peer
 memcpy(peerInfo.peer_addr, broadcastAddress1, 6);
 if (esp_now_add_peer(&peerInfo) != ESP_OK){
 Serial.println("Failed to add peer");
 return;
 }
 // register second peer
 memcpy(peerInfo.peer_addr, broadcastAddress2, 6);
 if (esp_now_add_peer(&peerInfo) != ESP_OK){
 Serial.println("Failed to add peer");
 return;
 }
 /// register third peer
 memcpy(peerInfo.peer_addr, broadcastAddress3, 6);
 if (esp_now_add_peer(&peerInfo) != ESP_OK){
 Serial.println("Failed to add peer");
 return;
 }
 memcpy(peerInfo.peer_addr, broadcastAddress4, 6);
 if (esp_now_add_peer(&peerInfo) != ESP_OK){
 Serial.println("Failed to add peer");
 return;
 }
}
void loop() {
 test.x = random(0,20);
 test.y = random(0,20);
 esp_err_t result = esp_now_send(0, (uint8_t *) &test, sizeof(test_struct));

 if (result == ESP_OK) {
 Serial.println("Sent with success");
 }
 else {
 Serial.println("Error sending the data");
 }
 delay(2000);
}

```

### ESP-NOW One-Way, Many-to-One Communication

```bash
  #include <esp_now.h>
#include <WiFi.h>
// REPLACE WITH THE RECEIVER'S MAC Address
uint8_t broadcastAddress[] = {0x94, 0xB5, 0x55, 0x2C, 0x47, 0xB4};
// Structure example to send data
// Must match the receiver structure
typedef struct struct_message {
 int id; // must be unique for each sender board
 int x;
 int y;
} struct_message;
// Create a struct_message called myData
struct_message myData;
// Create peer interface
esp_now_peer_info_t peerInfo;
// callback when data is sent
void OnDataSent(const uint8_t *mac_addr, esp_now_send_status_t status) {
 Serial.print("\r\nLast Packet Send Status:\t");
 Serial.println(status == ESP_NOW_SEND_SUCCESS ? "Delivery Success" : "Delivery Fail");
}
void setup() {
 // Init Serial Monitor
 Serial.begin(115200);
 // Set device as a Wi-Fi Station
 WiFi.mode(WIFI_STA);
 // Init ESP-NOW
 if (esp_now_init() != ESP_OK) {
 Serial.println("Error initializing ESP-NOW");
 return;
 }
 // Once ESPNow is successfully Init, we will register for Send CB to
 // get the status of Trasnmitted packet
 esp_now_register_send_cb(OnDataSent);

 // Register peer
 memcpy(peerInfo.peer_addr, broadcastAddress, 6);
 peerInfo.channel = 0;
 peerInfo.encrypt = false;

 // Add peer
 if (esp_now_add_peer(&peerInfo) != ESP_OK){
 Serial.println("Failed to add peer");
 return;
 }
}
void loop() {
 // Set values to send
 myData.id = 1;
 myData.x = random(0,50);
 myData.y = random(0,50);
 // Send message via ESP-NOW
 esp_err_t result = esp_now_send(broadcastAddress, (uint8_t *) &myData,
sizeof(myData));

 if (result == ESP_OK) {
 Serial.println("Sent with success");
 }
 else {
 Serial.println("Error sending the data");
 }
 delay(10000);
}

```

### ESP-NOW Two-Way Communication

```bash
  #include "DHT.h"
#define DHTPIN 4 // Digital pin connected to the DHT sensor
// Uncomment whatever type you're using!
#define DHTTYPE DHT11 // DHT 11
//#define DHTTYPE DHT22 // DHT 22 (AM2302), AM2321
//#define DHTTYPE DHT21 // DHT 21 (AM2301)
DHT dht(DHTPIN, DHTTYPE);
void setup() {
 Serial.begin(9600);
 Serial.println(F("DHT11 Embedded System Test!"));
 dht.begin();
}
void loop() {
 // Wait a few seconds between measurements.
 delay(2000);
 // Reading temperature or humidity takes about 250 milliseconds!
 // Sensor readings may also be up to 2 seconds 'old' (its a very slow sensor)
 float h = dht.readHumidity();
 // Read temperature as Celsius (the default)
 float t = dht.readTemperature();
 // Read temperature as Fahrenheit (isFahrenheit = true)
 float f = dht.readTemperature(true);
 // Check if any reads failed and exit early (to try again).
 if (isnan(h) || isnan(t) || isnan(f)) {
 Serial.println(F("Failed to read from DHT sensor!"));
 return;
 }
 // Compute heat index in Fahrenheit (the default)
 float hif = dht.computeHeatIndex(f, h);
 // Compute heat index in Celsius (isFahreheit = false)
 float hic = dht.computeHeatIndex(t, h, false);
 Serial.print(F("Humidity: "));
 Serial.print(h);
 Serial.print(F("% Temperature: "));
 Serial.print(t);
 Serial.print(F("°C "));
 Serial.print(f);
 Serial.print(F("°F Heat index: "));
 Serial.print(hic);
 Serial.print(F("°C "));
 Serial.print(hif);
 Serial.println(F("°F"));
}

```

## Hasil

https://github.com/sariwhyu/JobSheet1.1/blob/main/Praktikum-2-B-Sender.mp4

https://github.com/sariwhyu/JobSheet1.1/blob/main/Praktikum-2-C-Sender-poin%20a%20success%20semua.mp4

https://github.com/sariwhyu/JobSheet1.1/blob/main/Praktikum-2-D-Sender.mp4

https://github.com/sariwhyu/JobSheet1.1/blob/main/praktikum-2-E-1.mp4

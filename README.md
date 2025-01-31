#include "WiFiProv.h"
#include "WiFi.h"

const char *password = "abcd1234";  // Wi-Fi password
const char *deviceName = "ESP32";   // Device name
bool resetData = true;              // Reset previous data

void eventHandler(arduino_event_t *event) {
  switch (event->event_id) {
    case ARDUINO_EVENT_WIFI_STA_GOT_IP:
      Serial.println("Wi-Fi Connected!");
      break;
    case ARDUINO_EVENT_WIFI_STA_DISCONNECTED:
      Serial.println("Wi-Fi Disconnected. Reconnecting...");
      break;
    case ARDUINO_EVENT_PROV_START:
      Serial.println("Provisioning Started. Provide Wi-Fi credentials.");
      break;
    case ARDUINO_EVENT_PROV_CRED_RECV:
      Serial.print("Wi-Fi SSID: ");
      Serial.println((const char *)event->event_info.prov_cred_recv.ssid);
      WiFi.begin((const char *)event->event_info.prov_cred_recv.ssid, (const char *)event->event_info.prov_cred_recv.password);
      break;
    case ARDUINO_EVENT_PROV_CRED_FAIL:
      Serial.println("Provisioning Failed.");
      break;
    case ARDUINO_EVENT_PROV_CRED_SUCCESS:
      Serial.println("Provisioning Successful.");
      break;
    case ARDUINO_EVENT_PROV_END:
      Serial.println("Provisioning Ended.");
      break;
    default:
      break;
  }
}

void setup() {
  Serial.begin(115200);
  WiFi.onEvent(eventHandler);

  Serial.println("Starting Provisioning...");

  uint8_t deviceUUID[16] = {0xb4, 0xdf, 0x5a, 0x1c, 0x3f, 0x6b, 0xf4, 0xbf, 0xea, 0x4a, 0x82, 0x03, 0x04, 0x90, 0x1a, 0x02};

  WiFiProv.beginProvision(NETWORK_PROV_SCHEME_BLE, NETWORK_PROV_SCHEME_HANDLER_FREE_BLE, NETWORK_PROV_SECURITY_1, password, deviceName, NULL, deviceUUID, resetData);
  WiFiProv.printQR(deviceName, password, "ble");
}

void loop() {
  // No need for additional code in the loop
}

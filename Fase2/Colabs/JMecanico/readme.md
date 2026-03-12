# 🎮 Jogo 3 – Labirinto ⚙️

O desenvolvimento do **Jogo 3**, um projeto eletromecânico interativo foi desenvolvido no 5º Semestre.
Para esta fase do projeto o Jogo será atualizado implementando a comunicação via BLE ou Wi-fi com a Caixa, liberando uma parte do destravamento.
O objetivo principal do labirinto é fazer a bolinha atingir o fim do percurso, liberando uma parte do destravamento da caixa.

Nessa primeira fase de pesquisa encontramos 2 possibilidades a testar de comunicação: BLE ou Wi-Fi.

Em um teste inicial, usando um celular e duas Esp32, uma chamarei de Esp32A e outra de Esp32B.
- O celular deve enviar um sinal para a Esp32A e este deverá enviar um sinal para Esp32B que ao receber deverá acender o led interno da placa.

Usei o seguinte código:

- Para Esp32A - Este código utiliza Bluetooth Serial (mais simples para testes rápidos) e ESP-NOW. Você precisará do MAC Address do ESP32 B.

#include <BluetoothSerial.h>
#include <esp_now.h>
#include <WiFi.h>
BluetoothSerial SerialBT;
// Substituído pelo MAC Address real do seu ESP32 B conforme a imagem
uint8_t broadcastAddress[] = {0x80, 0xF3, 0xDA, 0x63, 0x8B, 0xD0};
void setup() {
Serial.begin(115200);
// Configuração Bluetooth
SerialBT.begin("ESP32_Jogo_Gateway");
// Configuração ESP-NOW
WiFi.mode(WIFI_STA);
if (esp_now_init() != ESP_OK) {
return;
}
esp_now_peer_info_t peerInfo;
memcpy(peerInfo.peer_addr, broadcastAddress, 6);
peerInfo.channel = 0;
peerInfo.encrypt = false;
esp_now_add_peer(&peerInfo);
}
void loop() {
if (SerialBT.available()) {
String command = SerialBT.readStringUntil('\n');
command.trim();
// Lógica simples: se o celular enviar "WIN", o gateway envia "unlock"
if (command == "WIN") {
const char *msg = "unlock";
esp_now_send(broadcastAddress, (uint8_t *) msg, strlen(msg));
Serial.println("Comando unlock enviado para o ESP32 B");
}
}
}

- Para Esp32B -Este código apenas aguarda a mensagem e verifica se o conteúdo é "unlock".

#include <esp_now.h>
#include <WiFi.h>
// Pino do LED interno (GPIO 2 é o padrão para a maioria das placas)
#define LED_PIN 2
// Função de callback executada quando uma mensagem chega via ESP-NOW
void OnDataRecv(const uint8_t * mac, const uint8_t *incomingData, int len) {
// Converte os bytes recebidos em uma String
char bu􀆯er[len + 1];
memcpy(bu􀆯er, incomingData, len);
bu􀆯er[len] = '\0';
String message = String(bu􀆯er);
// Verifica se o código de desbloqueio foi recebido
if (message == "unlock") {
digitalWrite(LED_PIN, HIGH); // Liga o LED
Serial.println("Porta Destravada (LED ON)");
// Opcional: Desligar após 3 segundos (exemplo de pulso)
// delay(3000);
// digitalWrite(LED_PIN, LOW);
}
}
void setup() {
Serial.begin(115200);
// Configura o pino do LED como saída
pinMode(LED_PIN, OUTPUT);
digitalWrite(LED_PIN, LOW); // Garante que comece desligado
// Inicializa o Wi-Fi no modo Station (necessário para ESP-NOW)
WiFi.mode(WIFI_STA);
// Inicializa o protocolo ESP-NOW
if (esp_now_init() != ESP_OK) {
Serial.println("Erro ao inicializar ESP-NOW");
return;
}
// Registra a função que trata os dados recebidos
esp_now_register_recv_cb(OnDataRecv);
}
void loop() {
// O loop fica livre para outras tarefas, pois o ESP-NOW funciona por interrupção
}



Próximos Passos:
- MAC Address: Use um código de "Scanner" no ESP32 B para descobrir o
endereço físico dele antes de carregar o código no Gateway.
- App Celular: Você pode usar aplicativos como o Serial Bluetooth Terminal
(Android) para enviar o texto "WIN" e testar a lógica imediatamente.

### 🗺️ Referências
https://randomnerdtutorials.com/get-change-esp32-esp8266-mac-address-arduino/#esp32-get-mac-address




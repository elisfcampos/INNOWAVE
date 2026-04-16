# 🎮 Jogo 3 – Labirinto ⚙️

O desenvolvimento do **Jogo 3**, um projeto eletromecânico interativo foi desenvolvido no 5º Semestre.
Para esta fase do projeto no 6º Semestre, o Jogo será atualizado implementando a comunicação com a Caixa, liberando uma parte do destravamento.
O objetivo principal do labirinto é fazer a bolinha atingir o fim do percurso, liberando uma parte do destravamento da caixa.
O problema atual está em resolver a integração de todos os jogos com a Caixa do qual é parte da minha responsabilidade neste projeto.


## Situação Atual

De acordo com algumas pesquisas, encontrei uma forma de resolver a nossa situação problema utilizando um sistema de rede local da própria esp32. A esp32 da Caixa será o Servidor e as demais Esp32 serão Clientes quanto ao Jogo que será acessado via Tablet o acesso se fará logando na rede e via IP.

Para o funcionamento deste sistema utilizei os seguintes códigos para testes:

__Código Da Esp32 - CAIXA - Servidor.__

/*
*** Código da CAIXA (LocalHost)
USE AS PORTAS GPIO 18, 19 E 21
*/

#include <WiFi.h>
#include <WebServer.h>

const int ledPinLab = 18;
const int ledPinTet = 19;
const int ledPinMem = 21;


// Configurações da rede Access Point
const char* ssid = "INNOWAVE";
const char* password = "SSID1234";

WebServer server(80);

void setup() {

  Serial.begin(115200);
  
  // Configura ESP32 como Access Point
  WiFi.softAP(ssid, password);
  
  IPAddress IP = WiFi.softAPIP();
  Serial.print("IP do servidor: ");
  Serial.println(IP);
  
  // Configurar rotas
  server.on("/", handleRoot);
  server.on("/ledLab/on", ledPinLabOn); //Jogo Labirinto
  server.on("/ledTet/on", ledPinTetOn); //Jogo Tetris
  server.on("/ledMem/on", ledPinMemOn); //Jogo Memória


  server.begin();
  Serial.println("Servidor HTTP iniciado");

  pinMode(ledPinLab, OUTPUT);
  pinMode(ledPinTet, OUTPUT);
  pinMode(ledPinMem, OUTPUT);

  digitalWrite(ledPinLab, LOW);
  digitalWrite(ledPinTet, LOW);
  digitalWrite(ledPinMem, LOW);
}

void loop() {
  server.handleClient();
}

void handleRoot() {
  String html = "<!DOCTYPE html><html>";
  html += "<head><meta name=\'viewport\' content=\'width=device-width, initial-scale=1\'>";
  html += "<link rel=\'icon\' href=\'data:,\'>";
  html += "<style>html { font-family: Helvetica; display: inline-block; margin: 0px auto; text-align: center;}";
  html += ".button { background-color: #4CAF50; border: none; color: white; padding: 16px 40px;";
  html += "text-decoration: none; font-size: 30px; margin: 2px; cursor: pointer;}";
  html += ".button2 {background-color: #555555;}</style></head>";
  html += "<body><h1>INNOWAVE MED LOCK</h1>";
  html +="<body><h1>*Destrave a Caixa*</h1>";
  html += "<p><a href=\'/ledTet/on\'><button class=\'button\'>CONCLUIR</button></a></p>";
  html += "</body></html>";
  
  server.send(200, "text/html", html);
}

void ledPinLabOn() { //para cada jogo montar uma etapa
  digitalWrite(ledPinLab, HIGH);
}

void ledPinTetOn() {
  digitalWrite(ledPinTet, HIGH);
  // Redireciona o navegador de volta para a raiz "/"
  server.sendHeader("Location", "/");
  server.send(303); 
}

void ledPinMemOn() { //para cada jogo montar uma etapa
  digitalWrite(ledPinMem, HIGH);
}


__Código Da Esp32 - Jogo Memória representado por acionamento de um Botão (Pull-Down) - Client.__

/*
*** Código do Jogo (representado por acionamento do botão)
//Acender o LED DA GPIO 21 DA CAIXA
*/
#include <WiFi.h>
#include <HTTPClient.h>

// Configurações da rede do Servidor INNOWAVE
const char* ssid = "INNOWAVE";
const char* password = "SSID1234";

// IP padrão do ESP32 em modo Access Point
const char* serverName = "http://192.168.4.1";

// Configuração do Pino (GPIO 4 com Pull-down externo)
const int buttonPin = 4;
const int ledFeedback = 2; // LED interno da placa cliente

// Variáveis de controle
int lastButtonState = LOW;
bool ledStatus = false;

void setup() {
  Serial.begin(115200);

  // No Pull-down externo, configuramos apenas como INPUT
  pinMode(buttonPin, INPUT);
  pinMode(ledFeedback, OUTPUT);

  // Conecta na rede Wi-Fi criada pelo Servidor
  WiFi.begin(ssid, password);
  Serial.print("Conectando à rede INNOWAVE");
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("\nConectado ao Servidor!");
}

void loop() {
  int currentButtonState = digitalRead(buttonPin);

  // Detecta o pressionamento (Transição de LOW para HIGH no Pull-down)
  if (currentButtonState == HIGH && lastButtonState == LOW) {
    delay(50); // Debounce para evitar leituras falsas
    
    ledStatus = !ledStatus; // Inverte o estado
    enviarComando(ledStatus);
  }

  lastButtonState = currentButtonState;
}

void enviarComando(bool acender) {
  if (WiFi.status() == WL_CONNECTED) {
    HTTPClient http;
    
    // Define a rota baseada no estado desejado
    String requestPath = acender ? "/led/on" : "/led/off";
    String fullUrl = String(serverName) + requestPath;

    Serial.print("Enviando: ");
    Serial.println(fullUrl);

    http.begin(fullUrl);
    int httpResponseCode = http.GET();

    if (httpResponseCode > 0) {
      Serial.print("Resposta do Servidor: ");
      Serial.println(httpResponseCode);
      // Sincroniza o LED local com o do servidor para confirmação visual
      digitalWrite(ledFeedback, acender ? HIGH : LOW);
    } else {
      Serial.print("Erro na comunicação: ");
      Serial.println(httpResponseCode);
    }
    http.end();
  } else {
    Serial.println("Erro: Wi-Fi desconectado");
  }
}



## Pesquisas anteriores


Na primeira fase de pesquisa encontramos 2 possibilidades a testar de comunicação: BLE ou Wi-Fi.

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


Em um teste inicial, usando um celular e duas Esp32, uma chamarei de Esp32A e outra de Esp32B.

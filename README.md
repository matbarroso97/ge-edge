
# 🌊 AquaSafe — Sistema de Monitoramento de Enchentes

## 📌 Descrição do Problema

As enchentes são um dos desastres naturais mais frequentes e perigosos, especialmente em regiões próximas a rios, córregos e áreas urbanas baixas.
O aumento rápido do nível da água pode causar grandes prejuízos materiais e colocar vidas em risco.
A falta de sistemas locais de monitoramento e alerta agrava ainda mais a situação, impedindo ações preventivas eficazes.

## 🚀 Visão Geral da Solução

Pensando nisso, a **AquaSafe** desenvolveu uma solução prática, acessível e eficiente para monitorar o nível da água em tempo real e emitir alertas visuais e sonoros.

O sistema é baseado em:

- **Arduino Uno** como controlador principal.
- **Sensor Ultrassônico HC-SR04** para medir a distância da superfície da água.
- **Display LCD 16x2** para exibição do volume de água e o percentual de enchimento.
- **Potenciômetro** para ajuste de contraste do LCD.
- **LEDs (Verde, Amarelo, Vermelho)** para sinalização visual de segurança, atenção e perigo.
- **Resistores** para proteger os LEDs, limitando a corrente elétrica.
- **Buzzer** para alerta sonoro em situações críticas.

💡 O sistema foi projetado para ser **autossuficiente** e **independente de internet**, garantindo operação mesmo em cenários de emergência.


> ## 🖼️ Esquema do circuito:
![Circuito Montado](Sensor%20de%20nivel%20de%20Agua.png)


## 📦 Como Reproduzir

O projeto foi desenvolvido e simulado na plataforma Tinkercad.

Você pode acessar a ferramenta em:
🔗 https://www.tinkercad.com

Monte o circuito conforme o esquema:

- Sensor Ultrassônico HC-SR04:
  - VCC no 5V
  - GND no GND
  - TRIG no pino digital 7
  - ECHO no pino digital 6
- Display LCD 16x2 com potenciômetro para ajuste de contraste:
  - Conectado nos pinos digitais 12, 11, 5, 4, 3 e 2
- LEDs:
  - Verde no pino 9
  - Amarelo no pino 10
  - Vermelho no pino A0
  - Cada LED com resistor de 220Ω em série
- Buzzer no pino digital 8

Faça o upload do seguinte código:

### 📄 **Arquivo do Código Completo**

```cpp
// Incluímos a biblioteca LCD
#include <LiquidCrystal.h>

// Configuração dos pinos
LiquidCrystal lcd(12, 11, 5, 4, 3, 2);

#define COLS 16
#define ROWS 2

const int PinTrig = 7;
const int PinEcho = 6;

const float VelSon = 34000.0; // Velocidade do som (cm/s)
const int numLecturas = 3;
const float distancia100 = 0.94; // 100 ml
const float distanciaVazio = 14.11; // recipiente vazio

const int ledVerde = 9;
const int ledAmarelo = 10;
const int ledVermelho = A0;
const int buzzer = 8;

float lecturas[numLecturas];
int lecturaAtual = 0;
float total = 0;
float media = 0;
bool primeiraMedia = false;

void setup() {
  Serial.begin(9600);
  pinMode(PinTrig, OUTPUT);
  pinMode(PinEcho, INPUT);

  pinMode(ledVerde, OUTPUT);
  pinMode(ledAmarelo, OUTPUT);
  pinMode(ledVermelho, OUTPUT);
  pinMode(buzzer, OUTPUT);

  for (int i = 0; i < numLecturas; i++) {
    lecturas[i] = 0;
  }

  lcd.begin(COLS, ROWS);
}

void loop() {
  total = total - lecturas[lecturaAtual];

  iniciarTrigger();
  unsigned long tempo = pulseIn(PinEcho, HIGH);
  float distancia = tempo * 0.000001 * VelSon / 2.0;

  lecturas[lecturaAtual] = distancia;
  total = total + lecturas[lecturaAtual];

  lecturaAtual = lecturaAtual + 1;

  if (lecturaAtual >= numLecturas) {
    primeiraMedia = true;
    lecturaAtual = 0;
  }

  media = total / numLecturas;

  if (primeiraMedia) {
    float distanciaCheio = distanciaVazio - media;
    float quantidadeLiquido = distanciaCheio * 100 / distancia100;
    int percentual = (int)(distanciaCheio * 100 / distanciaVazio);

    lcd.clear();
    lcd.setCursor(0, 0);
    lcd.print(String(quantidadeLiquido) + " ml");

    lcd.setCursor(0, 1);
    lcd.print(String(percentual) + " %");

    if (percentual <= 60) {
      digitalWrite(ledVerde, HIGH);
      digitalWrite(ledAmarelo, LOW);
      digitalWrite(ledVermelho, LOW);
      digitalWrite(buzzer, LOW);
    }
    else if (percentual > 60 && percentual <= 70) {
      digitalWrite(ledVerde, LOW);
      digitalWrite(ledAmarelo, HIGH);
      digitalWrite(ledVermelho, LOW);
      digitalWrite(buzzer, LOW);
    }
    else {
      digitalWrite(ledVerde, LOW);
      digitalWrite(ledAmarelo, LOW);
      digitalWrite(ledVermelho, HIGH);
      digitalWrite(buzzer, HIGH);
    }
  }
  else {
    lcd.setCursor(0, 0);
    lcd.print("Calculando: " + String(lecturaAtual));
  }

  delay(500);
}

void iniciarTrigger() {
  digitalWrite(PinTrig, LOW);
  delayMicroseconds(2);
  digitalWrite(PinTrig, HIGH);
  delayMicroseconds(10);
  digitalWrite(PinTrig, LOW);
}
```

### 📏 **Notas sobre a calibração:**
- **14.11 cm** corresponde ao nível vazio.
- **0.94 cm** corresponde a **100 ml** de água adicionados.

⚠️ *Importante:* Para aplicações práticas (rios, bueiros, etc), é necessária uma nova calibração conforme o ambiente e o sensor usados.

## 🎥 Vídeo Demonstrativo

Assista à demonstração prática completa no vídeo abaixo:


# 👥 Integrantes
- Matheus da Costa Barroso

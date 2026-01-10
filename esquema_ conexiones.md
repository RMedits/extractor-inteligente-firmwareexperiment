# 🔌 Esquema de Conexiones - v6.0C FINAL
PROYECTO: Extractor Inteligente (Delta 12V 2.70A)

---

## ⚠️ ADVERTENCIA DE SEGURIDAD
Este montaje utiliza un ventilador de alta potencia. Es **OBLIGATORIO** el uso del **Diodo 1N5408** y las resistencias en el MOSFET para evitar daños irreversibles en el ESP32. Todos los GND deben estar unidos.

---

## 1. Conexiones de Lógica y Control (ESP32)

| Pin ESP32 | Componente | Función | Notas |
| :--- | :--- | :--- | :--- |
| **GPIO 21** | Bus I2C | SDA | OLED + AHT20/BMP280 |
| **GPIO 22** | Bus I2C | SCL | OLED + AHT20/BMP280 |
| **GPIO 32** | Encoder | TRA (CLK) | Giro del encoder |
| **GPIO 33** | Encoder | TRB (DT) | Giro del encoder |
| **GPIO 27** | Encoder PUSH | OK | Pulsar la rueda |
| **GPIO 25** | Botón CONFIRM | BACK | Botón físico lateral |
| **GPIO 26** | Botón BAK | PAUSE | Botón físico lateral (2s) |
| **GPIO 34** | Sensor MQ135 | AOUT | Analógico (Calidad Aire) |
| **GPIO 19** | MOSFET Gate | PWM | Control velocidad |
| **GPIO 4**  | LED Rojo | Ánodo (+) | Error / Standby |
| **GPIO 15** | LED Verde | Ánodo (+) | Funcionamiento OK |

---

## 2. Circuito de Potencia (12V)

### MOSFET FQP30N06L
- **PIN 1 (Gate):** GPIO 19. *Añadir Pulldown 10kΩ a GND*.
- **PIN 2 (Drain):** Cable NEGATIVO (-) del Ventilador.
- **PIN 3 (Source):** GND Común (Negativo fuente 12V).

### Ventilador (Potencia Directa)
- **Cable ROJO (+):** +12V directo desde la fuente.
- **Cable NEGRO (-):** Drain del MOSFET.
- **Cable PWM azul:** Desconectado (aislar con termoretráctil).

### Protección (Diodo 1N5408)
- **Cátodo (Franja):** Al cable POSITIVO del ventilador.
- **Ánodo:** Al cable NEGATIVO del ventilador (Drain del MOSFET).

---

## 3. Alimentación de Sensores
- **AHT20/BMP280:** VCC a 3.3V, GND a GND.
- **MQ135:** VCC a 5V (Vin), GND a GND.
- **OLED/Encoder:** VCC a 3.3V, GND a GND.

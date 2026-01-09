# 📍 Pinout ESP32 38-PIN (Tipo-C) - v6.0C FINAL
Referencia para placa de 38 pines sobre Shield de expansión.

---

## ⬅️ Lado Izquierdo (Antena Arriba)
| Pin Shield | Función | Dispositivo |
| :--- | :--- | :--- |
| **3V3** | Alimentación | Sensores / OLED |
| **EN** | Reset | - |
| **VP (36)** | - | - |
| **VN (39)** | - | - |
| **G34** | **MQ135 AO** | Calidad Aire |
| **G35** | - | - |
| **G32** | **Encoder TRA** | Phase A |
| **G33** | **Encoder TRB** | Phase B |
| **G25** | **Confirm** | Botón BACK |
| **G26** | **BAK** | Botón PAUSA |
| **G27** | **Encoder PUSH**| Botón OK |
| **G14** | - | - |
| **G12** | - | - |
| **G13** | - | - |
| **GND** | GND | Común |
| **5V** | Vin | Alimentación Shield |

---

## ➡️ Lado Derecho (Antena Arriba)
| Pin Shield | Función | Dispositivo |
| :--- | :--- | :--- |
| **GND** | GND | Común |
| **G23** | - | - |
| **G22** | **I2C SCL** | Bus Sensores/OLED |
| **G1** | TX | Debug |
| **G3** | RX | Debug |
| **G21** | **I2C SDA** | Bus Sensores/OLED |
| **G19** | **Fan PWM** | MOSFET Gate |
| **G18** | - | - |
| **G5** | - | - |
| **G17** | - | - |
| **G16** | - | - |
| **G4** | **LED ROJO** | Error / Standby |
| **G0** | - | - |
| **G2** | - | - |
| **G15** | **LED VERDE**| Funcionamiento OK |
| **G8** | - | - |

---

## 💡 Ventaja de tu Shield (Headers G-V-S)
Tu placa de expansión tiene filas de 3 pines para cada GPIO:
- **G (Black/Blue):** GND
- **V (Red):** VCC (Normalmente 5V o 3.3V según jumper)
- **S (Yellow/White):** Señal (GPIO)

**CONSEJO:** Para el módulo OLED y los sensores, puedes conectar los cables directamente a estos grupos G-V-S para evitar usar una protoboard externa.

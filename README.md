# 🌬️ Extractor de Aire Inteligente v6.6C FINAL

Sistema de extracción automatizado basado en ESP32 para baño o galería, optimizado para ventiladores industriales de alta potencia (Delta 12V 2.70A) con seguridad redundante.

## 🚀 Características Finales
- **Modo Automático:** Control inteligente de velocidad basado en Humedad (AHT20/BMP280), Temperatura (AHT20/BMP280) y Calidad de Aire (MQ135).
- **Modo Manual:** Temporizador programable (30/60/90 min) con selección de potencia.
- **Seguridad Mejorada:**
    - **Watchdog Timer:** Reinicio automático si el sistema se bloquea por 8 segundos.
    - **Sensor Failover:** Si un sensor I2C falla, el sistema intenta usar el otro o entra en modo seguro.
    - **Modo Ciego:** Si la pantalla OLED falla, el sistema sigue funcionando indicando estado por LEDs.
- **Interfaz OLED:** Pantalla de 1.3" (SH1106) con navegación mediante Encoder rotativo y 3 botones físicos.
- **LEDs de Estado:** Verde (OK) y Rojo (Error/Standby) para diagnóstico rápido.

## 🛠️ Hardware Confirmado
- **Microcontrolador:** ESP32-WROOM-32 (38 pines + Shield).
- **Sensores:** Módulo Dual AHT20+BMP280 (I2C) + MQ135 (Analógico).
- **Control:** Módulo OLED Estardyn con Encoder y 2 botones extra.
- **Actuadores:** MOSFET FQP30N06L (PWM de potencia directa).
- **Ventilador:** Delta QFR1212GHE (12V, 2.70A).
- **Protección:** Diodo 1N5408 + resistencia pull-down 10kΩ (Gate → GND).
- **Nota crítica:** El cable PWM azul del ventilador queda desconectado (control por potencia).

## 📌 Pinout Resumido
| Componente | Pin ESP32 | Función |
| :--- | :--- | :--- |
| **I2C** | SDA: 21 / SCL: 22 | Sensores + OLED |
| **Encoder** | TRA: 32 / TRB: 33 / PUSH: 27 | Control Usuario |
| **Botones** | BACK: 25 / PAUSA: 26 | Control Usuario |
| **MQ135** | 34 | Calidad Aire (Analógico) |
| **MOSFET** | 19 | PWM Ventilador |
| **LEDs** | Rojo: 4 / Verde: 15 | Estado Sistema |

## 📚 Documentación clave
- `docs/GUIA_MIGRACION_MOSFET.md` (migración relé → MOSFET)
- `docs/html/cableado_mosfet_remoto.html` (diagrama remoto a 1 metro)
- `docs/html/configuracion_personalizada.html` (layout personalizado)

## 💻 Instalación
1. Clonar este repositorio.
2. Abrir con **PlatformIO**.
3. Compilar y subir al ESP32.
4. Las librerías necesarias se gestionan automáticamente en `platformio.ini`.

## 📜 Licencia
Este proyecto es de código abierto. Siéntete libre de mejorarlo.

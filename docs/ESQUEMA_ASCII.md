# ESQUEMA ELECTRICO ASCII - v6.0C FINAL
PROYECTO: Extractor Inteligente (Delta 12V 2.7A)

================================================================================
1. CONEXIONES DE CONTROL (ESP32 Placa Expansion)
================================================================================

   [ MODULO OLED + ENCODER ]             [ ESP32 WROOM 32 ]
   -----------------------               ------------------
   VCC (3.3V-5V)          <----------->  3.3V
   GND                    <----------->  GND
   OLED_SCL               <----------->  GPIO 22 (SCL)
   OLED_SDA               <----------->  GPIO 21 (SDA)
   ENCODER_TRA (CLK)      <----------->  GPIO 32
   ENCODER_TRB (DT)       <----------->  GPIO 33
   ENCODER_PUSH (OK)      <----------->  GPIO 27
   CONFIRM (BACK)         <----------->  GPIO 25
   BAK (PAUSE)            <----------->  GPIO 26

   [ SENSORES ]
   ------------
   AHT20+BMP280 VCC (3.3V) <----------->  3.3V
   AHT20+BMP280 GND       <----------->  GND
   AHT20+BMP280 SCL       <----------->  GPIO 22 (I2C compartido)
   AHT20+BMP280 SDA       <----------->  GPIO 21 (I2C compartido)

   MQ135 VCC (5V)         <----------->  5V (Vin)
   MQ135 GND              <----------->  GND
   MQ135 AO/AD            <----------->  GPIO 34 (Analog In)
   MQ135 DO               <----------->  NC (No conectado)

   [ LEDS ESTADO ]
   -------------
   ROJO (+)      --[220R]-- GPIO 4  (Standby/Error)
   VERDE (+)     --[220R]-- GPIO 15 (Funcionando)
   GND           ---------- GND Comun

================================================================================
2. CIRCUITO DE POTENCIA (Ventilador Delta 12V)
================================================================================

   [ ESP32 ]          [ MOSFET FQP30N06L ]
   ---------          --------------------
   GPIO 19  --------- Gate (Pin 1)
                        |
                      [10K] (Resistencia Pulldown)
                        |
                       GND
                      [100nF] (Opcional, cable largo)
                        |
                       GND

   [ ESQUEMA DE CARGA (12V) ]
   --------------------------
   FUENTE 12V (+) -------------------------------------> VENTILADOR (+)
                                                       VENTILADOR (-) --+--> MOSFET DRAIN (Pin 2)
                                                                        |
                                                                [DIODO 1N5408] (Flyback)
                                                                        |
   FUENTE 12V (-) ------------------------------------------------------+--> MOSFET SOURCE (Pin 3)
                                                                             |
                                                                            GND (Comun)

================================================================================
3. NOTAS DE MONTAJE CRITICAS
================================================================================
- DIODO 1N5408: Instalar en paralelo al ventilador. La franja (catodo) va al positivo.
- MOSFET: El pinout del FQP30N06L visto de frente es 1:Gate, 2:Drain, 3:Source.
- RESISTENCIAS: La de 10K debe ir lo mas cerca posible del Gate del MOSFET.
- PWM AZUL: El cable PWM azul del ventilador queda desconectado (aislar).
- GND: El GND de la fuente de 12V debe estar unido al GND del ESP32.

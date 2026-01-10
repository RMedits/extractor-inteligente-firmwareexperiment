# 📝 Guía de Montaje Físico y Consideraciones v6.0C

La correcta disposición física de los componentes es fundamental para el buen funcionamiento del sistema, la precisión de los sensores y la seguridad.

## 1. Diseño General y Separación de Circuitos

La regla más importante es **separar la electrónica de potencia (12V) de la electrónica de control y sensores (3.3V/5V)**.

- **Carcasa/Caja**: Se recomienda montar todo dentro de una caja de plástico.
- **Zonas**: Crea una "Zona de Potencia" (MOSFET, diodo, 12V) y una "Zona de Control" (ESP32, Sensores).
- **GND Común**: Es obligatorio que el negativo (-) de la fuente de 12V y el GND del ESP32 estén unidos en un solo punto.
- **Cable largo**: Si el MOSFET queda a ~1m del ESP32, ubícalo cerca del ventilador para acortar el tramo de potencia.

---

## 2. Montaje del MOSFET FQP30N06L (Crítico)

El MOSFET controla la velocidad del ventilador mediante PWM.

- **Ubicación de Resistencias**:
    - **Resistencia 10kΩ (Pulldown)**: DEBE ir soldada o conectada lo más cerca posible de los pines **Gate** y **Source** del MOSFET. Esto evita que el ventilador se encienda solo por ruido estático.
    - **Capacitor 100nF (opcional)**: Entre Gate y Source si el cable de control es largo (anti-ruido).
- **Pinout (Visto de frente, letras hacia ti)**:
    1. **Gate** (Izquierda) -> GPIO 19
    2. **Drain** (Centro/Tab) -> Negativo del Ventilador
    3. **Source** (Derecha) -> GND Común
- **Disipación**: Aunque el FQP30N06L aguanta 30A, con el ventilador Delta de 2.7A se recomienda usar un pequeño disipador de aluminio TO-220 si va a funcionar por periodos largos.

---

## 3. Protección con Diodo 1N5408

El ventilador Delta es un motor potente que genera picos de voltaje al apagarse (fuerza contraelectromotriz).

- **Instalación**: El diodo debe ir en paralelo con el ventilador.
- **Polaridad**:
    - El lado con la **franja blanca (Cátodo)** va al cable **POSITIVO (+12V)** del ventilador.
    - El otro lado (Ánodo) va al cable **NEGATIVO** del ventilador (el que va al Drain del MOSFET).
- **Importancia**: Sin este diodo, el MOSFET se quemará en pocos usos.

---

## 4. Ubicación de los Sensores

- **BME280**: Lejos de fuentes de calor (ESP32, MQ135). Debe estar cerca de una rejilla para medir el aire real.
- **MQ135**: Necesita precalentarse. Colócalo de forma que el calor que genera no afecte al BME280.

---

## 5. Módulo de Pantalla y Controles

El módulo Estardyn facilita el montaje:
1. Realiza un corte rectangular en el frontal de la caja.
2. Fija el módulo con tornillos o adhesivo.
3. Asegúrate de que el **Encoder PUSH** (pulsar rueda) tiene espacio para hacer "clic" correctamente.

---

## 6. Longitud de Cables
- **I2C (OLED/BME280)**: Máximo 25cm. Si el cable es muy largo, la pantalla puede mostrar basura o el sensor fallar.
- **Potencia**: Usa cable de calibre 18 AWG o similar para los 12V del ventilador.
- **Control MOSFET**: Para distancias de ~1m, usa par trenzado o cable apantallado para GPIO19/GND.
- **PWM azul del ventilador**: Debe quedar desconectado y aislado con termoretráctil.

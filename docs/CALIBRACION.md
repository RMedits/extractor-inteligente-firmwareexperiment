# 🛠️ Guía de Calibración de Sensores v6.0C

Para que el modo automático del extractor funcione de manera óptima, es fundamental ajustar los umbrales de los sensores a las condiciones específicas de tu entorno.

## 1. Calibración del Sensor MQ135 (Calidad de Aire)

El sensor MQ135 mide la concentración de varios gases (humo, alcohol, CO2, etc.). No proporciona una lectura directa en PPM, sino un valor analógico (0-4095) que representa la "calidad" del aire. Un valor más alto indica peor calidad del aire.

### Proceso de Calibración:

1.  **Calentamiento Inicial (¡Muy Importante!)**:
    - El sensor MQ135 tiene un elemento calefactor interno que necesita estabilizarse.
    - Al conectar el sistema por primera vez, déjalo encendido **al menos 24 horas** para un curado y estabilización completos.
    - Para ajustes posteriores, un calentamiento de **30 minutos** es suficiente.

2.  **Observar el Valor Base en Aire Limpio**:
    - Con el firmware cargado, abre el Monitor Serial de PlatformIO.
    - Ventila bien la habitación (abre ventanas y puertas) para tener una referencia de "aire limpio".
    - Observa los logs de sensores que se imprimen cada 10 segundos. Fíjate en el valor `Aire: XXXX`.
    - Anota este valor. En un ambiente limpio y ventilado, un valor típico podría ser **entre 250 y 400**.

3.  **Ajustar el Umbral `AIR_QUALITY_THRESHOLD`**:
    - Edita el archivo `src/main.cpp`.
    - Localiza la línea `#define AIR_QUALITY_THRESHOLD   600`.
    - El umbral debe ser significativamente más alto que tu valor base de aire limpio. Una buena regla es que sea **al menos 200-300 puntos por encima** de tu base.
    - **Ejemplo**: Si tu valor base es `350`, un umbral inicial de `600` es adecuado.

4.  **Prueba de Estrés**:
    - Genera vapor cerca del sensor (por ejemplo, después de una ducha o con un hervidor).
    - Deberías ver cómo el valor `Aire` sube rápidamente. Si supera el umbral que definiste, el ventilador se activará.

### Tabla de Referencia (Valores Aproximados)

| Condición del Aire | Valor Analógico Típico |
| ------------------ | ---------------------- |
| Aire muy limpio    | 250 - 350              |
| Aire normal        | 350 - 500              |
| Vapor de ducha     | 600 - 900+             |
| Humo ligero        | 800 - 1200+            |

---

## 2. Ajuste de Umbrales de Humedad (AHT20/BMP280)

Los umbrales de humedad (`HUMIDITY_THRESHOLD_HIGH` y `HUMIDITY_THRESHOLD_LOW`) dependen de tu clima local y del lugar de instalación.

-   `HUMIDITY_THRESHOLD_HIGH` (70% por defecto): Activa el ventilador al 100%. Ideal para el vapor denso de una ducha.
-   `HUMIDITY_THRESHOLD_LOW` (65% por defecto): Activa el ventilador al 70%. Para condiciones de humedad moderada.

**Ajuste Fino**:
-   **Si el ventilador se activa sin motivo aparente**: Vives en una zona húmeda. Considera subir ambos umbrales. Por ejemplo, a `75%` y `70%`.
-   **Si el ventilador no se activa después de una ducha**: Baja los umbrales. Por ejemplo, a `65%` y `60%`.

---

## 3. Ajuste Fino del PWM del Ventilador (Delta 12V)

El firmware v6.6C mapea la velocidad del 1% al 100% a un rango de PWM de **80 a 255**. El valor mínimo de 80 (~31%) es para asegurar que el motor Delta arranque correctamente y no se quede bloqueado.

-   **Si el ventilador no arranca en la velocidad mínima**: Necesitas subir `PWM_MIN_VALUE` en `src/main.cpp`. Auméntalo a `90` o `100`.
-   **Si el ventilador hace ruido eléctrico pero no gira**: Sube el valor mínimo.

Para hacer el cambio, modifica la línea `#define PWM_MIN_VALUE 80` en `src/main.cpp`.

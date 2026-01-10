# 🔧 Guía de Migración: Control de Ventilador con MOSFET

## Estado Actual vs. Estado Final

### ❌ QUITAR (Configuración Relé Actual)

```
ESP32 GPIO23 ──→ Relé KY-019 (Pin IN)
                 Relé COM ──→ +12V Fuente
                 Relé NO ──→ Ventilador (+)
```

**Acción:** Desconectar y retirar el módulo relé KY-019 completamente.

---

### ✅ AÑADIR (Configuración MOSFET)

```
                    ┌─────────────────────┐
                    │  Placa Existente    │
                    │  (CONSERVAR TODO)   │
                    ├─────────────────────┤
+12V Fuente ────────┤→ Cap 470µF (+)      │
                    │  Cap 100nF          │
                    │                     │
                    │  Diodo 1N5408       │
                    │  (Cátodo a +12V)    │
                    └──────┬──────────────┘
                           │
                           ↓
                    Ventilador (+) Rojo
                    
Ventilador (-) Negro ──→ MOSFET FQP30N06L (Drain)
                    
                         MOSFET Source ──→ GND Común
                    
ESP32 GPIO19 (PWM) ──┬──→ MOSFET Gate
                     │
                    [R] 10kΩ (Pull-Down)
                     │
                    GND
```

---

## Componentes Necesarios

### Ya Tienes ✅
- [x] MOSFET FQP30N06L
- [x] Diodo flyback (1N5408)
- [x] Capacitores de filtrado (470µF + 100nF)

### Necesitas Añadir ➕
- [ ] **1× Resistencia 10kΩ** (1/4W, cualquier tolerancia)
  - **Función:** Pull-down del Gate para Fail-Safe
  - **Conexión:** Entre Gate del MOSFET y GND

---

## Paso a Paso de Montaje

### 1️⃣ Preparar el MOSFET FQP30N06L

**Identificación de pines** (mirando el MOSFET de frente con la pestaña metálica hacia ti):

```
     ┌─────────┐
     │  Tab    │ ← Pestaña metálica (Drain internamente)
     │ (Metal) │
     └────┬────┘
          │
    G   D   S
    │   │   │
   Gate │  Source
      Drain
```

- **Pin 1 (Izquierda):** Gate
- **Pin 2 (Centro):** Drain
- **Pin 3 (Derecha):** Source

### 2️⃣ Conexiones Nuevas

**A. Circuito de Potencia:**
```
Ventilador (-) Negro ──→ MOSFET Pin 2 (Drain)
MOSFET Pin 3 (Source) ──→ GND Común (mismo GND de la fuente 12V y ESP32)
```

**B. Circuito de Control:**
```
ESP32 GPIO19 ──→ MOSFET Pin 1 (Gate)
Resistencia 10kΩ:
  - Un extremo ──→ MOSFET Pin 1 (Gate)
  - Otro extremo ──→ GND
```

**C. Placa Existente (NO TOCAR):**
```
Mantener tal cual:
  - Cap 470µF entre +12V y GND
  - Cap 100nF entre +12V y GND
  - Diodo 1N5408 en paralelo con ventilador
    (Cátodo a +12V del ventilador, Ánodo a -)
```

### 3️⃣ Cambios en el Código

**Modificar `src/main.cpp`:**

```cpp
// ANTES (Relé en GPIO23)
#define RELAY_PIN    23
#define FAN_PWM_PIN  14

// DESPUÉS (MOSFET en GPIO19)
// ¡Eliminar RELAY_PIN completamente!
#define FAN_MOSFET_PIN  19  // Ahora controla el MOSFET directamente
```

**Función `controlFan()` actualizada:**

```cpp
void controlFan(int percentage) {
  if (percentage == currentFanSpeed) return;
  
  currentFanSpeed = percentage;
  
  if (percentage <= 0) {
    // MOSFET OFF (Gate a 0V por pull-down)
    ledcWrite(PWM_CHANNEL, 0);
    Serial.println("💨 Ventilador: APAGADO");
  } else {
    // MOSFET ON con PWM variable
    int pwmValue = map(percentage, 1, 100, 80, 255);
    ledcWrite(PWM_CHANNEL, pwmValue);
    Serial.printf("💨 Ventilador: %d%% (PWM: %d)\n", percentage, pwmValue);
  }
}
```

**Eliminar del `setup()`:**

```cpp
// QUITAR estas líneas:
pinMode(RELAY_PIN, OUTPUT);
digitalWrite(RELAY_PIN, LOW);
```

---

## Verificación de Seguridad

### ✅ Checklist Pre-Encendido

- [ ] **MOSFET Gate tiene resistencia 10kΩ a GND**
  - Verificar continuidad con multímetro: Gate-GND debe medir ~10kΩ
  
- [ ] **Diodo flyback correctamente orientado**
  - Cátodo (banda plateada) conectado a +12V del ventilador
  - Ánodo al lado negativo (Drain del MOSFET)
  
- [ ] **GND común establecido**
  - GND de fuente 12V conectado a GND del ESP32
  
- [ ] **Conexiones de potencia robustas**
  - Source del MOSFET a GND con cable de calibre adecuado (AWG 20-22)
  - Drain al ventilador (-) con cable similar

### ✅ Test de Fail-Safe (¡MUY IMPORTANTE!)

**Antes de conectar el ESP32:**

1. Con el ESP32 **desconectado/apagado**
2. Aplicar 12V a la fuente
3. **Resultado esperado:** Ventilador APAGADO
4. Medir voltaje Gate-Source: debe ser ~0V (gracias al pull-down)

Si el ventilador arranca, **¡DETENER!** Hay un error de cableado.

---

## Diagrama de Conexión Final

```
┌─────────────────────────────────────────────────┐
│            FUENTE 12V / 5A                      │
│  (+12V) ────┬──────────────────┬────────────┐   │
│             │                  │            │   │
│  (GND) ─────┼──────────────────┼────┐       │   │
└─────────────┼──────────────────┼────┼───────┼───┘
              │                  │    │       │
              │ ┌────────────────┘    │       │
              │ │ Placa Filtrado      │       │
              │ │ ┌────────────┐      │       │
              │ └─┤ 470µF      │      │       │
              │   │ 100nF      │      │       │
              │   │ 1N5408     │      │       │
              │   └────┬───────┘      │       │
              │        │              │       │
              │        └──────────┐   │       │
              │                   │   │       │
          ┌───▼───────┐       ┌───▼───▼───┐   │
          │ Ventilador│       │  MOSFET   │   │
          │  12V 2.7A │       │ FQP30N06L │   │
          │           │       │           │   │
          │  (+) ─────┘       │  D   G   S│   │
          │  (-)──────────────┤  │   │   ││   │
          │  (PWM)            │  │  ┌┴┐  ││   │
          │  (Tacho)          │  │  │R│  ││   │
          └───────────────────┤  │  │1│  ││   │
                              │  │  │0│  ││   │
                              │  │  │k│  ││   │
                              │  │  └┬┘  ││   │
                              │  │   │   ││   │
                          ────┴──┴───┴───┴┴───┴─── GND Común
                                      │   
                          ┌───────────┘
                          │
                    ┌─────▼─────────────┐
                    │   ESP32 DevKit    │
                    │                   │
                    │  GPIO19 ──────────┤ (Sale al Gate)
                    │  GND ─────────────┤ (GND Común)
                    │                   │
                    └───────────────────┘
```

---

## Ventajas de esta Configuración

1. ✅ **Fail-Safe robusto:** Resistencia pull-down pasiva garantiza OFF
2. ✅ **Sin componentes redundantes:** Un solo elemento de conmutación
3. ✅ **Filtrado óptimo:** Tus capacitores reducen ruido y picos
4. ✅ **Protección contra transitorios:** Diodo flyback adecuado (3A)
5. ✅ **Escalable:** Si cambias al ventilador de 12V nativo, cero cambios de hardware

---

## Notas Importantes

### Sobre el Ventilador de 24V Temporal

- **Funcionará perfectamente** a 12V con el MOSFET
- Velocidad máxima: ~50% de la nominal
- Consumo real: ~2.7A (bien dentro del margen del MOSFET)
- Cuando cambies al de 12V nativo: **cero cambios necesarios**

### Sobre el Cable PWM del Ventilador (azul) - IMPORTANTE

**Configuración actual (INCORRECTA):**
- ❌ GPIO19 → Cable PWM azul del ventilador
- ❌ Relé controlando alimentación

**Configuración nueva (CORRECTA):**
- ✅ **DESCONECTAR cable PWM azul completamente**
- ✅ GPIO19 → Gate del MOSFET (control de potencia PWM)
- ✅ Ventilador usa solo 2 cables: Rojo (+12V) y Negro (a MOSFET)

**Razón del cambio:**
El cable PWM azul es para controladores PWM de fábrica (motherboards, etc). 
Con el MOSFET, controlas la potencia directamente modulando los 12V, 
que es más eficiente y compatible con cualquier ventilador DC.

---

## Solución de Problemas

| Síntoma | Causa Probable | Solución |
|---------|----------------|----------|
| Ventilador arranca sin ESP32 | Falta pull-down o está mal conectado | Verificar R de 10kΩ entre Gate y GND |
| Ventilador no arranca con código | Gate no recibe señal | Verificar conexión GPIO19 → Gate |
| MOSFET se calienta mucho | Conexión pobre en Source/Drain | Revisar conexiones de potencia |
| Velocidad errática | Ruido en Gate | Añadir capacitor 100nF entre Gate-Source |

---

**¿Listo para migrar?** Una vez montado, comparte los resultados del test de Fail-Safe. 🚀

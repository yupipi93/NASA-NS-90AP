---
name: nasa-ns-90ap-fundamentals
description: Conocimientos básicos de electrónica analógica/digital, lectura de esquemas, identificación de componentes pasivos y activos, y uso seguro del multímetro. Cargar como prerrequisito de cualquier sesión de diagnóstico de la NASA NS-90AP cuando el operador no es ingeniero o cuando se vayan a hacer mediciones in-circuit.
when_to_use: Antes de tocar la placa con instrumental. También cuando el agente deba explicar al usuario por qué se mide un punto concreto o qué significa una lectura.
---

# SKILL 1 — Fundamentos de Electrónica y Diagnóstico Base

> **Misión del agente:** Antes de pedirle al operador que mida o desolde nada, asegúrate de que entiende qué está midiendo y por qué. Si el operador es nuevo, recorre primero esta skill paso a paso.

## 1. Componentes pasivos esenciales

| Componente | Símbolo en esquemas | Cómo se mide | Uso típico en NS-90AP |
|-----------|---------------------|--------------|------------------------|
| **Resistencia (R)** | Zigzag o rectángulo, valor en Ω/kΩ/MΩ | Multímetro en Ω, **fuera de circuito** o desconectando un extremo | Pull-ups del slot, divisores de polarización del oscilador y video |
| **Condensador cerámico** | Dos placas, valores pF–nF | Capacímetro o modo continuidad (debe medir abierto en CC) | Desacoplo de Vcc (0.01 µF, 0.1 µF), red del cristal |
| **Condensador electrolítico** | Una placa curva (–) y una recta (+) | Capacímetro; visualmente revisar abultamiento o fugas | Filtro de la fuente: 100 µF / 220 µF / 470 µF |
| **Diodo** | Triángulo apuntando a barra (cátodo) | Modo diodo: 0.5–0.7 V en directa, OL en inversa | Puente rectificador (4 diodos), protección |
| **Transistor BJT (NPN/PNP)** | Flecha hacia fuera = NPN, hacia dentro = PNP | Modo diodo entre pares B-E y B-C | 2SC2021/2SC1740 (osc. de cristal, buffer vídeo), 2SA937 (driver vídeo) |
| **Cristal de cuarzo (X)** | Rectángulo entre dos placas | No medible con multímetro común; sustituir por descarte | X1 ≈ **26.6017 MHz** (PAL) en NS-90AP |
| **Inductor / ferrita** | Bobina | Modo Ω: pocos ohmios; debe haber continuidad | Filtro audio (39 µH), filtros de fuente |

### Lectura rápida de un valor

- Resistencias SMD impresas: **103 = 10·10³ Ω = 10 kΩ**, 472 = 4.7 kΩ, R10 = 0.10 Ω.
- Cerámicos SMD: 104 = 100 nF = 0.1 µF, 103 = 10 nF = 0.01 µF.
- Electrolíticos: tensión y capacidad serigrafiadas; respetar polaridad al sustituir.

## 2. Componentes activos relevantes

- **Reguladores lineales (LM7805):** entran ~7.5–25 V DC, salen 5 V regulados. Pinout TO-220: pin 1 = IN, pin 2 = GND, pin 3 = OUT. La pestaña metálica está conectada a GND.
- **CIs lógicos TTL/CMOS** (74LSxx, 74HCxx): pines de potencia típicos en una DIP-14/16: GND en el inferior izquierdo (pin 7 o 8), Vcc en el superior derecho (pin 14 o 16).
- **Memorias SRAM 2K×8** (TMM2114/2115 equivalentes): 24 pines, líneas de dirección (A0–A10), datos (D0–D7), `/CE`, `/OE`, `/WE`.
- **CPU UA6527P / PPU UA6538:** ambos DIP-40. Vcc en pin 40 (CPU) / pin 40 (PPU), GND en pin 20 (CPU) / pin 20 (PPU). Mismas posiciones que los Ricoh 2A03/2C02.

## 3. Cómo leer un esquema

1. **Identifica las redes de alimentación primero**: localiza GND (rayas horizontales decrecientes), VCC/+5V (flecha hacia arriba). Todo lo demás cuelga de ellas.
2. **Sigue señales por nombre, no por trazado**: en esquemas grandes, las señales se nombran (ej. `CPU_CLK`, `PPU_AD0`) y "saltan" de una hoja a otra. Busca todas las apariciones del nombre.
3. **Bloques funcionales**: el esquema NES-001 está dividido en bloques `Clock Generator`, `CIC`, `Controller Inputs`, `Power/Reset Connector`. La NS-90AP omite el bloque CIC.
4. **Polaridad y sentido**: en transistores NPN la flecha (emisor) sale hacia GND típicamente; los electrolíticos tienen marcado el negativo.
5. **Punto de prueba (TP)**: cuando el esquema marca un nodo con etiqueta, ese punto puede medirse en placa siguiendo la pista.

## 4. Uso seguro del multímetro

### Reglas de oro

1. **Modo y rango antes de tocar puntas**: si esperas 5 V DC, el multímetro debe estar en V= con rango ≥ 20 V.
2. **Negro a GND**: la sonda negra (COM) siempre va al chasis o un punto de masa fiable. Una buena masa es la pestaña del LM7805, la trenza del cable de los RCA o el pin GND del slot.
3. **Una mano en la espalda** cuando midas tensión > 50 V (no aplica aquí, todo es ≤ 12 V DC, pero como hábito).
4. **Nunca midas resistencia/continuidad con la consola encendida**: dañarías el ohmímetro y falsearías la lectura.
5. **Para corriente**, el multímetro va **en serie** rompiendo el camino. Si lo pones en paralelo con el fusible interno, lo quemas.

### Funciones que vas a usar

| Función | Cuándo | Lectura esperada típica |
|---------|--------|-------------------------|
| **V= (DC)** | Verificar fuente, alimentación lógica, niveles digitales estáticos | 5.00 V ±5 % en VCC, 0 V en GND |
| **V~ (AC)** | Sólo si el adaptador es AC; medir secundario antes del puente | ~9 V AC RMS si el adaptador es AC |
| **Continuidad (con pitido)** | Trazas rotas, soldaduras frías, masa común | Pitido (<50 Ω) entre dos puntos del mismo nodo |
| **Resistencia (Ω)** | Comprobar resistencias fuera de circuito o pull-ups con la placa apagada | Según valor del componente |
| **Diodo** | Puentes rectificadores, uniones B-E/B-C de transistores | 0.5–0.7 V en directa |
| **Frecuencímetro** (si lo tiene) | Salida del oscilador de cristal | ~26.6 MHz en X1, ~5.32 MHz en PPU CLK, ~1.66 MHz en CPU CLK (PAL) |

### Errores frecuentes que el agente debe prevenir

- **Sondas en el zócalo de A/medida de corriente y selector en V**: cortocircuita la fuente al conectar.
- **Probar continuidad con la placa alimentada**: lecturas erráticas, posible daño.
- **Tocar dos pines con la misma punta** (sondas con clip ayudan): evita cortos accidentales en SMD.
- **Confundir GND digital con masa del chasis**: en esta consola coinciden, pero no asumirlo siempre.

## 5. Buenas prácticas de manipulación

- **ESD**: usa pulsera antiestática o, mínimo, toca un punto de masa antes de manipular CIs.
- **Soldador a 320–340 °C**, punta limpia y estañada. Estaño con plomo 60/40 si la placa original lo lleva (el sin plomo necesita más temperatura).
- **Calor en patas DIP**: máximo 3 s seguidos por pata; deja enfriar entre pasadas para no levantar pista.
- **Antes de desoldar un CI**, fotografía la orientación (pin 1 marcado por punto o muesca) y anota ubicación.
- **Documenta cada cambio**: anota componente sustituido, valor antiguo/nuevo, voltaje medido antes y después.

## 6. Vocabulario que el agente debe usar con el operador

- "**Punto de prueba**" en lugar de "el sitio ese del chip".
- "**Tensión respecto a masa**" siempre que pidas una medida (las DC son siempre referenciadas a GND salvo que digas lo contrario).
- "**En directa / en inversa**" cuando hables de diodos.
- "**Bypassear**" = puentear / saltarse.
- "**Caliente al tacto**" no es diagnóstico: pedir medida de temperatura o duración hasta que duela quitar el dedo (>60 °C suele requerir 1–2 s).

## 7. Checklist antes de cualquier diagnóstico

```
[ ] Consola desenchufada
[ ] Cartucho fuera del slot
[ ] Multímetro en buen estado (autotest: cortocircuita puntas → debe pitar y leer 0.0 Ω)
[ ] Foto general de la placa antes de tocar nada
[ ] Identificar visualmente: hinchazones, fugas, pistas quemadas, soldaduras frías
[ ] Fijar la placa para que no se mueva durante la medida
```

Sólo cuando estas casillas estén marcadas, el agente puede pasar a la skill de **arquitectura** y luego a la de **troubleshooting**.

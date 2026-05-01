---
name: nasa-ns-90ap-troubleshooting
description: Árbol de diagnóstico paso a paso para fallos comunes de la NASA NS-90AP, con voltajes esperados en puntos clave y orden de comprobación. Cargar cuando el operador ya ha descrito un síntoma concreto (no enciende, pantalla gris, sin audio, gráficos corruptos, mando muerto, recalentamiento).
when_to_use: Cuando hay un síntoma reportado y queremos llegar al componente defectuoso con el mínimo número de medidas.
---

# SKILL 3 — Troubleshooting NASA NS-90AP

> **Cómo usar esta skill (instrucciones para el agente):**
> 1. Pregunta primero el síntoma exacto y observable (LED, pantalla, sonido, mando).
> 2. Identifica la rama del árbol que aplica (sección 2).
> 3. Pide medidas sólo en el orden indicado: nunca hagas medir todo a la vez. Una medida → una decisión → la siguiente medida.
> 4. Al cerrar el caso, registra qué se cambió y qué voltaje quedó después de la reparación.

## 0. Información previa que debe tener el agente

Antes de bajar al árbol, recopila:

- ¿LED de POWER enciende? (sí / no / parpadea)
- ¿Hay imagen? (negra / gris / con interferencias / con gráficos pero corruptos / sí pero sin sonido)
- ¿Se calienta algo de forma anómala en los primeros 30 s? (sí — qué chip / no)
- ¿Cartucho metido y limpio? Probar **mínimo dos cartuchos distintos** antes de declarar fallo de consola.
- Adaptador en uso: tensión y polaridad. Comprobar con multímetro **antes** de conectar a la consola.

## 1. Voltajes de referencia rápidos (consola encendida, cartucho insertado, juego corriendo o pantalla)

Usa esta tabla como "north star". Cualquier desviación >5 % apunta a la rama correspondiente.

| Punto de medida | Esperado | Si está fuera de rango → mira sección |
|-----------------|----------|---------------------------------------|
| Jack DC (pin central) respecto al anillo | +9 a +12 V DC | §2.1 |
| Tras puente rectificador (+ del electrolítico de filtro) | +8 a +11 V DC, rizado <200 mV | §2.1 |
| **LM7805 pin 1 (IN)** | ≥ 7.5 V DC | §2.1 |
| **LM7805 pin 3 (OUT) = +5V** | **5.00 V ±0.25 V** | §2.2 |
| **CPU UA6527P pin 40 (Vcc)** | 5.00 V | §2.2 |
| **CPU UA6527P pin 20 (GND)** | 0.00 V | §2.2 |
| **PPU UA6538 pin 40 (Vcc)** | 5.00 V | §2.2 |
| CPU pin 29 (CLK) | onda cuadrada ~1.66 MHz, ~5 V pp | §2.3 |
| PPU pin 1 (CLK) | onda cuadrada ~5.32 MHz, ~5 V pp | §2.3 |
| CPU pin 31 (Ø2 / M2) | onda cuadrada ~1.66 MHz, mismo periodo que CLK | §2.3 |
| CPU pin 3 (/RESET) | 5 V (alto) en operación; 0 V mientras se pulsa RESET | §2.4 |
| Pin 36 del slot (+5V cartucho) | 5.00 V | §2.5 |
| PPU pin 21 (VOUT, vídeo) | ~1.5–2.0 V DC con señal montada | §2.6 |
| CPU pin 1/2 (AD1/AD2, audio) | 0.5–1.0 V DC con audio activo | §2.7 |

## 2. Árboles de diagnóstico por síntoma

### 2.1 SÍNTOMA: La consola NO ENCIENDE (LED apagado, sin imagen)

```mermaid
flowchart TD
    START([Síntoma:<br/>NO ENCIENDE]):::action
    Q1{1 Adaptador desconectado:<br/>¿9-12V DC<br/>centro positivo?}:::signal
    F1[Sustituir adaptador<br/>por uno DC 9-12V<br/>≥1A centro +]:::fail
    Q2{2 Conectar a consola.<br/>¿9-12V DC en jack DC<br/>de la placa?}:::signal
    F2[Resoldar / sustituir<br/>jack DC]:::fail
    Q3{3 Tras puente rectif.<br/>¿8-11V DC en +<br/>del electrolítico?}:::signal
    F3[Verificar diodos en modo diodo<br/>0.5-0.7V directa / OL inversa.<br/>Sustituir puente: 4× 1N4007<br/>o DB107. Bypass temporal posible<br/>conectando DC al +C1 y -GND]:::fail
    Q4{4 Pin 1 IN del LM7805<br/>¿≥7.5V DC?}:::signal
    F4[Pista rota entre C1 y 7805<br/>o resistencia abierta.<br/>Continuidad con consola off]:::fail
    Q5{5 Pin 3 OUT del LM7805<br/>¿5.00V ±0.25V?}:::signal
    F5A[7805 muy caliente<br/>en menos de 30s?<br/>→ cortocircuito aguas abajo.<br/>Mide R entre +5V y GND apagada<br/>si menor 50Ω hay corto duro.<br/>Aislar electrolíticos y CIs uno a uno]:::fail
    F5B[7805 frío sin salida<br/>→ 7805 muerto. Sustituir]:::fail
    F5C[Salida 5-7V inestable<br/>→ limitación térmica.<br/>Pasta térmica nueva,<br/>medir consumo: normal 200-400mA<br/>si más de 800mA sin cart hay corto]:::fail
    Q6{6 LED apagado<br/>con +5V OK?<br/>¿5V en ánodo del LED?}:::signal
    F6A[Pulsador POWER sucio<br/>o R limitadora abierta]:::fail
    F6B[LED muerto:<br/>sustituir LED rojo 3mm]:::fail
    OK([+5V correcto y LED OK<br/>→ pasa a sección 2.2]):::ok

    START --> Q1
    Q1 -->|NO| F1
    Q1 -->|SI| Q2
    Q2 -->|NO| F2
    Q2 -->|SI| Q3
    Q3 -->|NO| F3
    Q3 -->|SI| Q4
    Q4 -->|NO| F4
    Q4 -->|SI| Q5
    Q5 -->|"0V o muy bajo<br/>+ 7805 caliente"| F5A
    Q5 -->|"0V<br/>+ 7805 frío"| F5B
    Q5 -->|"5-7V inestable"| F5C
    Q5 -->|"5.00V OK"| Q6
    Q6 -->|NO 5V| F6A
    Q6 -->|"5V OK pero<br/>LED apagado"| F6B
    Q6 -->|"LED enciende"| OK

    classDef action  fill:#fff9c4,stroke:#f57f17,stroke-width:2px,color:#000
    classDef signal  fill:#bbdefb,stroke:#0d47a1,stroke-width:2px,color:#000
    classDef fail    fill:#ef9a9a,stroke:#b71c1c,stroke-width:2px,color:#000
    classDef ok      fill:#a5d6a7,stroke:#1b5e20,stroke-width:2px,color:#000
```

### 2.2 SÍNTOMA: LED enciende, pantalla NEGRA o GRIS sin imagen

> "Pantalla gris" en NES significa que el PPU genera el blanking pero la CPU no escribe en sus registros (PPU desactivado). Casi siempre es la CPU o su reloj.

```mermaid
flowchart TD
    START([Síntoma:<br/>LED encendido,<br/>pantalla negra o gris]):::action
    Q1{1 Pin 40 CPU y PPU<br/>¿5.00V ±5%?}:::signal
    F1[Corto interno en CI o pista.<br/>Aislar desoldando parcialmente]:::fail
    Q2{2 CPU pin 29 CLK<br/>¿onda cuadrada<br/>1.66 MHz a 5V pp?}:::clock
    F2[Bloque oscilador caído<br/>→ ir a sección 2.3]:::fail
    Q3{3 CPU pin 31 Ø2/M2<br/>¿1.66 MHz invertido<br/>respecto CLK?}:::clock
    F3[CPU UA6527P muerta.<br/>Sustituir.<br/>Cuidado con clones AliExpress<br/>de mala calidad nesdev.org/t14827]:::fail
    Q4{4 PPU pin 1 CLK<br/>¿5.32 MHz a 5V pp?}:::clock
    F4[U9 74HC04 no propaga<br/>o C45 51pF abierto.<br/>Sustituir 74HC04]:::fail
    Q5{5 /RESET CPU pin 3<br/>y PPU pin 3<br/>¿5V firmes?}:::control
    F5[Pulsador RESET en corto<br/>o C1 0.47µF en corto.<br/>Sustituir]:::fail
    Q6{6 SRAM U1 WRAM<br/>¿líneas A0..A10<br/>con actividad lógica?}:::signal
    F6[CPU no direcciona<br/>→ CPU dañada]:::fail
    Q7{7 Slot 72 pines:<br/>pin 36 = 5V, pin 38 pulsa,<br/>pin 50 PRG ROM CE pulsa?}:::signal
    F7[Limpiar contactos slot<br/>alcohol isopropílico<br/>retensar pines vencidos<br/>o sustituir slot]:::fail
    Q8{8 Presionar cartucho<br/>encendido<br/>¿aparece imagen?}:::action
    F8A[Mal contacto del slot<br/>limpiar/retensar]:::fail
    F8B[PPU UA6538 muerta.<br/>Sustituir]:::fail

    START --> Q1
    Q1 -->|NO| F1
    Q1 -->|SI| Q2
    Q2 -->|NO| F2
    Q2 -->|SI| Q3
    Q3 -->|NO| F3
    Q3 -->|SI| Q4
    Q4 -->|NO| F4
    Q4 -->|SI| Q5
    Q5 -->|NO| F5
    Q5 -->|SI| Q6
    Q6 -->|NO| F6
    Q6 -->|SI| Q7
    Q7 -->|NO| F7
    Q7 -->|SI| Q8
    Q8 -->|SI| F8A
    Q8 -->|NO| F8B

    classDef action  fill:#fff9c4,stroke:#f57f17,stroke-width:2px,color:#000
    classDef signal  fill:#bbdefb,stroke:#0d47a1,stroke-width:2px,color:#000
    classDef clock   fill:#fff59d,stroke:#f57f17,stroke-width:2px,color:#000
    classDef control fill:#c8e6c9,stroke:#1b5e20,stroke-width:2px,color:#000
    classDef fail    fill:#ef9a9a,stroke:#b71c1c,stroke-width:2px,color:#000
    classDef ok      fill:#a5d6a7,stroke:#1b5e20,stroke-width:2px,color:#000
```

### 2.3 SÍNTOMA: Reloj caído (oscilador no oscila)

```mermaid
flowchart TD
    START([Síntoma:<br/>Sin reloj en CPU/PPU]):::action
    Q1{1 Frecuencímetro<br/>en X1 cristal<br/>¿26.6017 MHz?}:::clock
    F1A[Oscilador no arranca<br/>→ pasos 2-4]:::fail
    F1B[Sólo en un extremo<br/>→ buffer Q2 muerto<br/>sustituir 2SC2021]:::fail
    Q2{2 DC en Q3:<br/>Base 1.5-2.5V<br/>Emisor 0.7V bajo Base<br/>Colector 4-5V}:::signal
    F2A[Q3 2SC2021 defectuoso<br/>o R10/R11 fuera de valor.<br/>Sustituir Q3 y verificar R]:::fail
    F2B[DC OK pero sin oscilar<br/>→ cristal X1 muerto.<br/>Sustituir HC-49 26.6017 MHz PAL]:::fail
    Q3{3 TC1 trimmer 30pF<br/>¿continuidad y valor<br/>en capacímetro?}:::signal
    F3[TC1 abierto o roto.<br/>Sustituir trimmer 5-30 pF]:::fail
    Q4{4 C22 18pF, C3 51pF,<br/>C42 15pF<br/>¿algún corto?}:::signal
    F4[Sustituir cap en corto]:::fail
    OK([Reloj operativo<br/>→ volver a sección 2.2]):::ok

    START --> Q1
    Q1 -->|NO en ambos| F1A
    Q1 -->|"sólo en uno"| F1B
    F1A --> Q2
    Q2 -->|"DC mal"| F2A
    Q2 -->|"DC OK"| F2B
    F2A --> Q3
    F2B --> Q3
    Q3 -->|NO| F3
    Q3 -->|SI| Q4
    Q4 -->|"corto detectado"| F4
    Q4 -->|"caps OK"| OK

    classDef action  fill:#fff9c4,stroke:#f57f17,stroke-width:2px,color:#000
    classDef signal  fill:#bbdefb,stroke:#0d47a1,stroke-width:2px,color:#000
    classDef clock   fill:#fff59d,stroke:#f57f17,stroke-width:2px,color:#000
    classDef fail    fill:#ef9a9a,stroke:#b71c1c,stroke-width:2px,color:#000
    classDef ok      fill:#a5d6a7,stroke:#1b5e20,stroke-width:2px,color:#000
```

### 2.4 SÍNTOMA: Botón RESET no funciona o se queda en reset permanente

```mermaid
flowchart TD
    START([Síntoma:<br/>RESET no funciona<br/>o reset permanente]):::action
    Q1{1 Sin pulsar RESET:<br/>CPU pin 3 /RESET<br/>¿5V firmes?}:::control
    F1[Pulsador en corto<br/>o pista a masa.<br/>Sustituir pulsador]:::fail
    Q2{2 Pulsando RESET:<br/>¿pin 3 cae a 0V?}:::control
    F2[Pulsador roto<br/>o pista abierta entre<br/>pulsador y pin 3]:::fail
    Q3{3 Consola se resetea<br/>sola de forma aleatoria?}:::signal
    F3[C1 0.47µF degradado.<br/>Sustituir por cerámico<br/>o tantalio del mismo valor]:::fail
    OK([RESET operativo]):::ok

    START --> Q1
    Q1 -->|"0V"| F1
    Q1 -->|"5V OK"| Q2
    Q2 -->|NO cae| F2
    Q2 -->|"cae a 0V"| Q3
    Q3 -->|SI| F3
    Q3 -->|NO| OK

    classDef action  fill:#fff9c4,stroke:#f57f17,stroke-width:2px,color:#000
    classDef signal  fill:#bbdefb,stroke:#0d47a1,stroke-width:2px,color:#000
    classDef control fill:#c8e6c9,stroke:#1b5e20,stroke-width:2px,color:#000
    classDef fail    fill:#ef9a9a,stroke:#b71c1c,stroke-width:2px,color:#000
    classDef ok      fill:#a5d6a7,stroke:#1b5e20,stroke-width:2px,color:#000
```

### 2.5 SÍNTOMA: Cartucho no se reconoce / fallos aleatorios al menear

> Es el fallo más común en clones NES de 72 pines.

```mermaid
flowchart TD
    START([Síntoma:<br/>cartucho no reconoce<br/>o falla al mover]):::action
    S1[1 Inspección visual:<br/>contactos slot dorados<br/>no negros/grises]:::action
    S2[2 Limpieza:<br/>alcohol isopropílico ≥95%<br/>NO usar limpiador con aceite<br/>NO gomas de borrar]:::action
    Q1{3 Pin 36 slot = 5V<br/>Pin 1/72 = GND<br/>continuidad con masa?}:::signal
    F1[Pista de potencia/masa<br/>rota en el slot.<br/>Resoldar/reparar]:::fail
    Q2{4 Cart insertado:<br/>Pin 38 Ø2 pulsa 1.66 MHz?<br/>Pin 50 /PRG_ROM_CE pulsa?<br/>Pin 14 R/W activo?}:::signal
    F2[Señales no llegan al slot.<br/>Volver a sección 2.2 paso 6]:::fail
    Q3{5 Pines del slot<br/>¿vencidos hacia atrás?}:::action
    F3A[Retensar contactos<br/>con destornillador fino<br/>o sustituir slot completo<br/>repuesto NES 72p estándar]:::fail
    OK([Slot operativo]):::ok

    START --> S1 --> S2 --> Q1
    Q1 -->|NO| F1
    Q1 -->|SI| Q2
    Q2 -->|NO| F2
    Q2 -->|SI| Q3
    Q3 -->|SI| F3A
    Q3 -->|NO| OK

    classDef action  fill:#fff9c4,stroke:#f57f17,stroke-width:2px,color:#000
    classDef signal  fill:#bbdefb,stroke:#0d47a1,stroke-width:2px,color:#000
    classDef fail    fill:#ef9a9a,stroke:#b71c1c,stroke-width:2px,color:#000
    classDef ok      fill:#a5d6a7,stroke:#1b5e20,stroke-width:2px,color:#000
```

### 2.6 SÍNTOMA: Imagen con interferencias / colores raros / gráficos corruptos

```mermaid
flowchart TD
    START([Síntoma:<br/>imagen anómala]):::action
    Q0{Tipo de anomalía}:::signal
    A1[Gráficos rotos<br/>pero consola arranca]:::action
    A2[Franjas/ondas<br/>pero contenido OK]:::action
    A3[Imagen B/N<br/>sin color]:::action
    A4[Scroll roto<br/>pantalla salta]:::action

    Q1{Probar otro cartucho<br/>¿mismo fallo?}:::action
    F1[PPU UA6538 dañada<br/>nesdev.org/t23916.<br/>Sustituir]:::fail

    Q2{Modo AC sobre +5V<br/>¿rizado >100 mV pp?}:::video
    F2A[Electrolítico filtro seco.<br/>Sustituir 220-470µF]:::fail
    Q3{Q1 2SA937<br/>Base ≈ 2V Emisor ≈ 1.3V<br/>¿correcto?}:::video
    F3[2SA937 dañado.<br/>Sustituir]:::fail

    Q4{Frecuencímetro X1<br/>¿26.6017 MHz exacto?}:::clock
    F4A[TC1 desajustado:<br/>tocar 1/8 vuelta máx<br/>sólo último recurso]:::fail
    F4B[Cristal X1 desviado:<br/>sustituir HC-49 26.6017 MHz]:::fail

    F5[Línea NMI rota<br/>PPU pin → CPU pin 33.<br/>Continuidad y resoldar]:::fail
    OK([Imagen restaurada]):::ok

    START --> Q0
    Q0 --> A1
    Q0 --> A2
    Q0 --> A3
    Q0 --> A4
    A1 --> Q1
    Q1 -->|SI todos juegos| F1
    Q1 -->|NO depende| OK
    A2 --> Q2
    Q2 -->|SI| F2A
    Q2 -->|NO| Q3
    Q3 -->|NO| F3
    Q3 -->|SI| OK
    A3 --> Q4
    Q4 -->|"sólo desviado<br/>poco"| F4A
    Q4 -->|"muy desviado"| F4B
    A4 --> F5

    classDef action  fill:#fff9c4,stroke:#f57f17,stroke-width:2px,color:#000
    classDef signal  fill:#bbdefb,stroke:#0d47a1,stroke-width:2px,color:#000
    classDef clock   fill:#fff59d,stroke:#f57f17,stroke-width:2px,color:#000
    classDef video   fill:#b2ebf2,stroke:#006064,stroke-width:2px,color:#000
    classDef fail    fill:#ef9a9a,stroke:#b71c1c,stroke-width:2px,color:#000
    classDef ok      fill:#a5d6a7,stroke:#1b5e20,stroke-width:2px,color:#000
```

### 2.7 SÍNTOMA: Imagen correcta, pero SIN SONIDO

```mermaid
flowchart TD
    START([Síntoma:<br/>imagen OK,<br/>sin sonido]):::action
    Q1{1 Insertar y sacar jack 3.5mm<br/>varias veces<br/>¿sale sonido a medio camino?}:::audio
    F1[Switch del jack vencido.<br/>Sustituir jack o<br/>puentear pastillas]:::fail
    Q2{2 CPU pines 1 AD1 y 2 AD2<br/>¿0.5-1.0V DC con audio?}:::audio
    F2[CPU/APU dañada.<br/>Confirmar con sonda<br/>en bus de datos]:::fail
    Q3{3 Continuidad de la cadena:<br/>R6 47k pull-up<br/>R7 20k, R8 12k, R9 12k<br/>C20/C21 220pF<br/>C23 1µF acoplo<br/>FC1 39µH LPF}:::audio
    F3A[C23 abierto<br/>→ sustituir 1µF]:::fail
    F3B[FC1 inductor abierto<br/>→ sustituir 39µH 0.5W]:::fail
    F3C[Resistencia abierta<br/>→ sustituir]:::fail
    Q4{4 RCA AUDIO con tono:<br/>AC 0.3-1V pp?<br/>DC 0V?}:::audio
    F4[C23 fugando DC<br/>→ sustituir]:::fail
    OK([Audio restaurado]):::ok

    START --> Q1
    Q1 -->|SI| F1
    Q1 -->|NO| Q2
    Q2 -->|"0V fijo"| F2
    Q2 -->|"OK"| Q3
    Q3 -->|"C23 abierto"| F3A
    Q3 -->|"FC1 abierto"| F3B
    Q3 -->|"R abierta"| F3C
    Q3 -->|"todo OK"| Q4
    Q4 -->|"DC presente"| F4
    Q4 -->|"AC OK / DC 0"| OK

    classDef action  fill:#fff9c4,stroke:#f57f17,stroke-width:2px,color:#000
    classDef audio   fill:#e1bee7,stroke:#4a148c,stroke-width:2px,color:#000
    classDef fail    fill:#ef9a9a,stroke:#b71c1c,stroke-width:2px,color:#000
    classDef ok      fill:#a5d6a7,stroke:#1b5e20,stroke-width:2px,color:#000
```

### 2.8 SÍNTOMA: Mando 1 o 2 no responde

```mermaid
flowchart TD
    START([Síntoma:<br/>Mando no responde]):::action
    Q0{1 Cambiar mando<br/>al otro puerto<br/>¿funciona?}:::action
    PATH_M[Fallo del MANDO]:::fail
    PATH_P[Fallo del PUERTO]:::fail

    Q1{Mando: cable y gomas}:::action
    F1A[Cable interno roto.<br/>Continuidad hilo a hilo,<br/>resoldar/sustituir]:::fail
    F1B[Gomas conductoras<br/>degradadas o duras.<br/>Limpiar con alcohol o<br/>sustituir por nuevas]:::fail
    F1C[Pulsador no cierra a GND.<br/>Comprobar continuidad<br/>al pulsar]:::fail

    Q2{Puerto:<br/>pin 7 = 5V?<br/>pin 1 = GND?}:::signal
    F2[Pista de potencia/masa rota<br/>en el puerto. Resoldar]:::fail
    Q3{Pin 3 OUT0/strobe<br/>y pin 4 clock<br/>¿pulsos al leer $4016/$4017?}:::signal
    F3[CPU UA6527P pin 39 OUT0 muerto.<br/>Sustituir CPU]:::fail
    Q4{Pin 5 datos:<br/>alto en reposo<br/>baja al pulsar?}:::signal
    F4[Buffer 74LS368 U7 o U8 defectuoso.<br/>Sustituir]:::fail
    OK([Mando + puerto OK]):::ok

    START --> Q0
    Q0 -->|"funciona en otro puerto"| PATH_P
    Q0 -->|"sigue sin funcionar"| PATH_M
    PATH_M --> Q1
    Q1 --> F1A
    Q1 --> F1B
    Q1 --> F1C
    PATH_P --> Q2
    Q2 -->|NO| F2
    Q2 -->|SI| Q3
    Q3 -->|NO| F3
    Q3 -->|SI| Q4
    Q4 -->|NO| F4
    Q4 -->|SI| OK

    classDef action  fill:#fff9c4,stroke:#f57f17,stroke-width:2px,color:#000
    classDef signal  fill:#bbdefb,stroke:#0d47a1,stroke-width:2px,color:#000
    classDef fail    fill:#ef9a9a,stroke:#b71c1c,stroke-width:2px,color:#000
    classDef ok      fill:#a5d6a7,stroke:#1b5e20,stroke-width:2px,color:#000
```

### 2.9 SÍNTOMA: Consola se calienta y se apaga sola tras X minutos

```mermaid
flowchart TD
    START([Síntoma:<br/>se apaga sola<br/>tras X minutos]):::action
    Q0{¿Qué chip<br/>está caliente?}:::signal
    A_REG[LM7805 muy caliente]:::action
    A_LOG[CPU o PPU caliente]:::action

    Q1{Disipador del 7805<br/>bien atornillado<br/>con pasta térmica?}:::vcc
    F1[Reapretar disipador<br/>aplicar pasta térmica nueva]:::fail
    Q2{Consumo en +5V<br/>¿más de 700 mA<br/>al iniciar sin cartucho?}:::signal
    F2[CI tirando corriente extra.<br/>Aislar electrolíticos<br/>y CIs uno a uno]:::fail
    Q3{Tensión entrada del 7805<br/>¿más de 12V DC bajo carga?}:::vin
    F3[Adaptador demasiado alto.<br/>Cambiar a 9V DC<br/>baja P de 4W a 2W]:::fail
    OK([Térmica OK]):::ok

    F4[CPU/PPU UMC degradado<br/>nesdev.org/t14827.<br/>Sustituir UA6527P o UA6538]:::fail

    START --> Q0
    Q0 --> A_REG
    Q0 --> A_LOG
    A_REG --> Q1
    Q1 -->|NO| F1
    Q1 -->|SI| Q2
    Q2 -->|SI| F2
    Q2 -->|NO| Q3
    Q3 -->|SI| F3
    Q3 -->|NO| OK
    A_LOG --> F4

    classDef action  fill:#fff9c4,stroke:#f57f17,stroke-width:2px,color:#000
    classDef signal  fill:#bbdefb,stroke:#0d47a1,stroke-width:2px,color:#000
    classDef vcc     fill:#ffcdd2,stroke:#b71c1c,stroke-width:2px,color:#000
    classDef vin     fill:#ffe0b2,stroke:#e65100,stroke-width:2px,color:#000
    classDef fail    fill:#ef9a9a,stroke:#b71c1c,stroke-width:2px,color:#000
    classDef ok      fill:#a5d6a7,stroke:#1b5e20,stroke-width:2px,color:#000
```

## 3. Anatomía de una sesión de reparación tipo

```mermaid
flowchart TD
    S1([1 Recoger síntoma<br/>ver sección 0]):::action
    S2([2 Inspección visual:<br/>hinchazones, fugas,<br/>pistas verde/azul oxidación,<br/>soldaduras frías]):::action
    S3([3 Medir +5V<br/>siempre la 1ª medida]):::vcc
    Q1{¿+5V OK<br/>5.00V ±5%?}:::signal
    BR1[→ rama §2.1<br/>fallo de alimentación]:::fail
    S4([4 Medir reloj<br/>CPU pin 29 y PPU pin 1]):::clock
    Q2{¿Reloj OK<br/>1.66 / 5.32 MHz?}:::signal
    BR2[→ rama §2.3<br/>oscilador caído]:::fail
    S5([5 Ejecutar rama del árbol<br/>que aplica al síntoma]):::action
    S6([6 Cambiar UN componente<br/>cada vez<br/>+ comprobar tras cada cambio]):::action
    S7([7 Documentar:<br/>foto antes/después,<br/>valor antiguo/nuevo,<br/>voltaje tras reparación]):::ok

    S1 --> S2 --> S3 --> Q1
    Q1 -->|NO| BR1
    Q1 -->|SI| S4 --> Q2
    Q2 -->|NO| BR2
    Q2 -->|SI| S5 --> S6 --> S7

    classDef action  fill:#fff9c4,stroke:#f57f17,stroke-width:2px,color:#000
    classDef signal  fill:#bbdefb,stroke:#0d47a1,stroke-width:2px,color:#000
    classDef vcc     fill:#ffcdd2,stroke:#b71c1c,stroke-width:2px,color:#000
    classDef clock   fill:#fff59d,stroke:#f57f17,stroke-width:2px,color:#000
    classDef fail    fill:#ef9a9a,stroke:#b71c1c,stroke-width:2px,color:#000
    classDef ok      fill:#a5d6a7,stroke:#1b5e20,stroke-width:2px,color:#000
```

## 4. Recambios recomendados a tener en stock

| Pieza | Equivalente | Notas |
|-------|-------------|-------|
| LM7805 | LM7805CT, KA7805, MC7805 (TO-220) | Equivalente directo |
| Puente rectificador | DB107 (1 A) o 4× 1N4007 | Vía sustitución integrada o discreta |
| Electrolítico filtro | 220 µF / 25 V o 470 µF / 16 V | Bajo ESR |
| Cristal X1 | HC-49 **26.6017 MHz** PAL | Crítico, sólo este valor |
| Q2/Q3 | 2SC2021 NPN RF | 2SC1815 sirve como sustituto pobre |
| Q1 | 2SA937 PNP | 2SA1015 como alternativa |
| 74HC04, 74LS373, 74LS139, 74LS368 | DIP standard | Stock TI/Toshiba |
| SRAM 2K×8 | TMM2115AP, HM6116, 6264 (8K) si zócalo lo permite | Pin-compatibles |
| CPU | UA6527P **calidad seleccionada** | Comprar a vendedor con garantía; los AliExpress baratos son lotería |
| PPU | UA6538 **calidad seleccionada** | Mismo aviso |
| Slot 72 pines | Repuesto NES estándar | Reapretar contactos antes de sustituir |

## 5. Cuándo NO seguir reparando

- Si la placa tiene corrosión que ha levantado más de 2–3 vías o pistas en zona crítica (alrededor de CPU/PPU): la reparación es antieconómica para una consola de coleccionista. Documentar y archivar.
- Si los CIs UMC tienen daño de cristal visible (CI partido) o se ha quemado el encapsulado.
- Si el operador no tiene el material o la habilidad para desoldar DIP-40 sin levantar pistas (recomendar estación de aire caliente o profesional).

## 6. Métricas de éxito

Al terminar una reparación, deben cumplirse:

- [ ] +5V estable a 5.00 V ±0.10 V con juego corriendo 30 minutos.
- [ ] LM7805 caliente al tacto pero soportable (<70 °C).
- [ ] Imagen sin franjas, sonido en RCA y headphone, los dos mandos responden.
- [ ] La consola arranca con al menos 3 cartuchos distintos sin tocar.
- [ ] Caja cerrada, tornillos puestos, el switch RF (si existe) no induce ruido.

Si todas las casillas están marcadas, el caso se cierra.

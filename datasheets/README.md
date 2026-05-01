# Datasheets y esquemas de referencia

⚠️ **No existe documentación oficial pública del fabricante** de la NASA NS-90AP: era una clónica taiwanesa sin manual técnico publicado. Los esquemas que encontrarás aquí son los **diseños de referencia** del Famicom/NES original y los **datasheets de los chips equivalentes** que monta esta consola. Para reparar la NS-90AP son funcionalmente válidos porque la arquitectura es prácticamente la misma.

## Contenido

| Archivo | Qué es | Para qué sirve en la NS-90AP |
|---------|--------|------------------------------|
| [`NES-001-schematic.pdf`](./NES-001-schematic.pdf) | Esquema completo de la NES‑001 (consola PAL/NTSC original), redibujado en KiCad por [schenkzoola](https://github.com/schenkzoola/NES) y verificado con multímetro. | Referencia principal de la sección lógica (CPU/PPU/RAM/CIC/etapa AV). El cableado coincide en lo esencial con el de la NS-90AP, salvo por la **ausencia del CIC 10NES** y diferencias menores en la etapa de salida de vídeo. |
| [`HVC-001-Famicom-schematic.pdf`](./HVC-001-Famicom-schematic.pdf) | Esquema oficial del Famicom japonés (HVC-001). | Útil porque la NS-90AP es más cercana al Famicom que a la NES occidental (mismo chipset 6527/6538 sin lockout). |
| [`NESDoc.pdf`](./NESDoc.pdf) | "Nintendo Entertainment System Documentation" (NESDev, v1.0, 2004). | Documento técnico de 60+ páginas con mapas de memoria, comportamiento de CPU/PPU/APU, registros, pinouts y temporización. |
| [`NES-Famicom-cartridge-pinouts.pdf`](./NES-Famicom-cartridge-pinouts.pdf) | Pinouts del conector de cartucho NES (72 pines) y Famicom (60 pines), de Ben Heck. | Imprescindible para diagnosticar fallos del slot de cartucho (líneas de dirección/datos, /CE, /OE, CHR/PRG, etc.). |
| [`LM7805-datasheet-TI.pdf`](./LM7805-datasheet-TI.pdf) | Datasheet de Texas Instruments para la familia LM340/LM7805 (regulador lineal 5 V, 1.5 A). | El regulador de la placa de la NS-90AP es un LM7805. Sirve para comprobar pinout, tensiones de entrada/salida, disipación y modos de protección. |

## Chipset interno de la NS-90AP

| Chip | Función | Equivalencia oficial Nintendo |
|------|---------|-------------------------------|
| **UA6527P** (UMC) | CPU de 8 bits con APU integrada | Ricoh **2A07** (variante PAL del 2A03) |
| **UA6538** (UMC) | PPU (procesador gráfico) PAL | Ricoh **2C07** |

> No hay datasheets públicos oficiales de UMC para los UA6527P / UA6538. La información disponible está repartida en hilos de [forums.nesdev.org](https://forums.nesdev.org) y [forum.6502.org](https://forum.6502.org). Si encuentras alguno fiable, añádelo aquí.
>
> Como referencia eléctrica/funcional pueden usarse los datasheets de los Ricoh 2A07/2C07 (no publicados oficialmente tampoco) o, equivalentemente, los del 2A03/2C02 documentados extensamente en `NESDoc.pdf` y la [NESdev Wiki](https://www.nesdev.org/wiki).

## Diferencias clave NS-90AP vs. NES-001 oficial

A la hora de leer los esquemas anteriores, ten presente que en la NS-90AP:

1. **No hay chip 10NES (CIC)**: omite todo el subcircuito de lockout de la NES occidental.
2. **Conector de cartucho 72 pines sin ZIF**: contactos directos, no socket de fuerza cero.
3. **Salida AV**: jack 3.5 mm (auriculares) + RCA audio (mono) + RCA vídeo compuesto. No hay modulador RF.
4. **Reloj PAL** ≈ 26.6017 MHz (cristal típico para UA6527P/UA6538).
5. **Alimentación** mediante adaptador externo + puente rectificador + LM7805.

## Fuentes

- Esquemas NES/Famicom: <https://github.com/schenkzoola/NES>
- NESDoc: <https://www.nesdev.org/NESDoc.pdf>
- Pinouts de cartucho: <https://www.benheck.com/Downloads/NES_Famicom_Pinouts.pdf>
- LM7805/LM340 (Texas Instruments): <https://www.ti.com/lit/ds/symlink/lm340.pdf>
- Hilo sobre UA6527/UA6528/UA6538: <https://6502.org/forum/viewtopic.php?f=4&t=7356>
- NESdev Wiki — variantes de CPU: <https://www.nesdev.org/wiki/CPU_variants>

# Skills para reparación de la NASA NS-90AP

Carpeta de habilidades (skills) cargables por un agente de IA para asistir en el diagnóstico y reparación de la consola **NASA NS-90AP** (famiclone NES PAL, Taiwan, 1990–1991).

Cada subcarpeta contiene un fichero `SKILL.md` autocontenido con frontmatter YAML compatible con el formato de skills de Claude Code: el `description` indica al runtime cuándo activar la skill.

| Skill | Cuándo se activa |
|-------|------------------|
| [`nasa-ns-90ap-fundamentals/`](./nasa-ns-90ap-fundamentals/SKILL.md) | Conceptos base de electrónica, lectura de esquemas y uso seguro del multímetro (prerrequisito) |
| [`nasa-ns-90ap-architecture/`](./nasa-ns-90ap-architecture/SKILL.md) | Descripción funcional bloque a bloque de la NS-90AP (alimentación, lógica, A/V) |
| [`nasa-ns-90ap-troubleshooting/`](./nasa-ns-90ap-troubleshooting/SKILL.md) | Árboles de diagnóstico con voltajes esperados para fallos comunes |
| [`mermaid-electrical-diagrams/`](./mermaid-electrical-diagrams/SKILL.md) | Convenciones (paleta, formas, plantillas) para producir diagramas Mermaid coherentes en toda la documentación |

## Orden de invocación recomendado

1. Si el operador es novato: cargar primero **fundamentals**.
2. Cargar **architecture** para situar el bloque sospechoso.
3. Bajar a **troubleshooting** y seguir la rama del síntoma reportado.

## Fuentes consolidadas

- Esquemas de referencia: NES-001 y HVC-001 (Famicom) en [`../datasheets/`](../datasheets/).
- Datasheet del LM7805 (Texas Instruments): [`../datasheets/LM7805-datasheet-TI.pdf`](../datasheets/LM7805-datasheet-TI.pdf).
- Pinout cartucho 72/60 pines (Ben Heck): [`../datasheets/NES-Famicom-cartridge-pinouts.pdf`](../datasheets/NES-Famicom-cartridge-pinouts.pdf).
- NESDoc.pdf (NESdev): registros, mapas de memoria, temporización.
- Foros: nesdev.org, 6502.org, elotrolado.net (hilos sobre UA6527P/UA6538).

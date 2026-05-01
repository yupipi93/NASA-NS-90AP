# NASA NS-90AP

Repositorio personal para recopilar documentación, datasheets, información técnica y guías de reparación de mi consola **NASA NS-90AP** (Nº de serie: **047185**), una clónica de la Nintendo Entertainment System (NES) comercializada en España a principios de los años 90.

## Sobre la consola

La **NASA** fue uno de los famiclones (clones de Famicom/NES) más populares en España. Fabricada en Taiwán y distribuida hacia 1990–1991, se anunciaba en la prensa nacional por unas **9.900 pesetas** (~60 €), siendo una alternativa asequible a la NES oficial de Nintendo.

Al carecer del chip antipiratería **10NES** (CIC), aceptaba tanto cartuchos oficiales NES como copias y bootlegs, y era compatible con juegos PAL y NTSC, así como con cartuchos de Famicom japonesa mediante adaptador de 60 a 72 pines.

### Modelos de la familia NASA

| Modelo       | Acabado    |
| ------------ | ---------- |
| **NS-90AP**  | Silver     |
| NS-90APR     | Gold       |
| NS-90P (Top) | Variante   |

Esta unidad concreta es la **NS-90AP Silver**.

## Especificaciones técnicas

- **Tipo:** Famiclone NES (8 bits)
- **CPU:** UA6527P (clon compatible con Ricoh 2A03)
- **PPU:** UA6538 (clon compatible con Ricoh 2C02)
- **Conector de cartucho:** 72 pines (compatible NES, sin ZIF)
- **Sin chip 10NES** → compatible con cartuchos legales y no oficiales
- **Salidas A/V:** Compuesto RCA (vídeo amarillo, audio rojo) + jack 3,5 mm de auriculares
- **Alimentación:** Adaptador externo a través de puente rectificador + regulador lineal **LM7805** (5 V) en placa
- **Mandos:** 2 mandos tipo NES con cable

## Conexiones (panel frontal/lateral)

```
[ HEAD PHONE ]   [ AUDIO ]   [ VIDEO ]
   jack 3,5mm     RCA rojo    RCA amarillo
```

Ver fotos en [`photos/`](./photos/).

## Reparaciones y averías comunes

Notas de fallos típicos documentados en esta familia de consolas:

- **Puente rectificador defectuoso:** La consola no enciende (LED apagado) porque no llega corriente al LM7805. Solución: reemplazar el puente, o bypassearlo y alimentar directamente con un adaptador **DC** respetando polaridad.
- **Regulador LM7805 caliente / sin 5 V:** Verificar entrada (~9 V DC tras rectificado) y consumo en placa.
- **Imagen con interferencias o sin sincronía:** Limpieza del conector de 72 pines y de los contactos del cartucho (clásico fallo NES).
- **Mandos que no responden:** Cable interno roto cerca del conector, o gomas conductoras de los pulsadores degradadas.
- **Sin sonido pero con imagen:** Revisar condensadores de la etapa de audio y el jack de auriculares (puede cortar la salida del altavoz/RCA al insertar conector).

## Estructura del repositorio

```
.
├── README.md
├── photos/         # Fotografías de la consola
└── datasheets/     # Esquemas NES/Famicom y datasheets de los chips
```

A medida que recopile material se irán añadiendo carpetas para `manuals/` y `repairs/`.

## Referencias y fuentes

- [Reparación de consola clónica NES — TheGarage.space](https://thegarage.space/2015/05/13/reparacion-de-consola-clonica-nes/)
- [NASA Computer Video Game — Facultad de CC. de la Información (UCM)](https://ccinformacion.ucm.es/famiclon)
- [NASA NS-90 — Retro Maquinitas](https://retromaquinitas.com/catalogo/consolas/clonicas/nasa-ns-90/)
- [Entertainment Computer System NASA NS-90AP — Centre for Computing History](https://www.computinghistory.org.uk/det/41657/Entertainment-Computer-System-NASA-NS-90AP/)
- [Famiclone — Wikipedia](https://en.wikipedia.org/wiki/Famiclone)
- [Hilo en EOL: Clon de NES (NASA)](https://www.elotrolado.net/hilo_duda-clon-de-nes-nasa_2453321)

## Licencia

Documentación recopilada con fines educativos y de preservación. Las marcas Nintendo, NES y Famicom pertenecen a sus respectivos propietarios.

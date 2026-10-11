# GEO-PIROFUGA · Visor de ruta a zona segura

Visor web estático (Leaflet + mapas base Esri). Calcula en el navegador la ruta de menor costo
desde 2 orígenes hasta una zona segura, penalizando fuego, tráfico y un punto de incendio.

## Estructura

```
/
├── index.html
├── README.md
└── data/
    ├── red_evacuacion_dinamica.geojson
    └── zonas_seguras_todas.geojson
```

Los nombres de los archivos de `data/` deben ser exactos (GitHub distingue mayúsculas).
Si prefieres otros nombres, cambia `CFG.datos` al inicio del script de `index.html`.

## Publicar en GitHub Pages

1. Sube `index.html`, `README.md` y la carpeta `data/` a un repositorio.
2. En el repositorio: **Settings → Pages → Build and deployment**.
3. En *Source* elige **Deploy from a branch**, rama `main`, carpeta `/ (root)`.
4. Espera un minuto: el visor queda en `https://<usuario>.github.io/<repositorio>/`.

## Generar los datos desde R

En el script `geo_pirofuga_impedancia_dinamica.R`, tras las exportaciones, agrega:

```r
exportar_geojson(zonas, "zonas_seguras_todas")
```

Luego copia a `data/`:

- `salidas_geojson/red_evacuacion_dinamica.geojson`
- `salidas_geojson/zonas_seguras_todas.geojson`

### Peso de los archivos

GitHub rechaza archivos de más de 100 MB y recomienda mantenerlos bajo 50 MB. Pages comprime
(gzip) lo que sirve, pero el navegador igual debe parsear todo. Si la red es muy pesada:

- Conserva solo las columnas que usa el visor. Red: `longitud_m`, `Indice_Fuego`,
  `Factor_Congestion`, `Peso_Trafico`. Zonas: `id_zona`, `Aforo_Personas`, `Area_m2`.
- Reduce los decimales (`COORDINATE_PRECISION=5` en `layer_options`). El visor une los tramos por
  sus extremos, así que mantén la misma precisión en todos los tramos.
- Simplifica las zonas con `sf::st_simplify(preserveTopology = TRUE, dTolerance = 5)`.

## Enlace del escenario

El visor guarda en la URL (después del `#`) los puntos A, B, el incendio, la zona elegida y los
radios. El botón **Copiar enlace del escenario** permite compartir un caso exacto.

## Uso local

Con doble clic en `index.html` el navegador bloquea la lectura de `data/`; el visor muestra
entonces un selector para cargar los dos GeoJSON a mano. Para probarlo igual que en GitHub:

```
python -m http.server 8000
```

y abre `http://localhost:8000`.

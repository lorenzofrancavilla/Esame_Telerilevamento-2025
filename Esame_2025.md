# Analisi della Copertura Vegetale nell’Agordino Post-Tempesta Vaia (2018-2024)

##  Obiettivo
Analizzare le variazioni della copertura forestale nell’area dell’Agordino (Veneto) tra il 2018, 2019 e 2024 a seguito della Tempesta Vaia, utilizzando immagini satellitari Sentinel-2 e l'indice NDVI.

##  Dati
- Fonte: Google Earth Engine (GEE)
- Sensore: Sentinel-2
- Periodi osservati:
  - **2018**: luglio–ottobre (pre-Vaia)
  - **2019**: luglio–ottobre (post-Vaia)
  - **2024**: luglio–ottobre (situazione attuale)
- Risoluzione: 10 m
- Bande usate: B2 (Blu), B3 (Verde), B4 (Rosso), B8 (NIR)

## Pacchetti R utilizzati

```r
library(imageRy)
library(terra)
library(viridis)
library(ggplot2)
library(patchwork)

```

Impostazione della working directory e importazione delle imaggini scaricate da google earth engine

importo immagini e do nome (le immagini se plottate sono divise in tre bande(RGB) devo unirle)

```r
setwd("Desktop/Vaia")
vaia18 = rast("Vaia18Bande.tif")
vaia19 = rast("Vaia19Bande.tif")
vaia24 = rast("Vaia24.tif")
```

Visualizzazione RGB, unendo le tre bande RGB con :

```r

plotRGB(vaia18, r = 1, g = 2, b = 3, stretch = "lin", main = "sentinel (median)")
plotRGB(vaia19, r = 1, g = 2, b = 3, stretch = "lin", main = "sentinel (median)")
```
Output delle due immagini con le bande RGB sovrapposte: 






per visualizzare in maniera migliore la vegetazione è possibile convertire l'immagine rgb con il falso colore evidenziando in rosso le parti di vegetazione. questo passaggio è fondamentale per vedere le parti di vegetazione persa dal 2018 al 2019 nella conca Agordina






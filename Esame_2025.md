# Analisi della Copertura Vegetale nell’Agordino Post-Tempesta Vaia (2018-2024)

##  Analisi della tempesta Vaia

Tra il 28 ed il 30 ottobre 2018 ampie zone delle Alpi orientali sono state interessate da venti che hanno superato i 200 km/h ed hanno provocato dei danni gravissimi alle foreste in particolare della Lombardia, del Veneto, del Trentino-Alto Adige e del Friuli Venezia-Giulia. L’evento, chiamato dai meteorologi “tempesta Vaia”, ha provocato secondo le prime stime l’abbattimento di 6-8 milioni di metri cubi di legname ed è sicuramente il più importante disturbo da vento avvenuto recentemente in Italia.

In Italia ci sono aree che storicamente, sia per fattori meteorologici, topografici e stazionali e sia per la struttura dei popolamenti forestali, sono particolarmente sensibili ai danni da vento. Tra queste la conca agordina, insieme a molte aree boschive comprese tra il Veneto e il Trentino sono principalemnte composte da abete rosso. la monocoltura di abete rosso ha causato post-vaia la diffusione del coleottero bostrico tipografo il quale a vivendo e deponendo le uova sotto la corteccia degli abeti rossi fa morire l'albero, diffondendosi in maniera capillare sulle piante a se stanti.

La tempesta Vaia ha danneggiato in maniera massiccia le foreste nella parte nord est dell'arco alpino e in questo caso ha coplito anche l'area dei comuni di Agordo, Alleghe, Caprile, Rocca Pietore e Livinallongo del Col di Lana. 
Nella conca agordina martedi 24 ottobre 2018 si verifica un incendio e in seguito una settimana dopo si verifica la tempesta di vento Vaia, questo susseguirsi di eventi estremi porterà alla devastazione di aree boschive in tutta la conca causando disagi sia all'ecosistema che alle persone che abitano quest'area.

l'obiettivo di questa ricerca sarà di cercare du analizzare analizzare le variazioni della copertura forestale nell’area dell’Agordino tra il 2018, 2019 e 2024 a seguito della Tempesta Vaia, utilizzando immagini satellitari Sentinel-2 e l'indice NDVI.

##  Dati
- Fonte: [Google Earth Engine (GEE)](https://developers.google.com/earth-engine/datasets/catalog/COPERNICUS_S2_SR_HARMONIZED?hl=it) 
- Satellite: Sentinel-2
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

### Impostazione della working directory e importazione delle immagini scaricate da Google Earth Engine

importo immagini dalla cartella "Vaia" che si trova nel desktop e assegno un nome (le immagini se plottate sono divise in tre bande(RGB) devo unirle)

```r
setwd("Desktop/Vaia")
vaia18 = rast("Vaia18Bande.tif")
vaia19 = rast("Vaia19Bande.tif")
vaia24 = rast("Vaia24.tif")
```

Le immagini se plottate sono divise in tre bande (RGB), per vedere l'immagine con colori reali devo unirle.

Unisco le tre bande RGB con :

```r

plotRGB(vaia18, r = 1, g = 2, b = 3, stretch = "lin", main = "sentinel (median)")
plotRGB(vaia19, r = 1, g = 2, b = 3, stretch = "lin", main = "sentinel (median)")
```
Output delle due immagini con le bande RGB sovrapposte: 






per visualizzare in maniera migliore la vegetazione è possibile convertire l'immagine rgb con il falso colore evidenziando in rosso le parti di vegetazione. questo passaggio è fondamentale per vedere le parti di vegetazione persa dal 2018 al 2019 nella conca Agordina






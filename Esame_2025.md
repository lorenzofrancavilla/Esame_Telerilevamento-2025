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


# Immagine Sentinel del 2018 e del 2019
<img width="350" alt="Vaia18RGB" src="https://github.com/user-attachments/assets/08a7d043-d4fc-4164-b48e-394f6ab55b8f" />

<img width="350" alt="Vaia19RGB" src= "https://github.com/user-attachments/assets/6817406d-aaa1-4a98-a8b8-9b069c36088b"/>



## Immagini con falsi colori 

Per visualizzare meglio la vegetazione è possibile convertire l'immagine rgb con il falso colore evidenziando in rosso le parti di vegetazione. questo passaggio è fondamentale per vedere le parti di vegetazione persa dal 2018 al 2019 nella conca Agordina

per il falso falso colore metto la banda NIR sul rosso per vedere la vegetazione nel 2018

```r
im.plotRGB(vaia18, r=4, g=2, b=3) 
im.plotRGB(vaia19, r=4, g=2, b=3) 
```
L'output riulterà così per le due immagini :

2018 <img width="350" alt="Vaia18false" src="https://github.com/user-attachments/assets/941979f5-2f57-403a-b9d2-ada167c034c7" /> 

2019 <img width="350" alt="Vaia18false" src="https://github.com/user-attachments/assets/5db067ea-cf33-413e-9827-79d48726e0dc" /> 

In rosso è indicata la vegetazione che riflette la luce e in grigio/azzurro si notano le aree senza copertura vegetale o con copertura vegetale malata/danneggiata

# Calcolo NDVI (Normalized Difference Vegetation Index)
L'Ndvi è L’NDVI (Normalized Difference Vegetation Index) è un indice spettrale usato in telerilevamento per misurare la salute e densità della vegetazione. Viene calcolato usando le bande NIR (infrarosso vicino) e Red (rosso) di immagini satellitari (come quelle del Sentinel-2).
le paiante che saranno sane rifletteranno bene la luce nel vicino infrarosso(NIR) invece se la pianta è stressata rifletterà di meno e il colore risulterà piu spento. 

questa è la formula dell'NDVI

$$
NDVI = \frac{NIR - RED}{NIR + RED}
$$

NIR (Near Infrared): Banda sensibile alla riflettanza della vegetazione sana.
Red: Banda in cui la vegetazione assorbe luce per la fotosintesi.

Per calcolare l'NDVI su r è necessario applicare questa formula alle bande NIR e RED delle immagini selezionate:

```r
ndvi18 = (vaia18[[4]] - vaia18[[1]]) / (vaia18[[4]] + vaia18[[1]])
plot(ndvi18)

ndvi19 = (vaia19[[4]] - vaia19[[1]]) / (vaia19[[4]] + vaia19[[1]])
plot(ndvi19)

```
confrontiamo le NDVI con la funzione im.multiframe

```r
im.multiframe(1,2)
plot(ndvi18)
plot(ndvi19)
```

L'output sarà questo :

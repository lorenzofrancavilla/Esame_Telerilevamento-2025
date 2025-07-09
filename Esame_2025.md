# Analisi della Copertura Vegetale nell’Agordino Post-Tempesta Vaia (2018-2019)

## Lorenzo Francavilla 
## 9 Luglio 2025





##  Tempesta Vaia

Tra il 28 ed il 30 ottobre 2018 ampie zone delle Alpi orientali sono state interessate da venti che hanno superato i 200 km/h ed hanno provocato dei danni gravissimi alle foreste in particolare della Lombardia, del Veneto, del Trentino-Alto Adige e del Friuli Venezia-Giulia. L’evento, chiamato dai meteorologi “tempesta Vaia”, ha provocato l’abbattimento di 6-8 milioni di metri cubi di legname ed è sicuramente il più importante disturbo da vento avvenuto recentemente in Italia.

In Italia ci sono aree che storicamente, sia per fattori meteorologici, topografici e stazionali e sia per la struttura dei popolamenti forestali, sono particolarmente sensibili ai danni da vento. Tra queste la conca agordina, insieme a molte aree boschive comprese tra il Veneto e il Trentino sono principalemnte composte da abete rosso. La monocoltura di abete rosso ha causato post-vaia la diffusione del coleottero bostrico tipografo il quale  vivendo e deponendo le uova sotto la corteccia degli abeti rossi danneggia e provoca la morte agli alberi, diffondendosi in maniera capillare sulle piante vicine.

La tempesta Vaia ha danneggiato in maniera massiccia le foreste nella parte nord est dell'arco alpino e in questo caso ha coplito anche l'area dei comuni di Agordo, Alleghe, Caprile, Rocca Pietore e Livinallongo del Col di Lana. 
Inoltre nella conca agordina martedi 24 ottobre 2018, una settimana prima della tempesta, si è verificato un incendio causando disagi sia all'ecosistema che alle persone che abitano quest'area.

l'obiettivo di questa ricerca è di cercare di analizzare attraverso il telerilevamento le variazioni della copertura forestale nell’area dell’Agordino tra il 2018 e 2019  a seguito della Tempesta Vaia, utilizzando immagini satellitari Sentinel-2 e l'indice NDVI.

##  Dati
- Fonte: [Google Earth Engine (GEE)](https://developers.google.com/earth-engine/datasets/catalog/COPERNICUS_S2_SR_HARMONIZED?hl=it) 
- Satellite: Sentinel-2
- Periodi osservati:
  - **2018**: luglio–ottobre (pre-Vaia)
  - **2019**: luglio–ottobre (post-Vaia)
- Risoluzione: 10 m
- Bande usate: `[1]`B2 (Blu),`[2]` B3 (Verde),`[3]` B4 (Rosso), `[4]`B8 (NIR).

## Pacchetti R utilizzati

```r
library(imageRy) 
library(terra)
library(viridis)
library(ggplot2)
library(patchwork)

```

### Impostazione della working directory e importazione delle immagini scaricate da Google Earth Engine

importo immagini dalla cartella "Vaia" che si trova nel desktop e assegno un nome (le immagini scaricate da GEE sono divise in tre bande(RGB) devo unirle)

```r
setwd("Desktop/Vaia")
vaia18 = rast("Vaia18Bande.tif")
vaia19 = rast("Vaia19Bande.tif")

```

Le immagini se plottate sono divise in tre bande (RGB), per vedere l'immagine con colori reali devo unirle.

Unisco le tre bande RGB con :

```r

plotRGB(vaia18, r = 1, g = 2, b = 3, stretch = "lin", main = "sentinel (median)")
plotRGB(vaia19, r = 1, g = 2, b = 3, stretch = "lin", main = "sentinel (median)")
```
Output delle due immagini con le bande RGB sovrapposte: 


## Immagine Sentinel del 2018 e del 2019
<img width="350" alt="Vaia18RGB" src="https://github.com/user-attachments/assets/08a7d043-d4fc-4164-b48e-394f6ab55b8f" />

<img width="350" alt="Vaia19RGB" src= "https://github.com/user-attachments/assets/6817406d-aaa1-4a98-a8b8-9b069c36088b"/>



## Immagini con falsi colori 

Per visualizzare meglio la vegetazione è possibile convertire l'immagine rgb con il falso colore evidenziando in rosso le parti di vegetazione. questo passaggio è fondamentale per vedere le parti di vegetazione persa dal 2018 al 2019 nella conca Agordina.
Nello spettro elettromagnetico la banda del vicino infrarosso  (non visibile all'occhio umano) viene assorbita e riflessa dalle piante in maniera chiara perciò è utile per vedere la vegetazione sana.

per il falso falso colore metto la banda NIR sul rosso per vedere la vegetazione nel 2018

```r
im.plotRGB(vaia18, r=4, g=2, b=3) 
im.plotRGB(vaia19, r=4, g=2, b=3) 
```
L'output riulterà così per le due immagini :

<img width="350" alt="Vaia18false" src="https://github.com/user-attachments/assets/941979f5-2f57-403a-b9d2-ada167c034c7" /> 

<img width="350" alt="Vaia18false" src="https://github.com/user-attachments/assets/5db067ea-cf33-413e-9827-79d48726e0dc" /> 

In rosso è indicata la vegetazione che riflette la luce e in grigio/azzurro si notano le aree senza copertura vegetale o con copertura vegetale malata/danneggiata.


# Calcolo NDVI (Normalized Difference Vegetation Index)
L'Ndvi è L’NDVI (Normalized Difference Vegetation Index) è un indice spettrale usato in telerilevamento per misurare la salute e densità della vegetazione. Viene calcolato usando le bande NIR (infrarosso vicino) e Red (rosso) di immagini satellitari (come quelle del Sentinel-2).
le paiante che saranno sane rifletteranno bene la luce nel vicino infrarosso(NIR) invece se la pianta è stressata rifletterà di meno e il colore risulterà piu spento. 
l'NDVI ha dei valori che variano da 0 a 1, solitamente i valori che si avvicinano allo 0 indicano suolo nudo, fiumi o superfici antropiche invece i valori che si avvicinano all'uno indicano una vegetazione sana.

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
confrontiamo le NDVI con la funzione `im.multiframe`

```r
im.multiframe(1,2)
plot(ndvi18)
plot(ndvi19)
```

L'output sarà questo :
![Ndvi1819](https://github.com/user-attachments/assets/2a0c8b16-e4e3-4e46-afbd-75c595b075c5)

Per visualizzare meglio la differenza e creare un multiframe che sia adatto anche alle persone affette da daltonismo possiamo utilizzare una palette specifica del pacchetto [Viridis](https://cran.r-project.org/web/packages/viridis/vignettes/intro-to-viridis.html) con il seguente codice:

```r
im.multiframe(1,2)
plot(ndvi18, col=inferno(100))
plot(ndvi19, col=inferno(100))
```
l'outout sarà di questo tipo :



![ndviviridis](https://github.com/user-attachments/assets/0ad7b0b0-ef11-48a3-a3f0-c5e26b87655c)



(Codice r per esportare l'immagine nella cartella settata all'inizio con relativa risoluzione, larghezza e altezza dell'immagine)

```r
png ("ndviviridis.png", width = 3000, height = 3000, res = 150)

im.multiframe(1,2)
plot(ndvi18, col=inferno(100))
plot(ndvi19, col=inferno(100))
dev.off()
```


Si puo vedere nel plot che i valori superiori a 5 si identificano con colori accessi verso verde e giallo indivando la vegetazione sana. 
Nel 2018 nei mesi pre-Vaia l'NDVI indica un bosco sano dove le piante hanno una buona riflettanza sul NIR, invece nel 2019 si nota l'apparizione di macchie blu che indicano gli schianti delle piante su tutta la conca agordina e sopratutto si nota che verso nord sul versante sud delle pale di dan lucano è apparsa una grande macchia che indica il danno fatto dall'incendio avvenuto una settimana prima della tempesta.

l'NDVI in questo caso ci indica come la composizione e la sanità dei boschi nella vallata possa cambiare in maniera accentuata nel giro di una notte di tempesta.

In seguito al calcolo dell'NDVI dell'area è possibile fare una clasificazione binaria dei pixel in R per distinguere prima di tutto il bosco dalla roccia/ insediamenti urbani e in seguito vedere come nel giro di un anno ci sia stato l'aumento dei pixel nell'immagine simboleggiando la perdita di bosco in seguito alla tempesta/ incendio 



# Classificazione 

Grazie al pacchetto `imagery` in r è possibile analizzare i dati raster e fare una classificazione dei pixel  in 2 parti. in questo caso si può fare una classificazione in due classi tra bosco e roccia/urbanizzazione/suolo nudo con la funzione im.classify partendo dalle due NDVI.

```r
bosco18c = im.classify(ndvi18, num_clusters = 2)
bosco19c = im.classify(ndvi19, num_clusters = 2)
```
codice per invertire le classi di `bosco19c`, per risolvere problema inversione colori grafico. 

```r
bosco19c = classify(bosco19c, rcl = matrix(c(1, 2, 2, 1), ncol = 2, byrow = TRUE))
```
multiframe con la classificazione in due cluster dell'output dell'NDVI

```r
im.multiframe(1,2)
plot(bosco18c)
plot(bosco19c)
```

Risultato dell'output con colori non invertiti:
![bosco1819classify](https://github.com/user-attachments/assets/8b8ab317-c55a-460e-b550-b72ff8b155cd)

1 = bosco

2 = roccia, suolo nudo, umano

Risulta che nel 2019 la quantità di bosco è diminuita.
Ora che abbiamo la classificazione in due classi sulle due immagini possiamo calcolare i pixel di entrambe le immagini e ricavare la percentuale di bosco e di area urbanizzata/suolo nudo. 


# Calcolo percentuale bosco nel 2018, 2019 
calcolo i pixel nell'immagine 2018, 2019 e la percentuale bosco 18 e 19 

Questa funzione fornisce un data frame con colonne value (1 o 2) e count (numero di pixel)

```r
f18 = freq(bosco18c)  # classi e conteggi per il 2018
f19 = freq(bosco19c)  # classi e conteggi per il 2019
```
risultato 
```r
f18
  layer value   count
1     1     1 2659214
2     1     2  508774

f19
  layer value   count
1     1     1 2531701
2     1     2  636287
```

Totale pixel validi, calcolo il totale dei pixel validi all'interno della calssificazione

```r
tot18 = ncell(bosco18c) 
tot19 = ncell(bosco19c)

```

Calcolo la percentuale 

```r
perc18 = freq(bosco18c)$count * 100 / ncell(bosco18c)
perc19 = freq(bosco19c)$count * 100 / ncell(bosco19c)
```
Le due percentuali rappresentano le aree urbanizzate più il bosco perso , prendo solo la percentuale bosco 

*83,94* nel 2018, arrotondato *84%*

*79,92* nel 2019, arrotondato *80%*

Ora faccio la differenza delle percentuali e vedo la percentuale di bosco perso 

```r

boscoperso= perc18-perc19
```

= 4,03%

considerando che la tempesta è avvenuta in un'unica notte e in concomitanza ad un incendio boschivo, la percentuale di bosco perso è significativa (4%)



## Ggplot con i dati ottenuti 


Visualizzazione delle **variazioni nella copertura boschiva** dell’Agordino tra il 2018 e il 2019 usando `ggplot2`.

```r
# Definizione della classe osservata
class = c("Percentuale boschi agordino")

# Percentuale di copertura boschiva per anno
y2018 = c(84)  # anno 2018
y2019 = c(80)  # anno 2019

# Calcolo perdita
perdite = y2018 - y2019

# Creazione tabella
tabout = data.frame(class, y2018, y2019, perdite)

```
ora crea i grafici `ggplot2` per le varie percentuali :

Grafico per percentuale boschi 2018

```r
p1 = ggplot(tabout, aes(x=class, y=y2018, color=class)) + 
  geom_bar(stat="identity", fill="white") + 
  ylim(c(0,100))
p1
```

Grafico per percentuale boschi 2019

```r
p2 = ggplot(tabout, aes(x=class, y=y2019, color=class)) + 
  geom_bar(stat="identity", fill="white") + 
  ylim(c(0,100))
p2

```
Grafico per identificare perdita totale del bosco :

```r
p3 = ggplot(tabout, aes(x=class, y=perdite, color=class)) + 
  geom_bar(stat="identity", fill="white") + 
  ylim(c(0,100))
p3
```

per unire i grafici in un unico file unisco i grafici con  `+ `

```r
p1 + p2 + p3

```

l'output finale risulterà così :


![ggplot3](https://github.com/user-attachments/assets/0b14902a-84f6-4dcf-9265-59525b7ecb1e)

Le prime due colonne rappresentano la percentuale di bosco tra il 2018 e il 2019 e invece nell'ultima colonna è rappresentata la perdita di superfice boschiva nella conca agordina che risulta il 4%

Si può calcolare la perdita di bosco in ettari calcolando la superfice in metri quadrati grazie alla risoluzione dell'immagine  Sentinel :

`vaia18`

 Guardo i valori della risoluzione li moltiplico per altezza e larghezza e li moltiplico per 10

 `(1644*1927)*10`

il risultato sarà = 31679880 metri quadrati 

li possiamo moltiplicare per 0,04 ovvero il 4% di bosco perso tra il 2018 e il 2019:

`31679880*0.04`

Il risultato è di  1.267.195 $m^2$  che in ettari diventano 126,82 ha. Considerando la tempesta e l'incendio è una perdita significativa in una arco di tempo molto breve (una settimana).

# Conclusioni 

L'analisi della conca agordina durante la tempesta Vaia ha prodotto dei risulati che esprimono l'intensità del fenomeno ventoso che è avvenuto in questa area di montagna. In una notte sono stati sradicati circa 126,82 ha di bosco, oltre a questo ci sono stati fenomeni alluvione rottura degli argini e ovviamente danni a strutture ed abitazioni. Questo evento climatico estremo apre gli occhi sul futuro degli ambienti montani i quali essendo molto sensibili ai cambiamenti climatici saranno sempre di piu soggetti a questi ultimi. Riconoscere il cambiamento e l'aumento di questi fenomeni è fondamentale per ricostruire e attivare le comunità montane in modo da affrontare il fenomeno. In un epoca dove la mercificazione e spettacolarizzazione degli ambienti montani dissolve lo sguardo sulla sua fragilità e complessità sia dal punto divista ecologico, ambientale e umano è necessario agire e sensibilizzare su temi come cambiamento climatico, perdita di biodiversità, turismo e spopolamento. 





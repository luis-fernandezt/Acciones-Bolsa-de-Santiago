# Principales Stocks de precios de la Bolsa de Santiago

En este ejercicio se observaran las principales acciones del mercado de La Bolsa Bolsa de Santiago. El siguiente código en lenguaje **R** permitirá obtener los precios de los instrumentos en sus diferentes etapas de transacción ("Open", "Hight", "Low", "Close"), para posteriormente visualizar dichos resultados en un periodo de tiempo definido.

### Librerías necesarias.

```{r librerias, include=TRUE, echo=FALSE}
library(quantmod)
library(dygraphs)
library(dplyr)
library(data.table)
library(xts)
library(htmlwidgets)
```

### Definimos el periodo de tiempo que analizaremos. 

```{r time, include=TRUE, echo=FALSE}
start <- as.Date("2014-01-01")
end <- as.Date("2020-05-05")
```

### Descargamos los stock de nuestro interés. En este caso algunos de la banca y retail.

```{r acciones, echo=FALSE}
getSymbols("BCH", src = "yahoo", from = start, to = end)
getSymbols("BSANTANDER.SN", src = "yahoo", from = start, to = end)
getSymbols("ITCB", src = "yahoo", from = start, to = end)

getSymbols("CENCOSUD.SN", src = "yahoo", from = start, to = end)
getSymbols("FALABELLA.SN", src = "yahoo", from = start, to = end)
getSymbols("FORUS.SN", src = "yahoo", from = start, to = end)
getSymbols("SMU.SN", src = "yahoo", from = start, to = end) 
```

### Extraemos los precios de cierre y creamos un data set para cada grupo.

```{r close,  echo=FALSE}
BCH.C <- BCH$BCH.Close
BSANTANDER <- BSANTANDER.SN$BSANTANDER.SN.Close
ITCB <- ITCB$ITCB.Close

CENCOSUD <- CENCOSUD.SN$CENCOSUD.SN.Close
FALABELLA <- FALABELLA.SN$FALABELLA.SN.Close
FORUS <- FORUS.SN$FORUS.SN.Close
SMU <- SMU.SN$SMU.SN.Close
```

```{r cbind,  echo=FALSE}
banca <- cbind(BCH.C, BSANTANDER, ITCB)
retail <- cbind(CENCOSUD, FALABELLA, FORUS, SMU)
```

### Luego ploteamos los datos en dos graficos diferentes, uno por cada grupo. Se indica un periodo de tiempo acotado en la escala de rangos, para un análisis en detalle.

## Acciones de la banca.

```{r dyg_banca, echo= True}
dyg_banca <- 
dygraph(banca, main = "Acciones de la banca") %>%
  dySeries("BSANTANDER.SN.Close", label = "Santander") %>%
  dySeries("BCH.Close", label = "Banco.de.Chile") %>%
  dySeries("ITCB.Close", label = "Itaú") %>%
  dyOptions(gridLineColor = "white") %>%
  dyRoller(rollPeriod = 7)  %>%
  dyRangeSelector(height = 20) %>% 
  dyRangeSelector(dateWindow = c("2018-01-01", "2020-05-05")) %>% 
  dyLegend(show = "follow")

dyg_banca
```
![banca](https://raw.githubusercontent.com/luis-fernandezt/Acciones-Bolsa-de-Santiago/master/html/banca.png)

## Acciones del retail.

```{r dyg_retail, echo= True}
dyg_retail <- 
dygraph(retail, main = "Acciones del retail") %>%
  dySeries("FALABELLA.SN.Close", label = "Falabella") %>%
  dySeries("FORUS.SN.Close", label = "Forus") %>%
  dySeries("CENCOSUD.SN.Close", label = "Cencosud") %>%
  dySeries("SMU.SN.Close", label = "SMU") %>%
  dyOptions(gridLineColor = "white") %>%
  dyRoller(rollPeriod = 7)  %>%
  dyRangeSelector(height = 20) %>% 
  dyRangeSelector(dateWindow = c("2018-01-01", "2020-05-05")) %>% 
  dyLegend(show = "follow")

dyg_retail
```
![retail](https://raw.githubusercontent.com/luis-fernandezt/Acciones-Bolsa-de-Santiago/master/html/retail.png)

### Guardamos cada gráfico en un archivo html.

```{r saveWidget, echo=FALSE}
saveWidget(dyg_banca, 
           ".//stock_dy_banca.html",
           selfcontained = TRUE)

saveWidget(dyg_retail, 
           ".//stock_dy_retail.html",
           selfcontained = TRUE)
```

### Grafico candlestick.

Opcionalmente se puede analizar de forma individual cada acción en un grafico Candlestick, por ejemplo a Forus y Banco de Chile. 

## Acciones Forus

```{r FORUS_CDLSTK,  echo= True}
FORUS_CDLSTK <- FORUS.SN[,c(1,2,3,4)]
names(FORUS_CDLSTK) <- c("Open", "Hight", "Low", "Close")

cdlstk_Forus <- 
  dygraph(FORUS_CDLSTK, main = "Forus") %>%
  dyOptions(gridLineColor = "white") %>%
  dyRoller(rollPeriod = 7)  %>%
  dyRangeSelector(height = 20) %>% 
  dyRangeSelector(dateWindow = c("2020-03-05", "2020-05-05")) %>% 
  dyCandlestick()

cdlstk_Forus
```
![forus](https://raw.githubusercontent.com/luis-fernandezt/Acciones-Bolsa-de-Santiago/master/html/forus.png)

## Acciones Banco de Chile

```{r BCH_CDLSTK, echo= True}
BCH_CDLSTK <- BCH[,c(1,2,3,4)]
names(BCH_CDLSTK) <- c("Open", "Hight", "Low", "Close")

cdlstk_bch <- 
  dygraph(BCH_CDLSTK, main = "Banco de Chile") %>%
  dyOptions(gridLineColor = "white") %>%
  dyRoller(rollPeriod = 7)  %>%
  dyRangeSelector(height = 20) %>% 
  dyRangeSelector(dateWindow = c("2020-03-05", "2020-05-05")) %>% 
  dyCandlestick()

cdlstk_bch
```
![bch](https://raw.githubusercontent.com/luis-fernandezt/Acciones-Bolsa-de-Santiago/master/html/bch.png)

### Guardamos cada gráfico en un archivo html.

```{r save, echo=FALSE}
saveWidget(cdlstk_Forus, 
           ".//forus.html",
           selfcontained = TRUE)

saveWidget(cdlstk_bch, 
           ".//bch.html",
           selfcontained = TRUE)
```

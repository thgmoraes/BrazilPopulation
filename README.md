# Brazil's Population  
Creating a dataset about the Brazil's population from the last 20 yeas (1998 - 2017)  
author: Rodrigo Silva  


```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = FALSE)
```

The goal of this project is just create a dataset about Brazil's population from the last 20 years to make easy any demographic processing. All the data was obtained from IBGE (Brazilian Institute of Geography and Statistics) and TCU (Federal General Accounting Office).

```{r libraries, eval=FALSE, echo=TRUE, warning=FALSE}
library(foreign)
library(dplyr)
```

```{r merging population, eval=FALSE, warning=FALSE, echo=TRUE}
setwd('Data/')

files <- list.files(pattern = '.DBF')

myfiles <- lapply(files, read.dbf)

populacao <- do.call(rbind, myfiles)
```

## Nomenclature of municipalities and regions

```{r municipalities and regions, eval=FALSE, warning=FALSE, echo=TRUE}

municipios <- read.csv2('Municipios_Brasileiros.csv', header=TRUE, sep = ',')

regioes <- read.csv2('UF-Regiões.csv', header = TRUE)

```

Merging data about location

```{r merging location, eval=FALSE, warning=FALSE, echo=TRUE}
names(municipios)[3] <- 'CO_UF'

brasil <- merge(municipios, regioes, all.x = TRUE)
```

Filtering for the desired parameters

```{r filtering parameters, eval=FALSE, warning=FALSE, echo=TRUE}
brasil <- brasil %>%
  select(Código.IBGE, Nome.do.Município, Estado, UF, NO_REGIAO, Latitude, Longitude)
```

Renaming columns

```{r renaming columns, eval=FALSE, warning=FALSE, echo=TRUE}

# Location
names(brasil)[1] <- 'code'
names(brasil)[2] <- 'city'
names(brasil)[3] <- 'state'
names(brasil)[4] <- 'abbreviation'
names(brasil)[5] <- 'region'
names(brasil)[6] <- 'lat'
names(brasil)[7] <- 'long'

# Population
names(populacao)[1] <- 'code'
names(populacao)[2] <- 'year'
names(populacao)[3] <- 'population'

```

The *code* about municipalities used to have 7 digits, the 7th digit was necessary to verify the code, so as we don't use it anymore I'll cut it out from the data. 

```{r remove digit verification, eval=FALSE, warning=FALSE, echo=TRUE}
brasil$code <- substr(as.character(brasil$code),1,6)
```

Other thing is bothering me is the name *REGIAO* in the *region* column, I gonna take it out too. 

```{r remove name regiao, eval=FALSE, warning=FALSE, echo=TRUE}
brasil$region <- substr(as.character(brasil$region), 8, 20)
```

## Merging data about population with data about location

```{r generating result, eval=FALSE, warning=FALSE, echo=TRUE}
resultado <- merge(brasil, populacao, all.x = TRUE)
```

## Spreading the result to better viewing

```{r spreading results, eval=FALSE, warning=FALSE, echo=TRUE}

library(tidyr)

resultado_horizontal <- spread(resultado, year, population)

```

## Exporting results

```{r exporting results, eval=FALSE, warning=FALSE, echo=TRUE}
write.csv2(resultado, "brazil-population-2012-2017.csv", row.names = FALSE)

write.csv2(resultado_horizontal, "brazil-population-2012-2017-spread.csv", row.names = FALSE)
```

ASAP I'll continue improving this dataset!
I really hope you enjoy this!


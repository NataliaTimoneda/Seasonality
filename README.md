Pipeline per estudiar la estacionalitat de dades utilitzant el paquet EcolUtils de R CRAN.

EcolUtils depen dels paquests Vegan i spaa.

Input: Les dades han d'estar ordenades i no hi pot haver NA's.

## Preparació de les dades.

Les dades per calcular la estacinalitat tenen que està en format data frame, les columnes els OTU/ASV i les files les mostres; en tota la taula no hi pot haver cap NA i les mostres han d'estar ordenades temporalment.

Una de les posibilitats és que partim d'un objecte phyloseq. Si es així, despres d'asegurar-nos que les dades esan normalitzades, aglomerament al nivell taxonomic que volem estudiar, i extraiem la taula de OTU/ASV transposada

```{r}

physeq_glom <- tax_glom(physeq_object, taxrank = "Group", NArm=FALSE)
group.tab<-t(otu_table(physeq_glom))

```

## Càlcul RI i SI. Taula de resultats 
Una vegada tenim la taula de dades (columnes la taxonomia, linies mostres) calculem la estacionalitat.

-Opcions a canviar:
* n: número de permutes
* lag.max : número de files

S'obtindrà un resultat per cada columna del input.

```{r}

esta<-seasonality.test(group.tab, n = 1000, probs = c(0.025, 0.975),  lag.max = 120, na.action = na.pass)

```
|      | Observed | mean.simulated | lowCI | uppCI |sign |
| -----|---------:| -----:|-------------:| -----:|-----:|
| asv2 | 3.96 | 3.22 | 2.65 | 3.89 | SIGNIFICANTLY HIGHER |
| asv4 | 3.03 | 2.61 | 2.45 | 2.83 | SIGNIFICANTLY HIGHER |

##Per visualitzar la distribució 

Opcions:
* frequency: indicar la estacionalitat a estudiar (anual: 12)
* start: valors al eix de les X, el primer número i increment a cada punt.

```{r}
times<-ts(group.tab, frequency = 12, start = c(2004,1))
plot.plot.ts(times)
```
![alt text](https://github.com/NataliaTimoneda/Seasonality/dsitribucio.png?raw=true)
##Per realitzar el gràfic ACF

En aquest cas no es pot plotar més d'una variable simultaneament, s'ha de realitzar 1 a 1.

```{r}
acf(group.tab[,1])
```

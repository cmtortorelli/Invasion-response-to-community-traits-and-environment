species in trait space NMS
================
Claire Tortorelli
September 3, 2019

## Read in data

``` r
traits <- read.csv(here("data","allTraits_averagedBySpecies_USDAcodes.csv"))

#read in abudnance data
abund <- read.csv(here("data","2020_USDAspeciesCover_4traits_seededCommunitySubplots.csv"))
```

## Prep data for NMS

Average cover by species

``` r
#get m
abund_sum <- abund[,2:40] %>% pivot_longer(cols = 1:39, 
                            names_to = "species", values_to = "abund") %>%
                            group_by(species) %>%
                            summarise(total_cover = sum(abund),
                                      mean_cover = mean(abund),
                                      log_cover = log(abund + 1),
                                     sd_mean = sd(abund))
```

    ## `summarise()` regrouping output by 'species' (override with `.groups` argument)

Log transform trait values

``` r
#Set species to row names
rownames(traits) <- traits$species 
traits$species <- NULL 

# log transform trait values
traits.log <- log(traits)
```

## explore correlations

``` r
#svg("figures/log_tranformed_raw_traits_corPlot.svg")
#check out correlations
cor(traits.log) 
```

    ##                             SLA root.shoot RootLength.cm RootAvgDiam.mm
    ## SLA                   1.0000000 -0.3522963   -0.14970149     -0.4779211
    ## root.shoot           -0.3522963  1.0000000    0.46778217      0.7232873
    ## RootLength.cm        -0.1497015  0.4677822    1.00000000      0.1853360
    ## RootAvgDiam.mm       -0.4779211  0.7232873    0.18533604      1.0000000
    ## finerootV.totalrootV  0.3568936 -0.6927719   -0.23491559     -0.8076571
    ## height               -0.1888102  0.4056450    0.53651966      0.3715756
    ## N.perc               -0.1721119  0.2775948    0.09456644      0.2967015
    ##                      finerootV.totalrootV      height      N.perc
    ## SLA                             0.3568936 -0.18881023 -0.17211191
    ## root.shoot                     -0.6927719  0.40564505  0.27759480
    ## RootLength.cm                  -0.2349156  0.53651966  0.09456644
    ## RootAvgDiam.mm                 -0.8076571  0.37157564  0.29670150
    ## finerootV.totalrootV            1.0000000 -0.32587813 -0.21597686
    ## height                         -0.3258781  1.00000000 -0.05025046
    ## N.perc                         -0.2159769 -0.05025046  1.00000000

``` r
pairs(traits.log)
```

![](Speices_Traits_NMS_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

``` r
#dev.off()
```

Some traits appear not linearly correlated, so using NMDS rather than
PCA

scale and center data

``` r
traits.stan <- standardize(traits.log, centerFun = mean, scaleFun = sd)
```

## Run NMS (species in trait space)

metaMDS can’t be run on negative values so shifting to the minimum value
so that scores are not negative

``` r
#shift min so that scores are not negative
minShift <- function(x) {x + abs(min(x))}
traits.stan.shift <- apply(traits.stan, 2, minShift)

#run NMS on shifted data with bray distance measure
traits.stan.shift.NMS <- metaMDS(traits.stan.shift, distance='bray', k=2, trymax=50, autotransform=FALSE, wasscores=TRUE, noshare=FALSE) 
```

    ## Run 0 stress 0.1340695 
    ## Run 1 stress 0.136378 
    ## Run 2 stress 0.1479128 
    ## Run 3 stress 0.1479129 
    ## Run 4 stress 0.1515962 
    ## Run 5 stress 0.1624792 
    ## Run 6 stress 0.1372077 
    ## Run 7 stress 0.1610013 
    ## Run 8 stress 0.1450676 
    ## Run 9 stress 0.1450676 
    ## Run 10 stress 0.1340695 
    ## ... Procrustes: rmse 2.303709e-05  max resid 6.823295e-05 
    ## ... Similar to previous best
    ## Run 11 stress 0.1501046 
    ## Run 12 stress 0.1581752 
    ## Run 13 stress 0.1400015 
    ## Run 14 stress 0.1385578 
    ## Run 15 stress 0.1364973 
    ## Run 16 stress 0.1340695 
    ## ... Procrustes: rmse 0.0004261489  max resid 0.001990378 
    ## ... Similar to previous best
    ## Run 17 stress 0.1401348 
    ## Run 18 stress 0.1363781 
    ## Run 19 stress 0.1450677 
    ## Run 20 stress 0.3970357 
    ## *** Solution reached

``` r
#bray curtis tends to perform better with species and sample similarity
traits.stan.shift.NMS
```

    ## 
    ## Call:
    ## metaMDS(comm = traits.stan.shift, distance = "bray", k = 2, trymax = 50,      autotransform = FALSE, noshare = FALSE, wasscores = TRUE) 
    ## 
    ## global Multidimensional Scaling using monoMDS
    ## 
    ## Data:     traits.stan.shift 
    ## Distance: bray 
    ## 
    ## Dimensions: 2 
    ## Stress:     0.1340695 
    ## Stress type 1, weak ties
    ## Two convergent solutions found after 20 tries
    ## Scaling: centring, PC rotation, halfchange scaling 
    ## Species: expanded scores based on 'traits.stan.shift'

``` r
#plot species ('sites') and traits ('species')
plot(traits.stan.shift.NMS, type='t', display=c('sites', 'species'))
```

![](Speices_Traits_NMS_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

``` r
#extract scores to plot with vectors
variableScores <- data.frame(traits.stan.shift.NMS$species)
sampleScores <- data.frame(traits.stan.shift.NMS$points)

#add species abundances to points dataframe
sampleScores$species <- rownames(sampleScores)
sampleScores_abund <- merge(sampleScores, abund_sum)
```

## Plot NMS

``` r
#svg("figures/traitnms_scaled_by_abund.svg", height = 5.5, width = 8)

par(family = "sans")
plot(sampleScores_abund$MDS1, sampleScores_abund$MDS2, xlab='NMS 1', ylab='NMS 2', type='n', asp=1, las=1)

points(sampleScores_abund[,2:3], pch=16, cex=(sampleScores_abund$log_cover/2), col="#98DEEC")

points(sampleScores_abund[,2:3], pch=16, col ="#2F76BC", cex = 0.5)


arrows(0, 0, variableScores[, 1], variableScores[, 2], length=0.1, angle=20, col ="#A6761D")
 
#set text
text(variableScores[, 1], variableScores[, 2], pos = 3, offset = 0.5, col = "black", font = 2, rownames(variableScores), cex=0.7)

text(sampleScores_abund$MDS1, sampleScores_abund$MDS2, pos = 1, offset = 0.3, col =  "grey3", font = 1, sampleScores_abund$species,  cex=0.7)
```

![](Speices_Traits_NMS_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

``` r
#dev.off()
```

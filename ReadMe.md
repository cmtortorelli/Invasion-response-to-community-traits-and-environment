# Invasion-response-to-community-traits-and-environment

Metadata for sript and data files

### R Script files

* Calculating community weighted means:
Code walks through calculating community weighted means for each subplot

* Calculating nearest species metrics:
Code walks through calculating nearest species distance metrics for each subplot

* Modeling community trait metrics:
All models are present here (all traits by all community metrics). Plots were initiated using this code and cleaned up in inkscape as svg files

* Species traits NMS:
Here we calculated and plotted species in trait space using NMS 

* Subplot traits NMS:
Here we calculated and plotted sublots in community weighted mean trait space using NMS

### data files

* 2020 biomass by subplot:
biomass collected from 20x20cm subplots in 2020

  * plot_quad: represents the individual subplots from which biomass was collected. 
    * First 4 letters refer to vegetation code (ARAR = Artemisia arbuscula = low sage steppe; ARRI = artimisia rigida= scabflat; SEEP = wet meadow)
    * Number after vegetation type references the block number (1*5)
    * "CC." indicates that these were all community plots with ventenata seed added
    * Final number indicates the number of the subplot at each plot (1*7)

  * resident_g: resident biomass (excluding ventenata) collected from within the subplots in 2020 measured in g/subplot
  * vedu_g: ventenata biomass collected from within the subplots in 2020 measured in g/subplot
  
<br/>

* nearest species distances:
nearest species distance calculated from the "Calculating nearest species metrics" script
  * plot_quad: see above
  * finerootV.totalrootV (fine root Volume to total root volume ratio)
  * height (cm)
  * N.perc (% leaf N)
  * root.shoot (root to shoot mass ratio )
  * RootAvgDiam.mm (root average diameter (mm))
  * RootLength.cm (cm)
  * SLA (specific leaf area; leaf area/ leaf mass)
  
  * community weighted means:
Community weighted means output from the "Calculating community weighted means" script
  * plot_quad: see above
  * finerootV.totalrootV (fine root Volume to total root volume ratio)
  * height (cm)
  * N.perc (% leaf N)
  * root.shoot (root to shoot mass ratio )
  * RootAvgDiam.mm (root average diameter (mm))
  * RootLength.cm (cm)
  * SLA (specific leaf area; leaf area/ leaf mass)
  * vegtype: (ARAR = Artemisia arbuscula = low sage steppe; ARRI = artimisia rigida= scabflat; SEEP = wet meadow)
  * plot: vegetation type with block number
  * plotno: just the block number

* 2020_USDAspeciesCover_4traits_seededCommunitySubplots: Species foliar cover (values) for each subplot (row names) for each species represented by their USDA plants code (column names)

* allTraits_averagedBySpecies_USDAcodes: Averaged raw trait values for each species (row names) for each trait (column names)



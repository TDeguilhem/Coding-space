### Application : ACP avec SPSS



## Z_scores des variables concernées


```{r}

IF (OECD=1) OECD_CAT=1.
  FILTER BY OECD_CAT.
EXECUTE.


DESCRIPTIVES  Life_exp_women Life_exp_men Anemia_preg_women Poverty2USD Vulnerable Socprotection_exp 
    Elecrural HealthExp
    /SAVE.
    
```

## ACP


```{r}

FACTOR
  /VARIABLES Life_exp_women Life_exp_men Anemia_preg_women Poverty2USD Vulnerable Socprotection_exp 
    Elecrural HealthExp
  /MISSING LISTWISE 
  /ANALYSIS Life_exp_women Life_exp_men Anemia_preg_women Poverty2USD Vulnerable Socprotection_exp 
    Elecrural HealthExp
  /PRINT UNIVARIATE INITIAL CORRELATION SIG KMO EXTRACTION ROTATION FSCORE
  /PLOT EIGEN ROTATION
  /CRITERIA MINEIGEN(1) ITERATE(25)
  /EXTRACTION PC
  /CRITERIA ITERATE(25)
  /ROTATION VARIMAX
  /SAVE REG(ALL)
  /METHOD=CORRELATION.
  
```
  
## Représentations graphiques  
  
  ```{r}
  
GRAPH
  /SCATTERPLOT(BIVAR)=FAC1_2 WITH FAC2_2 BY Country (IDENTIFY)
  /MISSING=LISTWISE.


GRAPH
  /BAR(SIMPLE)=MEAN(FAC1_2) BY HDI
  /INTERVAL CI(95.0).


GRAPH
  /BAR(SIMPLE)=MEAN(FAC2_2) BY HDI
  /INTERVAL CI(95.0).


COMPUTE Mean_MULTI=MEAN(FAC1_2,FAC2_2).
EXECUTE.


GRAPH
  /BAR(SIMPLE)=MEAN(Mean_MULTI) BY HDI
  /INTERVAL CI(95.0).



GGRAPH
  /GRAPHDATASET NAME="graphdataset" VARIABLES=FAC1_2 FAC2_2 REGION Country MISSING=LISTWISE 
    REPORTMISSING=NO
  /GRAPHSPEC SOURCE=INLINE
  /FITLINE TOTAL=NO SUBGROUP=NO.
BEGIN GPL
  SOURCE: s=userSource(id("graphdataset"))
  DATA: FAC1_2=col(source(s), name("FAC1_2"))
  DATA: FAC2_2=col(source(s), name("FAC2_2"))
  DATA: REGION=col(source(s), name("REGION"), unit.category())
  DATA: Country=col(source(s), name("Country"), unit.category())
  GUIDE: axis(dim(1), label("REGR factor score   1 for analysis 2"))
  GUIDE: axis(dim(2), label("REGR factor score   2 for analysis 2"))
  GUIDE: legend(aesthetic(aesthetic.color.interior), label("REGION"))
  GUIDE: text.title(label("Diagramme de dispersion : superposé de REGR factor score   2 for ",
    "analysis 2 par REGR factor score   1 for analysis 2 par REGION"))
  SCALE: cat(aesthetic(aesthetic.color.interior), include("1", "2", "3", "4", "5"))
  ELEMENT: point(position(FAC1_2*FAC2_2), color.interior(REGION), label(Country))
END GPL.





GRAPH
  /BAR(SIMPLE)=MEAN(Mean_MULTI) BY REGION
  /INTERVAL CI(95.0).

FILTER OFF.
  
```

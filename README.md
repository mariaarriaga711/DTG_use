# DTG_use
Dolutegravir use is related to lower HTLV-1 proviral load in people co-infected by HIV-1: a cross-sectional study
In this cross-sectional study we quantified HTLV-1 PVL in HIV-HTLV-1 coinfected patients and HTLV-1 mono-infected ones. We compared HTLV-1 PVL across groups, adjusting for age and sex, ART use and CD4+ cells count. 
We used a propensity score derived from a regression model for DTG use and HTLV-1 PVL, that included covariates like age and ART duration.
Software: SPSS V 29.0.1.0
Dataset: DTG_dataset_1

# Propensity Score Matching.
STATS PSM GROUP = use_DTG BY = age time_ART CD4 PROPENSITY=re
  FUZZ=1.0 NEWDEMANDERIDVAR=e SUPPLIERID=ID
  OUTPUTDS=fa 
/OPTIONS SAMPLEWITHREPLACEMENT=FALSE
SHUFFLE=FALSE.

# Regression model 1
REGRESSION
  /MISSING LISTWISE
  /STATISTICS COEFF OUTS CI(95) R ANOVA
  /CRITERIA=PIN(.05) POUT(.10) TOLERANCE(.0001)
  /NOORIGIN 
  /DEPENDENT proviral_load
  /METHOD=ENTER use_DTG propensity_score.
  
# Regression model 2
LOGISTIC REGRESSION VARIABLES proviral_load_median
  /METHOD=ENTER propensity_score use_DTG 
  /CONTRAST (use_DTG)=Indicator
  /PRINT=CI(95)
  /CRITERIA=PIN(0.05) POUT(0.10) ITERATE(20) CUT(0.5).

# Regression model 3
LOGISTIC REGRESSION VARIABLES proviral_IQR_75
  /METHOD=ENTER propensity_score use_DTG 
  /CONTRAST (use_DTG)=Indicator
  /PRINT=CI(95)
  /CRITERIA=PIN(0.05) POUT(0.10) ITERATE(20) CUT(0.5).

# Regression model 4
LOGISTIC REGRESSION VARIABLES proviral_load_50
  /METHOD=ENTER propensity_score use_DTG 
  /CONTRAST (use_DTG)=Indicator
  /PRINT=CI(95)
  /CRITERIA=PIN(0.05) POUT(0.10) ITERATE(20) CUT(0.5).

# Regression model 1 with bootstrap
 /SAMPLING METHOD=SIMPLE
  /VARIABLES TARGET=proviral_load INPUT=  use_DTG propensity_score  
  /CRITERIA CILEVEL=95 CITYPE=PERCENTILE  NSAMPLES=1000
  /MISSING USERMISSING=EXCLUDE.
REGRESSION
  /MISSING LISTWISE
  /STATISTICS COEFF OUTS CI(95) R ANOVA
  /CRITERIA=PIN(.05) POUT(.10) TOLERANCE(.0001)
  /NOORIGIN 
  /DEPENDENT proviral_load
  /METHOD=ENTER use_DTG propensity_score.
  
# Regression model 2 with bootstrap
 /SAMPLING METHOD=SIMPLE
  /VARIABLES TARGET=proviral_load_median INPUT=propensity_score use_DTG  
  /CRITERIA CILEVEL=95 CITYPE=PERCENTILE  NSAMPLES=1000
  /MISSING USERMISSING=EXCLUDE.
LOGISTIC REGRESSION VARIABLES proviral_load_median
  /METHOD=ENTER propensity_score use_DTG 
  /CONTRAST (use_DTG)=Indicator
  /PRINT=CI(95)
  /CRITERIA=PIN(0.05) POUT(0.10) ITERATE(20) CUT(0.5).

# Regression model 3 with bootstrap
BOOTSTRAP
  /SAMPLING METHOD=SIMPLE
  /VARIABLES TARGET=proviral_IQR_75 INPUT=propensity_score use_DTG  
  /CRITERIA CILEVEL=95 CITYPE=PERCENTILE  NSAMPLES=1000
  /MISSING USERMISSING=EXCLUDE.
LOGISTIC REGRESSION VARIABLES proviral_IQR_75
  /METHOD=ENTER propensity_score use_DTG 
  /CONTRAST (use_DTG)=Indicator
  /PRINT=CI(95)
  /CRITERIA=PIN(0.05) POUT(0.10) ITERATE(20) CUT(0.5).

# Regression model 4 with bootstrap
  BOOTSTRAP
  /SAMPLING METHOD=SIMPLE
  /VARIABLES TARGET=proviral_load_50 INPUT=propensity_score use_DTG  
  /CRITERIA CILEVEL=95 CITYPE=PERCENTILE  NSAMPLES=1000
  /MISSING USERMISSING=EXCLUDE.
LOGISTIC REGRESSION VARIABLES proviral_load_50
  /METHOD=ENTER propensity_score use_DTG 
  /CONTRAST (use_DTG)=Indicator
  /PRINT=CI(95)
  /CRITERIA=PIN(0.05) POUT(0.10) ITERATE(20) CUT(0.5).

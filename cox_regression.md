---
title: "Cox Regression"
author: "Stephan Voegele"
bibliography: references.bib
date: last-modified
format: 
  html:
    self-contained: true
    number-sections: true
    number-depth: 3
    anchor-sections: true
    smooth-scroll: true
    theme: journal
    toc: true
    toc-depth: 3
    toc-title: Contents
    toc-location: right
    code-link: false
    code-tools: true
    code-fold: show
    code-block-bg: "#f1f3f5"
    code-block-border-left: "#31BAE9"
    reference-location: margin
    fig-cap-location: margin
    keep-md: true
execute:
  warning: false
  message: false
---



# Objectives

-   Provide theoretical background
-   Apply Cox regression to dataset
-   Check assumptions

Dataset: Prostate cancer dataset from @harrell2015regression

-   Response: Survival time
-   Predictors: several


::: {.cell}

:::


# Theoretical background

## Survival Function


## Hazard Function

Relative risks or are not applicable for survival times, because we usually cannot assume that the risk is constant over time. We need to create a different measure of risk which takes the time into account, the `hazard ratio`. The central concept is the hazard function.

The idea behind the hazard function is to divide the time in consecutive intervals

$$
[0, t^{(1)}], [t^{(2)}, t^{(3)}], [t^{(q-1)}, t^{(q)}] 
$$
The relative risk in the q-th interval is then defined as the quotient of the conditional probability that a patient is still alive at the beginning of the interval, for therapy A and B. 

From this idea follows the hazard function:
$$
\lambda(t)=\lim_{h \to 0}\frac{P(t<T<t+h|T>t)}{h}
$$

::: callout-note
Therefore, the hazard function can be defined as the instantaneous probability of having an event at time t, given that one has survived up to time t. 
:::

The hazard ratio is the quotient of the hazard function under therapy A and the hazard function of therapy B.
$$
HR=\frac{\lambda_a(t)}{\lambda_B(t)}
$$
It is assumed that this ratio is constant over time, i.e. proportional. 

## Estimation of the survival function and the hazard function
The survival function $S(t)$ and the hazard function $\lambda(t)$ are usually unknown. The survival function can be estimated by the Kaplan-Meier estimates $S_{KM}(t)$. 
$$
S_{KM}(t)=\prod_{i:t_i \leq t}(1-\frac{d_i}{n_i})
$$
The Kaplan-Meier estimator of $\lambda(t)$ is $\lambda_{KM}(t)=-log S_{KM}(t)$.

::: column-margin
Therefore, survival function and hazard function are related.
:::

## Cox Proportional Hazards Model
The general notation of the model is
$$
\lambda(t|X)=\lambda(t) exp(X\beta)
$$
By dividing both sides with $\lambda(t)$ and taking the logs, it follows
$$
log[\frac{\lambda(t|X)}{\lambda(t)}]=X\beta
$$

::: column-margin
$\lambda(t)$ is the baseline hazard at time t, representing the hazard for a person with the value 0 for all the predictor variables. 
:::

The primary interest is the estimation of $\beta$. The $\beta$s are the regression coefficients and represent the influence of each respective covariate on the survival time. See chapter 20.1.3 in @harrell2015regression for details of the derivation of $\beta$.

::: callout-note
Cox proportional hazard model is a semi-parametric model, as it makes a parametric assumption regarding the effect of the predictors on the hazard function $\lambda(t)$, but makes no assumption on the hazard function (e.g. constant like in the exponential model, Weibull).
:::

`Example` (from @schumacher2008methodik ):
Consider a Cox PH model with two categorical covariates $X_1$ and $X_2$ (coded as 0 and 1). The model is therefore
$$
\lambda(t|X_1,X_2)=\lambda(t) exp(\beta_1X_1 + \beta_2X_2)
$$
The HR for $X_1$ (therapy B vs. therapy A) is therefore
$$
HR(B:A)=\frac{\lambda(t|X_1=1,X_2)}{\lambda(t|X_1=0,X_2)}=exp(\beta_1)
$$
  
::: column-margin
The $\beta$s are therefore equal to the logarithms of the hazard ratios. 
:::

Interpretations from @rosner2006fundamentals

`Interpretation of HR for categorical predictors:`
The quantity $exp(beta_1)$ can be interpreted as the instantaneous relative risk of an event per unit time for a person with the risk factor present compared with a person with the risk factor absent, given that both individuals have survived to time t and are the same on all other covariates.

`Interpretation of HR for continuous predictors:`
The quantity $exp(beta_1)$ can be interpreted as the instantaneous relative risk of an event per unit time for an individual with risk-factor level $x_j + \Delta$ compared with someone with risk-level factor $x_j$, given that both individuals have survived to time t and are the same on all other covariates.

## Stratification
see @harrell2015regression chapter 20.1.7

## Test Statistics
Usually, the Wald-Test is used to test whether a regression coefficient is significantly different from 0. The corresponding test statistic is
$$
z=\frac{\beta_j}{SE(\beta_j)}
$$

::: column-margin
For a 2-sided  significance test:

if $z<z_{\alpha/2}$ or $z > z_{1-\alpha/2}$ then reject $H_0$

if $z_{\alpha/2} \le z \le z_{1-\alpha/2}$ then do not reject $H_0$
:::

This test statistic is standard normally distributed under the hypothesis that $\beta_j=0$, with the 100(1-$\alpha$)% confidence interval 
$$
[\beta_j \pm u_{1-\frac{\alpha}{2}} * SE (\beta_j) ]
$$
The corresponding confidence interval of the hazard ratio can be calculated by applying exponentials to the limits. 

## Model Assumptions
- linearity and additivity (see chapter 20.6.1 in @harrell2015regression)
- proportional hazards (various methods exist)


# Descriptive Statistics


::: {.cell}

```{.r .cell-code}
describe(prostate)
```

::: {.cell-output .cell-output-stdout}
```
prostate 

 18  Variables      502  Observations
--------------------------------------------------------------------------------
patno : Patient Number 
       n  missing distinct     Info     Mean      Gmd      .05      .10 
     502        0      502        1    251.7    168.1    26.05    51.10 
     .25      .50      .75      .90      .95 
  126.25   251.50   376.75   451.90   479.95 

lowest :   1   2   3   4   5, highest: 502 503 504 505 506
--------------------------------------------------------------------------------
stage : Stage 
       n  missing distinct     Info     Mean      Gmd 
     502        0        2    0.733    3.424   0.4895 
                      
Value          3     4
Frequency    289   213
Proportion 0.576 0.424
--------------------------------------------------------------------------------
rx 
       n  missing distinct 
     502        0        4 
                                                                          
Value              placebo 0.2 mg estrogen 1.0 mg estrogen 5.0 mg estrogen
Frequency              127             124             126             125
Proportion           0.253           0.247           0.251           0.249
--------------------------------------------------------------------------------
dtime : Months of Follow-up 
       n  missing distinct     Info     Mean      Gmd      .05      .10 
     502        0       76        1    36.13    26.89     1.05     5.00 
     .25      .50      .75      .90      .95 
   14.25    34.00    57.75    67.00    71.00 

lowest :  0  1  2  3  4, highest: 72 73 74 75 76
--------------------------------------------------------------------------------
status 
       n  missing distinct 
     502        0       10 

lowest : alive                        dead - prostatic ca          dead - heart or vascular     dead - cerebrovascular       dead - pulmonary embolus    
highest: dead - other ca              dead - respiratory disease   dead - other specific non-ca dead - unspecified non-ca    dead - unknown cause        
--------------------------------------------------------------------------------
age : Age in Years 
       n  missing distinct     Info     Mean      Gmd      .05      .10 
     501        1       41    0.996    71.46    7.497       56       60 
     .25      .50      .75      .90      .95 
      70       73       76       78       80 

lowest : 48 49 50 51 52, highest: 84 85 87 88 89
--------------------------------------------------------------------------------
wt : Weight Index = wt(kg)-ht(cm)+200 
       n  missing distinct     Info     Mean      Gmd      .05      .10 
     500        2       67    0.999    99.03    14.93    77.95    82.90 
     .25      .50      .75      .90      .95 
   90.00    98.00   107.00   116.00   123.00 

lowest :  69  71  72  73  74, highest: 136 142 145 150 152
--------------------------------------------------------------------------------
pf 
       n  missing distinct 
     502        0        4 
                                                                         
Value           normal activity in bed < 50% daytime in bed > 50% daytime
Frequency                   450                   37                   13
Proportion                0.896                0.074                0.026
                               
Value           confined to bed
Frequency                     2
Proportion                0.004
--------------------------------------------------------------------------------
hx : History of Cardiovascular Disease 
       n  missing distinct     Info      Sum     Mean      Gmd 
     502        0        2    0.733      213   0.4243   0.4895 

--------------------------------------------------------------------------------
sbp : Systolic Blood Pressure/10 
       n  missing distinct     Info     Mean      Gmd      .05      .10 
     502        0       18     0.98    14.35    2.596       11       12 
     .25      .50      .75      .90      .95 
      13       14       16       17       18 
                                                                            
Value          8     9    10    11    12    13    14    15    16    17    18
Frequency      1     3    14    27    65    74    98    74    72    34    17
Proportion 0.002 0.006 0.028 0.054 0.129 0.147 0.195 0.147 0.143 0.068 0.034
                                                    
Value         19    20    21    22    23    24    30
Frequency     12     3     2     3     1     1     1
Proportion 0.024 0.006 0.004 0.006 0.002 0.002 0.002

For the frequency table, variable is rounded to the nearest 0
--------------------------------------------------------------------------------
dbp : Diastolic Blood Pressure/10 
       n  missing distinct     Info     Mean      Gmd      .05      .10 
     502        0       12    0.945    8.149    1.553        6        6 
     .25      .50      .75      .90      .95 
       7        8        9       10       10 
                                                                            
Value          4     5     6     7     8     9    10    11    12    13    14
Frequency      4     5    43   107   165    94    66     9     5     2     1
Proportion 0.008 0.010 0.086 0.213 0.329 0.187 0.131 0.018 0.010 0.004 0.002
                
Value         18
Frequency      1
Proportion 0.002

For the frequency table, variable is rounded to the nearest 0
--------------------------------------------------------------------------------
ekg 
       n  missing distinct 
     494        8        7 

lowest : normal                            benign                            rhythmic disturb & electrolyte ch heart block or conduction def     heart strain                     
highest: rhythmic disturb & electrolyte ch heart block or conduction def     heart strain                      old MI                            recent MI                        
--------------------------------------------------------------------------------
hg : Serum Hemoglobin (g/100ml) 
       n  missing distinct     Info     Mean      Gmd      .05      .10 
     502        0       91        1    13.45     2.16     10.2     10.7 
     .25      .50      .75      .90      .95 
    12.3     13.7     14.7     15.8     16.4 

lowest : 5.89941 7       7.19922 7.7998  8.19922
highest: 17.2969 17.5    17.5977 18.1992 21.1992
--------------------------------------------------------------------------------
sz : Size of Primary Tumor (cm^2) 
       n  missing distinct     Info     Mean      Gmd      .05      .10 
     497        5       55    0.998    14.63    13.05      2.0      3.0 
     .25      .50      .75      .90      .95 
     5.0     11.0     21.0     32.0     39.2 

lowest :  0  1  2  3  4, highest: 54 55 61 62 69
--------------------------------------------------------------------------------
sg : Combined Index of Stage and Hist. Grade 
       n  missing distinct     Info     Mean      Gmd      .05      .10 
     491       11       11    0.959    10.31    2.245        8        8 
     .25      .50      .75      .90      .95 
       9       10       11       13       13 
                                                                            
Value          5     6     7     8     9    10    11    12    13    14    15
Frequency      3     8     7    67   137    33   114    26    75     5    16
Proportion 0.006 0.016 0.014 0.136 0.279 0.067 0.232 0.053 0.153 0.010 0.033

For the frequency table, variable is rounded to the nearest 0
--------------------------------------------------------------------------------
ap : Serum Prostatic Acid Phosphatase 
       n  missing distinct     Info     Mean      Gmd      .05      .10 
     502        0      128    0.996    12.18    21.71    0.300    0.300 
     .25      .50      .75      .90      .95 
   0.500    0.700    2.975   21.689   38.470 

lowest : 0.0999908 0.199982  0.299988  0.399963  0.5      
highest: 316       353.5     367       596       999.875  
--------------------------------------------------------------------------------
bm : Bone Metastases 
       n  missing distinct     Info      Sum     Mean      Gmd 
     502        0        2     0.41       82   0.1633   0.2739 

--------------------------------------------------------------------------------
sdate : Date on study 
         n    missing   distinct       Info       Mean        Gmd        .05 
       502          0        305          1 1978-04-28      254.9 1977-05-23 
       .10        .25        .50        .75        .90        .95 
1977-06-27 1977-10-30 1978-04-10 1978-10-10 1979-03-27 1979-05-14 

lowest : 1977-04-06 1977-04-12 1977-04-14 1977-04-21 1977-04-24
highest: 1979-05-29 1979-05-30 1979-06-03 1979-06-04 1979-06-28
--------------------------------------------------------------------------------
```
:::
:::


# Prepare the data

In a first part, levels from certain variables are combined, e.g. if there are only few observations in one category. Additionally, the transcan function from the rms package is used to impute missing data.

Continuous predictors are expanded by fitting four-knot restricted cubic spline functions, which contain two nonlinear terms and thus have a total of three d.f. Therefore, the rcs function from the rms package is used.

What are `restricted cubic splines`?



::: {.cell}

```{.r .cell-code}
levels(prostate$ekg)[levels(prostate$ekg) %in% c('old MI', 'recent MI')] <- 'MI'
# combines last 2 levels and uses a new name , MI

prostate$pf.coded <- as.integer(prostate$pf)
# save original pf , re-code to 1-4

levels(prostate$pf) <- c(levels(prostate$pf)[1:3], levels(prostate$pf)[3])
# combine last 2 levels

w <- transcan(~ sz + sg + ap + sbp + dbp + age + wt + hg + ekg + pf + bm + hx, imputed=TRUE, data =prostate, pl=FALSE, pr=FALSE )

attach(prostate)
sz <- impute(w, sz, data=prostate)
sg <- impute(w, sg, data=prostate)
age <- impute(w, age, data=prostate)
wt <- impute(w, wt, data=prostate)
ekg <- impute(w, ekg, data=prostate)

dd <- datadist(prostate); options(datadist = 'dd')
```
:::


# Fit the model


::: {.cell}

```{.r .cell-code}
units(dtime) <- 'Month'
S <- Surv(dtime, status != 'alive')

f <- cph(S ~ rx + rcs(age, 4) + rcs(wt, 4) + pf + hx + rcs(sbp, 4) + rcs(dbp, 4) + ekg + rcs(hg, 4) + rcs(sg, 4) + rcs(sz, 4) + rcs(log(ap), 4) + bm)

print(f, latex=T, coefs=F)
```

::: {.cell-output .cell-output-stdout}
```
Cox Proportional Hazards Model

cph(formula = S ~ rx + rcs(age, 4) + rcs(wt, 4) + pf + hx + rcs(sbp, 
    4) + rcs(dbp, 4) + ekg + rcs(hg, 4) + rcs(sg, 4) + rcs(sz, 
    4) + rcs(log(ap), 4) + bm)

                        Model Tests     Discrimination    
                                               Indexes    
Obs        502    LR chi2    136.22     R2       0.238    
Events     354    d.f.           36    R2(36,502)0.181    
Center -2.9933    Pr(> chi2) 0.0000    R2(36,354)0.247    
                  Score chi2 143.62     Dxy      0.333    
                  Pr(> chi2) 0.0000                       
```
:::
:::


The likelihood ratio $\chi^2$ is 136.22 with 36 df (p\<0.05).

::: column-margin
The LR $\chi^2$ is calculated by comparing the deviance (- 2 \* log likelihood) of the model, with all of the covariates you have specified, against the model with all covariates dropped.
:::

The AIC value is not provided in the output, but can calculated from LF $\chi^2$ as follows: $$
AIC = 2k - 2ln(\hat L)
$$ with k as the number of estimated parameters and $\hat L$ as the maximum value of the likelihood function for the model. $ln\hat L$ is the log likelihood.

AIC = 136.22 - 2\*36 = 64.2

In the following an informal data reduction is applied:

::: {.cell}

```{.r .cell-code}
heart <- hx + ekg %nin% c('normal','benign')
label(heart) <- 'Heart Disease Code'
map   <- (2*dbp + sbp)/3
label(map) <- 'Mean Arterial Pressure/10'
dd <- datadist(dd, heart, map)

f <- cph(S ~ rx + rcs(age,4) + rcs(wt,3) + pf.coded +
         heart + rcs(map,3) + rcs(hg,4) +
         rcs(sg,3) + rcs(sz,3) + rcs(log(ap),5) + bm,
         x=TRUE, y=TRUE, surv=TRUE, time.inc=5*12)
print(f, coefs=T)
```

::: {.cell-output .cell-output-stdout}
```
Cox Proportional Hazards Model

cph(formula = S ~ rx + rcs(age, 4) + rcs(wt, 3) + pf.coded + 
    heart + rcs(map, 3) + rcs(hg, 4) + rcs(sg, 3) + rcs(sz, 3) + 
    rcs(log(ap), 5) + bm, x = TRUE, y = TRUE, surv = TRUE, time.inc = 5 * 
    12)

                        Model Tests     Discrimination    
                                               Indexes    
Obs        502    LR chi2    118.37     R2       0.210    
Events     354    d.f.           24    R2(24,502)0.171    
Center -2.4307    Pr(> chi2) 0.0000    R2(24,354)0.234    
                  Score chi2 125.58     Dxy      0.321    
                  Pr(> chi2) 0.0000                       

                   Coef     S.E.    Wald Z Pr(>|Z|)
rx=0.2 mg estrogen  -0.0002  0.1493  0.00  0.9987  
rx=1.0 mg estrogen  -0.4160  0.1657 -2.51  0.0121  
rx=5.0 mg estrogen  -0.1107  0.1571 -0.70  0.4812  
age                 -0.0078  0.0235 -0.33  0.7392  
age'                 0.0110  0.0383  0.29  0.7742  
age''                0.4132  0.4898  0.84  0.3988  
wt                  -0.0237  0.0094 -2.53  0.0116  
wt'                  0.0178  0.0112  1.59  0.1110  
pf.coded             0.2327  0.1196  1.95  0.0517  
heart                0.3901  0.0805  4.85  <0.0001 
map                  0.0171  0.0843  0.20  0.8389  
map'                -0.0192  0.0918 -0.21  0.8345  
hg                  -0.1535  0.0764 -2.01  0.0446  
hg'                  0.0563  0.2086  0.27  0.7872  
hg''                 0.7041  1.2580  0.56  0.5757  
sg                   0.0612  0.0782  0.78  0.4336  
sg'                 -0.0209  0.0976 -0.21  0.8304  
sz                   0.0129  0.0144  0.90  0.3692  
sz'                  0.0060  0.0236  0.25  0.7990  
ap                  -0.5010  0.2926 -1.71  0.0869  
ap'                  8.2562 11.9607  0.69  0.4900  
ap''               -15.4767 38.8280 -0.40  0.6902  
ap'''                5.1543 29.9200  0.17  0.8632  
bm                   0.0307  0.1835  0.17  0.8670  
```
:::
:::

* Improved AIC of 70
* Rough shrinkage estimate improved to 0.80 (but still worrisome)

# Checking assumptions
`Smoothed scales Schoenfeld residuals` are used.


::: {.cell}

```{.r .cell-code}
# compute scale Schoenfeld residuals separately for each predictor and 
# test the assumption of PH
phtest <- cox.zph(f, transform='identity')
phtest
```

::: {.cell-output .cell-output-stdout}
```
                   chisq df    p
rx              4.07e+00  3 0.25
rcs(age, 4)     4.27e+00  3 0.23
rcs(wt, 3)      2.22e-01  2 0.89
pf.coded        5.34e-02  1 0.82
heart           4.95e-01  1 0.48
rcs(map, 3)     3.20e+00  2 0.20
rcs(hg, 4)      5.26e+00  3 0.15
rcs(sg, 3)      1.01e+00  2 0.60
rcs(sz, 3)      3.07e-01  2 0.86
rcs(log(ap), 5) 3.59e+00  4 0.47
bm              2.11e-06  1 1.00
GLOBAL          2.30e+01 24 0.52
```
:::
:::

To graphically examine the PH assumption use:

::: {.cell}

```{.r .cell-code}
plot(phtest, var='rx')
```

::: {.cell-output-display}
![](cox_regression_files/figure-html/unnamed-chunk-6-1.png){width=672}
:::
:::



 
# References {.unnumbered}

::: {#refs}
:::

# R-data-analysis
---
title: "General Steps to do DataAnalysis"
author: "Wajid"
date: "2025-02-22"
output: html_document
---

## General Steps to do Data Analysis
### Package installation
```{r}
pacman::p_load(tidyverse, ggplot2,dplyr,Hmisc,sjPlot,lmtest,
               multcomp,emmeans,outliers,performance,rsq,
               sjstats,nlme,effects,lmerTest,lme4,here,readxl,
               tidyr,reshape2,predictmeans, MASS,lattice,plotly,
               boot,rcompanion,DHARMa,car,descr,olsrr,purrr, conflicted,
               stringr, tibble,glmmTMB, sommer, AICcmodavg, broom.mixed, stargazer, ggstatsplot, lmeresampler, boot)
```

### Import Data
```{r}
library(readxl)
df <- read_excel("C:/Users/wumar/Desktop/R Programming/Project/Project.xlsx")
```

### Data Cleaning
```{r}
df1<-df %>% select(#select columns if needed) %>% rename(#rename columns if needed) %>% filter(#filter rows if needed) %>% mutate(#mutate columns if needed) 
```

### Description of Data
```{r}
# Use any of the following commands
descr::describe(df1) 
summary(df1)
```

### Normality check
```{r}
ggplot(data=, aes(variable)) + 
  geom_histogram() +
  theme(
    axis.title = element_text(size = 20),
    axis.text = element_text(size = 16))
```

```{r}
qqnorm(df$variable)
qqline(df$variable)
plotNormalDensity(df$variable)
```

### Data transformation
```{r}
# Log transformation (natural log)
log_data <- log(data)

# Square root transformation
sqrt_data <- sqrt(data)

# Cube root transformation
cube_root_data <- data^(1/3)
```

### Data Visualization
```{r}
ggplot(df, aes(x = #ind.var, y = #dependant var)) + geom_boxplot(outlier.shape = NA) + 
  geom_jitter(width = 0.2, height = 0) + theme_light()+
  theme(axis.text.x =element_text(angle=40, vjust=0.9, hjust = 0.9)) +   
  labs(x="dhsd", y="dsjk")
```

### Modelling
```{r}
# converting independent variables to factors
df<-df %>% 
  mutate(across(c(study_id,expt_id, country, animal_catagory, 
                  animal_sub_cat, housing_type,
                  floor_type, bedding_material, ventilation_system, 
                  outdoor_access, manure_type, 
                  climate_zone, region, manure_removal, study_scale), ~ as.factor(.x)))
```

```{r}
model1<-lmer(NH3~region+number_of_animals, data = df, na.action = na.omit))

```

```{r}
sm<-summary(model1)
sm
anova(model1)
```

### Model Diagnostics
```{r}
plot(model1, resid(., scaled=TRUE) ~ fitted(.), abline = 0,pch=16,xlab="Fitted values",ylab="Standardised residuals")
```

```{r}
qnorm(resid(model1),pch=16, col="orange")
qqline(resid(model1), col="blue", lwd=2)
```

### Mean comparison
```{r}
# General approach
emmeans(model1, pairwise ~ region, adjust = "tukey")
```
### More robust approach
### Bootstrap approach

```{r}
# Bootstrap approach
groupwiseMean(NH3~region,
              data   = df,
              conf   = 0.95,
              digits = 3,
              R      = 10000,
              boot        = TRUE,
              traditional = FALSE,
              normal      = T,
              basic       = FALSE,
              percentile  = FALSE,
              bca         = TRUE)
```

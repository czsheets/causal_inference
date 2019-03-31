---
title: "R Tutorial"
output: learnr::tutorial
runtime: shiny_prerendered
---

### The following is based on the book [Causal Inference](https://www.hsph.harvard.edu/miguel-hernan/causal-inference-book/)

##### Hernán MA, Robins JM (2019). Causal Inference. Boca Raton: Chapman & Hall/CRC, forthcoming

```{r setup, exercise = FALSE, include=FALSE}
library(learnr)
library(dplyr)
library(sqldf)
knitr::opts_chunk$set(echo = FALSE)

# Entered tables by hand with a little help from Excel

Table_1_1 = data.frame(name = c('Rheia',	'Kronos',	'Demeter',	'Hades',	'Hestia',	'Poseidon',	'Hera',	'Zeus', 'Artemis',	'Apollo',	'Leto',	'Ares',	'Athena',	'Hephaestus',	'Aphrodite',	'Cyclope',	'Persephone',	'Hermes',	'Hebe',	'Dionysus'),
y_a_0 = c(	0,	1,	0,	0,	0,	1,	0,	0,	1,	1,	0,	1,	1,	0,	0,	0,	1,	1,	1,	1),
y_a_1 = c(	1,	0,	0,	0,	0,	0,	0,	1,	1,	0,	1,	1,	1,	1,	1,	1,	1,	0,	0,	0))

Table_1_2 = data.frame(name = c(	'Rheia',	'Kronos',	'Demeter',	'Hades',	'Hestia',	'Poseidon',	'Hera',	'Zeus',	'Artemis',	'Apollo',	'Leto',	'Ares',	'Athena',	'Hephaestus',	'Aphrodite',	'Cyclope',	'Persephone',	'Hermes',	'Hebe',	'Dionysus'),
A = c(	0,	0,	0,	0,	1,	1,	1,	1,	0,	0,	0,	1,	1,	1,	1,	1,	1,	1,	1,	1),
Y = c(	0,	1,	0,	0,	0,	0,	0,	1,	1,	1,	0,	1,	1,	1,	1,	1,	1,	0,	0,	0)
)

Table_2_1 = data.frame(name = c('Rheia',	'Kronos',	'Demeter',	'Hades',	'Hestia',	'Poseidon',	'Hera',	'Zeus',	'Artemis',	'Apollo',	'Leto',	'Ares',	'Athena',	'Hephaestus',	'Aphrodite',	'Cyclope',	'Persephone',	'Hermes',	'Hebe',	'Dionysus'),
A = c(	0,	0,	0,	0,	1,	1,	1,	1,	0,	0,	0,	1,	1,	1,	1,	1,	1,	1,	1,	1),
Y = c(	0,	1,	0,	0,	0,	0,	0,	1,	1,	1,	0,	1,	1,	1,	1,	1,	1,	0,	0,	0),
Y_1 = c(	0,	1,	0,	0,	NA,	NA,	NA,	NA,	1,	1,	0,	NA,	NA,	NA,	NA,	NA,	NA,	NA,	NA,	NA),
Y_0 = c(	NA,	NA,	NA,	NA,	0,	0,	0,	1,	NA,	NA,	NA,	1,	1,	1,	1,	1,	1,	0,	0,	0))

Table_2_2 = data.frame(name = c(	'Rheia',	'Kronos',	'Demeter',	'Hades',	'Hestia',	'Poseidon',	'Hera',	'Zeus',	'Artemis',	'Apollo',	'Leto',	'Ares',	'Athena',	'Hephaestus',	'Aphrodite',	'Cyclope',	'Persephone',	'Hermes',	'Hebe',	'Dionysus'),
L = c(	0,	0,	0,	0,	0,	0,	0,	0,	1,	1,	1,	1,	1,	1,	1,	1,	1,	1,	1,	1),
A = c(	0,	0,	0,	0,	1,	1,	1,	1,	0,	0,	0,	1,	1,	1,	1,	1,	1,	1,	1,	1),
Y = c(	0,	1,	0,	0,	0,	0,	0,	1,	1,	1,	0,	1,	1,	1,	1,	1,	1,	0,	0,	0))

Table_3_1 = Table_2_2

Table_4_1 = data.frame(name = c('Rheia',	'Demeter',	'Hestia',	'Hera', 'Artemis', 'Leto', 'Athena', 'Aphrodite',	'Persephone',	'Hebe', 'Kronos', 'Hades', 'Poseidon', 'Zeus',	'Apollo',	'Ares',		'Hephaestus',	'Cyclope',	'Hermes',	'Dionysus'),
M = c(rep(1,10), rep(0,10)),               
y_a_0 = c(	0,	0,	0,	0,	1,	0,	1,	0,	1,	1,	1,	0,	1,	0,	1,	1,	0, 0,	1,	1),
y_a_1 = c(	1,	0,	0,	0,  1,	1,	1,	1,	1,  0,	0,	0,	0,  1,  0,  1,  1, 1, 0,  0))  	

Table_4_2 = data.frame(name = c('Cybele', 'Saturn', 'Ceres', 'Pluto', 'Vesta','Neptune', 'Juno', 'Jupiter', 'Diana', 'Phoebus', 'Latona', 'Mars', 'Minerva', 'Vulcan', 'Venus', 'Seneca', 'Properpina', 'Mercury', 'Juventas', 'Bacchus'),
L = c(rep(0, 8), rep(1,12)),
A = c(0,0,0,0,1,1,1,1,0,0,0,1,1,1,1,1,1,1,1,1),
Y = c(0,1,0,0,0,0,1,1,0,1,0,1,1,1,1,1,1,0,0,0))

Table_4_3 = data.frame(name = c('Rheia',	'Kronos',	'Demeter',	'Hades',	'Hestia',	'Poseidon',	'Hera',	'Zeus', 'Artemis',	'Apollo',	'Leto',	'Ares',	'Athena',	'Hephaestus',	'Aphrodite',	'Cyclope',	'Persephone',	'Hermes',	'Hebe',	'Dionysus'),
M = c(rep(0,8), rep(1,12)),               
A = c(rep(0,4),rep(1,4),rep(0,3),rep(0,9)),
Z = c(0,1,rep(0,4),rep(1,4),0,1,1,1,rep(0,6)))

Table_4_4 = data.frame(name = c('Rheia',	'Kronos',	'Demeter',	'Hades',	'Hestia',	'Poseidon',	'Hera',	'Zeus', 'Artemis',	'Apollo',	'Leto',	'Ares',	'Athena',	'Hephaestus',	'Aphrodite',	'Cyclope',	'Persephone',	'Hermes',	'Hebe',	'Dionysus'),
M = c(rep(1,10), rep(0,10)),               
A = c(rep(0,5),rep(1,5),rep(0,5),rep(1,5)),
Z = c(rep(0,4),1,0,1,1,0,1,0,0,1,1,0,1,1,1,0,1))
```

## Chapter 1
### A Definition of Causal Effect


### Causality

Run the following code to see `Table 1.1`.

```{r table_1_1, exercise=TRUE}
Table_1_1
```


#### Calculating average causal effect (pg. 5)

What is the probability that a person would die if they *had* a heart transplant?  
$Pr[{Y}^{a = 1} = 1]$

```{r y_a_1, exercise=TRUE, exercise.lines = 2}
 
```

```{r y_a_1-hint-1}
# replace `...` at the start with the function, and 
# `...` at the end with the column name you need
...(Table_1_1...)

```

```{r y_a_1-hint-2}
# replace `...` at the start with the function `mean`
...(Table_1_1$y_a_1)

```


What is the probability that a person would die if they *did not* have a heart transplant?  
$Pr[{Y}^{a = 0} = 1]$

```{r y_a_0, exercise=TRUE, exercise.lines = 2}

```

What is the *average causal effect*?  
$Pr[{Y}^{a = 1} = 1]$ - $Pr[{Y}^{a = 0} = 1]$

```{r average_effect, exercise=TRUE, exercise.lines = 2}

```

<div id="average_effect-hint">
**Hint:** You want the difference between the two results above.
</div>

```{r average_effect_question, echo=FALSE}
question("How should we interpret the results of the *average causal effect* for this intervention on this population?",
  answer("There is an average difference between groups"),
  answer("There is no difference for any group members"),
  answer("There is no average difference between groups", correct = TRUE)
)
```


|Name|${Y}^a = 0$|${Y}^a = 1$|
|:--:|:---------:|:---------:|
|Jimmy| 1 | 1 |
|Lois | 1 | 1 |
|Perry| 0 | 0 |
|Clark| 0 | 0 |
|Lex | 1 | 1 |

```{r sharp_null_question, echo=FALSE}
question("Which of the following is true about the table above?",
  answer("There no average causal effect", correct = TRUE),
  answer("The causal null hypothesis is true", correct = TRUE),
  answer("The sharp causal null hypothesis is true", correct = TRUE,
    message = "In this case there is no effect for *anyone*, so **all** of the above are true")
)
```


#### Number needed to treat (NNT)

Let's adapt `Table 1.2` to have the same column header as `Table 1.1` (take a look at `Table 1.2` first if you like). Run the code below to make a duplicate table `nnt`, then update the column names. 

Once you've done that, using `nnt`, what is the number needed to treat (`NNT`)? (pg. 8)

\[
\frac{-1}{Pr[{Y}^{a = 1} = 1] - Pr[{Y}^{a = 0} = 1]}
\]

```{r NNT, exercise=TRUE, exercise.lines = 6}
nnt <- Table_1_2
names(nnt) <- names(Table_1_1)
```

```{r NNT_question, echo=FALSE}
question("For the epidemiologically inclined, how would you interpret this result?",
  answer("You need to perform 7 operations to prevent one death", correct = TRUE),
  answer("The absolute risk ratio is 0.15", correct = TRUE),
  answer("Performing the operations has resulted in 7 times as many deaths")
)
```


### Association

Take a look `Table 1.2`. Column A is the Treatment, Y is the outcome. 

```{r table_1_2, exercise=TRUE}

```

What is the proportion of individuals who developed outcome $Y$ among those who received treatment $a$? As we start to do some data manipulation, we'll use `dplyr` to see how it works, and also `sqldf` to get the same result. In this case the `SQL` code lines up very nicely with the conditional logic.

$Pr[Y = 1|A = 1]$

```{r Y_1_A_1, exercise=TRUE}
Table_1_2 %>% 
  filter(A == 1) %>% 
  summarize(Y1 = sum(Y), Y_tot = n())
  
sqldf("select sum(case when A = 1 then Y else 0 end) Y1, 
              sum(case when A = 1 then 1 else 0 end) Y_tot
       from Table_1_2")
       
```

What is the proportion of individuals who developed outcome $Y$ among those who *did not* receive treatment $a$?

$Pr[Y = 1|A = 0]$

```{r Y_1_A_0, exercise=TRUE}


```

<div id="Y_1_A_0-hint">
**Hint:** Replace $A = 1$ from the first calculation with $A = 0$.
</div>

Are $A$ and $Y$ independent?

$Pr[Y = 1|A = 1] - Pr[Y = 1|A = 0] = 0$    

**or**  

$\frac{Pr[Y = 1|A = 1]}{Pr[Y = 1|A = 0]} = 1$

```{r independent, exercise=TRUE}
diff = Table_1_2 %>% 
  filter(A == 1) %>% 
  summarize(prob_Y = sum(Y)/n()) -
Table_1_2 %>% 
  filter(A == 0) %>% 
  summarize(prob_Y = sum(Y)/n())

diff

```

```{r association_question, echo=FALSE}
question("How should we interpret these results?",
  answer("There is no causal effect of the intervention"),
  answer("There is an association between treatment and outcome", correct = TRUE),
  answer("We have demonstrated exchangeability")
)
```

## Chapter 2
### Randomized Experiments

`Exchangeability`: risk of outcome in group A would have been the same as the risk of death in the group B had individuals in the group A received the treatment given to those in group B. The risk under the potential treatment value $a$ among the treated, $Pr[{Y}^{a} = 1|A = 1]$, equals the risk under the potential treatment value $a$ among the untreated,$Pr[{Y}^{a} = 1|A = 0]$ for both $a$ = 0 and $a$ = 1.  


##### Checking exchangeability in our data 

Combine `Table 1.2` with `Table 1.1` to include the counterfactual

```{r comb_11_21, exercise=TRUE}
exchange = data.frame(Table_2_1 %>% select(name:Y), Table_1_1 %>% select(-name))

exchange = sqldf("select a.name, a.A, a.Y, b.y_a_0, b.y_a_1
                 from Table_2_1 a
                 inner join Table_1_1 b
                 on a.name = b.name")
```

```{r, echo=FALSE}
exchange = data.frame(Table_2_1 %>% select(name:Y), Table_1_1 %>% select(-name))
```


Calculate risk of death under no treatment in the 13 treated individuals: $Pr[Y^{a=0} = 1|A = 1]$
Assign the results to `treated`

```{r death_no_tx, exercise=TRUE}
treated = exchange %>% 
  filter(A == 1) %>%
  summarize(sum(y_a_0)/n())

treated = sqldf("select sum(y_a_0)/count(*)
        from exchange
        where A = 1")
```

```{r, echo = FALSE}
treated = exchange %>% filter(A == 1) %>% summarize(sum(y_a_0)/n())
```

Check your preferred method below to see if it matches the book's answer of 7/13.
 
```{r death_no_tx_check, exercise = TRUE}
treated == 7/13

```

Calculate risk of death under no treatment in the 7 untreated individuals: $Pr[Y^{a=0} = 1|A = 0]$
Assign the result to `untreated`.

```{r death_no_tx_2, exercise=TRUE, exercise_lines = 8}

```

<div id="death_no_tx_2-hint">
**Hint:** Replace $A = 1$ from the first calculation with $A = 0$.
</div>

```{r, echo = FALSE}
untreated = exchange %>% filter(A == 0) %>% summarize(sum(y_a_0)/n())
```

Is the risk of death under no treatment greater in the treated than the untreated?
```{r risk_tx_no_tx, exercise=TRUE, exercise_lines = 8}

```

<div id="risk_tx_no_tx-hint">
**Hint:** Use $>$ to check if there is a difference in size between the two variables you just created.
</div>

```{r exchangeability_question, echo=FALSE}
question("How should we interpret these results?",
  answer("There **is** exchangeability between the treated and untreated"),
  answer("There is __not__ exchangeability between the treated and untreated", correct = TRUE),
  answer("The treated have a **better** prognosis than the untreated", correct = TRUE),
  answer("The treated have a **worse** prognosis than the untreated")
)
```

Repeat the exercise for $a = 1$. Before you start, what do you expect to find?

```{r exchange_test, exercise=TRUE, exercise_lines = 8}

```


#### Conditional randomization

For `Table 2.2`, we've added the variable $L$ representing critical $(L = 1)$ or non-critical $(L = 0)$ condition.  
Inclusion of this variable gives us two possibilities for randomization:  
* Randomization based on the values of $L$ is a `conditionally randomized experiment`  
    + randomization is *conditional* on the values of $L$   
* Randomization based on the full sample is a `marginally randomized experiment`  
    + randomization uses a single unconditional probability common to all participants  
  
```{r randomization_question, echo=FALSE}
question("Which design will produce exchangeability?",
  answer("Conditional randomization"),
  answer("Marginal randomization", correct = TRUE)
)
```


The data in `Table 2.2` could not have arisen from a marginally randomized experiment because 69% treated versus 43% untreated individuals were in critical condition. This imbalance indicates that the risk of death in the treated, had they remained untreated, would have been higher than the risk of death in the untreated.

What is the causal risk ratio for a marginally randomized experiment performed on the data in `Table 2.2`?

```{r causal_risk_ratio, exercise=TRUE, exercise_lines = 8}

```

<div id="causal_risk_ratio-hint">
**Hint:** Divide your `treated` variable by `untreated`.
</div>

##### Stratification

We can check for *effect modification* by $L$ by assessing the stratum-specific risk ratios.  
For $L = 1$ (critical condition): $\frac{Pr[Y = 1|L = 1, A = 1]}{Pr[Y = 1|L = 1, A = 0]}$

```{r stratum_1, exercise=TRUE, exercise_lines = 8}
treated = Table_2_2 %>% 
  filter(L == 1 & A == 1) %>%
  summarize(sum(Y)/n())

non_treated = Table_2_2 %>% 
  filter(L == 1 & A == 0) %>%
  summarize(sum(Y)/n())

treated/non_treated

```

For $L = 0$ (non-critical condition): $\frac{Pr[Y = 1|L = 0, A = 1]}{Pr[Y = 1|L = 0, A = 0]}$

```{r stratum_2, exercise=TRUE, exercise_lines = 8}
treated = Table_2_2 %>% 
  filter(L == 0 & A == 1) %>%
  summarize(sum(Y)/n())

non_treated = Table_2_2 %>% 
  filter(L == 0 & A == 0) %>%
  summarize(sum(Y)/n())

treated/non_treated

```

How do these results compare to the causal risk ratio?

```{r stratification_question, echo=FALSE}
question("What is the result of stratification in the example you just completed?",
  answer("Stratification produced the same risk ratios as the marginal risk ratio"),
  answer("Stratification resulted in no causal effect within the conditional strata", correct = TRUE)
)
```


### Standardization

Calculating the causal risk through standardization

$Pr[{Y}^{a=1} = 1]$/$Pr[{Y}^{a=0} = 1]$
```{r standardization, exercise=TRUE, }
# Step 1
L0 = Table_2_2 %>%
  filter(L == 0) %>% 
  mutate(prop = n()/nrow(Table_2_2)) %>%
  filter(A == 1) %>%
  summarize(prop = mean(prop), risk = sum(Y)/n())

L1 = Table_2_2 %>%
  filter(L == 1) %>% 
  mutate(prop = n()/nrow(Table_2_2)) %>%
  filter(A == 1) %>%
  summarize(prop = mean(prop), risk = sum(Y)/n())
  
all_treated = L0$risk * L0$prop + L1$risk * L1$prop

# Step 2
L0 = Table_2_2 %>%
  filter(L == 0) %>% 
  mutate(prop = n()/nrow(Table_2_2)) %>%
  filter(A == 0) %>%
  summarize(prop = mean(prop), risk = sum(Y)/n())

L1 = Table_2_2 %>%
  filter(L == 1) %>% 
  mutate(prop = n()/nrow(Table_2_2)) %>%
  filter(A == 0) %>%
  summarize(prop = mean(prop), risk = sum(Y)/n())
  
none_treated = L0$risk * L0$prop + L1$risk * L1$prop

all_treated/none_treated

```


### Inverse probability weighting

To calculate the IPW, we:  
* count each combination of $L$ and $A$ (`count`)  
* count each counterfactual value for $L$ (`cf_count`)  
* divide `cf_count/count`
```{r IPW, exercise=TRUE}
IPW = Table_2_2 %>% 
      group_by(L,A) %>%
      mutate(count = n()) %>% 
      group_by(L) %>%
      mutate(cf_count = n()) %>%
      mutate(IPW = cf_count/count) %>%
      mutate(IPW_outcome = IPW * Y)

print(IPW)
```

```{r, echo=FALSE}
IPW <- Table_2_2 %>% 
       group_by(L,A) %>%
       mutate(count = n()) %>% 
       group_by(L) %>%
       mutate(cf_count = n()) %>%
       mutate(IPW = cf_count/count) %>%
       mutate(IPW_outcome = IPW * Y)
```

What is the causal risk calculated through IPW?

```{r causal_IPW, exercise = TRUE}
IPW_outcome <- IPW %>% 
  group_by(A) %>%
  summarize(weighted_y = sum(IPW_outcome)) %>%
  data.frame(.)

IPW_outcome %>% filter(A == 0) %>% select(weighted_y)/
  IPW_outcome %>% filter(A == 1) %>% select(weighted_y)
```

```{r compare_standardization_IPW_question, echo = FALSE}
question("How do the results of standardization and IPW compare for this sample?",
  answer("Stratification produced the same causal risk result as the IPW", correct = TRUE),
  answer("Stratification produced different causal risk from the IPW")
)
```


## Chapter 3
### Observational Studies

An observational study can be conceptualized as a conditionally randomized experiment under the following three *identifiability* conditions: 
1. the values of treatment under comparison correspond to well-defined interventions that, in turn, correspond to the versions of treatment in the data ($consistency$)  
2. the conditional probability of receiving every value of treatment, though not decided by the investigators, depends only on the measured covariates ($exchangeability$)
3. the conditional probability of receiving every value of treatment is greater than zero, i.e., positive ($positivity$)

Checking exchangeability. Take a look at `Table 3.1`. 

```{r table_3_1, exercise = TRUE}
Table_3_1
```


#### Exchangeability
For exchangeability to hold, we expect to see the same percentage of treated and untreated individuals were in critical condition ($L = 1$). Is this accurate?

```{r exchangeability_3_1, exercise = TRUE}
Table_3_1 %>% 
  group_by(A) %>% 
  summarize(mean(L))

sqldf("select A, avg(L)
      from Table_3_1
      group by A")
```

```{r exchangeability_imbalance, echo = FALSE}
question("When do we expect to see this sort of imbalance?",
  answer("In a conditionally randomized experiment where investigators assigned treatment $A = 1$ with probability 0.75 to those with $L = 1$ and 0.50 to those with $L = 0$", correct = TRUE),
  answer("In a marginally randomized population-based experiment where the probability of treatment $A = 1$ is 0.75 among those with $L = 1$ and 0.50 among those with $L = 0$"),
  answer("An observational study where the probability of treatment $A = 1$ is 0.75 among those with $L = 1$ and 0.50 among those with $L = 0$", correct = TRUE)
)
```


A challenge with observational data is that there are always unmeasured variables that may influence the probability of treatment. In the example below we add an unmeasured variable $U$ that has a direct relationship on the probability of receiving treatment $A$ within the strata of $L$. The plausability of the exchangeability assumption depends on expert opinion.  

```{r unmeasured, exercise = TRUE}
Table_3_1 %>% 
  mutate(U = A) %>% 
  select(name,U,L,A,Y) %>%
  arrange(L,U,A,Y)

sqldf("select name, A as U, L, A, Y
      from Table_3_1
      order by L, U, A, Y")
```


#### Positivity

Each potential treatment must be possible to ensure positivity. This can be determined empirically; positivity would not hold if doctors always transplant a heart to individuals in critical condition $L = 1$: $Pr[A = 0|L = 1] = 0$.

Check the positivity for `Table 3.1`
```{r positivity, exercise = TRUE}
Table_3_1 %>% 
  group_by(L, A) %>% 
  summarize(total = n())

sqldf("select L,A, count(*) total
      from Table_3_1
      group by L, A")
```

```{r positivity_question, echo = FALSE}
question("Does positivity hold for Table 3.1?",
  answer("Yes, because there are people at all levels of treatment for each level of $L$", correct = TRUE),
  answer("No, because there are unequal numbers of people at different for different levels of $L$"),
  answer("Yes, because there are an equal number at each level of $A$ for $L = 1$")
)
```

```{r positivity_true_fals, echo = FALSE}
question("True or false: Positivity is only required for the variables $L$ that are required for exchangeability",
  answer("True", correct = TRUE),
  answer("False")
)
```


#### Consistency

`Consistency`: the observed outcome for every treated individual equals her outcome if she had received treatment, and that the observed outcome for every untreated individual equals her outcome if she had remained untreated: ${Y}^{a} = Y$ for every individual with $A = a$.  
* Treatments must be well specified to ensure that the treatment matches expectations/definitions
* The version of treatment described must match the treatment under study

#### The target trial

For each causal question that we intend to answer using observational data, we can describe:  
1. the target trial that we would like to, but cannot, conduct
2. how the observational data can be used to emulate that trial

```{r trial_question, echo = FALSE}
question("How can observational data be used when it cannot emulate a target trial of interest?",
  answer("Focus on non-causal prediction", correct = TRUE),
  answer("Assess the association between the intervention and the outcome, correct = TRUE"),
  answer("Assess the causal impact of the intervention on the outcome, but adjust confidence intervals to account for uncertainty")
)
```


#### Attributable fraction

The contrast between observed and counterfactual risks allows us to compute the proportion of cases that are attributable to treatment in an observational study, i.e., the proportion of cases that would not have occurred had treatment not occurred. Let's create a study where all 20
individuals in our population attended a dinner in which they were served either ambrosia ($A = 1$) or nectar ($A = 0$). The following day, 7 of the 10 individuals who received $A = 1$, and 1 of the 10 individuals who received $A = 0$, were sick.

Calculate the causal risk ratio and difference 
```{r attributable fraction, exercise = TRUE}
attrib = data.frame(id = 1:20,
                    A = rep(c(1,0), each = 10),
                    Y = c(rep(0,3), rep(1,7), rep(0,9),1))

# causal risk ratio
attrib %>% filter(A == 1) %>% summarize(mean(Y))/
  attrib %>% filter(A == 0) %>% summarize(mean(Y))

# causal risk difference
attrib %>% filter(A == 1) %>% summarize(mean(Y)) - attrib %>% filter(A == 0) %>% summarize(mean(Y))
```

```{r, echo = FALSE}
attrib = data.frame(id = 1:20,
                    A = rep(c(1,0), each = 10),
                    Y = c(rep(0,3), rep(1,7), rep(0,9),1))
```


It was later discovered that the ambrosia had been contaminated, which explains the increased risk summarized by both the causal risk ratio and the causal risk difference.

We now address the question 'what fraction of the cases was attributable to consuming ambrosia' by determining the $excess fraction/attributable fraction$. We calculate $\frac{Pr[Y = 1] -Pr[{Y}^{a=0} = 1]}{Pr[Y = 1]}$. This comes down to the difference between the observed risk and the risk if everyone had $A = 0$, standardized by the observed risk.

```{r excess-fraction, exercise = TRUE}
observed_risk = attrib %>% summarize(mean(Y))
counterfac_a_0 = attrib %>% filter(A == 0) %>% summarize(mean(Y))

(observed_risk - counterfac_a_0)/observed_risk
```

```{r excess-fraction_question, echo = FALSE}
question("How do we interpret these results?",
  answer("Those with $a = 0$ (nectar) had 75% lower risk than those with $a = 1$ (ambrosia)"),
  answer("There is an excess of 30% of the individuals who did fall ill but would not have fallen ill if everybody in the population had $a = 0$ (drank the nectar)", correct = TRUE),
  answer("75% of illnesses are attributable to treatment $a = 1$ (ambrosia)", correct = TRUE),
  answer("Only 2 cases would have occurred if everyone had received $a = 0$ (ambrosia), correct = TRUE")
)
```


## Chapter 4
### Effect Modification

#### Definition of effect modification

We started by looking at $average causal effects$. We'll now move to a more specific question: what is the average causal effect of $A$ on $Y$ in men vs. women?

We'll start by observing `Table 4.1`. To the basic counterfactual columns we see the addition of $M$ for gender.

```{r table_4_1, exercise = TRUE}
Table_4_1
```

What is the causal effect in women ($Pr[{Y}^{a = 1} = 1|M = 1]$)?

```{r table_4_1_women, exercise = TRUE}
# calculate risk
risk_tx = Table_4_1 %>% filter(M == 1) %>% summarize(mean(y_a_1))
risk_no_tx = Table_4_1 %>% filter(M == 1) %>% summarize(mean(y_a_0))

# causal risk ratio
risk_tx/risk_no_tx

# causal risk difference
risk_tx - risk_no_tx

```

What is the causal effect in men ($Pr[{Y}^{a = 1} = 1|M = 0]$)?

```{r table_4_1_men, exercise = TRUE}
# calculate risk
risk_tx = Table_4_1 %>% filter(M == 0) %>% summarize(mean(y_a_1))
risk_no_tx = Table_4_1 %>% filter(M == 0) %>% summarize(mean(y_a_0))

# causal risk ratio
risk_tx/risk_no_tx

# causal risk difference
risk_tx - risk_no_tx

```

What is the average causal effect?
```{r table_4_1_avg, exercise = TRUE}
# calculate risk
risk_tx = Table_4_1 %>% summarize(mean(y_a_1))
risk_no_tx = Table_4_1 %>% summarize(mean(y_a_0))

# causal risk ratio
risk_tx/risk_no_tx

# causal risk difference
risk_tx - risk_no_tx

```

```{r man_woman_risk_question, echo = FALSE}
question("How do we interpret these results?",
  answer("Heart transplant increases the risk of death in women", correct = TRUE),
  answer("Heart transplant decreases the risk of death in men", correct = TRUE),
  answer("There is an average causal effect for the entire population")
)
```


We say that $M$ is a modifier of the effect of $A$ on $Y$ when the average causal effect of $A$ on $Y$ varies across levels of $M$. The presence of effect measure being used. A $qualitative effect modification$ exists when average causal effects in subsets are in opposite directions.

Additive effect modification: $E[{Y}^{a=1} = {Y}^{a=1}|M = 1]  \neq E[{Y}^{a=1} = {Y}^{a=1}|M = 0]$

Multiplicative effect modification: $\frac{E[{Y}^{a=1}|M = 1]}{E[{Y}^{a=0}|M = 1]} \neq \frac{E[{Y}^{a=1}|M = 0]}{E[{Y}^{a=0}|M = 0]}$


#### Stratification to identify effect modification




## Appendix


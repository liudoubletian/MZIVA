# MZINBVA: Variational approximation for multilevel zero-inflated negative-binomial models for association analysis in microbiome surveys

# Introduction
The microbiome count data are over-dispersed with excess zeros. In addition, the observations from repeated measures are correlated. In order to deal with these characteristics, we propose an effective variational algorithm for fitting zero-inflated negative-binomial models for multilevel data and apply it to association analysis or differential abundance testing.

# Installation
```r
install.packages("devtools")  
devtools::install_github("liudoubletian/MZINBVA")  
library(MZINBVA)  
```
# Basic Usage
## simulate multilevel data
```r
sim_dat <- sim.data(sub.n, pos.n, vis.n, otu.n)
```
* `sub.n` : the number of subjects
* `pos.n` : the number of positions for each subject
* `vis.n` : the visit times for each subject
* `otu.n` : the number of OTUs


 

## differential abundance testing for each taxon
```r
est_m <- step_alg(data).  ###parameter estimation
res.t <- data.pro(est_m,nodes=3) ### Wald test based on a sandwich covariance structure
```
* `data` : a list including observed microbiome data and covariates
* `est_m` : a list of the estimated model parameters and variational parameters
* `nodes` : the required nodes for the parallel 

# Example
The following function shows how to simulate a multilevel data.  
```r
sub.n <- 10
pos.n <- 3
vis.n <- 3
otu.n <- 100
sim_dat <- sim.data(sub.n, pos.n, vis.n, otu.n)

```

Run the step_alg and data.pro function to test a taxon and return a p-value.

```r
Y_mat <- sim_dat$Y_mat
sim_dat["Y_mat"] <- NULL
data <- c(sim_dat, list(Y=Y_mat[,1]))
est_m <- step_alg(data)  ###parameter estimation
res.t <- data.pro(est_m,nodes=3) ### Wald test based on a sandwich covariance structure
```

[1] Tiantian Liu, Tao Wang. (2021) MZINBVA: Variational approximation for multilevel zero-inflated negative-binomial models for association analysis in microbiome surveys.




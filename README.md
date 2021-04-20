# MZIVA (Multilevel zero-inflated model in mricrobiome data : A variational approximation approach)

# Introduction
The microbiome data including some prominent attributes : sparsity and hierarchical structure introduced by study design. In order to deal with these features, we introduced a new framework based on variational approximation. 

# Installation
```r
install.packages("devtools")  
devtools::install_github("liudoubletian/MZIVA")  
library(MZIVA)  
```
# Basic Usage
## simulate multilevel data
```r
sub.n <- 10
pos.n <- 3
vis.n <- 3
otu.n <- 100
sim_dat <- sim.data(sub.n, pos.n, vis.n, otu.n)
```
* `sub.n` : the number of subjects
* `pos.n` : the number of positions for each subject
* `vis.n` : the visit times for each subject
* `otu.n` : the number of OTUs


 

## differential abundance testing for each taxa
```r
Y_mat <- sim_dat$Y_mat
sim_dat["Y_mat"] <- NULL
data <- c(sim_dat, list(Y=Y_mat[,1]))
est_m <- step_alg(data).  ###parameter estimation
res.t <- data.pro(est_m,nodes=3) ### Wald test based on a sandwich covariance structure
```
* `data` : a list including covariates and initial parameters
* `est_m` : a phylogenetic tree among m taxa
* `group` : a n-vector of group indicators
* `test.method` : t-test or Wilcoxon rank sum test
* `cutf` : level of significance
* `adj.m` : the adjustment methods for p-values

it returns a list of results:  
* `final.p` : the adjusted p values 
* `dif.otus` : the set of differentially abundant OTUs  
# Example
## simulation from DM
The following function shows how to simulate data from a Dirichlet-multinomial distribution.  
```r
set.seed(1)  
rand_pi <- runif(20)   
control_pi = case_pi = rand_pi/sum(rand_pi)   
control_theta = case_theta = 0.1  
group <- rep(c(0,1),each =20)  
ntree_table <- simulation_dm(p=20,seed=1, N=20,control_pi, case_pi,control_theta,case_theta)  
```

Run the eBay function to normalize the data and return a set of differentially abundant taxa.

```r
ebay.res <- eBay(otu.data=ntree_table, group=group, test.method="t", cutf=0.05,adj.m="BH")  
ebay.res  
```
## simulation from DTM
First, generate a tree randomly.
```r
p <- 40
set.seed(1)
tree <- simulate_tree(p)
```

The following function shows how to simulate data from the Dirichlet-tree multinomial model.
```r
set.seed(1)    
control_pi = case_pi = c()
for(j in (p+1):(p+tree$Nnode)){
   set.seed(j)
   random_pi <- runif(1,0.2,0.4)
   control_pi[which(tree$edge[,1]==j)] <- c(random_pi, 1-random_pi)
   case_pi[which(tree$edge[,1]==j)] <- c(random_pi, 1-random_pi)
}
control_theta = case_theta = rep(0.1, tree$Nnode) 
group <- rep(c(0,1),each =20)  
tree_table <- simulation_dtm(p=40,tree, seed=1, N=20,control_pi, case_pi,control_theta,case_theta)  
```
Run the eBay_tree function to normalize the data and return a set of differentially abundant taxa.
```r
ebay_tree.res <- eBay_tree(otu.data=tree_table, tree=tree, group=group, test.method="t", cutf=0.05,adj.m="BH")  
ebay_tree.res  
```

[1] Tiantian Liu, Tao Wang. (2020) An empirical Bayes approach to normalization and differential abundance testing for microbiome data (Under review).




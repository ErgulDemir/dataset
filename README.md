# dataDIF Dataset

## Description

1. The **dataDIF** dataset is a simulated dataset generated in **R**, including:
   - 1000 observations.
   - 20 dichotomous items with 6 random DIF items.
   - 2 grouping variables for 500 observations for each.

2. This dataset was generated for implementing and testing **Differential Item Functioning (DIF)** techniques.

3. The dataset is provided in two formats:
   - `.csv`
   - `.xlsx`  
   
   Users may download whichever format they prefer.

   Note: GitHub renders `.csv` files directly in the browser, whereas `.xlsx` files must be downloaded to be viewed.

4. The **R code used to generate the dataset** is provided below.  
   Please note that the generated output may vary depending on the **R version** and package versions.

5- The dataset can be downloaded directly using the links below:
   - CSV format: https://erguldemir.github.io/dataset/dataDIF.csv
   - Excel format: https://erguldemir.github.io/dataset/dataDIF.xlsx

---

## R Code for Generating the Dataset

### Generate dichotomous dataset with DIF for a two-group design  
*(31.10.2023)*

```r
set.seed(2310)

### Step 1. Item parameters
nitem <- 20

### Reference group
a.r <- as.matrix(rlnorm(nitem, meanlog = 0.10, sdlog = 0.10), ncol = 1)
b.r <- as.matrix(runif(nitem, -1.5, 1.5), ncol = 1)

### Focal group
difitems <- sample(1:20, 6)  # 6 random DIF items, 2 of them for uniform DIF, another 2 for crosssing non-uniform DIF and the remain 2 for non-crossing non-uniform DIF

a.f <- a.r
b.f <- b.r

# Uniform DIF
b.f[difitems[1]] <- b.f[difitems[1]] - 1.5
b.f[difitems[2]] <- b.f[difitems[2]] + 1.5

# Crossing non-uniform DIF
a.f[difitems[3]] <- a.f[difitems[3]] + 2.5
a.f[difitems[4]] <- a.f[difitems[4]] - 0.85

# Non-crossing non-uniform DIF
b.f[difitems[5]] <- b.f[difitems[5]] + 1.5
a.f[difitems[5]] <- a.f[difitems[5]] - 0.40

b.f[difitems[6]] <- b.f[difitems[6]] - 1.5
a.f[difitems[6]] <- a.f[difitems[6]] + 0.85

### Step 2. Ability parameters
sample.r <- 700
sample.f <- 300

ability.r <- as.matrix(rnorm(sample.r, mean = 0, sd = 1), ncol = 1)
ability.f <- as.matrix(rnorm(sample.f, mean = 0, sd = 1), ncol = 1)

### Step 3. Generate data
dat.r <- mirt::simdata(
  a = a.r, d = b.r, N = sample.r,
  itemtype = "dich", Theta = ability.r
)
dat.r <- data.frame(dat.r)

dat.f <- mirt::simdata(
  a = a.f, d = b.f, N = sample.f,
  itemtype = "dich", Theta = ability.f
)
dat.f <- data.frame(dat.f)

### Step 4. Combine data
group1 <- as.numeric(rep(c(0, 1), c(sample.r, sample.f)))
group2 <- factor(rep(c(0, 1), c(sample.r, sample.f)),
                 labels = c("ref", "foc"))

data.dif <- cbind(rbind(dat.r, dat.f), group1, group2)

write.csv(data.dif, "dataDIF.csv", row.names = FALSE)

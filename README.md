# 1- The "dataDIF" file is a generated dataset in R including 1000 observations, 20 dichotoumous items and 2 grouping variables.
# 2- This dataset was generated for some implementation of "Differential Item Functionning" techniques.
# 3- There are two file with the extentions of .csv and .xlsx. Users can select one to download. 
# 4- The R codes for generating the dataset are given below. Please keep this in mind; the outputs of the generation might be changed by R version.

## R Codes for generaitng the dataset
#Generate dichotom dataset with DMF for two group design (31.10.2023)

set.seed(2310)

#1#Item parameters

nitem = 20

  #for the refrence group
a.r <- as.matrix(rlnorm(nitem, meanlog = 0.10, sdlog = 0.10), ncol=1)
b.r <- as.matrix(runif(nitem, -1.5, 1.5), ncol=1)

  #for the focal group
(difitems <- sample(1:20, 6))		#random DIF items

a.f <- a.r
b.f <- b.r

b.f[difitems[1]] <- b.f[difitems[1]] - 1.5		#for uniform dif
b.f[difitems[2]] <- b.f[difitems[2]] + 1.5		

a.f[difitems[3]] <- a.f[difitems[3]] + 2.5		#for crossing-nonuniform dif
a.f[difitems[4]] <- a.f[difitems[4]] - .85	

b.f[difitems[5]] <- b.f[difitems[5]] + 1.5		#for noncrossing-nonuniform dif
a.f[difitems[5]] <- a.f[difitems[5]] - .40

b.f[difitems[6]] <- b.f[difitems[6]] - 1.5
a.f[difitems[6]] <- a.f[difitems[6]] + .85


#2# Theta parameters
sample.r = 700
sample.f = 300
ability.r <- as.matrix(rnorm(sample.r, mean = 0, sd = 1), ncol=1)
ability.f <- as.matrix(rnorm(sample.f, mean = 0, sd = 1), ncol=1)


#3#Generated data
dat.r <- mirt::simdata(a = a.r, d = b.r, N = sample.r, itemtype = 'dich',
 Theta = ability.r)
dat.r <- data.frame(dat.r)

dat.f <- mirt::simdata(a = a.f, d = b.f, N = sample.f, itemtype = 'dich',
 Theta = ability.f)
dat.f <- data.frame(dat.f)


#4#Combined data

group1 <- as.numeric(rep(c(0, 1), c(sample.r, sample.f)))
group2 <- factor(rep(c(0, 1), c(sample.r, sample.f)), labels = c("ref", "foc"))

data.dif <- cbind(rbind(dat.r, dat.f), group1, group2)

write.csv(data.dif, "dataDIF.csv")


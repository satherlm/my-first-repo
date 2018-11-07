---
title: "R_Assignment_3_LS"
author: "Leah"
date: "November 4, 2018"
output: 
  html_document: 
    keep_md: yes
---
####Question 2####
*Read in the .csv file (call it rna_counts). Write a function that calculates and can output mean expression (using the mean() function) for a given data column. Write this function so that it can work on either raw counts (as provided) or transformed to log2 values of the counts, with a logical argument that allows the user to choose whether to do this on the raw scale or log2 scale (i.e. log2 transform, then calculate the mean). Make sure all functions you write for this assignment have this argument. We often use log2 scale for RNAseq data. Demonstrate that it works on several of the columns of data.*

```r
rna_counts <- read.csv("C:/Users/Leah/Documents/Biol720/eXpress_dm_counts.csv")

get_mean_expr <- function(x, log2_transform = FALSE) {
  if (log2_transform == TRUE) {
    x <- log2(x + 1) #adding small non-zero value to all values to prevent taking log2(0)
  }
  mean(x, na.rm = TRUE)
}
```
To deal with the 0 values when transforming to log base 2, the function first adds 1 to all the values. This keeps the 0 values at 0 even when log2-transformed. This approach keeps the whole dataset intact and allows me to avoid dealing with NAs/missing values. 

Testing the function:

```r
get_mean_expr(rna_counts$F101_lg_female_hdhorn)
```

```
## [1] 1978.847
```

```r
get_mean_expr(rna_counts[[6]])
```

```
## [1] 1433.749
```

```r
get_mean_expr(rna_counts$M160_lg_male_wings, log2_transform = TRUE)
```

```
## [1] 8.806137
```

####Question 3####
*Now using the function you have written, use a loop to generate a vector of the mean expression value for each column (each sample). Make sure that your output vector have named elements (i.e. each element of the vector is associated with the names of the original columns of the data). Confirm that this function is giving you the correct answer based on what you found in question 2. Do you notice any patterns for which individuals or tissues have the highest mean expression?*

```r
mean_expr_vec <- numeric(length(rna_counts))
names(mean_expr_vec) <- c(names(rna_counts))

#Using [-1] here to skip first column
for (i in seq_along(rna_counts)[-1]) {
  mean_expr_vec[i] <- get_mean_expr(rna_counts[[i]])
}
#Removing first object in vector (corresponds to first column--non-numeric)
mean_expr_vec <- mean_expr_vec[-1]
str(mean_expr_vec)
```

```
##  Named num [1:55] 1979 1983 1584 2106 1434 ...
##  - attr(*, "names")= chr [1:55] "F101_lg_female_hdhorn" "F101_lg_female_thxhorn" "F101_lg_female_wings" "F105_lg_female_hdhorn" ...
```

Confirming that my vector contains means that match the previous answer:

```r
get_mean_expr(rna_counts$F101_lg_female_hdhorn) == mean_expr_vec["F101_lg_female_hdhorn"]
```

```
## F101_lg_female_hdhorn 
##                  TRUE
```

```r
get_mean_expr(rna_counts[[6]]) == mean_expr_vec[5]
```

```
## F105_lg_female_thxhorn 
##                   TRUE
```

Checking for any obvious patterns in highest mean expression:

```r
sort(mean_expr_vec)
```

```
## F196_sm_female_thxhorn     M257_lg_male_wings   M172_sm_male_thxhorn 
##               1025.301               1325.684               1344.019 
##  F196_sm_female_hdhorn F105_lg_female_thxhorn  F135_sm_female_hdhorn 
##               1348.898               1433.749               1452.913 
##   F101_lg_female_wings    M171_sm_male_hdhorn   M171_sm_male_thxhorn 
##               1583.904               1598.190               1621.659 
##    M172_sm_male_hdhorn M160_lg_male_genitalia   F135_sm_female_wings 
##               1713.119               1727.538               1728.483 
## F135_sm_female_thxhorn F136_sm_female_thxhorn     M171_sm_male_wings 
##               1776.309               1777.769               1825.344 
## M120_sm_male_genitalia   F105_lg_female_wings M180_lg_male_genitalia 
##               1832.780               1869.962               1922.634 
## F218_lg_female_thxhorn  F101_lg_female_hdhorn F101_lg_female_thxhorn 
##               1950.561               1978.847               1983.250 
##   F136_sm_female_wings   M180_lg_male_thxhorn    M200_sm_male_hdhorn 
##               1988.882               2003.293               2032.085 
## M171_sm_male_genitalia F197_sm_female_thxhorn  F136_sm_female_hdhorn 
##               2035.093               2047.151               2065.780 
##   F218_lg_female_wings   F197_sm_female_wings   M160_lg_male_thxhorn 
##               2074.992               2081.889               2087.583 
## M125_lg_male_genitalia   M120_sm_male_thxhorn    M120_sm_male_hdhorn 
##               2088.092               2101.163               2105.145 
##  F105_lg_female_hdhorn    M160_lg_male_hdhorn  F131_lg_female_hdhorn 
##               2105.712               2111.337               2117.847 
## M257_lg_male_genitalia     M160_lg_male_wings M172_sm_male_genitalia 
##               2170.258               2184.076               2196.101 
##     M200_sm_male_wings   F131_lg_female_wings F131_lg_female_thxhorn 
##               2203.813               2272.692               2307.529 
##  F218_lg_female_hdhorn    M257_lg_male_hdhorn    M125_lg_male_hdhorn 
##               2329.563               2361.912               2372.259 
## M200_sm_male_genitalia     M120_sm_male_wings     M125_lg_male_wings 
##               2412.038               2536.920               2559.085 
##     M172_sm_male_wings  F197_sm_female_hdhorn    M180_lg_male_hdhorn 
##               2602.351               2639.152               2670.498 
##   M257_lg_male_thxhorn   M200_sm_male_thxhorn   F196_sm_female_wings 
##               2749.767               2820.495               3067.287 
##     M180_lg_male_wings 
##               3216.476
```
Wings seem to have the highest mean expression. Indivuals M200 and M180 also have high mean expression.

####Question 4####
*Repeat this procedure (using the function you wrote, or slightly revised) that uses one of the apply family of functions to do the same as 3. Check which is faster (to compute not to write), and demonstrate how you did this.*

Vapply method:

```r
mean_expr_vec2 <- numeric(length(rna_counts))
names(mean_expr_vec2) <- c(names(rna_counts))
mean_expr_vec2 <- mean_expr_vec2[-1]

mean_expr_vec2 <- vapply(rna_counts[-1], get_mean_expr, FUN.VALUE = numeric(1))
identical(mean_expr_vec, mean_expr_vec2)
```

```
## [1] TRUE
```

Checking timing:

```r
#For loop method: 
system.time(for (i in seq_along(rna_counts)[-1]) {
  get_mean_expr(rna_counts[[i]])
}) 
```

```
##    user  system elapsed 
##    0.02    0.00    0.02
```

```r
#Vapply method:
system.time(vapply(rna_counts[-1], get_mean_expr, FUN.VALUE = numeric(1)))
```

```
##    user  system elapsed 
##    0.02    0.00    0.02
```
The vapply method is faster (0.01 elapsed vs 0.02 elapsed for the for loop).

####Question 5####
*What is a much easier way to do the operations we did in Q 3 and 4, (i.e. you don't need to write your own function) to calculate and output all of the column means? i.e. an Rish way of doing this?*


```r
q5test <- colMeans(rna_counts[-1], na.rm = TRUE)
# or, if you want log2 transform:
colMeans(log2(rna_counts[-1] + 0.000001), na.rm = TRUE)
```

```
##  F101_lg_female_hdhorn F101_lg_female_thxhorn   F101_lg_female_wings 
##               8.795177               8.800317               7.830691 
##  F105_lg_female_hdhorn F105_lg_female_thxhorn   F105_lg_female_wings 
##               8.934143               7.913235               8.112544 
##  F131_lg_female_hdhorn F131_lg_female_thxhorn   F131_lg_female_wings 
##               8.824441               9.177526               8.474668 
##   F135_sm_female_wings  F135_sm_female_hdhorn F135_sm_female_thxhorn 
##               8.019027               8.067931               8.653488 
##  F136_sm_female_hdhorn F136_sm_female_thxhorn   F136_sm_female_wings 
##               8.544339               8.622741               8.633080 
##  F196_sm_female_hdhorn F196_sm_female_thxhorn   F196_sm_female_wings 
##               8.323863               7.847838               9.504911 
##  F197_sm_female_hdhorn F197_sm_female_thxhorn   F197_sm_female_wings 
##               9.513287               8.756783               8.233058 
##  F218_lg_female_hdhorn F218_lg_female_thxhorn   F218_lg_female_wings 
##               9.210685               8.923346               8.332596 
## M120_sm_male_genitalia    M120_sm_male_hdhorn   M120_sm_male_thxhorn 
##               8.727576               8.387457               9.068303 
##     M120_sm_male_wings M125_lg_male_genitalia    M125_lg_male_hdhorn 
##               8.876723               8.457617               8.379780 
##     M125_lg_male_wings M160_lg_male_genitalia    M160_lg_male_hdhorn 
##               8.446681               8.628733               8.449260 
##   M160_lg_male_thxhorn     M160_lg_male_wings M171_sm_male_genitalia 
##               8.762399               8.461410               8.843214 
##    M171_sm_male_hdhorn   M171_sm_male_thxhorn     M171_sm_male_wings 
##               8.142591               8.214633               8.129912 
## M172_sm_male_genitalia    M172_sm_male_hdhorn   M172_sm_male_thxhorn 
##               9.020451               8.156405               7.996416 
##     M172_sm_male_wings M180_lg_male_genitalia    M180_lg_male_hdhorn 
##               8.832363               8.679673               8.603804 
##   M180_lg_male_thxhorn     M180_lg_male_wings M200_sm_male_genitalia 
##               8.298036               9.034336               9.163867 
##    M200_sm_male_hdhorn   M200_sm_male_thxhorn     M200_sm_male_wings 
##               8.242166               9.080588               8.351407 
## M257_lg_male_genitalia    M257_lg_male_hdhorn   M257_lg_male_thxhorn 
##               8.947102               8.559851               9.129658 
##     M257_lg_male_wings 
##               7.851052
```

```r
identical(q5test, mean_expr_vec2)
```

```
## [1] TRUE
```

####Question 6####
*It is common (say for a MAplot) to want the mean expression value of each given gene across all samples. Write a function to do this, and using one of the approaches from Q 3-5 generate and store these values in a variable.*

```r
get_row_mean_expr <- function(x, log2_transform = FALSE) {
  if (log2_transform == TRUE) {
    x <- log2(x + 0.000001) #adding small non-zero value to all values to prevent taking log2(0)
  }
  rowMeans(x, na.rm = TRUE)
}
by_gene_expr <- numeric(nrow(rna_counts))
by_gene_expr <- get_row_mean_expr(rna_counts[, -1])

names(by_gene_expr) <- rna_counts[,1] # For some reason, calling the function deleted the names, so am assigning names afterwards
str(by_gene_expr)
```

```
##  Named num [1:4375] 23.5 3446.9 79.5 139.2 145.1 ...
##  - attr(*, "names")= chr [1:4375] "FBpp0087248" "FBpp0293785" "FBpp0080383" "FBpp0077879" ...
```

####Question 7####
*We are very interested in what is going on in the head horns between small males and large males. Using the type of tools you have written (feel free to modify as you need, but show the new functions) calculate the mean expression for the subset of columns for large and small male head horns. Note you are calculating means on a gene by gene basis, NOT sample by sample. Now calculate the mean difference (again gene by gene) between large male and small males (for head horns). i.e. first calculate the mean expression among individuals who are large males (head horns), ditto for the small males, and calculate their difference.*


```r
#Mean expression by gene in all male head horns
male_hdhorns <- grep("_male_hdhorn", names(rna_counts))

m_hdhorns_by_gene <- numeric(nrow(rna_counts))
m_hdhorns_by_gene <- get_row_mean_expr(rna_counts[, male_hdhorns])
names(m_hdhorns_by_gene) <- rna_counts[,1]

str(m_hdhorns_by_gene)
```

```
##  Named num [1:4375] 29.4 5017.9 68.9 14.4 132 ...
##  - attr(*, "names")= chr [1:4375] "FBpp0087248" "FBpp0293785" "FBpp0080383" "FBpp0077879" ...
```

```r
#Mean expression by gene in small male head horns
sm_male_hdhorns <- grep("sm_male_hdhorn", names(rna_counts))

sm_m_hdhorns_by_gene <- numeric(nrow(rna_counts))
sm_m_hdhorns_by_gene <- get_row_mean_expr(rna_counts[, sm_male_hdhorns])
names(sm_m_hdhorns_by_gene) <- rna_counts[,1]

#Mean expression by gene in large male head horns
lg_male_hdhorns <- grep("lg_male_hdhorn", names(rna_counts))

lg_m_hdhorns_by_gene <- numeric(nrow(rna_counts))
lg_m_hdhorns_by_gene <- get_row_mean_expr((rna_counts[, lg_male_hdhorns]))
names(lg_m_hdhorns_by_gene) <- rna_counts[,1]

str(sm_m_hdhorns_by_gene)
```

```
##  Named num [1:4375] 37.5 3313.2 71.8 22.5 119 ...
##  - attr(*, "names")= chr [1:4375] "FBpp0087248" "FBpp0293785" "FBpp0080383" "FBpp0077879" ...
```

```r
str(lg_m_hdhorns_by_gene)
```

```
##  Named num [1:4375] 21.25 6722.5 66 6.25 145 ...
##  - attr(*, "names")= chr [1:4375] "FBpp0087248" "FBpp0293785" "FBpp0080383" "FBpp0077879" ...
```

```r
#Mean difference between large and small
hdhorn_diff <- numeric(nrow(rna_counts))
hdhorn_diff <- lg_m_hdhorns_by_gene - sm_m_hdhorns_by_gene
str(hdhorn_diff)
```

```
##  Named num [1:4375] -16.25 3409.25 -5.75 -16.25 26 ...
##  - attr(*, "names")= chr [1:4375] "FBpp0087248" "FBpp0293785" "FBpp0080383" "FBpp0077879" ...
```

####Question 8####
*Using the basic plot function (although you can use ggplot2 if you prefer), plot the mean expression of each gene on the X axis, and the difference in expression values on the Y axis. Now repeat, but with log2 transformed data. This is the basic idea of a MAplot.*

```r
#Question 8
plot(m_hdhorns_by_gene, hdhorn_diff, main = "Difference vs mean expression: large and small male headhorns", xlab = "Mean male headhorn expression", ylab = "Difference in expression (large and small)")
```

![](R_Assignment_3_LS_files/figure-html/unnamed-chunk-11-1.png)<!-- -->

```r
#Need to do this with log2 transformed data as well
#Lazy method (no names, no pre-allocation)
m_hdhorns_log2 <- get_row_mean_expr(rna_counts[, male_hdhorns], TRUE)
lg_m_hdhorns_log2 <- get_row_mean_expr(rna_counts[, lg_male_hdhorns], TRUE)
sm_m_hdhorns_log2 <- get_row_mean_expr(rna_counts[, sm_male_hdhorns], TRUE)
hdhorn_diff_log2 <- lg_m_hdhorns_log2 - sm_m_hdhorns_log2

plot(m_hdhorns_log2, hdhorn_diff_log2, main = "Difference vs mean headhorn expression: Log2 Transformed", xlab = "Mean male headhorn expression (log2)", ylab = "Difference in expression (large and small) (log2)")
```

![](R_Assignment_3_LS_files/figure-html/unnamed-chunk-11-2.png)<!-- -->


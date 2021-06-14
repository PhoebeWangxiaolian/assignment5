# Principle Component Aanalysis
```{r}
library(Factoshiny)
PCAshiny(D2)
```

The purpose of this project is to visualize students hints student's  based on how they perform on math problems.
<img align="center" width = "850" src="https://github.com/victoria-yang/interactive-visualization-project/blob/master/ShinyGif.gif">

## Data
The data you will be using comes from the Assistments online intelligent tutoring system (https://www.assistments.org/). It describes students working through online math problems. Each student has the following data associated with them:
- id
- prior_prob_count: How many problems a student has answered in the system prior to this session
- prior_percent_correct: The percentage of problems a student has answered correctly prior to this session
- problems_attempted: The number of problems the student has attempted in the current session
- mean_correct: The average number of correct answers a student made on their first attempt at problems in the current session
- mean_hint: The average number of hints a student asked for in the current session
- mean_attempt: The average number of attempts a student took to answer a problem in the current session
- mean_confidence: The average confidence each student has in their ability to answer the problems in the current session

## Create a correlation matrix of the relationships between the variables
<img align="center" src="https://github.com/PhoebeWangxiaolian/assignment5/blob/3099a3feab4b68ecfdaca43227a852a66e46634a/Rplot.png">
<img align="center" src="https://github.com/PhoebeWangxiaolian/assignment5/blob/3099a3feab4b68ecfdaca43227a852a66e46634a/Rplot01.png">

## Run the PCA on the new data frame
```
  pca$sdev,
  pca$sdev^2
  summary(pca)
  plot(pca, type = "lines")
```

```
 [1] 1.2825140 1.0543565 1.0245688 0.9621486 0.8556715 0.7320146 
 [1] 1.6448423 1.1116675 1.0497412 0.9257299 0.7321737 0.5358454
 
 Importance of components:
                           PC1    PC2    PC3     PC4    PC5    PC6               
 Standard deviation     1.2825   1.0544   1.0246   0.9621   0.8557   0.73201
 Proportion of Variance 0.2741   0.1853   0.1750   0.1543   0.1220   0.08931 
 Cumulative Proportion  0.2741   0.4594   0.6344   0.7887   0.9107   1.00000
 ```
 
 <img align="center" src="https://github.com/PhoebeWangxiaolian/assignment5/blob/3099a3feab4b68ecfdaca43227a852a66e46634a/PCA.png">
  We obtain 6 principal components, which you call PC1-6. Each of these explains a percentage of the total variation in the dataset. That is to say: PC1 explains 27% of the total variance. PC2 explains 19% of the variance. PC3 explains 18% of the total variance. PC4 explains 15% of the total variance. So, by knowing the position of a sample in relation to PC1, PC2, PC3, and PC4, We can get a view on where it stands in relation to other samples, as PC1, PC2, PC3, and PC4 can explain 79% of the variance.
  
  Thus, I will remove the PC5 and PC6 since they only account for a small proportion of the variance.
  
## Transformed data from PCA
```
D3 <- data.frame(pca$x)
D3 <- data.frame(D3,D1$mean_correct)

ggpairs(D3, progress = FALSE)
ggcorr(D3, method = c("everything", "pearson"))
```
<img align="center" src="https://github.com/PhoebeWangxiaolian/assignment5/blob/3099a3feab4b68ecfdaca43227a852a66e46634a/ggpairsD3.png">
<img align="center" src="https://github.com/PhoebeWangxiaolian/assignment5/blob/3099a3feab4b68ecfdaca43227a852a66e46634a/ggcorrD3.png">

  We would lost valuable information if drop PC6. Because the correlation between PC6 and mean correct is -0.393, which is not small comparing to others.
  
## Examine the eigenvectors
```
  pca$rotation
  loadings <- abs(pca$rotation) 
```

```
                             PC1         PC2         PC3        PC4          PC5
prior_prob_count      -0.26034140  0.45818753 -0.40090679 -0.6897642 -0.007142834
prior_percent_correct  0.16840319  0.81617867  0.09267306  0.2640040  0.298843852
problems_attempted    -0.45568733  0.31685183  0.36387724  0.3168141 -0.592336569
mean_hint             -0.63337594 -0.12501620 -0.08008842 -0.1122586 -0.102302115
mean_attempt          -0.54200011 -0.08510858 -0.04585364  0.3108682  0.697232132
mean_confidence        0.03581325  0.02547483 -0.83051917  0.4948890 -0.251357022
                              PC6
prior_prob_count      -0.29280482
prior_percent_correct  0.37134715
problems_attempted    -0.32911025
mean_hint              0.74412634
mean_attempt          -0.33781385
mean_confidence       -0.01452143
```
  1)PC1 indicates how much effort the student put in or how many attempts.
  2)PC2 represents the students’ prior knowledge to a certain extent.
  3)PC3 shows the high average confidence of the students, it related to the higher correctness in the question 2.
  4)PC4 means that the students’ previous attempts are related to their confidence level.
  5)PC5 represents a general attempt by students.
  6)PC6 stands for whether need tips or not.
  
## Generate a biplot
<img align="center" src="https://github.com/PhoebeWangxiaolian/assignment5/blob/3099a3feab4b68ecfdaca43227a852a66e46634a/ggbiplot.png">
  Correlation between mean_attempt vs mean_hint is highly correlated. But I don't see much here, but this isn't too surprising. PC1 and PC2 explain small percentages of the total variation, so it would be surprising if you found that they were very informative and separated the groups or revealed apparent patterns.
  
## New dataset shows students thought that a TC program was related to andother TC program
  Also in this repository is a data set collected from TC students (tc-program-combos.csv) that shows how many students thought that a TC program was related to andother TC program. Students were shown three program names at a time and were asked which two of the three were most similar. Use PCA to look for components that represent related programs. Explain why you think there are relationships between these programs.
  
  ```
library(ggplot2)
library(GGally)
library(dplyr)
```

```
DF1 <- read.csv("tc-program-combos.csv", header=TRUE)
DF2 <- DF1[,-1]
```

```
mypca <- prcomp(DF2, scale = TRUE)
attributes(mypca)
mypca$center
mypca$scale
summary(mypca)
plot(mypca, type = "l")
biplot(mypca, scale = 0)
DF3 <- data.frame(DF1, mypca$x[,c(1:2)])
ggplot(DF3, aes(PC1,PC2, col = program, fill = program)) + 
  stat_smooth(geom = "smooth")+ 
  geom_point(shape = 21, col = "black")
  ```
<img align="center" src="https://github.com/PhoebeWangxiaolian/assignment5/blob/3099a3feab4b68ecfdaca43227a852a66e46634a/mypcaplot.png">
<img align="center" src="https://github.com/PhoebeWangxiaolian/assignment5/blob/3099a3feab4b68ecfdaca43227a852a66e46634a/biplotpca.png">
<img align="center" src="https://github.com/PhoebeWangxiaolian/assignment5/blob/3099a3feab4b68ecfdaca43227a852a66e46634a/ggplot.png">
  

# Prediction-of-Cardiovascular-Disease
Team members: Bhagya ,Manisha, Nikki
Code 
R Notebook
#Team members: Bhagya,Manisha,Nikki
Project: Prediction of Cardiovascular Stroke
Problem: Which age groups and genders are more prone to heart stroke?
Data Description
#Male : 0 = Female; 1 = Male #age: age at exam time. #education: 1 = Some High School; 2 = High School or GED; 3 = Some College or Vocational School; 4 =college #currentSmoker0 = nonsmoker; 1 = smoker #cigsPerDay: number of cigarettes smoked per day (estimated average) #BPMeds0 = Not on Blood Pressure medications; 1 = Is on Blood Pressure medications #prevalentStroke #prevalentHyp #diabetes: 0 = No; 1 = Yes #totChol-mg/dL #sysBP-mmHg #diaBP-mmHg #BMI: Body Mass Index calculated as: Weight (kg) / Height(meter-squared) #heartRate-Beats/Min (Ventricular) #glucose-mg/dL #TenYearCHD
Install libraries
Hide
#install.packages("readr)
#install.packages("caret)
#install.packages("corrplot)
#install.packages("class)
#install.packages("rpart)
#install.packages("e1071)
#install.packages("ROCR")
#install.packages("pROC)
#install.packages("tidyverse)
#install.packages(cluster)
#install.packages("factoextra")
#install.packages("ISLR)
#install.packages("caTools")
#install.packages("RMySQL")
#install.packages("gridExtra")
#install.packages("PerformanceAnalytics")
#install.packages("arules")
#install.packages("arulesViz")
Load libraries
Hide
library(readr)
library(caret)
library(corrplot)
library(class)
library(rpart)
library(rpart.plot)
library(party)
library(e1071)
library(ROCR)
library(pROC)
library(tidyverse)  # data manipulation
library(cluster)    # clustering algorithms
library(factoextra) # clustering algorithms & visualization
library(ISLR)
library(caTools)
library(RMySQL)
library(gridExtra)
library(PerformanceAnalytics)
connect from AWS database
Hide
con <- dbConnect(MySQL(),
                             user = 'bigdata',
                             password = 'Best2019',
                             dbname = 'bigdata',
                             host = 'bigdata.cuvl8scby0ib.us-east-1.rds.amazonaws.com',
                             port = 3306)
Import the data
Hide
# send the query 

df <- dbSendQuery(con,"SELECT * FROM cvdata")
#fecth() converts the query results (stored on the server) to a local data frame
diseases<- fetch(df, n = -1)
summary(diseases)
      sex              age          education    currentSmoker      cigsPerDay    
 Min.   :0.0000   Min.   :32.00   Min.   :1.00   Min.   :0.0000   Min.   : 0.000  
 1st Qu.:0.0000   1st Qu.:42.00   1st Qu.:1.00   1st Qu.:0.0000   1st Qu.: 0.000  
 Median :0.0000   Median :49.00   Median :2.00   Median :0.0000   Median : 0.000  
 Mean   :0.4437   Mean   :49.56   Mean   :1.98   Mean   :0.4891   Mean   : 9.022  
 3rd Qu.:1.0000   3rd Qu.:56.00   3rd Qu.:3.00   3rd Qu.:1.0000   3rd Qu.:20.000  
 Max.   :1.0000   Max.   :70.00   Max.   :4.00   Max.   :1.0000   Max.   :70.000  
     BPMeds        prevalentStroke     prevalentHyp       diabetes          totChol     
 Min.   :0.00000   Min.   :0.000000   Min.   :0.0000   Min.   :0.00000   Min.   :113.0  
 1st Qu.:0.00000   1st Qu.:0.000000   1st Qu.:0.0000   1st Qu.:0.00000   1st Qu.:206.0  
 Median :0.00000   Median :0.000000   Median :0.0000   Median :0.00000   Median :234.0  
 Mean   :0.03036   Mean   :0.005744   Mean   :0.3115   Mean   :0.02708   Mean   :236.9  
 3rd Qu.:0.00000   3rd Qu.:0.000000   3rd Qu.:1.0000   3rd Qu.:0.00000   3rd Qu.:263.2  
 Max.   :1.00000   Max.   :1.000000   Max.   :1.0000   Max.   :1.00000   Max.   :600.0  
     sysBP           diaBP             BMI          heartRate         glucose      
 Min.   : 84.0   Min.   : 48.00   Min.   :16.00   Min.   : 44.00   Min.   : 40.00  
 1st Qu.:117.0   1st Qu.: 75.00   1st Qu.:23.00   1st Qu.: 68.00   1st Qu.: 71.00  
 Median :128.0   Median : 82.00   Median :25.00   Median : 75.00   Median : 78.00  
 Mean   :132.5   Mean   : 82.99   Mean   :25.79   Mean   : 75.73   Mean   : 81.86  
 3rd Qu.:144.0   3rd Qu.: 90.00   3rd Qu.:28.00   3rd Qu.: 82.00   3rd Qu.: 87.00  
 Max.   :295.0   Max.   :143.00   Max.   :57.00   Max.   :143.00   Max.   :394.00  
   TenYearCHD       Diseases        
 Min.   :0.0000   Length:3656       
 1st Qu.:0.0000   Class :character  
 Median :0.0000   Mode  :character  
 Mean   :0.1524                     
 3rd Qu.:0.0000                     
 Max.   :1.0000                     
Hide
print(diseases)
sex
<int>
age
<int>
education
<int>
currentSmoker
<int>
cigsPerDay
<int>
BPMeds
<int>
prevalentStroke
<int>
prevalentHyp
<int>
diabetes
<int>
totChol
<int>

0
66
1
0
0
0
0
1
0
214

0
46
3
0
0
0
0
1
0
237

0
57
1
0
0
0
0
1
0
233

1
59
1
1
3
0
0
1
1
230

0
48
2
0
0
0
0
1
0
224

0
64
1
0
0
0
0
1
0
194

1
40
1
0
0
0
0
1
0
175

1
57
1
1
3
0
0
1
0
225

1
48
2
1
17
0
0
1
0
250

0
62
1
0
0
1
0
1
0
186

Next
123456...100
Previous
1-10 of 3,656 rows | 1-10 of 17 columns
Rename the datafile
Hide
framingham_heart_disease <- diseases
Remove missing values
Hide
framingham_heart_disease <- drop_na(framingham_heart_disease)
print(framingham_heart_disease)
 
 
sex
<int>
age
<int>
education
<int>
currentSmoker
<int>
cigsPerDay
<int>
BPMeds
<int>
prevalentStroke
<int>
prevalentHyp
<int>
diabetes
<int>

1
0
66
1
0
0
0
0
1
0

2
0
46
3
0
0
0
0
1
0

3
0
57
1
0
0
0
0
1
0

4
1
59
1
1
3
0
0
1
1

5
0
48
2
0
0
0
0
1
0

6
0
64
1
0
0
0
0
1
0

7
1
40
1
0
0
0
0
1
0

8
1
57
1
1
3
0
0
1
0

9
1
48
2
1
17
0
0
1
0

10
0
62
1
0
0
1
0
1
0

Next
123456...100
Previous
1-10 of 3,656 rows | 1-10 of 17 columns
Correlation
Hide
corrr <-cor(framingham_heart_disease[5:16])
corrplot(corrr ,method="number",main = "correlatiom matrix")

Hide
library(PerformanceAnalytics)
chart.Correlation(corrr, histogram = TRUE, pch = 19)

Changing numbers to male and female
Hide
framingham_heart_disease$sex[framingham_heart_disease$sex== 1] = "Male"
framingham_heart_disease$sex[framingham_heart_disease$sex== 0] = "Female"
Exploratory Analysis
Hide
framingham_heart_disease$sex <-as.factor(framingham_heart_disease$sex)
ggplot(framingham_heart_disease,aes(x=age,y=cigsPerDay/30))+
         geom_bar(aes(fill=sex),stat="identity")+
            labs(x="Age",y="Current Smoker",title="Current smokers by age and gender")

Hide
ggplot(data = framingham_heart_disease, mapping = aes(x = as.factor(sex), y = currentSmoker,color = sex) ) + geom_bar(stat = "identity") + 
    labs(x = "Gender Group", y = "Smoker Rate", title = "Smokers by Gender") + theme(legend.position="none")

Hide
ggplot(data = framingham_heart_disease, mapping = aes(x = sex, y = cigsPerDay, color = sex)) + geom_bar(stat = "identity") + 
    labs(x = "Gender Group", y = "Cigarettes Consumption per day ", title = "Cigarettes Consumption per Day  by Gender")  +  theme(legend.position="none")

Hide
ggplot(data = framingham_heart_disease, mapping = aes(x = sex, y = totChol, color = sex)) + 
  coord_flip()+ geom_bar(stat = "identity") + labs(x = "Gender Group", y = "Cholestrol Level by Gender ", 
      title = "Cholestrol level by Gender")+  theme(legend.position="none")

Hide
ggplot(framingham_heart_disease,aes(x=age,y=totChol,color=sex))+geom_point()+facet_wrap(~sex)+
  scale_y_continuous(limits=c(0,600))

Hide
ggplot(framingham_heart_disease,aes(x = age)) + geom_histogram(bins =30,fill ="red") + 
  theme_bw() + theme_classic() +labs(title="age distribution",y="number of people")

Hide
ggplot(framingham_heart_disease,aes(x =sex, fill=sex)) + geom_bar(width = 0.2, color='black') + geom_text(stat = 'count',aes(label =..count..)) + theme_bw() + theme_classic() +labs(y="number of count",title="Gender Distribution")

Hide
ggplot(framingham_heart_disease,aes(x=age,y=totChol, color = sex))+geom_point(alpha=0.7)+facet_grid(~sex)+theme(legend.position="none")+
  labs(title = "Gender : Cholestrol Level by Age ", x = "Age Group",
                                            y = "Cholestrol Level") + theme_bw()

Hide
ggplot(framingham_heart_disease,aes(x=education,y=BMI/100,color = as.factor(framingham_heart_disease$education)))+geom_bar(stat="identity")+ 
  labs(title = "Temperatures\n", x = "TY [°C]", y = "Txxx", color = "Legend Title\n") +
  scale_color_manual(labels = c("1- High School", "2 - Assocciate Degree","3 - Bachelors Degree","4 -Masters Degree"), values = c("blue",   "red","yellow","pink")) +
  facet_grid(currentSmoker~sex) + labs(title = "Gender : BMI by Education ", x = "Education Level", y = "Body Mass Index (BMI)")

Hide
ggplot(framingham_heart_disease,aes(x=sex,y=heartRate,color=sex))+geom_boxplot()

Hide
NA
#cluster Analysis #Hierarchical clustering can’t handle big data well but K Means clustering can. This is because the time complexity of K Means is linear i.e. O(n) while that of hierarchical clustering is quadratic i.e. O(n2). #k-means clustering #given 3 clusters by age group
Hide
set.seed(20)
data <- diseases
df <- scale(data[,-17])
k2 <- kmeans(df, centers = 3, nstart = 25)
fviz_cluster(k2, data = df)

Hide
df %>%
  as_tibble() %>%
  mutate(cluster = k2$cluster,
         ages = row.names(framingham_heart_disease)) %>%
  ggplot(aes(x= age, y= heartRate,color = as.factor(cluster), label = ages)) +
  geom_text()

Hide
# checking cluster for 4,5,6 and visualizing
k3 <- kmeans(df, centers = 4, nstart = 25)
k4 <- kmeans(df, centers = 5, nstart = 25)
k5 <- kmeans(df, centers = 6, nstart = 25)

# plots to compare
p1 <- fviz_cluster(k2, geom = "point", data = df) + ggtitle("k = 3")
p2 <- fviz_cluster(k3, geom = "point",  data = df) + ggtitle("k =4")
p3 <- fviz_cluster(k4, geom = "point",  data = df) + ggtitle("k = 5")
p4 <- fviz_cluster(k5, geom = "point",  data = df) + ggtitle("k = 6")


grid.arrange(p1, p2, p3, p4, nrow = 2)

#Determining Optimal Clusters
Hide
set.seed(123)

# function to compute total within-cluster sum of square 
wss <- function(k) {
  kmeans(df, k, nstart = 25 )$tot.withinss
}

# Compute and plot wss for k = 1 to k = 15
k.values <- 1:15

# extract wss for 2-15 clusters
wss_values <- map_dbl(k.values, wss)
did not converge in 10 iterations
Hide
plot(k.values, wss_values,
       type="b", pch = 19, frame = FALSE, 
       xlab="Number of clusters K",
       ylab="Total within-clusters sum of squares")

Hide
set.seed(123)

fviz_nbclust(df, kmeans, method = "wss")

Hide
# function to compute average silhouette for k clusters
avg_sil <- function(k) {
  km.res <- kmeans(df, centers = k, nstart = 25)
  ss <- silhouette(km.res$cluster, dist(df))
  mean(ss[, 3])
}

# Compute and plot wss for k = 2 to k = 15
k.values <- 2:15

# extract avg silhouette for 2-15 clusters
avg_sil_values <- map_dbl(k.values, avg_sil)
did not converge in 10 iterations
Hide
plot(k.values, avg_sil_values,
       type = "b", pch = 19, frame = FALSE, 
       xlab = "Number of clusters K",
       ylab = "Average Silhouettes")

Hide
fviz_nbclust(df, kmeans, method = "silhouette")

#Backward elimination linear model
Hide
bk_lm <- lm(heartRate ~ .,
            data = framingham_heart_disease)
step(bk_lm,direction="backward")
Start:  AIC=16723.33
heartRate ~ sex + age + education + currentSmoker + cigsPerDay + 
    BPMeds + prevalentStroke + prevalentHyp + diabetes + totChol + 
    sysBP + diaBP + BMI + glucose + TenYearCHD + Diseases


Step:  AIC=16723.33
heartRate ~ sex + age + education + currentSmoker + cigsPerDay + 
    BPMeds + prevalentStroke + prevalentHyp + diabetes + totChol + 
    sysBP + diaBP + BMI + glucose + Diseases

                  Df Sum of Sq    RSS   AIC
- currentSmoker    1        14 348701 16722
- diabetes         1        31 348718 16722
- prevalentStroke  1        52 348739 16722
- BMI              1        58 348745 16722
- prevalentHyp     1       116 348803 16723
- sysBP            1       118 348804 16723
<none>                         348687 16723
- education        1       443 349129 16726
- BPMeds           1       560 349247 16727
- totChol          1       727 349414 16729
- age              1       831 349518 16730
- cigsPerDay       1      1283 349970 16735
- glucose          1      1458 350144 16737
- diaBP            1      1900 350587 16741
- sex              1      5785 354472 16782
- Diseases        15    131335 480022 17862

Step:  AIC=16721.48
heartRate ~ sex + age + education + cigsPerDay + BPMeds + prevalentStroke + 
    prevalentHyp + diabetes + totChol + sysBP + diaBP + BMI + 
    glucose + Diseases

                  Df Sum of Sq    RSS   AIC
- diabetes         1        31 348732 16720
- prevalentStroke  1        51 348752 16720
- BMI              1        63 348764 16720
- prevalentHyp     1       115 348816 16721
- sysBP            1       118 348819 16721
<none>                         348701 16722
- education        1       443 349143 16724
- BPMeds           1       561 349262 16725
- totChol          1       729 349429 16727
- age              1       822 349523 16728
- glucose          1      1457 350158 16735
- diaBP            1      1915 350615 16740
- cigsPerDay       1      2388 351089 16744
- sex              1      5777 354477 16780
- Diseases        15    131382 480083 17861

Step:  AIC=16719.81
heartRate ~ sex + age + education + cigsPerDay + BPMeds + prevalentStroke + 
    prevalentHyp + totChol + sysBP + diaBP + BMI + glucose + 
    Diseases

                  Df Sum of Sq    RSS   AIC
- prevalentStroke  1        51 348783 16718
- BMI              1        67 348799 16719
- prevalentHyp     1       115 348847 16719
- sysBP            1       117 348849 16719
<none>                         348732 16720
- education        1       446 349178 16723
- BPMeds           1       557 349289 16724
- totChol          1       732 349464 16726
- age              1       813 349545 16726
- diaBP            1      1907 350639 16738
- cigsPerDay       1      2384 351116 16743
- glucose          1      2711 351443 16746
- sex              1      5766 354498 16778
- Diseases        15    131352 480083 17859

Step:  AIC=16718.34
heartRate ~ sex + age + education + cigsPerDay + BPMeds + prevalentHyp + 
    totChol + sysBP + diaBP + BMI + glucose + Diseases

               Df Sum of Sq    RSS   AIC
- BMI           1        66 348850 16717
- prevalentHyp  1       111 348894 16718
- sysBP         1       118 348901 16718
<none>                      348783 16718
- education     1       439 349222 16721
- BPMeds        1       582 349365 16722
- totChol       1       735 349519 16724
- age           1       814 349598 16725
- diaBP         1      1906 350690 16736
- cigsPerDay    1      2401 351184 16741
- glucose       1      2704 351487 16745
- sex           1      5761 354545 16776
- Diseases     15    131547 480330 17858

Step:  AIC=16717.04
heartRate ~ sex + age + education + cigsPerDay + BPMeds + prevalentHyp + 
    totChol + sysBP + diaBP + glucose + Diseases

               Df Sum of Sq    RSS   AIC
- sysBP         1       118 348967 16716
- prevalentHyp  1       121 348970 16716
<none>                      348850 16717
- education     1       489 349338 16720
- BPMeds        1       579 349429 16721
- totChol       1       761 349611 16723
- age           1       830 349680 16724
- diaBP         1      2126 350975 16737
- cigsPerDay    1      2345 351195 16740
- glucose       1      2753 351603 16744
- sex           1      5697 354546 16774
- Diseases     15    131512 480362 17857

Step:  AIC=16716.27
heartRate ~ sex + age + education + cigsPerDay + BPMeds + prevalentHyp + 
    totChol + diaBP + glucose + Diseases

               Df Sum of Sq    RSS   AIC
- prevalentHyp  1       183 349150 16716
<none>                      348967 16716
- education     1       531 349498 16720
- BPMeds        1       540 349507 16720
- age           1       725 349692 16722
- totChol       1       771 349738 16722
- cigsPerDay    1      2394 351362 16739
- glucose       1      2866 351833 16744
- diaBP         1      4863 353831 16765
- sex           1      5988 354955 16777
- Diseases     15    132476 481444 17863

Step:  AIC=16716.19
heartRate ~ sex + age + education + cigsPerDay + BPMeds + totChol + 
    diaBP + glucose + Diseases

             Df Sum of Sq    RSS   AIC
<none>                    349150 16716
- BPMeds      1       368 349518 16718
- education   1       531 349682 16720
- age         1       659 349809 16721
- totChol     1       724 349874 16722
- cigsPerDay  1      2455 351605 16740
- glucose     1      2865 352015 16744
- diaBP       1      6014 355164 16777
- sex         1      6055 355206 16777
- Diseases   15    133750 482900 17872

Call:
lm(formula = heartRate ~ sex + age + education + cigsPerDay + 
    BPMeds + totChol + diaBP + glucose + Diseases, data = framingham_heart_disease)

Coefficients:
                       (Intercept)                             sexMale  
                          62.88738                            -2.81111  
                               age                           education  
                          -0.06695                            -0.37947  
                        cigsPerDay                              BPMeds  
                           0.10736                            -1.96002  
                           totChol                               diaBP  
                           0.01234                             0.13235  
                           glucose           DiseasesAortic Aneurysm\r  
                           0.03771                             2.98187  
         DiseasesAortic Stenosis\r           DiseasesAtherosclerosis\r  
                          -0.10873                             1.73035  
             DiseasesBradycardia\r            DiseasesCardiomyopathy\r  
                         -19.49384                            -0.92718  
                    DiseasesCOPD\r               DiseasesLung Cancer\r  
                           0.69296                            -0.81875  
            DiseasesLung fibrois\r             DiseasesLung fibrosis\r  
                           0.85338                            -2.28462  
    DiseasesMitral Regurgitation\r                DiseasesNo disease\r  
                           1.47785                             0.30522  
            DiseasesPericarditis\r    DiseasesPulmonary hypertension\r  
                           0.57203                            -0.89359  
             DiseasesTachycardia\r  DiseasesVentricular fibrillation\r  
                          34.19678                            -0.25584  
Forward Selection
Hide
fw_lm <- lm(heartRate ~ .,
            data = framingham_heart_disease)
nullmodel <- lm(heartRate~1, framingham_heart_disease)
step(nullmodel,direction="forward",
     scope = heartRate ~ sex + age + education + currentSmoker + cigsPerDay + 
    BPMeds + prevalentStroke + prevalentHyp + diabetes + totChol + 
    sysBP + diaBP + BMI + glucose + TenYearCHD + Diseases)
Start:  AIC=18160.24
heartRate ~ 1

                  Df Sum of Sq    RSS   AIC
+ Diseases        15    158707 366119 16874
+ sysBP            1     17884 506941 18036
+ diaBP            1     16861 507965 18043
+ prevalentHyp     1     11392 513433 18082
+ sex              1      6932 517894 18114
+ glucose          1      4941 519885 18128
+ totChol          1      4545 520281 18130
+ BMI              1      2868 521957 18142
+ education        1      2167 522659 18147
+ cigsPerDay       1      2120 522706 18147
+ diabetes         1      1953 522873 18149
+ currentSmoker    1      1336 523490 18153
<none>                         524826 18160
+ TenYearCHD       1       221 524605 18161
+ prevalentStroke  1       152 524674 18161
+ BPMeds           1        87 524738 18162
+ age              1         4 524822 18162

Step:  AIC=16873.68
heartRate ~ Diseases

                  Df Sum of Sq    RSS   AIC
+ diaBP            1    4850.2 361268 16827
+ sex              1    3874.0 362245 16837
+ sysBP            1    3697.1 362421 16839
+ glucose          1    2572.8 363546 16850
+ cigsPerDay       1    1125.6 364993 16864
+ diabetes         1    1117.4 365001 16865
+ totChol          1     888.0 365231 16867
+ prevalentHyp     1     837.4 365281 16867
+ BMI              1     671.7 365447 16869
+ currentSmoker    1     532.3 365586 16870
+ education        1     521.7 365597 16871
+ age              1     404.3 365714 16872
<none>                         366119 16874
+ prevalentStroke  1      35.8 366083 16875
+ BPMeds           1       0.7 366118 16876

Step:  AIC=16826.93
heartRate ~ Diseases + diaBP

                  Df Sum of Sq    RSS   AIC
+ sex              1    4395.9 356872 16784
+ glucose          1    2531.5 358737 16803
+ cigsPerDay       1    1289.9 359978 16816
+ diabetes         1    1128.0 360140 16818
+ currentSmoker    1     835.2 360433 16821
+ totChol          1     745.8 360523 16821
+ age              1     529.2 360739 16824
+ education        1     483.1 360785 16824
+ sysBP            1     332.3 360936 16826
+ BPMeds           1     247.6 361021 16826
<none>                         361268 16827
+ prevalentStroke  1      64.4 361204 16828
+ BMI              1      31.4 361237 16829
+ prevalentHyp     1      17.5 361251 16829

Step:  AIC=16784.17
heartRate ~ Diseases + diaBP + sex

                  Df Sum of Sq    RSS   AIC
+ cigsPerDay       1   2941.55 353931 16756
+ glucose          1   2547.63 354325 16760
+ currentSmoker    1   1460.91 355412 16771
+ diabetes         1   1172.22 355700 16774
+ age              1    880.64 355992 16777
+ totChol          1    511.06 356361 16781
+ education        1    404.47 356468 16782
+ BPMeds           1    391.09 356481 16782
<none>                         356872 16784
+ BMI              1     90.73 356782 16785
+ prevalentStroke  1     82.18 356790 16785
+ sysBP            1     71.59 356801 16785
+ prevalentHyp     1      0.80 356872 16786

Step:  AIC=16755.91
heartRate ~ Diseases + diaBP + sex + cigsPerDay

                  Df Sum of Sq    RSS   AIC
+ glucose          1   2746.84 351184 16729
+ diabetes         1   1311.49 352619 16744
+ totChol          1    533.47 353397 16752
+ education        1    420.11 353511 16754
+ BPMeds           1    388.85 353542 16754
+ age              1    343.00 353588 16754
+ BMI              1    219.83 353711 16756
<none>                         353931 16756
+ sysBP            1     85.61 353845 16757
+ prevalentStroke  1     60.34 353871 16757
+ currentSmoker    1     14.18 353917 16758
+ prevalentHyp     1      0.01 353931 16758

Step:  AIC=16729.42
heartRate ~ Diseases + diaBP + sex + cigsPerDay + glucose

                  Df Sum of Sq    RSS   AIC
+ totChol          1    518.54 350666 16726
+ BPMeds           1    452.33 350732 16727
+ age              1    435.51 350749 16727
+ education        1    386.50 350798 16727
<none>                         351184 16729
+ BMI              1    146.88 351037 16730
+ prevalentStroke  1     71.41 351113 16731
+ diabetes         1     31.05 351153 16731
+ sysBP            1     19.36 351165 16731
+ currentSmoker    1     13.89 351170 16731
+ prevalentHyp     1      0.93 351183 16731

Step:  AIC=16726.02
heartRate ~ Diseases + diaBP + sex + cigsPerDay + glucose + totChol

                  Df Sum of Sq    RSS   AIC
+ age              1    607.42 350058 16722
+ BPMeds           1    451.00 350215 16723
+ education        1    405.98 350260 16724
<none>                         350666 16726
+ BMI              1    124.74 350541 16727
+ prevalentStroke  1     67.58 350598 16727
+ diabetes         1     26.88 350639 16728
+ sysBP            1     12.01 350654 16728
+ currentSmoker    1     10.79 350655 16728
+ prevalentHyp     1      0.01 350666 16728

Step:  AIC=16721.68
heartRate ~ Diseases + diaBP + sex + cigsPerDay + glucose + totChol + 
    age

                  Df Sum of Sq    RSS   AIC
+ education        1    540.00 349518 16718
+ BPMeds           1    376.59 349682 16720
<none>                         350058 16722
+ sysBP            1    123.95 349934 16722
+ BMI              1    118.91 349939 16722
+ prevalentStroke  1     61.95 349996 16723
+ diabetes         1     36.05 350022 16723
+ currentSmoker    1     20.91 350037 16724
+ prevalentHyp     1     10.65 350047 16724

Step:  AIC=16718.04
heartRate ~ Diseases + diaBP + sex + cigsPerDay + glucose + totChol + 
    age + education

                  Df Sum of Sq    RSS   AIC
+ BPMeds           1    368.01 349150 16716
<none>                         349518 16718
+ sysBP            1     88.90 349429 16719
+ prevalentStroke  1     70.52 349448 16719
+ BMI              1     66.58 349452 16719
+ diabetes         1     31.31 349487 16720
+ currentSmoker    1     18.68 349499 16720
+ prevalentHyp     1     11.14 349507 16720

Step:  AIC=16716.19
heartRate ~ Diseases + diaBP + sex + cigsPerDay + glucose + totChol + 
    age + education + BPMeds

                  Df Sum of Sq    RSS   AIC
<none>                         349150 16716
+ prevalentHyp     1   182.677 348967 16716
+ sysBP            1   179.667 348970 16716
+ BMI              1    78.813 349071 16717
+ prevalentStroke  1    45.365 349105 16718
+ diabetes         1    35.083 349115 16718
+ currentSmoker    1    18.259 349132 16718

Call:
lm(formula = heartRate ~ Diseases + diaBP + sex + cigsPerDay + 
    glucose + totChol + age + education + BPMeds, data = framingham_heart_disease)

Coefficients:
                       (Intercept)           DiseasesAortic Aneurysm\r  
                          62.88738                             2.98187  
         DiseasesAortic Stenosis\r           DiseasesAtherosclerosis\r  
                          -0.10873                             1.73035  
             DiseasesBradycardia\r            DiseasesCardiomyopathy\r  
                         -19.49384                            -0.92718  
                    DiseasesCOPD\r               DiseasesLung Cancer\r  
                           0.69296                            -0.81875  
            DiseasesLung fibrois\r             DiseasesLung fibrosis\r  
                           0.85338                            -2.28462  
    DiseasesMitral Regurgitation\r                DiseasesNo disease\r  
                           1.47785                             0.30522  
            DiseasesPericarditis\r    DiseasesPulmonary hypertension\r  
                           0.57203                            -0.89359  
             DiseasesTachycardia\r  DiseasesVentricular fibrillation\r  
                          34.19678                            -0.25584  
                             diaBP                             sexMale  
                           0.13235                            -2.81111  
                        cigsPerDay                             glucose  
                           0.10736                             0.03771  
                           totChol                                 age  
                           0.01234                            -0.06695  
                         education                              BPMeds  
                          -0.37947                            -1.96002  
#Split the data
Hide
set.seed(123)   #  set seed to ensure you always have same random numbers generated
sample = sample.split(framingham_heart_disease$TenYearCHD,SplitRatio = 0.75) # splits the data in the ratio mentioned in SplitRatio. After splitting marks these rows as logical TRUE and the the remaining are marked as logical FALSE
train1 =subset(framingham_heart_disease,sample ==TRUE) # creates a training dataset named train1 with rows which are marked as TRUE
test1=subset(framingham_heart_disease, sample==FALSE)
Linear regression
Hide
fit_lm <- lm(heartRate ~ sysBP + sex + cigsPerDay + glucose + diaBP + age + 
               totChol + education + BPMeds + prevalentHyp, data=train1)
summary(fit_lm)

Call:
lm(formula = heartRate ~ sysBP + sex + cigsPerDay + glucose + 
    diaBP + age + totChol + education + BPMeds + prevalentHyp, 
    data = train1)

Residuals:
    Min      1Q  Median      3Q     Max 
-30.156  -7.938  -1.031   6.641  61.064 

Coefficients:
              Estimate Std. Error t value Pr(>|t|)    
(Intercept)  60.039242   2.606115  23.038  < 2e-16 ***
sysBP         0.054630   0.019173   2.849 0.004413 ** 
sexMale      -3.293742   0.474474  -6.942 4.82e-12 ***
cigsPerDay    0.135842   0.019838   6.847 9.25e-12 ***
glucose       0.044821   0.008885   5.044 4.85e-07 ***
diaBP         0.104868   0.030834   3.401 0.000681 ***
age          -0.144909   0.029780  -4.866 1.20e-06 ***
totChol       0.019768   0.005270   3.751 0.000180 ***
education    -0.741909   0.218576  -3.394 0.000698 ***
BPMeds       -4.287751   1.306152  -3.283 0.001041 ** 
prevalentHyp  1.191857   0.676353   1.762 0.078150 .  
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 11.48 on 2731 degrees of freedom
Multiple R-squared:  0.08918,   Adjusted R-squared:  0.08584 
F-statistic: 26.74 on 10 and 2731 DF,  p-value: < 2.2e-16
Hide
equation1=function(x){
  coef(fit_lm)[2]*x+coef(fit_lm)[1]
  }
equation2=function(x){
  coef(fit_lm)[2]*x+coef(fit_lm)[1]+coef(fit_lm)[3]
  }

ggplot(framingham_heart_disease,aes(y=heartRate,x=totChol,color= sex))+geom_point()+
        stat_function(fun=equation1,geom="line",color=scales::hue_pal()(2)[1])+
        stat_function(fun=equation2,geom="line",color=scales::hue_pal()(2)[2])+labs(x="total cholesterol",y="Heart rate",title = "Heart rate for males and females")

Hide
#predict on test data
pred_lm <- predict(fit_lm,test1)
results_lm <- data.frame(Actual =test1$heartRate,Prediction =pred_lm)
ggplot(data= results_lm,aes(x= Actual,y=Prediction))+geom_point()+geom_abline()

Hide
#predict on new data
newdata <- data.frame(totChol = 650,sysBP= 300, cigsPerDay= 70,glucose = 400,diaBP= 150,
                      sex = "Female",age =50,education =4,BPMeds = 1,prevalentHyp= 1)
 newdata1 <- data.frame(totChol= 650, sysBP= 300,cigsPerDay=70,glucose= 400, diaBP= 150,
                        sex ="Male",age= 50,education=4,BPMeds = 1,prevalentHyp=1)
result <- predict(fit_lm,newdata )
result1 <- predict(fit_lm,newdata1)
cat("The Predicted heartrate for female is" , result,"\n")
The Predicted heartrate for female is 119.1359 
Hide
cat("The Predicted heartrate for male is" , result1)
The Predicted heartrate for male is 115.8421
Classification Analysis
Knn algorithm
Hide
set.seed(123)
#create a random number equal 90% of total number of rows
ran <- sample(1:nrow(framingham_heart_disease),0.75 * nrow(framingham_heart_disease))
 
##the normalization function is created
nor <-function(x) { (x -min(x))/(max(x)-min(x))   }
 
##normalization function is created
df_nor <- as.data.frame(lapply(framingham_heart_disease[,c(10,11,12,13,14,15)], nor))
##training dataset extracted
df_train <- df_nor[ran,]

##test dataset extracted
df_test <- df_nor[-ran,]
##the 2nd column of training dataset because that is what we need to predict about testing dataset
##also convert ordered factor to normal factor
df_target <- as.factor(framingham_heart_disease[ran,16])
 
##the actual values of 2nd couln of testing dataset to compaire it with values that will be predicted
##also convert ordered factor to normal factor
test_target <- as.factor(framingham_heart_disease[-ran,16])
##run knn function
 
pred_knn <- knn(df_train,df_test,cl=df_target,k=20)
 
##create the confucion matrix
mat_knn <- table(test_target,pred_knn,dnn=c("Actual","Prediction"))
mat_knn
      Prediction
Actual   0   1
     0 784   3
     1 124   3
Hide
results_knn <- data.frame(Actual =test_target,Prediction =pred_knn)
#predict on new data
tptn <- nrow(subset(results_knn,Actual == Prediction))
total_size <- length(test_target)
#accuracy
knn_accuracy <- tptn/total_size
cat("The accuracy of Knn model is:",knn_accuracy,"\n")
The accuracy of Knn model is: 0.8610503 
Hide
#precision
tp <- nrow(subset(results_knn,Actual == 1,Prediction == 1))
fp <- nrow(subset(results_knn,Actual == 0,Prediction == 1))
fn <- nrow(subset(results_knn,Actual == 1,Prediction == 0))
knn_precision <- tp/(tp+fp)
cat("The Precision of Knn model is:",knn_precision,"\n")
The Precision of Knn model is: 0.1389497 
Hide
#recall
knn_recall <- tp/(tp+fn)
cat("The recall of Knn model is:",knn_recall,"\n")
The recall of Knn model is: 0.5 
Hide
knn <- ifelse(results_knn$Actual == results_knn$Prediction,1,0)
knn <- as.data.frame(knn)
knn$models <- paste("Knn_model")
colnames(knn)[1]<- "Accuracy"
Features By Importance
Hide
framingham_heart_disease$TenYearCHD <- as.factor(framingham_heart_disease$TenYearCHD)
# prepare training scheme
control <- trainControl(method="repeatedcv", number=10, repeats=3)
# train the model
model <- train(data=framingham_heart_disease,TenYearCHD ~ ., method="lvq", preProcess="scale",
               trControl=control)
# estimate variable importance
library(mlbench)
#importance <- varImp(model)
# summarize importance
#print(importance)
# plot importance
#plot(importance)
Logistic regression
Hide
fit_glm <- glm(TenYearCHD~ age+sysBP+prevalentHyp+diaBP+totChol+BMI+glucose, data = train1, family="binomial"(link="logit"))
pred_glm <- predict(fit_glm,test1, type = "response")
prediction_glm <- ifelse(pred_glm > 0.5,1,0)
mat_glm <-table(test1$TenYearCHD,prediction_glm,dnn=c("Actual","Prediction"))
results_glm <- data.frame(Actual1 =test1$TenYearCHD,Prediction1 =prediction_glm)
tptn1 <- nrow(subset(results_glm,Actual1 == Prediction1))
total_size1 <- nrow(test1)
#accuracy
glm_accuracy <- tptn1/total_size1
cat("The accuracy of logistic model is:",glm_accuracy,"\n")
The accuracy of logistic model is: 0.8468271 
Hide
#precision
tp1 <- nrow(subset(results_glm,Actual1== 1,Prediction1==1))
fp1 <- nrow(subset(results_glm,Actual1 == 0,Prediction1 == 1))
fn1 <- nrow(subset(results_glm,Actual1 == 1,Prediction1 == 0))
glm_precision <- tp1/(tp1+fp1)
cat("The Precision of logistic model is:",glm_precision,"\n")
The Precision of logistic model is: 0.1520788 
Hide
#recall
glm_recall <- tp1/(tp1+fn1)
cat("The recall of logistic model is:",glm_recall,"\n")
The recall of logistic model is: 0.5 
Hide
glm <- ifelse(results_glm$Actual1 == results_glm$Prediction1,1,0)
glm <- as.data.frame(glm)
glm$models <- paste("Glm_model")
colnames(glm)[1]<- "Accuracy"
rocCurve_logit <- roc(response = test1$TenYearCHD,
               predictor = prediction_glm)
Setting levels: control = 0, case = 1
Setting direction: controls < cases
Hide
auc_curve <- auc(rocCurve_logit)

plot(rocCurve_logit,legacy.axes = TRUE,print.auc = TRUE,col="red",main="ROC(Logistic Regression)")

Hide
library(ModelGood)
plot(Roc(list(fit_glm),data=test1))

decision tree
Hide
prop.table(table(train1$TenYearCHD))

        0         1 
0.8475565 0.1524435 
Hide
prop.table(table(test1$TenYearCHD))

        0         1 
0.8479212 0.1520788 
#in both cases the risk of heart stroke is 14%
#changing the TenyearCHD from 0 to no and 1 to yes
Hide
framingham_heart_disease$tenyrCHD <- ifelse(framingham_heart_disease$TenYearCHD == 1, "yes","no")
framingham_heart_disease <- framingham_heart_disease[,-16]
framingham_heart_disease$tenyrCHD <- as.factor(framingham_heart_disease$tenyrCHD)
Hide
framingham_heart <- framingham_heart_disease
Hide
set.seed(123)
#split the data
sample1 <- sample.split(framingham_heart$tenyrCHD,SplitRatio = 0.75)
train <- subset(framingham_heart,sample1== TRUE)
test <- subset(framingham_heart,sample1== FALSE)
#decision tree
Hide
fit_dc <- ctree(tenyrCHD~ age+sex+education+currentSmoker+cigsPerDay+BPMeds+prevalentStroke+
                  diabetes+sysBP+prevalentHyp+diaBP+totChol+BMI+
                  heartRate+glucose, data = train)
plot(fit_dc)

Hide
pred_dc <-predict(fit_dc, test,type= "response")
#confusion matrix
mat_dc <- table(test$tenyrCHD, pred_dc,dnn=c("Actual","Prediction"))
results_ct <- data.frame(Actual2 = test$tenyrCHD,Prediction2 = pred_dc)
tptn2 <- nrow(subset(results_ct,Actual2== Prediction2))
total_size2 <- nrow(test)
#accuracy
ct_accuracy <- tptn2/total_size2
cat("The accuracy of CTree model is:",ct_accuracy,"\n")
The accuracy of CTree model is: 0.8380744 
Hide
#precision
tp2 <- nrow(subset(results_ct,Actual2=="yes",Prediction2=="yes"))
fp2 <- nrow(subset(results_ct,Actual2 == "no",Prediction2 == "yes"))
fn2 <- nrow(subset(results_ct,Actual2 == "yes",Prediction2 == "no"))
ct_precision <- tp2/(tp2+fp2)
cat("The Precision of CTree model is:",ct_precision,"\n")
The Precision of CTree model is: 0.1520788 
Hide
#recall
ct_recall <- tp2/(tp2+fn2)
cat("The recall of CTree model is:",ct_recall,"\n")
The recall of CTree model is: 0.5 
Hide
ct <- ifelse(results_ct$Actual2 == results_ct$Prediction2,1,0)
ct <-as.data.frame(ct)
ct$models <- paste("ctree_model")
colnames(ct)[1] <- "Accuracy"
Naive bayes
Hide
nb_model<- naiveBayes(tenyrCHD~.,data =train,laplace = 1)
pred_nb <- predict(nb_model,test)
mat_nb <- table(test$tenyrCHD,pred_nb,dnn=c("Actual","Prediction"))
results_nb <- data.frame(Actual3 = test$tenyrCHD,Prediction3 = pred_nb)
tptn3 <- nrow(subset(results_nb,Actual3== Prediction3))
total_size3 <- nrow(test)
#accuracy
nb_accuracy <- tptn3/total_size3
cat("The accuracy of Naivebayes model is:",nb_accuracy,"\n")
The accuracy of Naivebayes model is: 0.9463895 
Hide
#precision
tp3 <- nrow(subset(results_nb,Actual3=="yes",Prediction3=="yes"))
fp3 <- nrow(subset(results_nb,Actual3 == "no",Prediction3 == "yes"))
fn3 <- nrow(subset(results_nb,Actual3 == "yes",Prediction3 == "no"))
nb_precision <- tp3/(tp3+fp3)
cat("The Precision of Naivebayes model is:",nb_precision,"\n")
The Precision of Naivebayes model is: 0.1520788 
Hide
#recall
nb_recall <- tp3/(tp3+fn3)
cat("The recall of Naivebayes model is:",nb_recall,"\n")
The recall of Naivebayes model is: 0.5 
Hide
nb <- ifelse(results_nb$Actual3 == results_nb$Prediction3,1,0)
nb<- as.data.frame(nb)
nb$models <- paste("Nb_model")
colnames(nb)[1]<-"Accuracy"
Hypothesis testing
Hide
accuracies <- rbind(knn,glm,nb,ct)
annova <- aov(Accuracy~models,data=accuracies)
summary(annova)
              Df Sum Sq Mean Sq F value   Pr(>F)    
models         3    6.8  2.2648   20.77 2.45e-13 ***
Residuals   3652  398.3  0.1091                     
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
Plot (Accuracies for each model)
Hide
accu <- data.frame(c(models= "K-nearest neighbor","Logistic","Naivebayes","Decision tree"),
                   c(knn_accuracy,glm_accuracy,nb_accuracy,ct_accuracy))
colnames(accu)[1]<-"models"
colnames(accu)[2]<-"Accuracy"
ggplot(data=accu,mapping=aes(x=models,y=Accuracy, fill = models))+geom_bar(stat="identity")+
  labs(x="Models",y="Accuracies",title = "Accuracy vs Models")

Hide
NA
Results
Hide
#knn results percentage
knn_data <-as.data.frame(pred_knn)
test$predicted_knn <- knn_data$pred_knn
ksub1<- subset(test,age>=30 & age<=45 )
ksub1$agegroup <-paste("30-45")
ksub2<- subset(test,age>45 & age<=60 )
ksub2$agegroup <-paste("45-60")
ksub3<- subset(test,age>60 & age<=75 )
ksub3$agegroup <-paste("60-75")
knn_pt <- rbind(ksub1,ksub2,ksub3)
kpt_pred1 <- subset(knn_pt,predicted_knn==1)
ggplot(kpt_pred1,aes(x=agegroup,fill=sex))+geom_bar(position = "dodge")

Hide
#logistic percentage results
glm_data <-as.data.frame(prediction_glm)
test$predicted_glm <- glm_data$prediction_glm
gsub1<- subset(test,age>=30 & age<=45 )
gsub1$agegroup <-paste("30-45")
gsub2<- subset(test,age>45 & age<=60 )
gsub2$agegroup <-paste("45-60")
gsub3<- subset(test,age>60 & age<=75 )
gsub3$agegroup <-paste("60-75")
glm_pt <- rbind(gsub1,gsub2,gsub3)
gpt_pred1 <- subset(glm_pt,predicted_glm==1)
ggplot(gpt_pred1,aes(x=agegroup,fill=sex))+geom_bar(position = "dodge")

Hide
#decision tree percentage results
dc_data <-as.data.frame(pred_dc)
test$predicted_dc <- dc_data$pred_dc
sub1<- subset(test,age>=30 & age<=45 )
sub1$agegroup <-paste("30-45")
sub2<- subset(test,age>45 & age<=60 )
sub2$agegroup <-paste("45-60")
sub3<- subset(test,age>60 & age<=75 )
sub3$agegroup <-paste("60-75")
dc_pt <- rbind(sub1,sub2,sub3)
dpt_pred1 <- subset(dc_pt,predicted_dc=="yes")
ggplot(dpt_pred1,aes(x=agegroup,fill=sex))+geom_bar(position = "dodge")

Hide
#naivebayes results percentage 
nB_data <-as.data.frame(pred_nb)
test$predicted_nb <- nB_data$pred_nb
nBsub1<- subset(test,age>=30 & age<=45 )
nBsub1$agegroup <-paste("30-45")
nBsub2<- subset(test,age>45 & age<=60 )
nBsub2$agegroup <-paste("45-60")
nBsub3<- subset(test,age>60 & age<=75 )
nBsub3$agegroup <-paste("60-75")
nB_pt <- rbind(nBsub1,nBsub2,nBsub3)
nBpt_pred1 <- subset(nB_pt,predicted_nb=="yes")
#outcomenB<- nrow(nBpt_act1)/nrow(nB_pt)
ggplot(nBpt_pred1,aes(x=agegroup,fill=sex))+geom_bar(position = "dodge")

Performance
Hide
results_ct$Prediction2 <- ifelse(results_ct$Prediction2 == "yes",1,0)
# List of predictions
preds_list <- list(results_knn$Prediction,results_glm$Prediction1, results_ct$Prediction2, results_nb$Prediction3)

# List of actual values (same for all)
m <- length(preds_list)
actuals_list <- rep(list(test$tenyrCHD), m)

# Plot the ROC curves
pred <- prediction(preds_list, actuals_list)
rocs <- performance(pred, "tpr", "fpr")
plot(rocs, col = as.list(1:m), main = "Test Set ROC Curves")
legend(x = "bottomright", 
       legend = c("K-Nearest Neighbor","Logistic Regression","Decision Tree", "Naive Bayes"),fill=c("yellow","blue","green","red"))

Hide
#ROC curves are commonly used to characterize the sensitivity/specificity tradeoffs for a binary classifier. ... The ROC curve plots true positive rate against false positive rate.
Text Analysis
Hide
library(arules)
library(arulesViz)
texthealth <- framingham_heart_disease
texthealth$education[texthealth$education== 1] = "Some High School"
texthealth$education[texthealth$education== 2] = "High School or GED"
texthealth$education[texthealth$education== 3] = 
  "Some College or Vocational School"
texthealth$education[texthealth$education== 4] = "college"
texthealth$currentSmoker[texthealth$currentSmoker== 0] = "nonsmoker"
texthealth$currentSmoker[texthealth$currentSmoker== 1] = "smoker"
texthealth$BPMeds[texthealth$BPMeds== 0] = "No BP Medictaion"
texthealth$BPMeds[texthealth$BPMeds== 1] = "BP Medictaion"
texthealth$prevalentStroke[texthealth$prevalentStroke== 0] = "No previous stroke"
texthealth$prevalentStroke[texthealth$prevalentStroke== 1] = "previous stroke"
texthealth$prevalentHyp[texthealth$prevalentHyp== 0] = "No Hyp"
texthealth$prevalentHyp[texthealth$prevalentHyp== 1] = "Hyp"
texthealth$diabetes[texthealth$diabetes== 0] = "No diabetes"
texthealth$diabetes[texthealth$diabetes== 1] = "diabetes"
library(tm)
texthealth <- subset(texthealth,tenyrCHD=="yes")
dis.corpus <- Corpus(VectorSource(texthealth$Diseases))
dis.corpus <- tm_map(dis.corpus, removePunctuation)
transformation drops documents
Hide
dis.corpus <- tm_map(dis.corpus, tolower)
transformation drops documents
Hide
dis.corpus <- tm_map(dis.corpus, function(x) removeWords(x, stopwords("english")))
transformation drops documents
Hide
tdm <- TermDocumentMatrix(dis.corpus)
library(quanteda)
#install.packages("tm")
inspect(tdm)
<<TermDocumentMatrix (terms: 12, documents: 557)>>
Non-/sparse entries: 641/6043
Sparsity           : 90%
Maximal term length: 15
Weighting          : term frequency (tf)
Sample             :
                 Docs
Terms             1 10 2 3 4 5 6 7 8 9
  aneurysm        1  1 1 1 1 1 1 1 1 1
  aortic          1  1 1 1 1 1 1 1 1 1
  atherosclerosis 0  0 0 0 0 0 0 0 0 0
  cardiomyopathy  0  0 0 0 0 0 0 0 0 0
  copd            0  0 0 0 0 0 0 0 0 0
  fibrois         0  0 0 0 0 0 0 0 0 0
  fibrosis        0  0 0 0 0 0 0 0 0 0
  hypertension    0  0 0 0 0 0 0 0 0 0
  lung            0  0 0 0 0 0 0 0 0 0
  pulmonary       0  0 0 0 0 0 0 0 0 0
Hide
library(wordcloud)
library(RColorBrewer)
m <- as.matrix(tdm)
v <- sort(rowSums(m),decreasing=TRUE)
disdata <- data.frame(word = names(v),freq=v)
pal <- brewer.pal(5, "BuGn")
pal <- pal[-(1:2)]
png("wordcloud.png", width=200,height=100)
plot text analysis
Hide
wordcloud(disdata$word,disdata$freq, scale=c(3,.3),min.freq=2,max.words=50, random.order=T,
          rot.per=.15, colors=pal, vfont=c("sans serif","plain"))

#another approch of text analysis
Hide

dfCorpus <- SimpleCorpus(VectorSource(texthealth$Diseases))
#View(corpus)
# 1. Stripping any extra white space:
dfCorpus <- tm_map(dfCorpus, stripWhitespace)
transformation drops documents
Hide
# 2. Transforming everything to lowercase
dfCorpus <- tm_map(dfCorpus, content_transformer(tolower))
transformation drops documents
Hide
# 3. Removing numbers 
dfCorpus <- tm_map(dfCorpus, removeNumbers)
transformation drops documents
Hide
# 4. Removing punctuation
dfCorpus <- tm_map(dfCorpus, removePunctuation)
transformation drops documents
Hide
# 5. Removing stop words
dfCorpus <- tm_map(dfCorpus, removeWords, stopwords("english"))
transformation drops documents
Hide
dfCorpus[[1]]$content
[1] "aortic aneurysm "
Hide
dfCorpus <- tm_map(dfCorpus, stemDocument)
transformation drops documents
Hide
dfCorpus[[1]]$content
[1] "aortic aneurysm"
Hide
DTM <- DocumentTermMatrix(dfCorpus)
#View(DTM)
inspect(DTM)
<<DocumentTermMatrix (documents: 557, terms: 12)>>
Non-/sparse entries: 641/6043
Sparsity           : 90%
Maximal term length: 14
Weighting          : term frequency (tf)
Sample             :
    Terms
Docs aneurysm aortic atherosclerosi cardiomyopathi copd fibroi fibrosi hypertens lung pulmonari
  1         1      1              0              0    0      0       0         0    0         0
  10        1      1              0              0    0      0       0         0    0         0
  2         1      1              0              0    0      0       0         0    0         0
  3         1      1              0              0    0      0       0         0    0         0
  4         1      1              0              0    0      0       0         0    0         0
  5         1      1              0              0    0      0       0         0    0         0
  6         1      1              0              0    0      0       0         0    0         0
  7         1      1              0              0    0      0       0         0    0         0
  8         1      1              0              0    0      0       0         0    0         0
  9         1      1              0              0    0      0       0         0    0         0
Hide
sums <- as.data.frame(colSums(as.matrix(DTM)))
library(tibble)
library(dplyr)
sums <- rownames_to_column(sums) 
colnames(sums) <- c("term", "count")
sums <- arrange(sums, desc(count))
head <- sums[1:5,]
wordcloud(words = head$term, freq = head$count, min.freq = 1000,
  max.words=1000, random.order=FALSE, rot.per=0.35, 
  colors=brewer.pal(8, "Dark2"))

#another approach of naive bayes
Hide
heart_disease <- framingham_heart_disease[,-16]

heart_disease$heartstroke <- ifelse(heart_disease$sysBP > 130 & heart_disease$diaBP > 80,
                                    "Stage1Hypertension","no stroke")
heart_disease$heartstroke <- as.factor(heart_disease$heartstroke)
sample1 <- sample.split(heart_disease$heartstroke,SplitRatio = 0.75)
trainingset <- subset(heart_disease,sample1 == TRUE)
testset <- subset(heart_disease,sample1 == FALSE)

fit_nb <- naiveBayes(heartstroke ~ ., data = trainingset)
fit_nb

Naive Bayes Classifier for Discrete Predictors

Call:
naiveBayes.default(x = X, y = Y, laplace = laplace)

A-priori probabilities:
Y
         no stroke Stage1Hypertension 
         0.6119621          0.3880379 

Conditional probabilities:
                    sex
Y                       Female      Male
  no stroke          0.5458880 0.4541120
  Stage1Hypertension 0.5657895 0.4342105

                    age
Y                        [,1]     [,2]
  no stroke          47.77294 8.361556
  Stage1Hypertension 52.57425 8.023257

                    education
Y                        [,1]     [,2]
  no stroke          2.041716 1.037485
  Stage1Hypertension 1.842105 0.984104

                    currentSmoker
Y                         [,1]      [,2]
  no stroke          0.5375447 0.4987370
  Stage1Hypertension 0.4323308 0.4956327

                    cigsPerDay
Y                        [,1]     [,2]
  no stroke          9.779499 11.90672
  Stage1Hypertension 8.172932 12.02898

                    BPMeds
Y                          [,1]       [,2]
  no stroke          0.00476758 0.06890341
  Stage1Hypertension 0.06578947 0.24803032

                    prevalentStroke
Y                           [,1]       [,2]
  no stroke          0.002979738 0.05452183
  Stage1Hypertension 0.010338346 0.10119827

                    prevalentHyp
Y                          [,1]      [,2]
  no stroke          0.07926103 0.2702263
  Stage1Hypertension 0.68327068 0.4654196

                    diabetes
Y                          [,1]      [,2]
  no stroke          0.01668653 0.1281323
  Stage1Hypertension 0.03759398 0.1903016

                    totChol
Y                        [,1]     [,2]
  no stroke          231.3772 42.20958
  Stage1Hypertension 246.5395 43.39237

                    sysBP
Y                        [,1]     [,2]
  no stroke          119.4762 11.50558
  Stage1Hypertension 153.0573 19.52540

                    diaBP
Y                        [,1]     [,2]
  no stroke          76.00477 7.391512
  Stage1Hypertension 93.70771 9.760254

                    BMI
Y                        [,1]     [,2]
  no stroke          24.84029 3.463344
  Stage1Hypertension 27.18985 4.490991

                    heartRate
Y                        [,1]     [,2]
  no stroke          74.11502 11.27117
  Stage1Hypertension 78.09117 12.67415

                    glucose
Y                        [,1]     [,2]
  no stroke          80.16210 20.94263
  Stage1Hypertension 83.76222 24.62068

                    tenyrCHD
Y                           no       yes
  no stroke          0.8873659 0.1126341
  Stage1Hypertension 0.7781955 0.2218045
Hide
pred_nb <- predict(fit_nb, testset, type = "class") 
mat_nb <- table(testset$heartstroke,pred_nb)
confusionMatrix(mat_nb)
Confusion Matrix and Statistics

                    pred_nb
                     no stroke Stage1Hypertension
  no stroke                505                 55
  Stage1Hypertension        96                258
                                          
               Accuracy : 0.8348          
                 95% CI : (0.8091, 0.8583)
    No Information Rate : 0.6575          
    P-Value [Acc > NIR] : < 2.2e-16       
                                          
                  Kappa : 0.6443          
                                          
 Mcnemar's Test P-Value : 0.001133        
                                          
            Sensitivity : 0.8403          
            Specificity : 0.8243          
         Pos Pred Value : 0.9018          
         Neg Pred Value : 0.7288          
             Prevalence : 0.6575          
         Detection Rate : 0.5525          
   Detection Prevalence : 0.6127          
      Balanced Accuracy : 0.8323          
                                          
       'Positive' Class : no stroke       
                                          

# Анализ данных

## Навигация

* [Лабораторная работа 1: Средства предварительной обработки данных](#средства-предварительной-обработки-данных)
* [Лабораторная работа 2: Линейные модели регрессии](#линейные-модели-регрессии)
* [Лабораторная работа 3: Дисперсионный анализ](#дисперсионный-анализ)
* [Лабораторная работа 4: Канонический анализ](#канонический-анализ)
* [Лабораторная работа 5: Кластерный анализ](#кластерный-анализ)
* [Лабораторная работа 6:  Анализ главных компонент и факторный анализ](#анализ-главных-компонент-и-факторный-анализ)
* [Лабораторная работа 7: Многомерное шкалирование](#многомерное-шкалирование)
* [Лабораторная работа 8: Временные ряды](#временные-ряды)
* [Индивидуальное задание](#индивидуальное-задание)

## Средства предварительной обработки данных

В данной работе проводится анализ выборки stackloss:  

```R
> stackloss
   Air.Flow Water.Temp Acid.Conc. stack.loss
1        80         27         89         42
2        80         27         88         37
3        75         25         90         37
4        62         24         87         28
5        62         22         87         18
6        62         23         87         18
7        62         24         93         19
8        62         24         93         20
9        58         23         87         15
10       58         18         80         14
11       58         18         89         14
12       58         17         88         13
13       58         18         82         11
14       58         19         93         12
15       50         18         89          8
16       50         18         86          7
17       50         19         72          8
18       50         19         79          8
19       50         20         80          9
20       56         20         82         15
21       70         20         91         15
```

Суммарная информация о выборке:  

```R
> data <- stackloss
> stat.desc(data)
				 Air.Flow  Water.Temp   Acid.Conc.  stack.loss
nbr.val        21.0000000  21.0000000 2.100000e+01  21.0000000
nbr.null        0.0000000   0.0000000 0.000000e+00   0.0000000
nbr.na          0.0000000   0.0000000 0.000000e+00   0.0000000
min            50.0000000  17.0000000 7.200000e+01   7.0000000
max            80.0000000  27.0000000 9.300000e+01  42.0000000
range          30.0000000  10.0000000 2.100000e+01  35.0000000
sum          1269.0000000 443.0000000 1.812000e+03 368.0000000
median         58.0000000  20.0000000 8.700000e+01  15.0000000
mean           60.4285714  21.0952381 8.628571e+01  17.5238095
SE.mean         2.0006802   0.6897369 1.169336e+00   2.2196300
CI.mean.0.95    4.1733457   1.4387659 2.439192e+00   4.6300671
var            84.0571429   9.9904762 2.871429e+01 103.4619048
std.dev         9.1682683   3.1607715 5.358571e+00  10.1716225
coef.var        0.1517208   0.1498334 6.210265e-02   0.5804459

```

 Корреляционная матрица:  

```R
> cor(data)
		    Air.Flow Water.Temp Acid.Conc. stack.loss
Air.Flow   1.0000000  0.7818523  0.5001429  0.9196635
Water.Temp 0.7818523  1.0000000  0.3909395  0.8755044
Acid.Conc. 0.5001429  0.3909395  1.0000000  0.3998296
stack.loss 0.9196635  0.8755044  0.3998296  1.0000000
```

Корелляционная матрица показывает досттаочно сильную связь между переменными ```Air.Flow```,  ```Water.Temp``` и ```stack.loss```.  

Дальнейший анализ проводился по переменным ```Air.Flow``` и ```stack.loss```.  

### Тесты значимости среднего

Проведём тесты значимости среднего:  

```R
> t.test(data$Air.Flow, mu = mean(data$Air.Flow))

		One Sample t-test

data:  data$Air.Flow
t = 0, df = 20, p-value = 1
alternative hypothesis: true mean is not equal to 60.42857
95 percent confidence interval:
 56.25523 64.60192
sample estimates:
mean of x 
 60.42857 
```

Полученное значение для ```Air.Flow``` находится в пределах доверительного интервала, поэтому гипотеза о равенстве мат. ожидания выборочному среднему принимается.  

```R
> wilcox.test(
    data$Air.Flow,
    mu = median(data$Air.Flow),
    conf.int = TRUE
)

		Wilcoxon signed rank test with continuity correction

data:  data$Air.Flow
V = 74, p-value = 0.4394
alternative hypothesis: true location is not equal to 58
95 percent confidence interval:
 55.99999 68.00001
sample estimates:
(pseudo)median 
      61.99994
```

Значение, полученное в тесте Уилкокса, также находится внутри доверительного интервала.  



Проведём тесты для ```stack.loss```:  

```R
> t.test(data$stack.loss, mu = mean(data$stack.loss))

	One Sample t-test

data:  data$stack.loss
t = 0, df = 20, p-value = 1
alternative hypothesis: true mean is not equal to 17.52381
95 percent confidence interval:
 12.89374 22.15388
sample estimates:
mean of x 
 17.52381
```

Полученное значение среднего находится внутри доверительного интервала, следовательно, гипотеза принимается.  

```R
> wilcox.test(
    data$stack.loss,
    mu = median(data$stack.loss),
    conf.int = TRUE
)

	Wilcoxon signed rank test with continuity correction

data:  data$stack.loss
V = 92.5, p-value = 0.7768
alternative hypothesis: true location is not equal to 15
95 percent confidence interval:
 11.49998 24.00005
sample estimates:
(pseudo)median 
      15.99999 
```

Значение, полученное в тесте Уилкокса, также находится в пределах доверительного интервала.  



### Проверка гипотез нормальности

Проверим соответствие распределений переменных ```Air.Flow``` и ```stack.loss``` нормальному распределению.  

```R
> shapiro.test(data$Air.Flow)

	Shapiro-Wilk normality test

data:  data$Air.Flow
W = 0.86115, p-value = 0.006651
```

Для ```Air.Flow``` ```p.value = 0.006 < 0.05```, что означает отклонение гипотезы о нормальном распределении в пользу альтернативной (распределение не соответствует нормальному).  

```R
> ks.test(data$Air.Flow, "pnorm")

	One-sample Kolmogorov-Smirnov test

data:  data$Air.Flow
D = 1, p-value < 2.2e-16
alternative hypothesis: two-sided
```

Значение, полученное в тесте Колмогорова-Смирнова, подтверждает результат теста Шапиро.  



Проведём тесты для переменной ```stack.loss```:  

```R
> shapiro.test(data$stack.loss)

	Shapiro-Wilk normality test

data:  data$stack.loss
W = 0.82651, p-value = 0.001718
```

Для ```stack.loss``` ```p.value < 0.05```, что означает отклонение гипотезы о нормальном распределении в пользу альтернативной (распределение не соответствует нормальному).  

```R
> ks.test(data$stack.loss, "pnorm")

	One-sample Kolmogorov-Smirnov test

data:  data$stack.loss
D = 1, p-value < 2.2e-16
alternative hypothesis: two-sided
```

Значение, полученное в тесте Колмогорова-Смирнова, подтверждает результат теста Шапиро.  

### Визуализация данных

Визуализируем переменные ```Air.Flow``` и ```stack.loss```:  

![](https://github.com/IHappyPlant/Data_Analysis/blob/master/images/homework_1/hw1_air_flow_plot.png)

![](https://github.com/IHappyPlant/Data_Analysis/blob/master/images/homework_1/hw1_stack_loss_plot.png)



Сравним квантили выборки с квантилями нормального распределения:  

![](https://github.com/IHappyPlant/Data_Analysis/blob/master/images/homework_1/hw1_air_flow_qqnorm.png)

![](https://github.com/IHappyPlant/Data_Analysis/blob/master/images/homework_1/hw1_stack_loss_qqnorm.png)

Приведённые графики подтверждают, что распределения переменных не полностью соответствуют нормальному.  



Сравним плотность распределения переменных из выборки с доверительной полосой плотности нормального распределения .  

![](https://github.com/IHappyPlant/Data_Analysis/blob/master/images/homework_1/hw1_air_flow_density.png)

![](https://github.com/IHappyPlant/Data_Analysis/blob/master/images/homework_1/hw1_stack_loss_density.png)

Построим гистограммы для переменных ```Air.Flow``` и ```stack.loss```:  

![](https://github.com/IHappyPlant/Data_Analysis/blob/master/images/homework_1/air_flow_hist.png)

![](https://github.com/IHappyPlant/Data_Analysis/blob/master/images/homework_1/stack_loss_hist.png)

## Линейные модели регрессии

В данной работе строятся и анализируются различные линейные модели регрессии для выборки stackloss.  

Из данных лабораторной работы 1 следует, что в выборке stackloss наибольший коэффициент корреляции имеют переменные ```stack.loss``` и ```Air.Flow```.  

Будем считать переменную ```stack.loss``` зависимой, а остальные переменные - предикторными.  

### Парная регрессия

Проведём регрессионный анализ для переменных ```stack.loss``` и ```Air.Flow```.  

Построим график связи переменных:  

![](https://github.com/IHappyPlant/Data_Analysis/blob/master/images/homework_2/hw2_stack_loss_air_flow_plot.png)

На графике видна зависимость ```stack.loss``` от ```Air.Flow```.  

Построим модель линейной регрессии:  

```R
> data <- stackloss
> lm.rat1<-lm(formula=data$Air.Flow~data$stack.loss)
> summary(lm.rat1)

Call:
lm(formula = data$Air.Flow ~ data$stack.loss)

Residuals:
    Min      1Q  Median      3Q     Max 
-7.1128 -2.3365 -0.3365  1.1767 11.6635 

Coefficients:
                Estimate Std. Error t value Pr(>|t|)    
(Intercept)     45.90229    1.63549   28.07  < 2e-16 ***
data$stack.loss  0.82895    0.08121   10.21 3.77e-09 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 3.694 on 19 degrees of freedom
Multiple R-squared:  0.8458,	Adjusted R-squared:  0.8377 
F-statistic: 104.2 on 1 and 19 DF,  p-value: 3.774e-09
```

Вероятность ```F-statistic ``` составляет ```3.774e-09```, что свидетельствует о значимости модели регрессии.  

Построим таблицу дисперсионного анализа:

```R
> anova(lm.rat1)

Analysis of Variance Table

Response: data$Air.Flow
                Df  Sum Sq Mean Sq F value    Pr(>F)    
data$stack.loss  1 1421.88 1421.88   104.2 3.774e-09 ***
Residuals       19  259.26   13.65
```

Вычислим доверительные области: 

```R
> CPI.df <- cbind(predict(lm.rat1,interval ="conf"), predict(lm.rat1,interval ="pred"))
> CPI.df <- CPI.df[,-4]
> colnames(CPI.df) <- c("Y_fit","CI_l","CI_u","PI_l","PI_u")
> head(CPI.df)

     Y_fit     CI_l     CI_u     PI_l     PI_u
1 80.71800 76.22876 85.20724 71.77760 89.65840
2 76.57327 72.85781 80.28873 67.99527 85.15128
3 76.57327 72.85781 80.28873 67.99527 85.15128
4 69.11276 66.65979 71.56574 61.00138 77.22415
5 60.82331 59.13420 62.51242 52.90936 68.73726
6 60.82331 59.13420 62.51242 52.90936 68.73726
```

Построим линию регрессии и визуализируем доверительные области:

![](https://github.com/IHappyPlant/Data_Analysis/blob/master/images/homework_2/hw2_regression_line_confidence_intervals.png)

### Множественная регрессия

Проведём анализ зависимости переменной ```stack.loss``` от всех остальных переменных.

Построим модель регрессии:

```R
> fm1 <- lm(stack.loss~., data = data)
> summary(fm1)

Call:
lm(formula = stack.loss ~ ., data = data)

Residuals:
    Min      1Q  Median      3Q     Max 
-7.2377 -1.7117 -0.4551  2.3614  5.6978 

Coefficients:
            Estimate Std. Error t value Pr(>|t|)    
(Intercept) -39.9197    11.8960  -3.356  0.00375 ** 
Air.Flow      0.7156     0.1349   5.307  5.8e-05 ***
Water.Temp    1.2953     0.3680   3.520  0.00263 ** 
Acid.Conc.   -0.1521     0.1563  -0.973  0.34405    
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 3.243 on 17 degrees of freedom
Multiple R-squared:  0.9136,	Adjusted R-squared:  0.8983 
F-statistic:  59.9 on 3 and 17 DF,  p-value: 3.016e-09
```

Значимыми являются коэффициенты ```(Intercept)```, ```Air.Flow``` и ```Water.Temp```. Модель в целом также является статистически значимой.

Построим таблицу дисперсионного анализа:

```R
> anova(fm1)

Analysis of Variance Table

Response: stack.loss
           Df  Sum Sq Mean Sq  F value    Pr(>F)    
Air.Flow    1 1750.12 1750.12 166.3707 3.309e-10 ***
Water.Temp  1  130.32  130.32  12.3886  0.002629 ** 
Acid.Conc.  1    9.97    9.97   0.9473  0.344046    
Residuals  17  178.83   10.52
```

Наибольший вклад вносят переменные ```Air.Flow``` и ```Water.Temp```.

Построим таблицу наблюдаемых и предсказанных значений для ```stack.loss```:

```R
> predict.fm1 = predict.lm(fm1)
> tabout <- cbind(data$stack.loss, predict.fm1)
> head(tabout, n = 30)

      predict.fm1
1  42   38.765363
2  37   38.917485
3  37   32.444467
4  28   22.302226
5  18   19.711654
6  18   21.006940
7  19   21.389491
8  20   21.389491
9  15   18.144379
10 14   12.732806
11 14   11.363703
12 13   10.220540
13 11   12.428561
14 12   12.050499
15  8    5.638582
16  7    6.094949
17  8    9.519951
18  8    8.455093
19  9    9.598257
20 15   13.587853
21 15   22.237713
```

Построим диаграмму квантиль-квантиль:

![](https://github.com/IHappyPlant/Data_Analysis/blob/master/images/homework_2/hw2_fm1_qqplot.png)

### Пошаговая множественная регрессия

Построим пошаговуб модель множественной регрессии:

```R
> fm2 <- step(lm(stack.loss~., data = data))
> summary(fm2)

Start:  AIC=52.98
stack.loss ~ Air.Flow + Water.Temp + Acid.Conc.

             Df Sum of Sq    RSS    AIC
- Acid.Conc.  1     9.965 188.80 52.119
<none>                    178.83 52.980
- Water.Temp  1   130.308 309.14 62.475
- Air.Flow    1   296.228 475.06 71.497

Step:  AIC=52.12
stack.loss ~ Air.Flow + Water.Temp

             Df Sum of Sq    RSS    AIC
<none>                    188.80 52.119
- Water.Temp  1    130.32 319.12 61.142
- Air.Flow    1    294.36 483.15 69.852

Call:
lm(formula = stack.loss ~ Air.Flow + Water.Temp, data = data)

Residuals:
    Min      1Q  Median      3Q     Max 
-7.5290 -1.7505  0.1894  2.1156  5.6588 

Coefficients:
            Estimate Std. Error t value Pr(>|t|)    
(Intercept) -50.3588     5.1383  -9.801 1.22e-08 ***
Air.Flow      0.6712     0.1267   5.298 4.90e-05 ***
Water.Temp    1.2954     0.3675   3.525  0.00242 ** 
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 3.239 on 18 degrees of freedom
Multiple R-squared:  0.9088,	Adjusted R-squared:  0.8986 
F-statistic: 89.64 on 2 and 18 DF,  p-value: 4.382e-10
```

На старте алгоритма, значение ```AIC``` составляло ```52.98```. По итогу работы, значение ```AIC``` составило ```52.12```, что означает, что модель регрессии улучшилась после удаления незначащих переменных.

Выполним анализ вариаций:

```R
> anova(fm2)

Analysis of Variance Table

Response: stack.loss
           Df  Sum Sq Mean Sq F value    Pr(>F)    
Air.Flow    1 1750.12 1750.12 166.859 1.528e-10 ***
Water.Temp  1  130.32  130.32  12.425  0.002419 ** 
Residuals  18  188.80   10.49
```

Выведем таблицу наблюдаемых и предсказанных значений, и проиллюстрируем её на графике квантиль-квантиль:

```R
> predict.fm2 <- predict.lm(fm2)
> tabout2 <- cbind(data$stack.loss, predict.fm2)
> head(tabout2, n = 30)

      predict.fm2
1  42   38.308002
2  37   38.308002
3  37   32.361527
4  28   22.341168
5  18   19.750465
6  18   21.045817
7  19   22.341168
8  20   22.341168
9  15   18.361199
10 14   11.884442
11 14   11.884442
12 13   10.589091
13 11   11.884442
14 12   13.179793
15  8    6.515207
16  7    6.515207
17  8    7.810558
18  8    7.810558
19  9    9.105909
20 15   13.132836
21 15   22.528998
```

![](https://github.com/IHappyPlant/Data_Analysis/blob/master/images/homework_2/hw2_fm2_qqplot.png)

## Дисперсионный анализ

В данной работе проводится построение и анализ нескольких дисперсионных моделей для выборки ```npk```.

Выведем выборку:

```R
> data <- npk
> data

   block N P K yield
1      1 0 1 1  49.5
2      1 1 1 0  62.8
3      1 0 0 0  46.8
4      1 1 0 1  57.0
5      2 1 0 0  59.8
6      2 1 1 1  58.5
7      2 0 0 1  55.5
8      2 0 1 0  56.0
9      3 0 1 0  62.8
10     3 1 1 1  55.8
11     3 1 0 0  69.5
12     3 0 0 1  55.0
13     4 1 0 0  62.0
14     4 1 1 1  48.8
15     4 0 0 1  45.5
16     4 0 1 0  44.2
17     5 1 1 0  52.0
18     5 0 0 0  51.5
19     5 1 0 1  49.8
20     5 0 1 1  48.8
21     6 1 0 1  57.2
22     6 1 1 0  59.0
23     6 0 1 1  53.2
24     6 0 0 0  56.0
```

Получим описательную статистику по всей выборке:

```R
> summary(data)

 block N      P      K          yield      
 1:4   0:12   0:12   0:12   Min.   :44.20  
 2:4   1:12   1:12   1:12   1st Qu.:49.73  
 3:4                        Median :55.65  
 4:4                        Mean   :54.88  
 5:4                        3rd Qu.:58.62  
 6:4                        Max.   :69.50
```

Получим описательную статистику по группам:

```R
> means <- tapply(data$yield, data$block, mean)
> means

     1      2      3      4      5      6 
54.025 57.450 60.775 50.125 50.525 56.350 
```

Построим графики для визуализации различия средних:

![](https://github.com/IHappyPlant/Data_Analysis/blob/master/images/homework_3/hw3_stripchart.png)

![hw3_boxplot](https://github.com/IHappyPlant/Data_Analysis/blob/master/images/homework_3/hw3_boxplot.png)

Оценим нормальность распределения ```yield```:

![](https://github.com/IHappyPlant/Data_Analysis/blob/master/images/homework_3/density_hist.png)

![](https://github.com/IHappyPlant/Data_Analysis/blob/master/images/homework_3/density_plot.png)Проведём тест Шапиро для проверки нормальности распределения:

```R
> shapiro.test(data$yield)

	Shapiro-Wilk normality test

data:  data$yield
W = 0.97884, p-value = 0.8735
```

Значение теста Шапиро подтверждает нормальность распределения для ```yield``` по всей выборке.

Проведём тест Бартлетта для проверки дисперсии по группам:

```R
> bartlett.test(data$yield, data$block)

	Bartlett test of homogeneity of variances

data:  data$yield and data$block
Bartlett's K-squared = 11.508, df = 5, p-value = 0.04219
```

Из результатов теста Бартлета следует, что дисперсия по группам неоднородна ```(p-value < 0.05)```.

### Однофакторный дисперсионный анализ

Выполним однофакторный дисперсионный анализ:

```R
> summary(aov(yield ~ block, data = data))
            Df Sum Sq Mean Sq F value Pr(>F)  
block        5  343.3   68.66   2.318 0.0861 .
Residuals   18  533.1   29.61                 
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
```

```p-value = 0.08 > 0.05```, из чего следует, что гипотезу о несущественности различия средних следует принять. Из этого следует, что различные комбинации значений ```N```, ```P```, ```K``` не оказывают существенного влияния на значение ```yield```.

Построим линейную модель дисперсионного анализа:

```R
> summary(lm(yield ~  block, data = data))

Call:
lm(formula = yield ~ block, data = data)

Residuals:
    Min      1Q  Median      3Q     Max 
-7.2250 -3.4937 -0.5375  2.1062 11.8750 

Coefficients:
            Estimate Std. Error t value Pr(>|t|)    
(Intercept)   54.025      2.721  19.855 1.09e-13 ***
block2         3.425      3.848   0.890   0.3852    
block3         6.750      3.848   1.754   0.0964 .  
block4        -3.900      3.848  -1.013   0.3243    
block5        -3.500      3.848  -0.910   0.3751    
block6         2.325      3.848   0.604   0.5532    
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 5.442 on 18 degrees of freedom
Multiple R-squared:  0.3917,	Adjusted R-squared:  0.2228 
F-statistic: 2.318 on 5 and 18 DF,  p-value: 0.08607
```

Здесь также видно, что ни одна из переменных не имеет существенного вклада в уравнение регрессии.

Поскольку дисперсия по группам была неоднородной, применим для дисперсионного анализа метод Уэлча:

```R
> oneway.test(yield ~  block, data = data)

	One-way analysis of means (not assuming equal variances)

data:  yield and block
F = 6.2463, num df = 5.0000, denom df = 8.0508, p-value = 0.01178
```

```p-value > 0.05``` для теста Уэлча означает, что гипотезу о несущественности различия средних следует отклонить, хотя на графиках и всех остальных тестах видно обратное. Таким образом, имеется два противоречащих значения. Для устранения неопределённости можно провести двухфакторный дисперсионный анализ.

### Двухфакторный дисперсионный анализ

Построим график плана эксперимента, и графики взаимодействия переменной ```yield``` с 

каждой из переменных ```N```, ```P```, ```K```:

![](https://github.com/IHappyPlant/Data_Analysis/blob/master/images/homework_3/experiment_plan_plot.png)

![](https://github.com/IHappyPlant/Data_Analysis/blob/master/images/homework_3/yield_n_plot.png)

![](https://github.com/IHappyPlant/Data_Analysis/blob/master/images/homework_3/yield_p_plot.png)

![](https://github.com/IHappyPlant/Data_Analysis/blob/master/images/homework_3/yield_k_plot.png)

Проведём двухфакторный дисперсионный анализ для проверки связи переменной ```yield``` с каждой из переменных ```N```, ```P```, ```K```:

```R
> mwb <- aov(yield ~ N + block + N:block, data = data)
> summary(mwb)
            Df Sum Sq Mean Sq F value Pr(>F)  
N            1  189.3  189.28   9.261 0.0102 *
block        5  343.3   68.66   3.359 0.0397 *
N:block      5   98.5   19.70   0.964 0.4769  
Residuals   12  245.3   20.44                 
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

> mwb <- aov(yield ~ P + block + P:block, data = data)
> summary(mwb)
            Df Sum Sq Mean Sq F value Pr(>F)
P            1    8.4    8.40   0.222  0.646
block        5  343.3   68.66   1.818  0.184
P:block      5   71.4   14.28   0.378  0.854
Residuals   12  453.3   37.77 

> mwb <- aov(yield ~ K + block + K:block, data = data)
> summary(mwb)
            Df Sum Sq Mean Sq F value Pr(>F)
K            1   95.2   95.20   3.108  0.103
block        5  343.3   68.66   2.241  0.117
K:block      5   70.3   14.05   0.459  0.799
Residuals   12  367.6   30.63
```

Полученные значения показывают, что переменная ```N``` вносит больший вклад в значение переменной ```yield```, чем другие.

## Канонический анализ

В работе проводится канонический анализ для различных подмножеств переменных выборки ```LifeCycleSavings```.

Выведем выборку ```LifeCycleSavings```:

```R
> lcs <- LifeCycleSavings
> lcs
                  sr pop15 pop75     dpi  ddpi
Australia      11.43 29.35  2.87 2329.68  2.87
Austria        12.07 23.32  4.41 1507.99  3.93
Belgium        13.17 23.80  4.43 2108.47  3.82
Bolivia         5.75 41.89  1.67  189.13  0.22
Brazil         12.88 42.19  0.83  728.47  4.56
Canada          8.79 31.72  2.85 2982.88  2.43
Chile           0.60 39.74  1.34  662.86  2.67
China          11.90 44.75  0.67  289.52  6.51
Colombia        4.98 46.64  1.06  276.65  3.08
Costa Rica     10.78 47.64  1.14  471.24  2.80
Denmark        16.85 24.42  3.93 2496.53  3.99
Ecuador         3.59 46.31  1.19  287.77  2.19
Finland        11.24 27.84  2.37 1681.25  4.32
France         12.64 25.06  4.70 2213.82  4.52
Germany        12.55 23.31  3.35 2457.12  3.44
Greece         10.67 25.62  3.10  870.85  6.28
Guatamala       3.01 46.05  0.87  289.71  1.48
Honduras        7.70 47.32  0.58  232.44  3.19
Iceland         1.27 34.03  3.08 1900.10  1.12
India           9.00 41.31  0.96   88.94  1.54
Ireland        11.34 31.16  4.19 1139.95  2.99
Italy          14.28 24.52  3.48 1390.00  3.54
Japan          21.10 27.01  1.91 1257.28  8.21
Korea           3.98 41.74  0.91  207.68  5.81
Luxembourg     10.35 21.80  3.73 2449.39  1.57
Malta          15.48 32.54  2.47  601.05  8.12
Norway         10.25 25.95  3.67 2231.03  3.62
Netherlands    14.65 24.71  3.25 1740.70  7.66
New Zealand    10.67 32.61  3.17 1487.52  1.76
Nicaragua       7.30 45.04  1.21  325.54  2.48
Panama          4.44 43.56  1.20  568.56  3.61
Paraguay        2.02 41.18  1.05  220.56  1.03
Peru           12.70 44.19  1.28  400.06  0.67
Philippines    12.78 46.26  1.12  152.01  2.00
Portugal       12.49 28.96  2.85  579.51  7.48
South Africa   11.14 31.94  2.28  651.11  2.19
South Rhodesia 13.30 31.92  1.52  250.96  2.00
Spain          11.77 27.74  2.87  768.79  4.35
Sweden          6.86 21.44  4.54 3299.49  3.01
Switzerland    14.13 23.49  3.73 2630.96  2.70
Turkey          5.13 43.42  1.08  389.66  2.96
Tunisia         2.81 46.12  1.21  249.87  1.13
United Kingdom  7.81 23.27  4.46 1813.93  2.01
United States   7.56 29.81  3.43 4001.89  2.45
Venezuela       9.22 46.40  0.90  813.39  0.53
Zambia         18.56 45.25  0.56  138.33  5.14
Jamaica         7.72 41.12  1.73  380.47 10.23
Uruguay         9.24 28.13  2.72  766.54  1.88
Libya           8.89 43.69  2.07  123.58 16.71
Malaysia        4.71 47.20  0.66  242.69  5.08
```

Получим описательную статистику и матрицу корреляции:

```R
> summary(lcs)
       sr             pop15           pop75            dpi               ddpi       
 Min.   : 0.600   Min.   :21.44   Min.   :0.560   Min.   :  88.94   Min.   : 0.220  
 1st Qu.: 6.970   1st Qu.:26.21   1st Qu.:1.125   1st Qu.: 288.21   1st Qu.: 2.002  
 Median :10.510   Median :32.58   Median :2.175   Median : 695.66   Median : 3.000  
 Mean   : 9.671   Mean   :35.09   Mean   :2.293   Mean   :1106.76   Mean   : 3.758  
 3rd Qu.:12.617   3rd Qu.:44.06   3rd Qu.:3.325   3rd Qu.:1795.62   3rd Qu.: 4.478  
 Max.   :21.100   Max.   :47.64   Max.   :4.700   Max.   :4001.89   Max.   :16.710  
> cor(lcs)
              sr       pop15       pop75        dpi        ddpi
sr     1.0000000 -0.45553809  0.31652112  0.2203589  0.30478716
pop15 -0.4555381  1.00000000 -0.90847871 -0.7561881 -0.04782569
pop75  0.3165211 -0.90847871  1.00000000  0.7869995  0.02532138
dpi    0.2203589 -0.75618810  0.78699951  1.0000000 -0.12948552
ddpi   0.3047872 -0.04782569  0.02532138 -0.1294855  1.00000000
```

Из матрицы корреляции видно, что сильнее всего связаны переменные ```pop15```, ```pop75``` и ```dpi```. Переменные ```sr``` и ```ddpi``` слабо связаны с остальными. На основании этого, объединим переменные ```pop15```, ```pop75``` и ```dpi``` в одно подмножество ```x```, а переменные ```sr``` и ```ddpi``` - в подмножество ```y```:

```R
> x <- lcs[2:4]
> y <- cbind(lcs[1], lcs[5])
```

Построим матрицы корреляции для полученных подмножеств:

```R
> cor(x)
           pop15      pop75        dpi
pop15  1.0000000 -0.9084787 -0.7561881
pop75 -0.9084787  1.0000000  0.7869995
dpi   -0.7561881  0.7869995  1.0000000
> cor(y)
            sr      ddpi
sr   1.0000000 0.3047872
ddpi 0.3047872 1.0000000
```

Можно видеть, что все коэффициенты корреляции сохранились.

Проведём канонический анализ:

```R
> cxy <- cancor(x, y)
> cxy
$cor
[1] 0.5264128 0.2468310

$xcoef
               [,1]          [,2]          [,3]
pop15 -2.984865e-02  0.0028588468 -2.307619e-02
pop75 -1.011675e-01 -0.1051054829 -2.442859e-01
dpi   -3.839998e-05  0.0002330122  1.649138e-05

$ycoef
            [,1]        [,2]
sr    0.03283325  0.00653719
ddpi -0.00590285 -0.05193059

$xcenter
    pop15     pop75       dpi 
  35.0896    2.2930 1106.7584 

$ycenter
    sr   ddpi 
9.6710 3.7576
```

Значение первого канонического корня означает, что множества средне связаны между собой.

Проведём проверку значимости канонических корреляций:

```R
> n <- dim(x)[1] # number of observations
> p <- dim(x)[2] # number of dependent variables
> q <- dim(y)[2] # number of independent variables
> n;p;q
[1] 50
[1] 3
[1] 2
> wilks_test <- p.asym(rho=cxy$cor, n, p, q, tstat = "Wilks")
Wilks' Lambda, using F-approximation (Rao's F):
              stat   approx df1 df2     p.value
1 to 2:  0.6788471 3.205611   6  90 0.006726398
2 to 2:  0.9390745 1.492200   2  46 0.235559566
```

Значения ```p-value ``` говорят о том, что значимостью обладают только модели из одной переменной.

Проведём перестановочные тесты:

```R
> p.perm(x, y, rhostart=1)
 Permutation resampling using Wilks 's statistic:
     stat0     mstat nboot nexcess          p
 0.6788471 0.8821888   999      10 0.01001001
> p.perm(x, y, rhostart=2)
 Permutation resampling using Wilks 's statistic:
     stat0     mstat nboot nexcess          p
 0.9390745 0.9805179   999      44 0.04404404
```

Значения ```p-value < 0.05 ``` перестановочных тестов говорят о том, что два подмножества ```x``` и ```y``` кореллируют между собой.

## Кластерный анализ

В этой работе впородится кластерный анализ выборки ```european.foods``` из пакета ```cluster.datasets```.

Выведем выборку:

```R
> library(cluster.datasets)
> data("european.foods")
> ef <- european.foods
> ef
   code              name wg it fr ns bm lg gb pl aa sd sw dk ny fd sp id
1    GC     ground coffee 90 82 88 96 94 97 27 72 55 73 97 96 92 98 70 13
2    IC    instant coffee 49 10 42 62 38 61 86 26 31 72 13 17 17 12 40 52
3    TB          tea bags 88 60 63 98 48 86 99 77 61 85 93 92 83 84 40 99
4    SS  sugarless sweets 19  2  4 32 11 28 22  2 15 25 31 35 13 20  0 11
5    BP packaged biscuits 57 55 76 62 74 79 91 22 29 31  0 66 62 64 62 80
6    SP     packaged soup 51 41 53 67 37 73 55 34 33 69 43 32 51 27 43 75
7    ST       tinned soup 19  3 11 43 25 12 76  1  1 10 43 17  4 10  2 18
8    IP  instant potatoes 21  2 23  7  9  7 17  5  5 17 39 11 17  8 14  2
9    FF       frozen fish 27  4 11 14 13 26 20 20 15 19 54 51 30 18 23  5
10   VF frozen vegetables 21  2  5 14 12 23 24  3 11 15 45 42 15 12  7  3
11   AF      fresh apples 81 67 87 83 76 85 76 22 49 79 56 81 61 50 59 57
12   OF     fresh oranges 75 71 84 89 76 94 68 51 42 70 78 72 72 57 77 52
13   FT      tinned fruit 44  9 40 61 42 83 89  8 14 46 53 50 34 22 30 46
14   JS          shop jam 71 46 45 81 57 20 91 16 41 61 75 64 51 37 38 89
15   CG      garlic clove 22 80 88 15 29 91 11 89 51 64  9 11 11 15 86  5
16   BR            butter 91 66 94 31 84 94 95 65 51 82 68 92 63 96 44 97
17   ME         margarine 85 24 47 97 80 94 94 78 72 48 32 91 94 94 51 25
18   OO         olive oil 74 94 36 13 83 84 57 92 28 61 48 30 28 17 91 31
19   YT            yogurt 30  5 57 53 20 31 11  6 13 48  2 11  2  0 16  3
20   CD        crispbread 26 18  3 15  5 24 28  9 11 30 93 34 62 64 13  9
```

Выборка содержит информацию о наличии различной еды в домах людей на момент опроса в процентном соотношении по европейским странам.

Очистим выборку от лишних колонок, и дадим превратим вторую колонку в названия строк:

```R
> rownames(ef) <- c(ef[2][,])
> ef.features <- ef[3:18]
> ef.features
                  wg it fr ns bm lg gb pl aa sd sw dk ny fd sp id
ground coffee     90 82 88 96 94 97 27 72 55 73 97 96 92 98 70 13
instant coffee    49 10 42 62 38 61 86 26 31 72 13 17 17 12 40 52
tea bags          88 60 63 98 48 86 99 77 61 85 93 92 83 84 40 99
sugarless sweets  19  2  4 32 11 28 22  2 15 25 31 35 13 20  0 11
packaged biscuits 57 55 76 62 74 79 91 22 29 31  0 66 62 64 62 80
packaged soup     51 41 53 67 37 73 55 34 33 69 43 32 51 27 43 75
tinned soup       19  3 11 43 25 12 76  1  1 10 43 17  4 10  2 18
instant potatoes  21  2 23  7  9  7 17  5  5 17 39 11 17  8 14  2
frozen fish       27  4 11 14 13 26 20 20 15 19 54 51 30 18 23  5
frozen vegetables 21  2  5 14 12 23 24  3 11 15 45 42 15 12  7  3
fresh apples      81 67 87 83 76 85 76 22 49 79 56 81 61 50 59 57
fresh oranges     75 71 84 89 76 94 68 51 42 70 78 72 72 57 77 52
tinned fruit      44  9 40 61 42 83 89  8 14 46 53 50 34 22 30 46
shop jam          71 46 45 81 57 20 91 16 41 61 75 64 51 37 38 89
garlic clove      22 80 88 15 29 91 11 89 51 64  9 11 11 15 86  5
butter            91 66 94 31 84 94 95 65 51 82 68 92 63 96 44 97
margarine         85 24 47 97 80 94 94 78 72 48 32 91 94 94 51 25
olive oil         74 94 36 13 83 84 57 92 28 61 48 30 28 17 91 31
yogurt            30  5 57 53 20 31 11  6 13 48  2 11  2  0 16  3
crispbread        26 18  3 15  5 24 28  9 11 30 93 34 62 64 13  9
```



Проведём иерархическую кластеризацию выборки и визуализируем результат:

```R
> ef.hc <- hclust(dist(ef.features), method="ward.D2")
> plot(ef.hc)
```

![](https://github.com/IHappyPlant/Data_Analysis/blob/master/images/homework_5/cluster_dendrogram.png)

Получившиеся кластеры можно интерпретировать, как группы продуктов, чаще всего встречающихся вместе.

Проведём кластеризацию с помощью ```k-means```, подобрав оптимальное число кластеров:

```R
> kl <- (nrow(ef.features) - 1) * sum(apply(ef.features, 2, var))
> for (i in 2:15) kl[i] <- sum(kmeans(ef.features, centers=i)$withinss)
> plot(1:15, kl, type="b", xlab="Число кластеров", ylab="Сумма квадратов расстояний внутри кластеров")
```

![](https://github.com/IHappyPlant/Data_Analysis/blob/master/images/homework_5/optimal_clusters.png)

Исходя из графика, можно говорить, что оптимальным числом кластеров является 2 или 4. Будем считать оптимальным число 4.

Проведём кластеризацию с помощью ```k-means``` для 4 кластеров:

```R
> opt_clusters <- 4
> kc <- kmeans(ef.features, opt_clusters)
> aggregate(ef.features,by=list(kc$cluster),FUN=mean)
  Group.1       wg        it       fr       ns       bm       lg       gb        pl       aa       sd       sw       dk       ny       fd       sp        id
1       1 85.00000 61.666667 77.16667 82.33333 76.33333 91.66667 76.50000 60.833333 55.00000 72.83333 70.66667 87.33333 77.50000 79.83333 56.83333 57.166667
2       2 54.40000 32.200000 51.20000 66.60000 49.60000 63.20000 82.40000 21.200000 29.60000 55.80000 36.80000 45.80000 43.00000 32.40000 42.60000 68.400000
3       3 23.28571  5.142857 16.28571 25.42857 13.57143 21.57143 28.28571  6.571429 10.14286 23.42857 43.85714 28.71429 20.42857 18.85714 10.71429  7.285714
4       4 48.00000 87.000000 62.00000 14.00000 56.00000 87.50000 34.00000 90.500000 39.50000 62.50000 28.50000 20.50000 19.50000 16.00000 88.50000 18.000000
```

Можно видеть явные различия значений переменных в кластерах.

Проведём нечёткую кластеризацию для выборки:

```R
> ef.f <- fanny(ef.features, opt_clusters, maxit=2000)
> ef.f$membership
                        [,1]      [,2]       [,3]       [,4]
ground coffee     0.31156215 0.2564245 0.12045496 0.31155836
instant coffee    0.24873102 0.3219937 0.18054036 0.24873496
tea bags          0.31875170 0.2580212 0.10447954 0.31874757
sugarless sweets  0.09933603 0.1209906 0.68033617 0.09933724
packaged biscuits 0.30010908 0.2890404 0.11074097 0.30010959
packaged soup     0.27557786 0.3320771 0.11676337 0.27558167
tinned soup       0.17069865 0.2077417 0.45085893 0.17070072
instant potatoes  0.11700202 0.1404780 0.62551667 0.11700335
frozen fish       0.12012958 0.1446979 0.61504154 0.12013100
frozen vegetables 0.08097406 0.0975308 0.74052014 0.08097500
fresh apples      0.33133308 0.2570378 0.08030113 0.33132802
fresh oranges     0.33546688 0.2490748 0.07999798 0.33546032
tinned fruit      0.25404251 0.3196074 0.17230393 0.25404619
shop jam          0.28843777 0.2954701 0.12765306 0.28843909
garlic clove      0.26232911 0.2843018 0.19103819 0.26233088
butter            0.31871783 0.2587270 0.10384119 0.31871397
margarine         0.30945078 0.2648620 0.11623898 0.30944827
olive oil         0.28281500 0.2888028 0.14556605 0.28281619
yogurt            0.19303706 0.2349183 0.37900528 0.19303938
crispbread        0.20191520 0.2313646 0.36480319 0.20191703
```

Построим график для результата fuzzy-кластеризации:

```R
> plot(ef.f, which=1)
```

![](https://github.com/IHappyPlant/Data_Analysis/blob/master/images/homework_5/fuzzy_clustering.png)

## Анализ главных компонент и факторный анализ

В данной работе проводится анализ главных компонент и факторный анализ для выборки ```mtcars```.

Выведем выборку ```mtcars```:

```R
> mt <- mtcars
> mt
                     mpg cyl  disp  hp drat    wt  qsec vs am gear carb
Mazda RX4           21.0   6 160.0 110 3.90 2.620 16.46  0  1    4    4
Mazda RX4 Wag       21.0   6 160.0 110 3.90 2.875 17.02  0  1    4    4
Datsun 710          22.8   4 108.0  93 3.85 2.320 18.61  1  1    4    1
Hornet 4 Drive      21.4   6 258.0 110 3.08 3.215 19.44  1  0    3    1
Hornet Sportabout   18.7   8 360.0 175 3.15 3.440 17.02  0  0    3    2
Valiant             18.1   6 225.0 105 2.76 3.460 20.22  1  0    3    1
Duster 360          14.3   8 360.0 245 3.21 3.570 15.84  0  0    3    4
Merc 240D           24.4   4 146.7  62 3.69 3.190 20.00  1  0    4    2
Merc 230            22.8   4 140.8  95 3.92 3.150 22.90  1  0    4    2
Merc 280            19.2   6 167.6 123 3.92 3.440 18.30  1  0    4    4
Merc 280C           17.8   6 167.6 123 3.92 3.440 18.90  1  0    4    4
Merc 450SE          16.4   8 275.8 180 3.07 4.070 17.40  0  0    3    3
Merc 450SL          17.3   8 275.8 180 3.07 3.730 17.60  0  0    3    3
Merc 450SLC         15.2   8 275.8 180 3.07 3.780 18.00  0  0    3    3
Cadillac Fleetwood  10.4   8 472.0 205 2.93 5.250 17.98  0  0    3    4
Lincoln Continental 10.4   8 460.0 215 3.00 5.424 17.82  0  0    3    4
Chrysler Imperial   14.7   8 440.0 230 3.23 5.345 17.42  0  0    3    4
Fiat 128            32.4   4  78.7  66 4.08 2.200 19.47  1  1    4    1
Honda Civic         30.4   4  75.7  52 4.93 1.615 18.52  1  1    4    2
Toyota Corolla      33.9   4  71.1  65 4.22 1.835 19.90  1  1    4    1
Toyota Corona       21.5   4 120.1  97 3.70 2.465 20.01  1  0    3    1
Dodge Challenger    15.5   8 318.0 150 2.76 3.520 16.87  0  0    3    2
AMC Javelin         15.2   8 304.0 150 3.15 3.435 17.30  0  0    3    2
Camaro Z28          13.3   8 350.0 245 3.73 3.840 15.41  0  0    3    4
Pontiac Firebird    19.2   8 400.0 175 3.08 3.845 17.05  0  0    3    2
Fiat X1-9           27.3   4  79.0  66 4.08 1.935 18.90  1  1    4    1
Porsche 914-2       26.0   4 120.3  91 4.43 2.140 16.70  0  1    5    2
Lotus Europa        30.4   4  95.1 113 3.77 1.513 16.90  1  1    5    2
Ford Pantera L      15.8   8 351.0 264 4.22 3.170 14.50  0  1    5    4
Ferrari Dino        19.7   6 145.0 175 3.62 2.770 15.50  0  1    5    6
Maserati Bora       15.0   8 301.0 335 3.54 3.570 14.60  0  1    5    8
Volvo 142E          21.4   4 121.0 109 4.11 2.780 18.60  1  1    4    2
```

Выведем суммарную информацию о выборке и построим матрицу корреляции:

```R
> summary(mt)
      mpg             cyl             disp             hp             drat             wt             qsec             vs               am              gear            carb      
 Min.   :10.40   Min.   :4.000   Min.   : 71.1   Min.   : 52.0   Min.   :2.760   Min.   :1.513   Min.   :14.50   Min.   :0.0000   Min.   :0.0000   Min.   :3.000   Min.   :1.000  
 1st Qu.:15.43   1st Qu.:4.000   1st Qu.:120.8   1st Qu.: 96.5   1st Qu.:3.080   1st Qu.:2.581   1st Qu.:16.89   1st Qu.:0.0000   1st Qu.:0.0000   1st Qu.:3.000   1st Qu.:2.000  
 Median :19.20   Median :6.000   Median :196.3   Median :123.0   Median :3.695   Median :3.325   Median :17.71   Median :0.0000   Median :0.0000   Median :4.000   Median :2.000  
 Mean   :20.09   Mean   :6.188   Mean   :230.7   Mean   :146.7   Mean   :3.597   Mean   :3.217   Mean   :17.85   Mean   :0.4375   Mean   :0.4062   Mean   :3.688   Mean   :2.812  
 3rd Qu.:22.80   3rd Qu.:8.000   3rd Qu.:326.0   3rd Qu.:180.0   3rd Qu.:3.920   3rd Qu.:3.610   3rd Qu.:18.90   3rd Qu.:1.0000   3rd Qu.:1.0000   3rd Qu.:4.000   3rd Qu.:4.000  
 Max.   :33.90   Max.   :8.000   Max.   :472.0   Max.   :335.0   Max.   :4.930   Max.   :5.424   Max.   :22.90   Max.   :1.0000   Max.   :1.0000   Max.   :5.000   Max.   :8.000  
> cor(mt)
            mpg        cyl       disp         hp        drat         wt        qsec         vs          am       gear        carb
mpg   1.0000000 -0.8521620 -0.8475514 -0.7761684  0.68117191 -0.8676594  0.41868403  0.6640389  0.59983243  0.4802848 -0.55092507
cyl  -0.8521620  1.0000000  0.9020329  0.8324475 -0.69993811  0.7824958 -0.59124207 -0.8108118 -0.52260705 -0.4926866  0.52698829
disp -0.8475514  0.9020329  1.0000000  0.7909486 -0.71021393  0.8879799 -0.43369788 -0.7104159 -0.59122704 -0.5555692  0.39497686
hp   -0.7761684  0.8324475  0.7909486  1.0000000 -0.44875912  0.6587479 -0.70822339 -0.7230967 -0.24320426 -0.1257043  0.74981247
drat  0.6811719 -0.6999381 -0.7102139 -0.4487591  1.00000000 -0.7124406  0.09120476  0.4402785  0.71271113  0.6996101 -0.09078980
wt   -0.8676594  0.7824958  0.8879799  0.6587479 -0.71244065  1.0000000 -0.17471588 -0.5549157 -0.69249526 -0.5832870  0.42760594
qsec  0.4186840 -0.5912421 -0.4336979 -0.7082234  0.09120476 -0.1747159  1.00000000  0.7445354 -0.22986086 -0.2126822 -0.65624923
vs    0.6640389 -0.8108118 -0.7104159 -0.7230967  0.44027846 -0.5549157  0.74453544  1.0000000  0.16834512  0.2060233 -0.56960714
am    0.5998324 -0.5226070 -0.5912270 -0.2432043  0.71271113 -0.6924953 -0.22986086  0.1683451  1.00000000  0.7940588  0.05753435
gear  0.4802848 -0.4926866 -0.5555692 -0.1257043  0.69961013 -0.5832870 -0.21268223  0.2060233  0.79405876  1.0000000  0.27407284
carb -0.5509251  0.5269883  0.3949769  0.7498125 -0.09078980  0.4276059 -0.65624923 -0.5696071  0.05753435  0.2740728  1.00000000
```

Видно, что большая часть переменных тесно коррелирует между собой.

### Анализ главных компонент

Проведём анализ главных компонент:

```R
> res = princomp(mt, scores=TRUE)
> res
Call:
princomp(x = mt, scores = TRUE)

Standard deviations:
     Comp.1      Comp.2      Comp.3      Comp.4      Comp.5      Comp.6      Comp.7      Comp.8      Comp.9     Comp.10     Comp.11 
134.3827868  37.5472829   3.0226511   1.2860724   0.8922099   0.6530910   0.3037193   0.2814568   0.2467490   0.2073344   0.1952988 

 11  variables and  32 observations.
```

Выведем суммарную информацию о результате:

```R
> summary(res)
Importance of components:
                            Comp.1     Comp.2       Comp.3       Comp.4       Comp.5       Comp.6       Comp.7       Comp.8       Comp.9      Comp.10      Comp.11
Standard deviation     134.3827868 37.5472829 3.0226510768 1.286072e+00 0.8922099047 6.530910e-01 3.037193e-01 2.814568e-01 2.467490e-01 2.073344e-01 1.952988e-01
Proportion of Variance   0.9269989  0.0723684 0.0004689947 8.490294e-05 0.0000408626 2.189471e-05 4.735181e-06 4.066450e-06 3.125380e-06 2.206656e-06 1.957902e-06
Cumulative Proportion    0.9269989  0.9993673 0.9998362482 9.999212e-01 0.9999620137 9.999839e-01 9.999886e-01 9.999927e-01 9.999958e-01 9.999980e-01 1.000000e+00
```

Наибольшую долю дисперсии и стандартное отклонение имеют первые две компоненты.

Выведем матрицу нагрузок и диаграмму дисперсий:

```R
> loadings(res)

Loadings:
     Comp.1 Comp.2 Comp.3 Comp.4 Comp.5 Comp.6 Comp.7 Comp.8 Comp.9 Comp.10 Comp.11
mpg                 0.982                0.144                                     
cyl                       -0.228 -0.239  0.794 -0.425 -0.189         0.132  -0.145 
disp -0.900  0.435                                                                 
hp   -0.435 -0.899                                                                 
drat                              0.133 -0.227        -0.939         0.184         
wt                         0.128  0.244  0.127  0.187  0.156  0.391  0.830         
qsec                       0.886  0.214  0.190 -0.255 -0.103        -0.204         
vs                         0.177        -0.103               -0.684  0.303  -0.626 
am                        -0.136        -0.205 -0.201         0.572 -0.163  -0.733 
gear                      -0.130  0.276 -0.335 -0.802  0.217 -0.156  0.204   0.191 
carb               -0.103 -0.269  0.855  0.284  0.165        -0.128 -0.240         

               Comp.1 Comp.2 Comp.3 Comp.4 Comp.5 Comp.6 Comp.7 Comp.8 Comp.9 Comp.10 Comp.11
SS loadings     1.000  1.000  1.000  1.000  1.000  1.000  1.000  1.000  1.000   1.000   1.000
Proportion Var  0.091  0.091  0.091  0.091  0.091  0.091  0.091  0.091  0.091   0.091   0.091
Cumulative Var  0.091  0.182  0.273  0.364  0.455  0.545  0.636  0.727  0.818   0.909   1.000

> plot(res)
```

![](https://github.com/IHappyPlant/Data_Analysis/blob/master/images/homework_6/dispersions_plot.png)

Из графика так же следует, что в анализ следует включить первую и вторую компоненты, имеющие ```0.99``` суммарного вклада в дисперсию.

Выведем график ```biplot```:

![](https://github.com/IHappyPlant/Data_Analysis/blob/master/images/homework_6/biplot_res.png)

На графике отображены переменные в пространстве из двух компонент. Можно видеть, что оси исходных переменных ориентированы, примерно, по первой главной оси.

Координаты переменных в пространстве главных компонент:

```R
                       Comp.1    Comp.2    Comp.3     Comp.4     Comp.5      Comp.6       Comp.7      Comp.8     Comp.9     Comp.10    Comp.11
Mazda RX4           79.596425  2.132241 -2.153336 -2.7073437  0.7023522  0.31486106  0.098695018 -0.07789812  0.2000092 -0.29008191 -0.1057706
Mazda RX4 Wag       79.598570  2.147487 -2.215124 -2.1782888  0.8843859  0.45343873  0.003554594 -0.09566630  0.3533243 -0.19283553 -0.1069047
Datsun 710         133.894096 -5.057570 -2.137950  0.3460330 -1.1061111 -1.17298584 -0.005755581  0.13624782  0.1976423  0.07634353 -0.2668713
Hornet 4 Drive      -8.516559 44.985630  1.233763  0.8273631 -0.4240145  0.05789705  0.024307168  0.22120800 -0.3559844 -0.09057039 -0.2088354
Hornet Sportabout -128.686342 30.817402  3.343421 -0.5211000 -0.7365801  0.33290957 -0.106304777 -0.05301719 -0.1532714 -0.18862217  0.1092563
Valiant             23.220146 35.106518 -3.259562  1.4005360 -0.8029768  0.08837864 -0.238946304  0.42390551 -0.1012944 -0.03769010 -0.2757693
```

Вычислим корреляцию между переменной ```mtcars$hp``` и первой компонентой:

```R
> cor(mt$hp, res$scores[,1])
[1] -0.8658103
```

Это можно интерпретировать, как обратную зависимость количества миль, на которые хватает одного галлона топлива от мощности двигателя.

Выделим главные компоненты:

```R
> mt.pc <- prcomp(mt, retx=TRUE, tol=0.2)
> mt.pc
Standard deviations (1, .., p=11):
 [1] 136.5330479  38.1480776   3.0710166   1.3066508   0.9064862   0.6635411   0.3085791   0.2859604   0.2506973   0.2106519   0.1984238

Rotation (n x k) = (11 x 2):
              PC1          PC2
mpg  -0.038118199  0.009184847
cyl   0.012035150 -0.003372487
disp  0.899568146  0.435372320
hp    0.434784387 -0.899307303
drat -0.002660077 -0.003900205
wt    0.006239405  0.004861023
qsec -0.006671270  0.025011743
vs   -0.002729474  0.002198425
am   -0.001962644 -0.005793760
gear -0.002604768 -0.011272462
carb  0.005766010 -0.027779208
```

Стандартные отклонения почти совпадают со значениями из ```princomp```.

### Факторный анализ

Проведём факторный анализ для выборки ```mtcars```. Число факторов выставим равным двум, потому что при анализе главных компонент было выделено две компоненты.

```R
> fc.mt <- factanal(mt, factors=2, start=NULL, scores="Bartlett", STATISTIC)
> fc.mt

Call:
factanal(x = mt, factors = 2, data = STATISTIC, start = NULL, scores = "Bartlett")

Uniquenesses:
  mpg   cyl  disp    hp  drat    wt  qsec    vs    am  gear  carb 
0.167 0.070 0.096 0.143 0.298 0.168 0.150 0.256 0.171 0.246 0.386 

Loadings:
     Factor1 Factor2
mpg   0.686  -0.602 
cyl  -0.629   0.731 
disp -0.730   0.609 
hp   -0.337   0.862 
drat  0.807  -0.225 
wt   -0.810   0.420 
qsec -0.162  -0.908 
vs    0.291  -0.812 
am    0.907         
gear  0.860   0.125 
carb          0.783 

               Factor1 Factor2
SS loadings      4.494   4.357
Proportion Var   0.409   0.396
Cumulative Var   0.409   0.805

Test of the hypothesis that 2 factors are sufficient.
The chi square statistic is 68.57 on 34 degrees of freedom.
The p-value is 0.000405 
```

Можно говорить, что факторная модель построена корректно.

Выведем значения факторов, и визуализируем их на графике:

```R
> fc.mt$scores
                       Factor1    Factor2
Mazda RX4            0.9890378  0.5511303
Mazda RX4 Wag        0.8678640  0.4389650
Datsun 710           0.8200264 -0.7190164
Hornet 4 Drive      -0.9037340 -1.0296695
Hornet Sportabout   -0.8180521  0.3763585
Valiant             -1.1309232 -1.2195912
Duster 360          -0.6028694  1.0218565
Merc 240D           -0.1651975 -1.4143499
Merc 230            -0.4862422 -1.8249638
Merc 280            -0.0913340 -0.3719778
Merc 280C           -0.1963929 -0.4731022
Merc 450SE          -0.8559393  0.3270799
Merc 450SL          -0.8060845  0.2863751
Merc 450SLC         -0.9053941  0.2295260
Cadillac Fleetwood  -1.5427300  0.5417737
Lincoln Continental -1.5083593  0.6003184
Chrysler Imperial   -1.2683883  0.6915240
Fiat 128             0.9684668 -1.0730046
Honda Civic          1.3885294 -0.7754735
Toyota Corolla       1.0450180 -1.1609839
Toyota Corona       -0.3190116 -1.5171176
Dodge Challenger    -0.9069538  0.2851125
AMC Javelin         -0.8451478  0.2286083
Camaro Z28          -0.4791284  1.1438922
Pontiac Firebird    -0.9677729  0.3824520
Fiat X1-9            1.0036224 -0.9081320
Porsche 914-2        1.6544060  0.1672327
Lotus Europa         1.6576535 -0.1531256
Ford Pantera L       1.0516751  2.1427956
Ferrari Dino         1.4384212  1.2463459
Maserati Bora        1.1095999  2.5390610
Volvo 142E           0.8053347 -0.5598994

> plot(fc.mt$scores)
```

![](https://github.com/IHappyPlant/Data_Analysis/blob/master/images/homework_6/two_factors_plot.png)

На диаграмме видно два сгущения: в районе ```-1.0``` и ```1.0``` по оси ```Factor1```.

## Многомерное шкалирование

В работе проводится анализ результатов, полученных при выполнении готовых скриптов для Метрического шкалирования для различных выборок.

Выполним следующий скрипт, и проаназилируем результаты:

```R
# Metric scaling for USJudgeRatings
jr <- USJudgeRatings
dd.jr <- as.dist((1 - cor(jr))/2)
print(round(1000 * dd.jr)) # (prints more nicely)
loc1 <- cmdscale(dd.jr,k=2)
print(loc1)
px1 <- loc1[, 1]
py1 <- loc1[, 2]
plot(px1, py1,type = "n", xlab = "", ylab = "", asp = 1, axes = FALSE,
     main = "cmdscale(jr)")
text(px1, py1, rownames(loc1), cex = 0.6)
```

Выведем полученную матрицу расстояний между переменными:

```R
     CONT INTG DMNR DILG CFMG DECI PREP FAMI ORAL WRIT PHYS
INTG  567                                                  
DMNR  577   18                                             
DILG  494   64   82                                        
CFMG  432   93   93   21                                   
DECI  457   99   98   22    9                              
PREP  494   61   72   11   21   21                         
FAMI  513   66   79   21   32   29    5                    
ORAL  506   44   47   23   25   26    8    9               
WRIT  522   46   53   20   29   27    7    5    3          
PHYS  473  129  106   94   60   64   76   78   54   72     
RTEN  517   31   28   35   36   38   25   29    9   16   47
```

Выведем координаты переменных в двумерном пространстве:

```R
             [,1]         [,2]
CONT  0.463314994 -0.003054957
INTG -0.103293806 -0.026630163
DMNR -0.113640296  0.013453627
DILG -0.030692225 -0.029171195
CFMG  0.023902256 -0.009077700
DECI  0.003698592 -0.004880505
PREP -0.031751297 -0.012601359
FAMI -0.048580596 -0.008905098
ORAL -0.043688117  0.005371000
WRIT -0.058549520 -0.002617129
PHYS -0.006450281  0.063178063
RTEN -0.054269703  0.014935414
```

Выведем график, построенный по полученным координатам:

![metric_scaling_plot](https://github.com/IHappyPlant/Data_Analysis/blob/master/images/homework_7/metric_scaling_plot.png)

Поскольку, матрица расстояний создавалась на основе корреляционной матрицы, те переменные, которые сильно коррелируют между собой, на графике находятся ближе друг к другу. Таким образом, можно видеть, что переменная ```CONT``` не коррелирует с остальными. Также, можно видеть два кластера: переменные, коррелирующие друг с другом, и переменные, не коррелирующие с остальными (```CONT```).



Выполним метрическое шкалирование для выборки ```eurodist```, объясним входные данные и полученные результаты:

```R
loc <- cmdscale(eurodist)
x <- loc[, 1]
y <- -loc[, 2] # reflect so North is at the top
## note asp = 1, to ensure Euclidean distances are represented correctly
plot(x, y, type = "n", xlab = "", ylab = "", asp = 1, axes = FALSE,
	 main = "cmdscale(eurodist)")
text(x, y, rownames(loc), cex = 0.6)
```

Входная выборка ```eurodist``` представляет собой матрицу расстояний между некоторыми городами Европы:

```R
> eurodist
                Athens Barcelona Brussels Calais Cherbourg Cologne Copenhagen Geneva Gibraltar Hamburg Hook of Holland Lisbon Lyons Madrid Marseilles Milan Munich Paris Rome
Barcelona         3313                                                                                                                                                       
Brussels          2963      1318                                                                                                                                             
Calais            3175      1326      204                                                                                                                                    
Cherbourg         3339      1294      583    460                                                                                                                             
Cologne           2762      1498      206    409       785                                                                                                                   
Copenhagen        3276      2218      966   1136      1545     760                                                                                                           
Geneva            2610       803      677    747       853    1662       1418                                                                                                
Gibraltar         4485      1172     2256   2224      2047    2436       3196   1975                                                                                         
Hamburg           2977      2018      597    714      1115     460        460   1118      2897                                                                               
Hook of Holland   3030      1490      172    330       731     269        269    895      2428     550                                                                       
Lisbon            4532      1305     2084   2052      1827    2290       2971   1936       676    2671            2280                                                       
Lyons             2753       645      690    739       789     714       1458    158      1817    1159             863   1178                                                
Madrid            3949       636     1558   1550      1347    1764       2498   1439       698    2198            1730    668  1281                                          
Marseilles        2865       521     1011   1059      1101    1035       1778    425      1693    1479            1183   1762   320   1157                                   
Milan             2282      1014      925   1077      1209     911       1537    328      2185    1238            1098   2250   328   1724        618                        
Munich            2179      1365      747    977      1160     583       1104    591      2565     805             851   2507   724   2010       1109   331                  
Paris             3000      1033      285    280       340     465       1176    513      1971     877             457   1799   471   1273        792   856    821           
Rome               817      1460     1511   1662      1794    1497       2050    995      2631    1751            1683   2700  1048   2097       1011   586    946  1476     
Stockholm         3927      2868     1616   1786      2196    1403        650   2068      3886     949            1500   3231  2108   3188       2428  2187   1754  1827 2707
Vienna            1991      1802     1175   1381      1588     937       1455   1019      2974    1155            1205   2937  1157   2409       1363   898    428  1249 1209
                Stockholm
Barcelona                
Brussels                 
Calais                   
Cherbourg                
Cologne                  
Copenhagen               
Geneva                   
Gibraltar                
Hamburg                  
Hook of Holland          
Lisbon                   
Lyons                    
Madrid                   
Marseilles               
Milan                    
Munich                   
Paris                    
Rome                     
Stockholm                
Vienna               2105
```

![eurodist_plot](https://github.com/IHappyPlant/Data_Analysis/blob/master/images/homework_7/eurodist_plot.png)

На построенном графике отображено расположение городов относительно друг друга. В запускаемом скрипте значение ```y``` инвертировано, чтобы север оказался сверху.

## Временные ряды

В работе проводится анализ временного ряда ```discoveries``` методами предварительного анализа, структурной модели Гаусса, авторегрессии, и модели ARIMA.

Выборка ```discoveries``` представляет собой количество крупных изобретений и научных открытий по годам с 1860 по 1959:

```R
> dsc <- discoveries
> dsc
Time Series:
Start = 1860 
End = 1959 
Frequency = 1 
  [1]  5  3  0  2  0  3  2  3  6  1  2  1  2  1  3  3  3  5  2  4  4  0  2  3  7 12  3 10  9  2  3  7  7  2  3  3  6  2  4  3  5  2  2  4  0  4  2  5  2  3  3  6  5  8  3  6  6  0  5
 [60]  2  2  2  6  3  4  4  2  2  4  7  5  3  3  0  2  2  2  1  3  4  2  2  1  1  1  2  1  4  4  3  2  1  4  1  1  1  0  0  2  0
> is.ts(dsc)
[1] TRUE
```

### Предварительный анализ

Выведем график временного ряда:

![](https://github.com/IHappyPlant/Data_Analysis/blob/master/images/homework_8/discoveries_plot.png)

Визуально, ряд показывает тенденцию к убыванию с уменьшением амплитуды колебаний значений переменной. Колебания переменной не случайны: после скачка вверх, значение переменной почти всегда падает вниз, а потом опять повышается, что логично, так как после многих открытий и изобретений прогресс на некоторое время приостанавливается, а потом опять продолжается.

Построим графики автокорреляции и частотной автокорреляции:

![](https://github.com/IHappyPlant/Data_Analysis/blob/master/images/homework_8/acf_plot.png)

![](https://github.com/IHappyPlant/Data_Analysis/blob/master/images/homework_8/pacf_plot.png)

Из графика ACF следует, что временной ряд имеет колебания с периодом 4.

### Подгонка структурной модели Гаусса

Построим трендовую модель ряда ```discoveries```:

```R
> dsc.st <- StructTS(dsc, type = "trend")
> dsc.st

Call:
StructTS(x = dsc, type = "trend")

Variances:
  level    slope  epsilon  
 0.4001   0.0000   3.4286
```

Выполним  подгонку значений:

```R
> dsc.st.fitted <- fitted(dsc.st)
> head(dsc.st.fitted)
         level      slope
[1,] 5.0000000  0.0000000
[2,] 3.5889585 -0.4588920
[3,] 1.3143265 -0.8924083
[4,] 1.2220451 -0.7420990
[5,] 0.2593573 -0.7762188
[6,] 0.9851156 -0.5786680
```

Отобразим полученные значения на графиках:

![](https://github.com/IHappyPlant/Data_Analysis/blob/master/images/homework_8/gaussian_fitted_plot.png)

Проведём диагностику построенной модели:

```R
> tsdiag(dsc.st)
```

![](https://github.com/IHappyPlant/Data_Analysis/blob/master/images/homework_8/ts_diag_plot.png)

Из графиков можно видеть:

* Стандартизированные остатки имеют близкое к нулю среднее
* Автокорреляция значима только для лага 1
* p-значения > 0.5, поэтому нулевая гипотеза ("модель не соответствует") принимается.

Тем не менее, выполним прогноз пяти последующих уровней временного ряда:

```R
> predict(dsc.st, n.ahead = 5)
$pred
Time Series:
Start = 1960 
End = 1964 
Frequency = 1 
[1] 0.6528276 0.6045233 0.5562191 0.5079148 0.4596105

$se
Time Series:
Start = 1960 
End = 1964 
Frequency = 1 
[1] 2.205681 2.301508 2.395176 2.486928 2.576969
```

### Подгонка модели авторегрессии

Выполним построение и подгонку модели авторегрессии для выборки. Будем использовать для построения модели различные методы. Примерим метод Юла-Уолкера:

```R
> dsc.arw <- ar(dsc)
> dsc.arw

Call:
ar(x = dsc)

Coefficients:
     1       2  
0.2217  0.1913  

Order selected 2  sigma^2 estimated as  4.62
> predict(dsc.arw, n.ahead = 5)
$pred
Time Series:
Start = 1960 
End = 1964 
Frequency = 1 
[1] 2.202328 2.308043 2.752723 2.871530 2.982924

$se
Time Series:
Start = 1960 
End = 1964 
Frequency = 1 
[1] 2.149515 2.201707 2.261546 2.270883 2.275473
```

Метод наименьших квадратов:

```R
> dsc.arw <- ar(dsc, method = "ols")
> dsc.arw

Call:
ar(x = dsc, method = "ols")

Coefficients:
     1       2       3  
0.1977  0.1783  0.1443  

Intercept: -0.00434 (0.2119) 

Order selected 3  sigma^2 estimated as  4.352
> predict(dsc.arw, n.ahead = 5)
$pred
Time Series:
Start = 1960 
End = 1964 
Frequency = 1 
[1] 1.839025 2.134685 2.232422 2.569936 2.696783

$se
Time Series:
Start = 1960 
End = 1964 
Frequency = 1 
[1] 2.086047 2.126438 2.174277 2.223313 2.235411
```

Метод Бурга:

```R
Можно видеть > dsc.arw <- ar(dsc, method = "burg")
> dsc.arw

Call:
ar(x = dsc, method = "burg")

Coefficients:
     1       2       3  
0.1959  0.1618  0.1444  

Order selected 3  sigma^2 estimated as  4.374
> predict(dsc.arw, n.ahead = 5)
$pred
Time Series:
Start = 1960 
End = 1964 
Frequency = 1 
[1] 1.867353 2.198325 2.276446 2.614887 2.741593

$se
Time Series:
Start = 1960 
End = 1964 
Frequency = 1 
[1] 2.091388 2.131122 2.171828 2.217993 2.228389
```

Значения ```se```, полученные разными методами, почти не отличаются друг от друга, и значений, полученных при использовании модели Гаусса.

### Подгонка модели ARIMA

Проведём анализ временного ряда, используя модель ARIMA.

Получим параметры ```p, d, q``` для модели ARIMA. Вначале получим параметр ```d```. Возьмём одну разность с лагом 1:

```R
> dsc.d <- diff(dsc, 1, 1)
> plot(dsc.d)
```

![](https://github.com/IHappyPlant/Data_Analysis/blob/master/images/homework_8/arima_d_param.png)

Из графика можно видеть, что среднее значение близко к нулю, и размах примерно одинаковый. Поэтому, выберем параметр ```d=1```.

На графиках из предварительного анализа, АКФ имеет резко выделяющееся значение на лаге 1, ЧАКФ имеет резко выделяющиеся значение на лаге 1, а также корреляции на других лагах, но они не выходят за пределы доверительного интервала. Исходя из этого, выберем параметры ```p=1, q=0```.

Построим и протестируем модель ARIMA с выбранными параметрами:

```R
> dsc.arima <- arima(dsc.d, order=c(1, 1, 0))
> dsc.arima

Call:
arima(x = dsc.d, order = c(1, 1, 0))

Coefficients:
          ar1
      -0.6599
s.e.   0.0749

sigma^2 estimated as 12.06:  log likelihood = -261.36,  aic = 526.71
> tsdiag(dsc.arima)
```

![](https://github.com/IHappyPlant/Data_Analysis/blob/master/images/homework_8/arima_test.png)

Стандартизированное среднее ненулевое, но близкое к нулю. Автокорреляция остатков убывает до определённого момента, а потом остаётся на одном уровне, менясь в положительную, или отрицательную сторону. p-значения близки к нулю, следовательно, модель является статистически значимой.

Предскажем следующие пять уровней ряда:

```R
> predict(dsc.arima, n.ahead = 5, se.fit = TRUE)
$pred
Time Series:
Start = 1960 
End = 1964 
Frequency = 1 
[1]  0.63977088 -1.10232669  0.04735792 -0.71136807 -0.21065238

$se
Time Series:
Start = 1960 
End = 1964 
Frequency = 1 
[1] 3.473067 3.668385 4.551130 4.856667 5.397168
```

Значения ```se``` отличаются от значений, полученных при использовании других моделей, и больше них похожи на значения исходного временного ряда.

Для сравнения, выполним анализ с параметрами ```p=0, d=0, q=2``` (будем считать, что график ЧАКФ имеет форму синусоиды, а график АКФ имеет высокие значения на первых нескольких лагах):

```R
> dsc.arima <- arima(dsc.d, order=c(0, 0, 2))
> dsc.arima

Call:
arima(x = dsc.d, order = c(0, 0, 2))

Coefficients:
          ma1      ma2  intercept
      -0.7724  -0.0666    -0.0156
s.e.   0.0971   0.1048     0.0368

sigma^2 estimated as 4.569:  log likelihood = -216.27,  aic = 440.53
> tsdiag(dsc.arima)
```

![](https://github.com/IHappyPlant/Data_Analysis/blob/master/images/homework_8/arima_bad_result.png)

Для модели с такими параметрами значения стандартизированного среднего соответствуют случайной ошибке (белому шуму), а сама модель статистически незначима (p-значения > 0.05).

```R
> predict(dsc.arima, n.ahead = 5, se.fit = TRUE)
$pred
Time Series:
Start = 1960 
End = 1964 
Frequency = 1 
[1]  1.03939726  0.08008513 -0.01561862 -0.01561862 -0.01561862

$se
Time Series:
Start = 1960 
End = 1964 
Frequency = 1 
[1] 2.137634 2.700999 2.704746 2.704746 2.704746
```

Предсказанные значения так же похожи на значения, полученные при использовании других неподходящих моделей (модель Гаусса и AR-модель).

## Индивидуальное задание

В работе проводится анализ выборки ```Boston``` из пакета ```MASS```. Выборка содержит данные о 506 жилищных участках пригорода Бостона, полученные в ходе переписи 1970 года. Выборка содержит 506 строк и 14 столбцов. Целевым столбцом является столбец ```medv```, содержащий среднюю стоимость домов, занимаемых владельцами, в тысячах долларов.

Выведем первые насколько строк выборки, и суммарную информацию о ней:

```R
> head(bs)
     crim zn indus chas   nox    rm  age    dis rad tax ptratio  black lstat medv
1 0.00632 18  2.31    0 0.538 6.575 65.2 4.0900   1 296    15.3 396.90  4.98 24.0
2 0.02731  0  7.07    0 0.469 6.421 78.9 4.9671   2 242    17.8 396.90  9.14 21.6
3 0.02729  0  7.07    0 0.469 7.185 61.1 4.9671   2 242    17.8 392.83  4.03 34.7
4 0.03237  0  2.18    0 0.458 6.998 45.8 6.0622   3 222    18.7 394.63  2.94 33.4
5 0.06905  0  2.18    0 0.458 7.147 54.2 6.0622   3 222    18.7 396.90  5.33 36.2
6 0.02985  0  2.18    0 0.458 6.430 58.7 6.0622   3 222    18.7 394.12  5.21 28.7
> summary(bs)
      crim                zn             indus            chas              nox               rm             age              dis              rad              tax       
 Min.   : 0.00632   Min.   :  0.00   Min.   : 0.46   Min.   :0.00000   Min.   :0.3850   Min.   :3.561   Min.   :  2.90   Min.   : 1.130   Min.   : 1.000   Min.   :187.0  
 1st Qu.: 0.08205   1st Qu.:  0.00   1st Qu.: 5.19   1st Qu.:0.00000   1st Qu.:0.4490   1st Qu.:5.886   1st Qu.: 45.02   1st Qu.: 2.100   1st Qu.: 4.000   1st Qu.:279.0  
 Median : 0.25651   Median :  0.00   Median : 9.69   Median :0.00000   Median :0.5380   Median :6.208   Median : 77.50   Median : 3.207   Median : 5.000   Median :330.0  
 Mean   : 3.61352   Mean   : 11.36   Mean   :11.14   Mean   :0.06917   Mean   :0.5547   Mean   :6.285   Mean   : 68.57   Mean   : 3.795   Mean   : 9.549   Mean   :408.2  
 3rd Qu.: 3.67708   3rd Qu.: 12.50   3rd Qu.:18.10   3rd Qu.:0.00000   3rd Qu.:0.6240   3rd Qu.:6.623   3rd Qu.: 94.08   3rd Qu.: 5.188   3rd Qu.:24.000   3rd Qu.:666.0  
 Max.   :88.97620   Max.   :100.00   Max.   :27.74   Max.   :1.00000   Max.   :0.8710   Max.   :8.780   Max.   :100.00   Max.   :12.127   Max.   :24.000   Max.   :711.0  
    ptratio          black            lstat            medv      
 Min.   :12.60   Min.   :  0.32   Min.   : 1.73   Min.   : 5.00  
 1st Qu.:17.40   1st Qu.:375.38   1st Qu.: 6.95   1st Qu.:17.02  
 Median :19.05   Median :391.44   Median :11.36   Median :21.20  
 Mean   :18.46   Mean   :356.67   Mean   :12.65   Mean   :22.53  
 3rd Qu.:20.20   3rd Qu.:396.23   3rd Qu.:16.95   3rd Qu.:25.00  
 Max.   :22.00   Max.   :396.90   Max.   :37.97   Max.   :50.00
```

Построим матрицу корреляции:

```R
> cor(bs)
               crim          zn       indus         chas         nox          rm         age         dis          rad         tax    ptratio       black      lstat       medv
crim     1.00000000 -0.20046922  0.40658341 -0.055891582  0.42097171 -0.21924670  0.35273425 -0.37967009  0.625505145  0.58276431  0.2899456 -0.38506394  0.4556215 -0.3883046
zn      -0.20046922  1.00000000 -0.53382819 -0.042696719 -0.51660371  0.31199059 -0.56953734  0.66440822 -0.311947826 -0.31456332 -0.3916785  0.17552032 -0.4129946  0.3604453
indus    0.40658341 -0.53382819  1.00000000  0.062938027  0.76365145 -0.39167585  0.64477851 -0.70802699  0.595129275  0.72076018  0.3832476 -0.35697654  0.6037997 -0.4837252
chas    -0.05589158 -0.04269672  0.06293803  1.000000000  0.09120281  0.09125123  0.08651777 -0.09917578 -0.007368241 -0.03558652 -0.1215152  0.04878848 -0.0539293  0.1752602
nox      0.42097171 -0.51660371  0.76365145  0.091202807  1.00000000 -0.30218819  0.73147010 -0.76923011  0.611440563  0.66802320  0.1889327 -0.38005064  0.5908789 -0.4273208
rm      -0.21924670  0.31199059 -0.39167585  0.091251225 -0.30218819  1.00000000 -0.24026493  0.20524621 -0.209846668 -0.29204783 -0.3555015  0.12806864 -0.6138083  0.6953599
age      0.35273425 -0.56953734  0.64477851  0.086517774  0.73147010 -0.24026493  1.00000000 -0.74788054  0.456022452  0.50645559  0.2615150 -0.27353398  0.6023385 -0.3769546
dis     -0.37967009  0.66440822 -0.70802699 -0.099175780 -0.76923011  0.20524621 -0.74788054  1.00000000 -0.494587930 -0.53443158 -0.2324705  0.29151167 -0.4969958  0.2499287
rad      0.62550515 -0.31194783  0.59512927 -0.007368241  0.61144056 -0.20984667  0.45602245 -0.49458793  1.000000000  0.91022819  0.4647412 -0.44441282  0.4886763 -0.3816262
tax      0.58276431 -0.31456332  0.72076018 -0.035586518  0.66802320 -0.29204783  0.50645559 -0.53443158  0.910228189  1.00000000  0.4608530 -0.44180801  0.5439934 -0.4685359
ptratio  0.28994558 -0.39167855  0.38324756 -0.121515174  0.18893268 -0.35550149  0.26151501 -0.23247054  0.464741179  0.46085304  1.0000000 -0.17738330  0.3740443 -0.5077867
black   -0.38506394  0.17552032 -0.35697654  0.048788485 -0.38005064  0.12806864 -0.27353398  0.29151167 -0.444412816 -0.44180801 -0.1773833  1.00000000 -0.3660869  0.3334608
lstat    0.45562148 -0.41299457  0.60379972 -0.053929298  0.59087892 -0.61380827  0.60233853 -0.49699583  0.488676335  0.54399341  0.3740443 -0.36608690  1.0000000 -0.7376627
medv    -0.38830461  0.36044534 -0.48372516  0.175260177 -0.42732077  0.69535995 -0.37695457  0.24992873 -0.381626231 -0.46853593 -0.5077867  0.33346082 -0.7376627  1.0000000
```

Сильнее всего переменная ```medv``` коррелирует с переменными ```rm``` и ```lstat```. В свою очередь, переменная ```rm``` сильно коррелирует только с ```lstat```, а ```lstat``` коррелирует с переменными ```indus, nox, rm, age, tax``` и ```medv```. 

Поскольку все колонки, в том числе целевая колонка ```medv```, содержат числовые данные, будем исследовать выборку с помощью моделей регрессии.

Построим парную регрессию для пар переменых ```medv, rm``` и ```medv, lstat```, а также множественную регрессию, чтобы оценить зависимость переменной ```medv``` от всех остальных переменных.

### Парная регрессия

Построим парную регрессию по переменным ```medv, rm```. Вначале, отобразим переменные на графике:

![](https://github.com/IHappyPlant/Data_Analysis/blob/master/images/homework_9/rm_medv_plot.png)



На графике можно видеть связь двух переменных, близкую к линейной. Построим модель линейной регрессии для этих переменных:

```R
> lm.rat1 <- lm(formula=bs$medv~bs$rm)
> summary(lm.rat1)

Call:
lm(formula = bs$medv ~ bs$rm)

Residuals:
    Min      1Q  Median      3Q     Max 
-23.346  -2.547   0.090   2.986  39.433 

Coefficients:
            Estimate Std. Error t value Pr(>|t|)    
(Intercept)  -34.671      2.650  -13.08   <2e-16 ***
bs$rm          9.102      0.419   21.72   <2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 6.616 on 504 degrees of freedom
Multiple R-squared:  0.4835,	Adjusted R-squared:  0.4825 
F-statistic: 471.8 on 1 and 504 DF,  p-value: < 2.2e-16
```

Переменная ```rm``` и константа ```Intersept``` являются значимыми для модели. ```p-value = 2.2e-16 << 0.05 ```, следовательно, модель линейной регрессии является статистически значимой.

Проведём дисперсионный анализ:

```R
> anova(lm.rat1)
Analysis of Variance Table

Response: bs$medv
           Df Sum Sq Mean Sq F value    Pr(>F)    
bs$rm       1  20654 20654.4  471.85 < 2.2e-16 ***
Residuals 504  22062    43.8                      
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
```

Вычислим доверительные области и построим линию регрессии:

```R
> CPI.df <- cbind(predict(lm.rat1,interval ="conf"),
+                 predict(lm.rat1,interval ="pred"))
> CPI.df <- CPI.df[,-4]
> colnames(CPI.df) <- c("Y_fit","CI_l","CI_u","PI_l","PI_u")
> head(CPI.df)
     Y_fit     CI_l     CI_u     PI_l     PI_u
1 25.17575 24.55039 25.80110 12.16206 38.18943
2 23.77402 23.18536 24.36269 10.76205 36.78599
3 30.72803 29.78817 31.66790 17.69545 43.76062
4 29.02594 28.20203 29.84984 16.00120 42.05067
5 30.38215 29.46676 31.29755 17.35131 43.41299
6 23.85594 23.26582 24.44606 10.84390 36.86798
> matplot(bs$rm, CPI.df,
+         type="l",lwd=c(2,1,1,1,1),col=c(1,2,2,4,4),
+         ylab="medv",xlab="rm")
> matpoints(bs$rm, bs$medv,pch=20)
```

![medv_rm_regr_plot](https://github.com/IHappyPlant/Data_Analysis/blob/master/images/homework_9/medv_rm_regr_plot.png)

Почти все значения находятся внутри доверительного интервала.



Построим парную регрессию по переменным ```medv``` и ```lstat```. Построим график для переменных:

![](https://github.com/IHappyPlant/Data_Analysis/blob/master/images/homework_9/medv_lstat_plot.png)



Зависимость между переменными меньше похожа на линейную, однако, возможно, линейная модель регрессии всё ещё применима. Построим модель регрессии, и проверим:

```R
> lm.rat2 <- lm(formula=bs$medv~bs$lstat)
> summary(lm.rat2)

Call:
lm(formula = bs$medv ~ bs$lstat)

Residuals:
    Min      1Q  Median      3Q     Max 
-15.168  -3.990  -1.318   2.034  24.500 

Coefficients:
            Estimate Std. Error t value Pr(>|t|)    
(Intercept) 34.55384    0.56263   61.41   <2e-16 ***
bs$lstat    -0.95005    0.03873  -24.53   <2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 6.216 on 504 degrees of freedom
Multiple R-squared:  0.5441,	Adjusted R-squared:  0.5432 
F-statistic: 601.6 on 1 and 504 DF,  p-value: < 2.2e-16

> print(anova(lm.rat2))
Analysis of Variance Table

Response: bs$medv
           Df Sum Sq Mean Sq F value    Pr(>F)    
bs$lstat    1  23244 23243.9  601.62 < 2.2e-16 ***
Residuals 504  19472    38.6                      
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
```

Переменная ```lstat``` и константа ```Intercept``` являются значимыми для модели. ```p-value = 2.2e-16 << 0.05```, значит, и вся модель является статистически значимой.

Построим и отобразим линии регрессии и доверительные интервалы:

```R
> CPI.df1 <- cbind(predict(lm.rat2,interval ="conf"),
+                  predict(lm.rat2,interval ="pred"))
Warning message:
In predict.lm(lm.rat2, interval = "pred") :
  predictions on current data refer to _future_ responses

> CPI.df1 <- CPI.df1[,-4]
> colnames(CPI.df1) <- c("Y_fit","CI_l","CI_u","PI_l","PI_u")
> head(CPI.df1)
     Y_fit     CI_l     CI_u     PI_l     PI_u
1 29.82260 29.02530 30.61989 17.58460 42.06059
2 25.87039 25.26525 26.47553 13.64341 38.09737
3 30.72514 29.87348 31.57681 18.48349 42.96680
4 31.76070 30.84359 32.67780 19.51432 44.00708
5 29.49008 28.71208 30.26808 17.25333 41.72683
6 29.60408 28.81952 30.38865 17.36691 41.84125
> matplot(bs$lstat, CPI.df1,
+         type="l",lwd=c(2,1,1,1,1),col=c(1,2,2,4,4),
+         ylab="medv",xlab="lstat")
> matpoints(bs$lstat, bs$medv,pch=20)
```

![](https://github.com/IHappyPlant/Data_Analysis/blob/master/images/homework_9/medv_lstat_regr_plot.png)

Почти все значения попали в доверительный интервал.

### Множественная регрессия

Построим множественную регрессию для переменной ```medv```, чтобы оценить её зависимость от всех остальных компонент:

```R
> fm1 <- lm(medv~., data = bs)
> summary(fm1)

Call:
lm(formula = medv ~ ., data = bs)

Residuals:
    Min      1Q  Median      3Q     Max 
-15.595  -2.730  -0.518   1.777  26.199 

Coefficients:
              Estimate Std. Error t value Pr(>|t|)    
(Intercept)  3.646e+01  5.103e+00   7.144 3.28e-12 ***
crim        -1.080e-01  3.286e-02  -3.287 0.001087 ** 
zn           4.642e-02  1.373e-02   3.382 0.000778 ***
indus        2.056e-02  6.150e-02   0.334 0.738288    
chas         2.687e+00  8.616e-01   3.118 0.001925 ** 
nox         -1.777e+01  3.820e+00  -4.651 4.25e-06 ***
rm           3.810e+00  4.179e-01   9.116  < 2e-16 ***
age          6.922e-04  1.321e-02   0.052 0.958229    
dis         -1.476e+00  1.995e-01  -7.398 6.01e-13 ***
rad          3.060e-01  6.635e-02   4.613 5.07e-06 ***
tax         -1.233e-02  3.760e-03  -3.280 0.001112 ** 
ptratio     -9.527e-01  1.308e-01  -7.283 1.31e-12 ***
black        9.312e-03  2.686e-03   3.467 0.000573 ***
lstat       -5.248e-01  5.072e-02 -10.347  < 2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 4.745 on 492 degrees of freedom
Multiple R-squared:  0.7406,	Adjusted R-squared:  0.7338 
F-statistic: 108.1 on 13 and 492 DF,  p-value: < 2.2e-16

> anova(fm1)
Analysis of Variance Table

Response: medv
           Df  Sum Sq Mean Sq  F value    Pr(>F)    
crim        1  6440.8  6440.8 286.0300 < 2.2e-16 ***
zn          1  3554.3  3554.3 157.8452 < 2.2e-16 ***
indus       1  2551.2  2551.2 113.2984 < 2.2e-16 ***
chas        1  1529.8  1529.8  67.9393 1.543e-15 ***
nox         1    76.2    76.2   3.3861 0.0663505 .  
rm          1 10938.1 10938.1 485.7530 < 2.2e-16 ***
age         1    90.3    90.3   4.0087 0.0458137 *  
dis         1  1779.5  1779.5  79.0262 < 2.2e-16 ***
rad         1    34.1    34.1   1.5159 0.2188325    
tax         1   329.6   329.6  14.6352 0.0001472 ***
ptratio     1  1309.3  1309.3  58.1454 1.266e-13 ***
black       1   593.3   593.3  26.3496 4.109e-07 ***
lstat       1  2410.8  2410.8 107.0634 < 2.2e-16 ***
Residuals 492 11078.8    22.5                       
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
```

Почти все переменные являются статистически значимыми для модели. Сама модель множественной регрессии так же является значимой с ```p-value = 2.2e-16 << 0.05```.

Построим таблицу наблюдаемых и предсказанных значений для ```medv```:

```R
> predict.fm1 = predict.lm(fm1)
> tabout <- cbind(bs$medv, predict.fm1)
> head(tabout, n = 10)
> head(tabout, n = 10)
        predict.fm1
1  24.0    30.00384
2  21.6    25.02556
3  34.7    30.56760
4  33.4    28.60704
5  36.2    27.94352
6  28.7    25.25628
7  22.9    23.00181
8  27.1    19.53599
9  16.5    11.52364
10 18.9    18.92026
```

Построим диаграмму квантиль-квантиль:

![](https://github.com/IHappyPlant/Data_Analysis/blob/master/images/homework_9/quantile_quantile_plot.png)

Диаграмма иллюстрирует соответствие предсказанных и наблюдаемых значений. Следовательно, модель регрессии подобрана и построена корректно.
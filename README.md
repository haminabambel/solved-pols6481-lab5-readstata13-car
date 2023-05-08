Download Link: https://assignmentchef.com/product/solved-pols6481-lab5-readstata13-car
<br>
<strong>Objectives</strong>: The primary objective is to explore tests of statistical significance involving regression slope coefficients. You will perform tests of whether a slope coefficient equals 0 (the most common test) and whether two slope coefficients equal each other.

<ol>

 <li><strong> Datasets</strong>: <em>speeding_tickets_text.dta</em></li>

</ol>

<strong>III. Packages</strong>:  <em>readstata13</em>, <em>car</em>

<ol>

 <li><strong> Preparation</strong></li>

</ol>

<ul>

 <li>Open RStudio by double-clicking the icon or selecting RStudio from the Windows Start menu.</li>

 <li>Open the project from the Projects menu</li>

 <li>Pull from Github for any updates</li>

 <li>Navigate in the lower right file area to the Lab 3 section</li>

 <li>5) Open the R script by typing <em>Ctrl</em>+<em>O</em> or by clicking on File in the upper-left corner, using the dropdown menu, and navigating to the script in your working directory.</li>

 <li>Line 1 will start the “here” library. If you have not installed that package, the code was the first line of Lab 3.</li>

 <li>Line 2 installs a package to read the older version Stata file, then loads the library.</li>

</ul>




<ol>

 <li><strong> Instructions for Lab 05</strong></li>

</ol>




You will use data on more than 30,000 traffic citations in Massachusetts, from Michael Makowsky and Thomas Stratmann’s 2009 article, “Political Economy at Any Speed: What Determines Traffic Citations?” <em>American Economic Review</em> 99: 509–527.




The dependent variable is <em>Amount</em>, which is the dollar amount of the fine on a moving violation.




Key independent variables are <em>Age</em>, the age of the offending driver, and <em>MPHover</em>, the miles per hour over the speed limit. There are also six dummy variables: <em>Female</em>, <em>Black</em>, <em>Hispanic</em>, <em>StatePol</em> (if the officer was a state patrolman), <em>OutTown</em> (if the driver resided outside the jurisdiction), and <em>OutState</em> (if the driver resided outside the state of Massachusetts).




The first question you will explore is how citations vary depending on the age of the driver and the speed at which he or she was driving.




Begin by loading the dataset, typing the following line, changing the directory if needed:

tickets &lt;- read.dta13(here(“data”,”speeding_tickets_text.dta”))

The data frame that you will start with is named tickets, however you should also run line 6 of the code to drop all the cases for which no dollar fine was recorded:

&gt; complete &lt;- subset(tickets, tickets$Amount!=”NA”)




Run line 8 to regress the citation amount (<em>Amount</em>) on <em>Age</em> and <em>MPHover</em>:

&gt; basic &lt;-lm(Amount ~ Age + MPHover, data=complete)

&gt; summary(basic)




Run lines 9–10 to save two important features of the model. First is the vector of coefficients, which is a list of three numbers: the estimated intercept and the two estimated slope coefficients. Second is the <strong><em>variance-covariance matrix of the estimators</em></strong>, which is a 3×3 square array; the first row (and column) is associated with the intercept,<a href="#_edn1" name="_ednref1">[1]</a> and the second and third rows (and columns) are associated with the two explanatory variables.

&gt; vector.b &lt;- basic$coefficients

&gt; matrix.b &lt;- vcov(basic)




Along the main diagonal of the matrix are the estimated variance of the estimated intercept and the estimated slope coefficients. The square roots of the entries along the main diagonal of the variance-covariance matrix of the estimators are standard errors of the regression intercept and coefficients. You can check this by running line 11, which calculates the square root of the entry in the second row and second column, which corresponds to the variable <em>Age</em>:

&gt; sqrt(matrix.b[2,2])

Does the result of this line of code equal the standard error of the coefficient for <em>Age</em>?




Run lines 12–13 to generate the <em>t</em> statistics shown after typing summary(model1); you will be dividing an element of the vector of estimated coefficients by the square root of the corresponding variance of the estimated coefficient.

For example, to find the <em>t </em>statistic for the regression model coefficient for <em>Age</em>, type:

&gt; t_Age = vector.b[2]/sqrt(matrix.b[2,2])

To find the <em>t </em>statistic for the regression model coefficient for <em>MPHover</em>, type:

&gt; t_MPHover = vector.b[3]/sqrt(matrix.b[3,3])




In order to test for statistical significance, it is important to know the critical value. One option is to inspect Student’s <em>t </em>Table, if you have one handy. Another option is to use the “leftover” degrees of freedom from the model that generated the estimated coefficients and their variances.




Recall that the degrees of freedom for the <em>t</em> test of a regression coefficient is <em>n</em>–<em>k</em>–1, where <em>k</em> represents the number of regressors and <em>n</em> represents the sample size. For 95% confidence, we want to know the number of standard errors that separates the lower 97.5% of the <em>t </em>distribution with <em>n</em>–<em>k</em>–1 degrees of freedom from the upper 2.5%. We can find that number using line 14:

&gt; qt(.975,basic$df.residual)




Another approach to testing the statistical significance (versus a null hypothesis that <em>b</em> = 0) is to examine whether the confidence interval excludes 0. To view the confidence intervals, type the following code, as shown in line 15:

&gt; confint(basic)




The <em>Age</em> variable does not have a statistically significant effect, and we will drop it from the rest of the analysis, even though there’s no harm to including it. Run line 17 of the R script to estimate a model with <em>MPHover </em>plus six dummy variables, indicating the driver’s race, ethnicity, gender, and residential status, as well as the officer’s employer. Run line 18 to view the estimates.




Sometimes we are interested in whether two slope coefficients are equal. For example, are residents of other states fined more than residents of other towns in Massachusetts? We can generate a <em>t </em>statistic for a test of the equality of coefficients for <em>OutTown </em>and <em>OutState</em>; if <em>t </em>is less than the appropriate critical value, then we retain the null hypothesis, which means that we do not have enough evidence to say that the coefficients have different values in the population.




Run lines 19–20 to save the vector of estimated coefficients (which has eight entries including the intercept) and the <strong><em>variance-covariance matrix of the estimators</em></strong>, which is a square array with eight rows and eight columns.

&gt; vector.d &lt;- dummies$coefficients

&gt; matrix.d &lt;- vcov(dummies)

Along the main diagonal of the matrix are the estimated variances of the estimated intercept and the estimated slope coefficients.




The numerator of the equality-of-coefficients <em>t</em> statistic is the difference between the coefficients. To compare <em>OutTown</em> to <em>OutState</em>, you need the sixth and seventh entries in the vector:

&gt; num = (vector.d[7] – vector.d[6])

The denominator of this test statistic is the standard error of the difference of coefficients, which adds the variance of one coefficient plus the variance of the other coefficient, and then subtracts two times the covariance of the two coefficients, and then takes the square root:

&gt; denom = sqrt(matrix.d[7,7] + matrix.d[6,6] – 2*matrix.d[7,6])

The <em>t </em>statistic will be the difference of coefficients divided by its standard error:

&gt; t_eq = num/denom

The above lines of code are shown in lines 21–23.




Another way of establishing that two coefficients are unequal begins by inspecting confidence intervals. You can run the code shown in line 24 to display all the 95% confidence intervals; if two confidence intervals do not intersect then it suggests the variables have different effects.




On your own (or with Tom’s assistance), repeat the process and examine whether race and gender have the same effects. Can you reject the null hypothesis that the coefficient for <em>Black</em> is equal to the coefficient for <em>Female</em>?




Line 27 shows code for an <em>F</em> test of this hypothesis. As we will cover this afternoon’s lecture, the <em>F</em> statistic for a test such as this equals the <em>t</em> statistic squared. If you correctly carry out a <em>t </em>test of the equality of coefficients, and square the result, you should obtain the same number as the <em>F</em> statistic. If you want to see that the <em>t </em>test and <em>F</em> test are equivalent, you should give your <em>t</em> statistic a name, give your degrees of freedom a name, and use those to find the <em>p</em> values as follows:

&gt; t_bf = num_bf/denom_bf

&gt; df &lt;- summary(dummies)$df[2]

&gt; 2*(1-pt(abs(t_bf),df))




In order to examine the measures of model fit, you will want to collect some information from this model by running lines 29–34. First, line 29 will show you the degrees of freedom; the first number is the degrees of freedom consumed by the model (i.e., <em>k </em>+ 1), and the second number is the degrees of freedom left over for the residual (i.e., <em>n – k – </em>1). You can save this latter number and name it dfr by running line 30.




Next, line 31 will show you the <em>F</em> statistic for the full model, which we will discuss next week, and repeats the degrees of freedom – although the number is one less than before because it does not subtract one for the intercept (because the null model, which has <em>only</em> an intercept – the sample mean value of <em>y</em> – has seven <em>more</em> degrees of freedom). Line 32 will show you the R-squared and adjusted R-squared.




To examine the ANOVA table, which will play a role in considering model specification next week, run the following lines of code, which are lines 34–38 in the R script:

&gt; anovatable = anova(dummies); anovatable[2]

&gt; ssr&lt;-sum(dummies$residuals^2)

&gt; sst&lt;-sum((complete$Amount-mean(complete$Amount))^2)

&gt; sse&lt;-sum((predict(dummies) – mean(complete$Amount))^2)

&gt; sst-sse




You can check that the first seven Sum Sq entries in the ANOVA table sum to equal the number that R calculates with the code in line 37; this is the explained sum of squares, or the model sum of squares. You can also subtract the number reported by line 37 from the number reported by line 36 (as shown in the last line above) and check that it equals the Residuals Sum Sq in the ANOVA table, which also equals the amount reported by line 35.




Then, write down the following values:

Total Sum of Squares (line 36)                                      __________

Explained Sum of Squares (line 37)                               __________

Residual Sum of Squares (line 35)                                 __________

Model R<sup>2</sup> (line 32)                                                        __________

Number of Observations [<em>n</em>]                                          __________

Number of Explanatory Variables [<em>k</em>]                             __________

Residual Degrees of Freedom (line 30, second entry)      __________




When you type summary(dummies), R automatically gives the F-statistic for the “overall significance of a regression” test; you will find it listed in the last line of code of regression results (<em>F</em> = 4759, or 4758.8). R notes that it is distributed with 7 numerator degrees of freedom (representing the <em>k </em>= 7 explanatory variables) and 31666 denominator degrees of freedom (representing <em>n</em> – <em>k </em>– 1).




You should be able to calculate this number yourself. The equation is:

<em>F</em> = <u> </u>




Of more immediate importance, you should be able to calculate the Multiple R<sup>2</sup> from the model:

You should also be able to calculate the Adjusted R<sup>2</sup> from the model using this equation:

1 – [1–<em>R</em><sup>2</sup>]




To clear the <strong>Environment</strong>, type rm(list=ls()) or click on the broom icon.

To clear the <strong>Console</strong> window, type Ctrl-<em>l</em>




<a href="#_ednref1" name="_edn1">[1]</a> Suppose you wanted to inspect the dependent variable with no independent variables, then you could estimate a “null model” that has no explanatory variables. Whereas we usually estimate the regression line E(<em>y</em> | <em>x</em>), we are just looking at <em>E</em>(<em>y</em>) when there are no <em>x</em>. The following three results should be observed:

<ol>

 <li>The intercept ought to equal the mean value of <em>y</em>. (There is no slope.)</li>

 <li>The residual standard error should equal the standard deviation of <em>y</em>.</li>

 <li>The standard error of the intercept ought to equal the standard deviation of <em>y</em> divided by the square root of <em>n</em>–1, which would also be equal to the standard error of .</li>

</ol>

Run the following lines of code to verify these properties:

&gt; null &lt;- lm(complete$Amount ~ 1); summary(null)

&gt; mu.y = mean(complete$Amount); mu.y

&gt; sd(complete$Amount)

&gt; sey = sd(complete$Amount)/sqrt(null$df.residual); se.y

&gt; t_crit = qt(.975, null$df.residual); t_crit

&gt; mu.y – t_crit*se.y; mu.y + t_crit*se.y

&gt; confint(null)

<strong> </strong>
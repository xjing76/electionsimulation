Summary
=======

We utilized data from 2008 and 2012 Election to predict the distribution of result for Election 2016 using simulation. A lot of the predictions of this election would only give an expectation of the final result. Some may also provide a probability of the certainty of their prediction. In this project, we tried to replicate the shape of probability density function of the result. We first fitted the data with logistic model with mixed effect at both state level and individual level. Then we used R package {arm} simulated 100 alphas’ and betas’ based of the fitted model to obtain 100 simulated models. Then we fitted the historical data into the simulated models, which gives us 100 simulated results for each state. And finally, we used the 100 simulated results to call for each state, which helps us to achieve the final result --- the distribution of votes.

Data
====

The data we used contained background information of each poll, ,identified by their pollers’ id, along with information about voting percentage for Democratic and Republican Party, start and end date, sample size, number of dates till the election day(recorded as negative number), the historical final results (from year 1992 to 2012) for each state, etc.
For each state, there were several time points of polling. We treated each poll differently regard to their time points when fitting the overall model, and then view each poll independently (ignore time effect) when using simulation.

There is a sneek peak of the data that we used.

    ##     state Clinton Trump Other Undecided poll_id              pollster
    ## 1 alabama      36    57    NA         7   25356 WashPost/SurveyMonkey
    ## 2 alabama      40    53    NA         7   25537         Ipsos/Reuters
    ## 3 alabama      36    58    NA        NA   25727            UPI/CVOTER
    ## 4 alabama      37    57    NA         6   26097         Ipsos/Reuters
    ## 5 alabama      38    56    NA        NA   26022            UPI/CVOTER
    ## 6 alabama      38    52    NA        10   26407         Ipsos/Reuters
    ##   start_date end_date sample_subpopulation sample_size     mode
    ## 1      20675    20698    Registered Voters         958 Internet
    ## 2      20692    20712        Likely Voters         628 Internet
    ## 3      20709    20722        Likely Voters         558 Internet
    ## 4      20713    20733        Likely Voters         580 Internet
    ## 5      20729    20736        Likely Voters         332 Internet
    ## 6      20727    20747        Likely Voters         773 Internet
    ##   partisanship partisan_affiliation date id DemPctAll DemPctHead2Head
    ## 1  Nonpartisan                 None  -68 25 0.3600000       0.3870968
    ## 2  Nonpartisan                 None  -54 25 0.4000000       0.4301075
    ## 3  Nonpartisan                 None  -44 25 0.3829787       0.3829787
    ## 4  Nonpartisan                 None  -33 25 0.3700000       0.3936170
    ## 5  Nonpartisan                 None  -30 25 0.4042553       0.4042553
    ## 6  Nonpartisan                 None  -19 25 0.3800000       0.4222222
    ##       wtsByVar wtsByState stateAbbrev pctRep92 pctRep96 pctRep00 pctRep04
    ## 1 0.0002476543        0.2          AL 53.82356  53.7307 57.58719  62.9003
    ## 2 0.0003903106        0.4          AL 53.82356  53.7307 57.58719  62.9003
    ## 3 0.0004234875        0.6          AL 53.82356  53.7307 57.58719  62.9003
    ## 4 0.0004115218        0.8          AL 53.82356  53.7307 57.58719  62.9003
    ## 5 0.0007254005        1.0          AL 53.82356  53.7307 57.58719  62.9003
    ## 6 0.0003155894        1.2          AL 53.82356  53.7307 57.58719  62.9003
    ##   pctRep08 pctRep12   evWts color
    ## 1 13.14419 11.52369 15.8972  blue
    ## 2 13.14419 11.52369 15.8972  blue
    ## 3 13.14419 11.52369 15.8972  blue
    ## 4 13.14419 11.52369 15.8972  blue
    ## 5 13.14419 11.52369 15.8972  blue
    ## 6 13.14419 11.52369 15.8972  blue

Linear model
============

We first tried simple OLS model for our prediction.

\(headothead = \beta_0 + \beta_1* date + \beta_2 * pctRep08+\beta_3 * pctRep12+\beta_4 *id + \epsilon\)

GLM model
=========

\(headothead = \beta_1*p08 + \beta_2*p12 +\alpha_{1i} * time +\alpha_{0i}+ \beta_0+\epsilon\)

\(\alpha_{1i} \sim N(\beta_{3i},\sigma_{\alpha_1})\)

\(\alpha_{0i} \sim N(0,\sigma_{\alpha_0})\)

<table style="text-align:center"><caption>
<strong>GLM Regression Results</strong>
</caption>
<tr><td colspan="3" style="border-bottom: 1px solid black"></td></tr><tr><td style="text-align:left"></td><td colspan="2">
<em>Dependent variable:</em>
</td></tr>
<tr><td></td><td colspan="2" style="border-bottom: 1px solid black"></td></tr>
<tr><td style="text-align:left"></td><td colspan="2">
DemPctHead2Head
</td></tr>
<tr><td style="text-align:left"></td><td>
<em>linear</em>
</td><td>
<em>OLS</em>
</td></tr>
<tr><td style="text-align:left"></td><td>
<em>mixed-effects</em>
</td><td>
<em></em>
</td></tr>
<tr><td style="text-align:left"></td><td>
(1)
</td><td>
(2)
</td></tr>
<tr><td colspan="3" style="border-bottom: 1px solid black"></td></tr><tr><td style="text-align:left">
I(date/365)
</td><td>
-0.013
</td><td></td></tr>
<tr><td style="text-align:left"></td><td>
(0.009)
</td><td></td></tr>
<tr><td style="text-align:left"></td><td></td><td></td></tr>
<tr><td style="text-align:left">
date
</td><td></td><td>
-0.0001<sup>\*\*\*</sup>
</td></tr>
<tr><td style="text-align:left"></td><td></td><td>
(0.00002)
</td></tr>
<tr><td style="text-align:left"></td><td></td><td></td></tr>
<tr><td style="text-align:left">
pctRep08
</td><td>
-0.001
</td><td>
-0.003<sup>\*\*\*</sup>
</td></tr>
<tr><td style="text-align:left"></td><td>
(0.002)
</td><td>
(0.001)
</td></tr>
<tr><td style="text-align:left"></td><td></td><td></td></tr>
<tr><td style="text-align:left">
pctRep12
</td><td>
-0.008<sup>\*\*\*</sup>
</td><td>
-0.005<sup>\*\*\*</sup>
</td></tr>
<tr><td style="text-align:left"></td><td>
(0.002)
</td><td>
(0.001)
</td></tr>
<tr><td style="text-align:left"></td><td></td><td></td></tr>
<tr><td style="text-align:left">
id
</td><td></td><td>
0.0004<sup>\*\*\*</sup>
</td></tr>
<tr><td style="text-align:left"></td><td></td><td>
(0.0001)
</td></tr>
<tr><td style="text-align:left"></td><td></td><td></td></tr>
<tr><td style="text-align:left">
Constant
</td><td>
0.509<sup>\*\*\*</sup>
</td><td>
0.498<sup>\*\*\*</sup>
</td></tr>
<tr><td style="text-align:left"></td><td>
(0.004)
</td><td>
(0.003)
</td></tr>
<tr><td style="text-align:left"></td><td></td><td></td></tr>
<tr><td colspan="3" style="border-bottom: 1px solid black"></td></tr><tr><td style="text-align:left">
Observations
</td><td>
945
</td><td>
945
</td></tr>
<tr><td style="text-align:left">
R<sup>2</sup>
</td><td></td><td>
0.746
</td></tr>
<tr><td style="text-align:left">
Adjusted R<sup>2</sup>
</td><td></td><td>
0.745
</td></tr>
<tr><td style="text-align:left">
Log Likelihood
</td><td>
1,971.102
</td><td></td></tr>
<tr><td style="text-align:left">
Akaike Inf. Crit.
</td><td>
-3,926.203
</td><td></td></tr>
<tr><td style="text-align:left">
Bayesian Inf. Crit.
</td><td>
-3,887.394
</td><td></td></tr>
<tr><td style="text-align:left">
Residual Std. Error
</td><td></td><td>
0.037 (df = 940)
</td></tr>
<tr><td style="text-align:left">
F Statistic
</td><td></td><td>
688.940<sup>\*\*\*</sup> (df = 4; 940)
</td></tr>
<tr><td colspan="3" style="border-bottom: 1px solid black"></td></tr><tr><td style="text-align:left">
<em>Note:</em>
</td><td colspan="2" style="text-align:right">
<sup>*</sup>p\<0.1; <sup>**</sup>p\<0.05; <sup>***</sup>p\<0.01
</td></tr>
</table>

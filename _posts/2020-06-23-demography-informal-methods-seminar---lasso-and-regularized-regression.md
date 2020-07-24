---
title: "Demography Informal Methods Seminar - Lasso and Regularized Regression"

author: "Corey Sparks, Ph.D. - UTSA Department of Demography"
date: "June 23, 2020"
layout: post
---


<section class="main-content">
<div id="regularization-in-regression" class="section level2">
<h2>Regularization in Regression</h2>
<p>I would suggest you read Chapter 6 and especially section 6.2 of <a href="https://utsacloud-my.sharepoint.com/:b:/g/personal/corey_sparks_utsa_edu/EcwJ43xnPG5Jg2XquDnHdmkBPstgvNIyqTTugCfEeFGDFQ?e=SSlWyT">An Introduction to Statistical Learning</a> to get a full treatment of this topic.</p>
<p>If you want to read the sources for the Lasso, check out the paper by <a href="https://web.stanford.edu/~hastie/Papers/glmnet.pdf">Friedman, Hastie and Tibshirani (2010)</a></p>
<p>Why do I want to talk about this? The Lasso and regularization are a popular technique in data science today that allows you to perform both variable selection and optimized prediction in one algorithm. It also can alleviate some problems of multicollinearity between predictors. The Lasso can be applied to linear, generalized linear, generalized linear mixed models and the Cox model, and there are R packages that provide this functionality.</p>
<p>We will first have a look at the linear model solution and extend it.</p>
</div>
<div id="the-good-ol-generalized-linear-model" class="section level1">
<h1>The good ol’ (generalized) linear model</h1>
<div id="linear-regression" class="section level3">
<h3>Linear regression</h3>
<p>The standard linear regression can be written:</p>
<p><span class="math display">\[\hat y = \hat \beta_0 + \sum {\hat \beta_k x_{ik}}\]</span></p>
<p>where the terms on the right correspond to the linear predictor for the outcome (<span class="math inline">\(y\)</span>), given the values of the predictors (<span class="math inline">\(x_k\)</span>). The <span class="math inline">\(\hat \beta\)</span> values are the unbiased estimates of the regression parameters, for the linear model, typically found by least squares estimation.</p>
</div>
<div id="generalized-linear-model" class="section level3">
<h3>Generalized linear model</h3>
<ul>
<li><p>Extension of the linear modeling concept to non-gaussian outcomes.</p></li>
<li><p>Link function <span class="math inline">\(\eta\)</span> links the mean of the outcome to a linear predictor with a nonlinear function <span class="math inline">\(\eta\)</span></p></li>
<li><p>E.G. Logistic regression -</p></li>
</ul>
<p><span class="math display">\[log \left( \frac{p_i}{1-p_i} \right ) =\eta_i = \sum {\beta_k x_{ik}}\]</span> To get the probability from the model, we use the inverse logit transform: <span class="math display">\[p_i = \frac{1}{1+exp^{\eta}}\]</span></p>
</div>
<div id="model-estimation" class="section level3">
<h3>Model estimation</h3>
<p>Find the parameters of the model so that the model <em>Deviance</em> is minimized. In the linear regression context, Deviance is the same as the <em>Residual Squared Error</em> or <em>Residual Sums of Squares</em></p>
<p><span class="math display">\[\text{Deviance} =  \sum (y_i - \sum{\hat{\beta_k x_{ik}}}) ^2  \]</span></p>
<p>The problem is when there are parameters in the model that are zero or nearly zero, the model may have higher deviance than it could if some of those parameters were not in the model.</p>
<p>The question is how to go about doing this?</p>
<p>You may have seen methods of regression subsetting via stepwise, forward or backward selection. These methods iteratively insert or remove predictor variables from the model, and in this process the models are scored via either their RSS or AIC or some other information criteria.</p>
<p>Here is an example where we fit a linear model and the perform variable subset selection.</p>
<pre><code>## 
## Attaching package: &#39;dplyr&#39;</code></pre>
<pre><code>## The following objects are masked from &#39;package:stats&#39;:
## 
##     filter, lag</code></pre>
<pre><code>## The following objects are masked from &#39;package:base&#39;:
## 
##     intersect, setdiff, setequal, union</code></pre>
<pre><code>## Warning: Number of logged events: 655</code></pre>
<p>A common data science approach would be to fit a model with all predictors included, and then winnow the model to include only the significant predictors. We start with this type of model:</p>
<div class="sourceCode" id="cb5"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb5-1" data-line-number="1"><span class="kw">library</span>(MASS)</a></code></pre></div>
<pre><code>## 
## Attaching package: &#39;MASS&#39;</code></pre>
<pre><code>## The following object is masked from &#39;package:dplyr&#39;:
## 
##     select</code></pre>
<div class="sourceCode" id="cb8"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb8-1" data-line-number="1">X&lt;-<span class="kw">scale</span>(<span class="kw">model.matrix</span>(e0total<span class="op">~</span>. , <span class="dt">data=</span>prb2))</a>
<a class="sourceLine" id="cb8-2" data-line-number="2">dat&lt;-<span class="kw">data.frame</span>(X[,<span class="op">-</span><span class="dv">1</span>])</a>
<a class="sourceLine" id="cb8-3" data-line-number="3">dat<span class="op">$</span>y&lt;-prb2<span class="op">$</span>e0total</a>
<a class="sourceLine" id="cb8-4" data-line-number="4"></a>
<a class="sourceLine" id="cb8-5" data-line-number="5">fm&lt;-<span class="kw">glm</span>(y<span class="op">~</span>., <span class="dt">data=</span>dat) </a>
<a class="sourceLine" id="cb8-6" data-line-number="6"><span class="kw">summary</span>(fm)</a></code></pre></div>
<pre><code>## 
## Call:
## glm(formula = y ~ ., data = dat)
## 
## Deviance Residuals: 
##     Min       1Q   Median       3Q      Max  
## -4.3470  -1.0362   0.1334   0.9305   8.1069  
## 
## Coefficients:
##                                   Estimate Std. Error t value Pr(&gt;|t|)    
## (Intercept)                       67.95694    0.12294 552.744  &lt; 2e-16 ***
## continentAsia                      0.07720    0.20327   0.380   0.7045    
## continentEurope                    0.22202    0.29448   0.754   0.4519    
## continentNorth.America             0.39021    0.19561   1.995   0.0475 *  
## continentOceania                  -0.10322    0.17471  -0.591   0.5554    
## continentSouth.America             0.24651    0.16210   1.521   0.1301    
## population.                       -0.07920    0.13370  -0.592   0.5543    
## cbr                               -6.23299    3.19702  -1.950   0.0527 .  
## cdr                               -2.37212    1.31148  -1.809   0.0721 .  
## rate.of.natural.increase           5.53259    2.88553   1.917   0.0568 .  
## net.migration.rate                -0.11659    0.20356  -0.573   0.5675    
## imr                               -2.37542    0.42992  -5.525 1.12e-07 ***
## womandlifetimeriskmaternaldeath    0.06185    0.21018   0.294   0.7689    
## tfr                                0.77591    0.87183   0.890   0.3746    
## percpoplt15                       -0.97796    0.78461  -1.246   0.2142    
## percpopgt65                        3.02097    0.45506   6.639 3.49e-10 ***
## percurban                         -0.10826    0.23176  -0.467   0.6410    
## percpopinurbangt750k               0.30498    0.21709   1.405   0.1618    
## percpop1549hivaids2007            -1.75795    0.27465  -6.401 1.26e-09 ***
## percmarwomcontraall               -0.11868    0.38931  -0.305   0.7608    
## percmarwomcontramodern             0.60254    0.33495   1.799   0.0737 .  
## percppundernourished0204          -0.28350    0.20518  -1.382   0.1687    
## motorvehper1000pop0005             0.37047    0.31006   1.195   0.2337    
## percpopwaccessimprovedwatersource  0.33456    0.24727   1.353   0.1777    
## gnippppercapitausdollars           0.82764    0.35362   2.341   0.0203 *  
## popdenspersqkm                     0.30807    0.20080   1.534   0.1267    
## ---
## Signif. codes:  0 &#39;***&#39; 0.001 &#39;**&#39; 0.01 &#39;*&#39; 0.05 &#39;.&#39; 0.1 &#39; &#39; 1
## 
## (Dispersion parameter for gaussian family taken to be 3.159121)
## 
##     Null deviance: 25164.61  on 208  degrees of freedom
## Residual deviance:   578.12  on 183  degrees of freedom
## AIC: 859.76
## 
## Number of Fisher Scoring iterations: 2</code></pre>
<div class="sourceCode" id="cb10"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb10-1" data-line-number="1"><span class="kw">length</span>(<span class="kw">coef</span>(fm))</a></code></pre></div>
<pre><code>## [1] 26</code></pre>
<p>One traditional approach would then be to do some sort of variable selection, what people used to be taught was stepwise (backward or forward) elmination to obtain the best subset of predictors:</p>
</div>
<div id="stepwise-selection-model" class="section level3">
<h3>Stepwise selection model</h3>
<div class="sourceCode" id="cb12"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb12-1" data-line-number="1">smod&lt;-fm<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb12-2" data-line-number="2"><span class="st">  </span><span class="kw">stepAIC</span>(<span class="dt">trace =</span> F, <span class="dt">direction =</span> <span class="st">&quot;both&quot;</span>)</a>
<a class="sourceLine" id="cb12-3" data-line-number="3"></a>
<a class="sourceLine" id="cb12-4" data-line-number="4"><span class="kw">summary</span>(smod)</a></code></pre></div>
<pre><code>## 
## Call:
## glm(formula = y ~ continentNorth.America + continentSouth.America + 
##     cbr + cdr + rate.of.natural.increase + imr + percpopgt65 + 
##     percpopinurbangt750k + percpop1549hivaids2007 + percmarwomcontramodern + 
##     percpopwaccessimprovedwatersource + gnippppercapitausdollars, 
##     data = dat)
## 
## Deviance Residuals: 
##     Min       1Q   Median       3Q      Max  
## -4.5939  -1.1511   0.1894   0.9632   8.0198  
## 
## Coefficients:
##                                   Estimate Std. Error t value Pr(&gt;|t|)    
## (Intercept)                        67.9569     0.1213 560.321  &lt; 2e-16 ***
## continentNorth.America              0.3215     0.1300   2.473   0.0142 *  
## continentSouth.America              0.2095     0.1317   1.591   0.1132    
## cbr                                -6.2002     3.0472  -2.035   0.0432 *  
## cdr                                -2.2039     1.2497  -1.764   0.0794 .  
## rate.of.natural.increase            5.5896     2.7241   2.052   0.0415 *  
## imr                                -2.4789     0.3761  -6.590 3.96e-10 ***
## percpopgt65                         3.6290     0.3294  11.016  &lt; 2e-16 ***
## percpopinurbangt750k                0.2849     0.1498   1.902   0.0586 .  
## percpop1549hivaids2007             -1.8568     0.2070  -8.971 2.37e-16 ***
## percmarwomcontramodern              0.3689     0.1699   2.172   0.0311 *  
## percpopwaccessimprovedwatersource   0.4307     0.2304   1.870   0.0630 .  
## gnippppercapitausdollars            1.2547     0.1883   6.665 2.62e-10 ***
## ---
## Signif. codes:  0 &#39;***&#39; 0.001 &#39;**&#39; 0.01 &#39;*&#39; 0.05 &#39;.&#39; 0.1 &#39; &#39; 1
## 
## (Dispersion parameter for gaussian family taken to be 3.074258)
## 
##     Null deviance: 25164.61  on 208  degrees of freedom
## Residual deviance:   602.55  on 196  degrees of freedom
## AIC: 842.41
## 
## Number of Fisher Scoring iterations: 2</code></pre>
<div class="sourceCode" id="cb14"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb14-1" data-line-number="1"><span class="kw">length</span>(smod<span class="op">$</span>coefficients)</a></code></pre></div>
<pre><code>## [1] 13</code></pre>
<p>Sure enough, there are fewer predictors in the model, and those that remain have small p values.</p>
<p>We can also see the relative model fits of the two models:</p>
<div class="sourceCode" id="cb16"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb16-1" data-line-number="1"><span class="kw">AIC</span>(fm)</a></code></pre></div>
<pre><code>## [1] 859.7625</code></pre>
<div class="sourceCode" id="cb18"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb18-1" data-line-number="1"><span class="kw">AIC</span>(smod)</a></code></pre></div>
<pre><code>## [1] 842.4147</code></pre>
<p>This shows that the original model had 26 parameters, while the model that used AIC to find the best subset had 13 parameters, and the relative model fit, in terms of AIC went from 859.762467 for the saturated model to 842.4147049 for the stepwise model.</p>
<p>An alternative to stepwise selection is called <em>Best Subset Regression</em> and considers all possible combinations of variables from the data, and scores the model based on seeing all possible predictors.</p>
<div class="sourceCode" id="cb20"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb20-1" data-line-number="1"><span class="kw">library</span>(bestglm)</a></code></pre></div>
<pre><code>## Loading required package: leaps</code></pre>
<div class="sourceCode" id="cb22"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb22-1" data-line-number="1">yx&lt;-<span class="kw">data.frame</span>(<span class="kw">cbind</span>(X[,<span class="op">-</span><span class="dv">1</span>],prb2<span class="op">$</span>e0total))</a>
<a class="sourceLine" id="cb22-2" data-line-number="2">b1&lt;-<span class="kw">bestglm</span>(yx,<span class="dt">IC =</span> <span class="st">&quot;AIC&quot;</span>, <span class="dt">family=</span>gaussian, <span class="dt">method =</span> <span class="st">&quot;exhaustive&quot;</span>)</a>
<a class="sourceLine" id="cb22-3" data-line-number="3"></a>
<a class="sourceLine" id="cb22-4" data-line-number="4"><span class="kw">summary</span>(b1<span class="op">$</span>BestModel)</a></code></pre></div>
<pre><code>## 
## Call:
## lm(formula = y ~ ., data = data.frame(Xy[, c(bestset[-1], FALSE), 
##     drop = FALSE], y = y))
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -4.5939 -1.1511  0.1894  0.9632  8.0198 
## 
## Coefficients:
##                                   Estimate Std. Error t value Pr(&gt;|t|)    
## (Intercept)                        67.9569     0.1213 560.321  &lt; 2e-16 ***
## continentNorth.America              0.3215     0.1300   2.473   0.0142 *  
## continentSouth.America              0.2095     0.1317   1.591   0.1132    
## cbr                                -6.2002     3.0472  -2.035   0.0432 *  
## cdr                                -2.2039     1.2497  -1.764   0.0794 .  
## rate.of.natural.increase            5.5896     2.7241   2.052   0.0415 *  
## imr                                -2.4789     0.3761  -6.590 3.96e-10 ***
## percpopgt65                         3.6290     0.3294  11.016  &lt; 2e-16 ***
## percpopinurbangt750k                0.2849     0.1498   1.902   0.0586 .  
## percpop1549hivaids2007             -1.8568     0.2070  -8.971 2.37e-16 ***
## percmarwomcontramodern              0.3689     0.1699   2.172   0.0311 *  
## percpopwaccessimprovedwatersource   0.4307     0.2304   1.870   0.0630 .  
## gnippppercapitausdollars            1.2547     0.1883   6.665 2.62e-10 ***
## ---
## Signif. codes:  0 &#39;***&#39; 0.001 &#39;**&#39; 0.01 &#39;*&#39; 0.05 &#39;.&#39; 0.1 &#39; &#39; 1
## 
## Residual standard error: 1.753 on 196 degrees of freedom
## Multiple R-squared:  0.9761, Adjusted R-squared:  0.9746 
## F-statistic: 665.8 on 12 and 196 DF,  p-value: &lt; 2.2e-16</code></pre>
<p>This creates a parsimonious model, with only 13 coefficients and an AIC of 842.4147049 which is slightly lower than the stepwise model.</p>
</div>
<div id="model-shrinkage" class="section level2">
<h2>Model shrinkage</h2>
<p>Variable selection methods are one way to approach finding the most parsimonious model in an analysis, but alternative methods also exist. The modern approach to doing this is lumped under the term <em>shrinkage methods</em></p>
<div class="sourceCode" id="cb24"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb24-1" data-line-number="1">knitr<span class="op">::</span><span class="kw">include_graphics</span>(<span class="st">&quot;/media/corey/extra/predictive_workinggroup/images/shrink2.gif&quot;</span>)</a></code></pre></div>
<p><img src="/media/corey/extra/predictive_workinggroup/images/shrink2.gif" width="600px" /></p>
<p>As opposed to the variable selection methods, shrinkage models fit a model that contains all the predictors in the data, and constrains or regularizes the coefficients so that they shrink towards 0, effectively eliminating the unnecessary parameters from the model.</p>
<p>There are two main classes of these methods, <em>Ridge regression</em> and the <em>Lasso</em>.</p>
</div>
<div id="ridge-regression" class="section level2">
<h2>Ridge regression</h2>
<p>Ridge regression is very similar to other regression methods, except the estimates are estimated using a slightly different objective criteria. The estimates of the model coefficient are estimates so that the quantity:</p>
<p><span class="math display">\[\text{Penalized Deviance} =  \sum (y_i - \sum{\hat{\beta_k x_k}}) ^2 + \lambda \sum_k \beta_k^2 = \text{Deviance} + \lambda \sum_k \beta_k^2\]</span> is minimized. This looks a lot like the ordinary Deviance for the models above, except it adds a penalization term to the fit criteria. The term <span class="math inline">\(\lambda\)</span>, <span class="math inline">\(\lambda \geq 0\)</span> is a tuning parameter. The Ridge regression coefficients are those that both fit the data well, and penalize the deviance the least.</p>
<p>Then penalty term is small when the <span class="math inline">\(\beta\)</span>s are close to 0. As the value of <span class="math inline">\(\lambda\)</span> increases, the coefficients will be forced to 0. This implies another set of estimation so that the “best” value for <span class="math inline">\(\lambda\)</span> can be found.</p>
<p>Unlike regular regression, which produces 1 set of regression parameters, the Ridge regression produces a set of regression parameters, each one for a given value of <span class="math inline">\(\lambda\)</span>. The best value for <span class="math inline">\(\lambda\)</span> is found by a grid search over a range of values.</p>
<p>Here is an example</p>
<div class="sourceCode" id="cb25"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb25-1" data-line-number="1"><span class="kw">library</span>(glmnet)</a></code></pre></div>
<pre><code>## Loading required package: Matrix</code></pre>
<pre><code>## Loaded glmnet 4.0-2</code></pre>
<div class="sourceCode" id="cb28"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb28-1" data-line-number="1">x&lt;-X[,<span class="op">-</span><span class="dv">1</span>]</a>
<a class="sourceLine" id="cb28-2" data-line-number="2">y&lt;-<span class="kw">as.matrix</span>(yx[, <span class="dv">26</span>])</a>
<a class="sourceLine" id="cb28-3" data-line-number="3"></a>
<a class="sourceLine" id="cb28-4" data-line-number="4">ridgemod&lt;-<span class="kw">glmnet</span>(<span class="dt">x=</span>x,<span class="dt">y=</span>y, <span class="dt">alpha =</span> <span class="dv">0</span>, <span class="dt">nlambda =</span> <span class="dv">100</span>)</a>
<a class="sourceLine" id="cb28-5" data-line-number="5"><span class="kw">plot</span>(ridgemod, <span class="dt">label =</span> T, <span class="dt">xvar =</span> <span class="st">&quot;lambda&quot;</span>)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/Lasso_intro_files/figure-html/unnamed-chunk-8-1.png" /><!-- --></p>
<p>So, the y axis represents the value of the <span class="math inline">\(\beta\)</span>’s from the model, and the x axis are the various values of <span class="math inline">\(\lambda\)</span> for each specific value of <span class="math inline">\(\lambda\)</span>. As you see when <span class="math inline">\(\lambda\)</span> is 0, the coefficients can be quite large, and as <span class="math inline">\(\lambda\)</span> increase, the <span class="math inline">\(\beta\)</span>’s converge to 0. This is nice, but we need to find a “best” value of <span class="math inline">\(\lambda\)</span>. This is done via cross-validation, typically.</p>
<div class="sourceCode" id="cb29"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb29-1" data-line-number="1">s1&lt;-<span class="kw">cv.glmnet</span>(<span class="dt">x =</span>x, <span class="dt">y=</span>y, <span class="dt">family=</span><span class="st">&quot;gaussian&quot;</span>, <span class="dt">alpha =</span> <span class="dv">0</span>) <span class="co">#alpha = 0 for ridge regression</span></a>
<a class="sourceLine" id="cb29-2" data-line-number="2"><span class="kw">plot</span>(s1)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/Lasso_intro_files/figure-html/unnamed-chunk-9-1.png" /><!-- --></p>
<div class="sourceCode" id="cb30"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb30-1" data-line-number="1">s1</a></code></pre></div>
<pre><code>## 
## Call:  cv.glmnet(x = x, y = y, family = &quot;gaussian&quot;, alpha = 0) 
## 
## Measure: Mean-Squared Error 
## 
##     Lambda Measure     SE Nonzero
## min 0.9822   4.382 0.5652      25
## 1se 2.2690   4.908 0.4604      25</code></pre>
<p>The plot shows the mean square error for the model with a particular value of log(<span class="math inline">\(\lambda\)</span>). The dotted lines represent the “best” value and the value of <span class="math inline">\(\lambda\)</span> that is 1 standard error larger than the true minimum.</p>
<p>Why the two values? Well, the minimum value of <span class="math inline">\(\lambda\)</span>, in this case about -0.0179864, and the minimum + 1se is 0.8193173. The smaller value gives the simplest model, while the 1 se value gives the simplest model that also has high accuracy.</p>
<p>How do the coefficient compare?</p>
<div class="sourceCode" id="cb32"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb32-1" data-line-number="1"><span class="kw">options</span>(<span class="dt">scipen=</span><span class="dv">999</span>)</a>
<a class="sourceLine" id="cb32-2" data-line-number="2">ridgemod2&lt;-<span class="kw">glmnet</span>(<span class="dt">x=</span>x,<span class="dt">y=</span>y, <span class="dt">alpha =</span> <span class="dv">0</span>, <span class="dt">lambda =</span> s1<span class="op">$</span>lambda.min)</a>
<a class="sourceLine" id="cb32-3" data-line-number="3">ridgemod2</a></code></pre></div>
<pre><code>## 
## Call:  glmnet(x = x, y = y, alpha = 0, lambda = s1$lambda.min) 
## 
##   Df  %Dev Lambda
## 1 25 97.32 0.9822</code></pre>
<div class="sourceCode" id="cb34"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb34-1" data-line-number="1">ridgemod2<span class="op">$</span>beta</a></code></pre></div>
<pre><code>## 25 x 1 sparse Matrix of class &quot;dgCMatrix&quot;
##                                            s0
## continentAsia                      0.16976947
## continentEurope                    0.34553181
## continentNorth America             0.46350047
## continentOceania                  -0.02282180
## continentSouth America             0.27165406
## population.                       -0.10805054
## cbr                               -0.77128773
## cdr                               -3.19773064
## rate.of.natural.increase           0.74050874
## net.migration.rate                -0.19158308
## imr                               -2.21869216
## womandlifetimeriskmaternaldeath    0.27651883
## tfr                               -0.35993995
## percpoplt15                       -0.65751353
## percpopgt65                        1.59794019
## percurban                         -0.02204929
## percpopinurbangt750k               0.36644454
## percpop1549hivaids2007            -2.34569547
## percmarwomcontraall                0.15264335
## percmarwomcontramodern             0.57816390
## percppundernourished0204          -0.34197021
## motorvehper1000pop0005             0.66058433
## percpopwaccessimprovedwatersource  0.58660140
## gnippppercapitausdollars           0.91519991
## popdenspersqkm                     0.46516889</code></pre>
<div class="sourceCode" id="cb36"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb36-1" data-line-number="1">df1&lt;-<span class="kw">data.frame</span>(<span class="dt">names =</span> <span class="kw">names</span>(<span class="kw">coef</span>(fm)) , <span class="dt">beta=</span><span class="kw">round</span>(<span class="kw">coef</span>(fm),<span class="dv">4</span>), <span class="dt">mod=</span><span class="st">&quot;gm&quot;</span>)</a>
<a class="sourceLine" id="cb36-2" data-line-number="2">df2&lt;-<span class="kw">data.frame</span>(<span class="dt">names =</span> <span class="kw">c</span>(<span class="kw">names</span>(<span class="kw">coef</span>(fm)[<span class="dv">1</span>]), <span class="kw">row.names</span>(ridgemod2<span class="op">$</span>beta)), <span class="dt">beta=</span><span class="kw">round</span>(<span class="kw">as.numeric</span>(<span class="kw">coef</span>(s1, <span class="dt">s =</span> s1<span class="op">$</span>lambda.min)),<span class="dv">4</span>), <span class="dt">mod=</span><span class="st">&quot;ridge&quot;</span>)</a>
<a class="sourceLine" id="cb36-3" data-line-number="3"></a>
<a class="sourceLine" id="cb36-4" data-line-number="4">dfa&lt;-<span class="kw">rbind</span>(df1, df2)</a>
<a class="sourceLine" id="cb36-5" data-line-number="5"></a>
<a class="sourceLine" id="cb36-6" data-line-number="6"><span class="kw">library</span>(ggplot2)</a>
<a class="sourceLine" id="cb36-7" data-line-number="7">dfa<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb36-8" data-line-number="8"><span class="st">  </span><span class="kw">ggplot</span>()<span class="op">+</span><span class="kw">geom_point</span>(<span class="kw">aes</span>(<span class="dt">y=</span>beta,<span class="dt">x=</span>names, <span class="dt">color=</span>mod))<span class="op">+</span><span class="kw">theme_minimal</span>()<span class="op">+</span><span class="kw">theme</span>(<span class="dt">axis.text.x =</span> <span class="kw">element_text</span>(<span class="dt">angle =</span> <span class="dv">45</span>, <span class="dt">vjust =</span> <span class="fl">0.5</span>, <span class="dt">hjust=</span><span class="dv">1</span>))</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/Lasso_intro_files/figure-html/unnamed-chunk-10-1.png" /><!-- --></p>
<p>An important note about the ridge regression is that no coefficients are forced to zero, so it is not doing variable selection, only shrinkage of the coefficients. The Lasso, on the other hand can do both.</p>
</div>
<div id="lasso" class="section level2">
<h2>Lasso</h2>
<div class="sourceCode" id="cb37"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb37-1" data-line-number="1">knitr<span class="op">::</span><span class="kw">include_graphics</span>(<span class="st">&quot;/media/corey/extra/predictive_workinggroup/images/cowboy.jpeg&quot;</span>)</a></code></pre></div>
<p><img src="/media/corey/extra/predictive_workinggroup/images/cowboy.jpeg" width="600px" /></p>
<p>What is the Lasso? Lasso is short for <em>least absolute shrinkage and selection operator</em>. It is designed so that it produces a model that is both strong in the prediction sense, and parsimonious, in that it performs feature (variable) selection as well.</p>
<p>The Lasso is similar to the ridge regression model, in that it penalizes the regression parameter solution, except that the penalty term is not the sum of squared <span class="math inline">\(\beta\)</span>’s, but the sum of the absolute values of the <span class="math inline">\(\beta\)</span>’s.</p>
<p><span class="math display">\[   \text{Deviance} + \lambda \sum_k | \beta_k| \]</span></p>
<div class="sourceCode" id="cb38"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb38-1" data-line-number="1">ridgemod3&lt;-<span class="kw">glmnet</span>(<span class="dt">x=</span>x,<span class="dt">y=</span>y, <span class="dt">alpha =</span> <span class="dv">1</span>) <span class="co">#alpha = 1 for Lasso</span></a>
<a class="sourceLine" id="cb38-2" data-line-number="2"><span class="kw">plot</span>(ridgemod3, <span class="dt">label =</span> T, <span class="dt">xvar =</span> <span class="st">&quot;lambda&quot;</span>)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/Lasso_intro_files/figure-html/unnamed-chunk-12-1.png" /><!-- --></p>
<div class="sourceCode" id="cb39"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb39-1" data-line-number="1">s2&lt;-<span class="kw">cv.glmnet</span>(<span class="dt">x =</span>x, <span class="dt">y=</span>y, <span class="dt">family=</span><span class="st">&quot;gaussian&quot;</span>, <span class="dt">alpha =</span> <span class="dv">1</span>) <span class="co">#alpha = 1 for Lasso</span></a>
<a class="sourceLine" id="cb39-2" data-line-number="2"><span class="kw">plot</span>(s2)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/Lasso_intro_files/figure-html/unnamed-chunk-12-2.png" /><!-- --></p>
<div class="sourceCode" id="cb40"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb40-1" data-line-number="1">s2</a></code></pre></div>
<pre><code>## 
## Call:  cv.glmnet(x = x, y = y, family = &quot;gaussian&quot;, alpha = 1) 
## 
## Measure: Mean-Squared Error 
## 
##     Lambda Measure     SE Nonzero
## min 0.0646   3.731 0.6382      15
## 1se 0.4559   4.352 0.7667      10</code></pre>
<div class="sourceCode" id="cb42"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb42-1" data-line-number="1">test&lt;-<span class="kw">glmnet</span>(<span class="dt">x =</span>x, <span class="dt">y=</span>y, <span class="dt">family=</span><span class="st">&quot;gaussian&quot;</span>, <span class="dt">alpha =</span> <span class="dv">1</span>, <span class="dt">lambda =</span> s2<span class="op">$</span>lambda<span class="fl">.1</span>se)</a>
<a class="sourceLine" id="cb42-2" data-line-number="2">test</a></code></pre></div>
<pre><code>## 
## Call:  glmnet(x = x, y = y, family = &quot;gaussian&quot;, alpha = 1, lambda = s2$lambda.1se) 
## 
##   Df  %Dev Lambda
## 1 10 96.97 0.4559</code></pre>
<div class="sourceCode" id="cb44"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb44-1" data-line-number="1"><span class="kw">print</span>(test<span class="op">$</span>beta)</a></code></pre></div>
<pre><code>## 25 x 1 sparse Matrix of class &quot;dgCMatrix&quot;
##                                            s0
## continentAsia                      .         
## continentEurope                    .         
## continentNorth America             .         
## continentOceania                   .         
## continentSouth America             .         
## population.                        .         
## cbr                                .         
## cdr                               -4.11173283
## rate.of.natural.increase           .         
## net.migration.rate                 .         
## imr                               -3.13251642
## womandlifetimeriskmaternaldeath    .         
## tfr                                .         
## percpoplt15                       -0.25132019
## percpopgt65                        2.81422569
## percurban                          .         
## percpopinurbangt750k               0.10711580
## percpop1549hivaids2007            -1.78945838
## percmarwomcontraall                .         
## percmarwomcontramodern             0.20523750
## percppundernourished0204           .         
## motorvehper1000pop0005             0.03841647
## percpopwaccessimprovedwatersource  0.33401182
## gnippppercapitausdollars           1.06967019
## popdenspersqkm                     .</code></pre>
</div>
<div id="elastic-net" class="section level2">
<h2>Elastic net</h2>
<p>A third method, the <em>Elastic net</em> regression, blends the shrinkage aspects of the ridge regression with the feature selection of the Lasso. This was proposed by Zou and Hastie <a href="https://rss.onlinelibrary.wiley.com/doi/abs/10.1111/j.1467-9868.2005.00503.x">2005</a></p>
<p><span class="math display">\[\text{Deviance} + \lambda [(1-\alpha) \sum_k |\beta_k| + \alpha \sum_k  \beta_k^2]\]</span></p>
<div class="sourceCode" id="cb46"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb46-1" data-line-number="1">ridgemod4&lt;-<span class="kw">glmnet</span>(<span class="dt">x=</span>x,<span class="dt">y=</span>y, <span class="dt">alpha =</span> <span class="fl">.5</span>, <span class="dt">nlambda =</span> <span class="dv">100</span>) <span class="co">#alpha = .5 for elastic net</span></a>
<a class="sourceLine" id="cb46-2" data-line-number="2"><span class="kw">plot</span>(ridgemod4, <span class="dt">label =</span> T, <span class="dt">xvar =</span> <span class="st">&quot;lambda&quot;</span>)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/Lasso_intro_files/figure-html/unnamed-chunk-13-1.png" /><!-- --></p>
<div class="sourceCode" id="cb47"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb47-1" data-line-number="1">s3&lt;-<span class="kw">cv.glmnet</span>(<span class="dt">x =</span>x, <span class="dt">y=</span>y, <span class="dt">family=</span><span class="st">&quot;gaussian&quot;</span>, <span class="dt">alpha =</span> <span class="fl">.5</span>) <span class="co">#alpha = .5 for elastic net</span></a>
<a class="sourceLine" id="cb47-2" data-line-number="2"><span class="kw">plot</span>(s3)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/Lasso_intro_files/figure-html/unnamed-chunk-13-2.png" /><!-- --></p>
<div class="sourceCode" id="cb48"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb48-1" data-line-number="1">s3</a></code></pre></div>
<pre><code>## 
## Call:  cv.glmnet(x = x, y = y, family = &quot;gaussian&quot;, alpha = 0.5) 
## 
## Measure: Mean-Squared Error 
## 
##     Lambda Measure     SE Nonzero
## min 0.1292   3.593 0.5248      16
## 1se 0.6284   4.037 0.5181      12</code></pre>
<div class="sourceCode" id="cb50"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb50-1" data-line-number="1">test3&lt;-<span class="kw">glmnet</span>(<span class="dt">x =</span>x, <span class="dt">y=</span>y, <span class="dt">family=</span><span class="st">&quot;gaussian&quot;</span>, <span class="dt">alpha =</span> <span class="fl">.5</span>, <span class="dt">lambda =</span> s3<span class="op">$</span>lambda<span class="fl">.1</span>se)</a>
<a class="sourceLine" id="cb50-2" data-line-number="2">test3<span class="op">$</span>beta</a></code></pre></div>
<pre><code>## 25 x 1 sparse Matrix of class &quot;dgCMatrix&quot;
##                                            s0
## continentAsia                      .         
## continentEurope                    .         
## continentNorth America             0.05233114
## continentOceania                   .         
## continentSouth America             .         
## population.                        .         
## cbr                               -0.22571539
## cdr                               -3.79036104
## rate.of.natural.increase           .         
## net.migration.rate                 .         
## imr                               -2.84336643
## womandlifetimeriskmaternaldeath    .         
## tfr                                .         
## percpoplt15                       -0.46055079
## percpopgt65                        2.31578751
## percurban                          .         
## percpopinurbangt750k               0.23562128
## percpop1549hivaids2007            -2.06460379
## percmarwomcontraall                .         
## percmarwomcontramodern             0.35690946
## percppundernourished0204           .         
## motorvehper1000pop0005             0.28068647
## percpopwaccessimprovedwatersource  0.52328787
## gnippppercapitausdollars           0.99759091
## popdenspersqkm                     .</code></pre>
<p>In these examples, we will use the Demographic and Health Survey <a href="https://dhsprogram.com/data/Model-Datasets.cfm">Model Data</a>. These are based on the DHS survey, but are publicly available and are used to practice using the DHS data sets, but don’t represent a real country.</p>
<p>In this example, we will use the outcome of contraceptive choice (modern vs other/none) as our outcome. An excellent guide for this type of literature is this <a href="https://journals.sagepub.com/doi/pdf/10.1177/0021909614547462">article</a></p>
<div class="sourceCode" id="cb52"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb52-1" data-line-number="1"><span class="kw">library</span>(haven)</a>
<a class="sourceLine" id="cb52-2" data-line-number="2">dat&lt;-<span class="kw">url</span>(<span class="st">&quot;https://github.com/coreysparks/data/blob/master/ZZIR62FL.DTA?raw=true&quot;</span>)</a>
<a class="sourceLine" id="cb52-3" data-line-number="3">model.dat&lt;-<span class="kw">read_dta</span>(dat)</a></code></pre></div>
<p>Here we recode some of our variables and limit our data to those women who are not currently pregnant and who are sexually active.</p>
<div class="sourceCode" id="cb53"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb53-1" data-line-number="1"><span class="kw">library</span>(dplyr)</a>
<a class="sourceLine" id="cb53-2" data-line-number="2"></a>
<a class="sourceLine" id="cb53-3" data-line-number="3">model.dat2&lt;-model.dat<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb53-4" data-line-number="4"><span class="st">  </span><span class="kw">mutate</span>(<span class="dt">region =</span> v024, </a>
<a class="sourceLine" id="cb53-5" data-line-number="5">         <span class="dt">modcontra=</span> <span class="kw">ifelse</span>(v364 <span class="op">==</span><span class="dv">1</span>,<span class="dv">1</span>, <span class="dv">0</span>),</a>
<a class="sourceLine" id="cb53-6" data-line-number="6">         <span class="dt">age =</span> <span class="kw">cut</span>(v012, <span class="dt">breaks =</span> <span class="dv">5</span>), </a>
<a class="sourceLine" id="cb53-7" data-line-number="7">         <span class="dt">livchildren=</span>v218,</a>
<a class="sourceLine" id="cb53-8" data-line-number="8">         <span class="dt">educ =</span> v106,</a>
<a class="sourceLine" id="cb53-9" data-line-number="9">         <span class="dt">currpreg=</span>v213,</a>
<a class="sourceLine" id="cb53-10" data-line-number="10">         <span class="dt">wealth =</span> <span class="kw">as.factor</span>(v190),</a>
<a class="sourceLine" id="cb53-11" data-line-number="11">         <span class="dt">partnered =</span> <span class="kw">ifelse</span>(v701<span class="op">&lt;=</span><span class="dv">1</span>, <span class="dv">0</span>, <span class="dv">1</span>),</a>
<a class="sourceLine" id="cb53-12" data-line-number="12">         <span class="dt">work =</span> <span class="kw">ifelse</span>(v731<span class="op">%in%</span><span class="kw">c</span>(<span class="dv">0</span>,<span class="dv">1</span>), <span class="dv">0</span>, <span class="dv">1</span>),</a>
<a class="sourceLine" id="cb53-13" data-line-number="13">         <span class="dt">knowmodern=</span><span class="kw">ifelse</span>(v301<span class="op">==</span><span class="dv">3</span>, <span class="dv">1</span>, <span class="dv">0</span>),</a>
<a class="sourceLine" id="cb53-14" data-line-number="14">         <span class="dt">age2=</span>v012<span class="op">^</span><span class="dv">2</span>, </a>
<a class="sourceLine" id="cb53-15" data-line-number="15">         <span class="dt">rural =</span> <span class="kw">ifelse</span>(v025<span class="op">==</span><span class="dv">2</span>, <span class="dv">1</span>,<span class="dv">0</span>),</a>
<a class="sourceLine" id="cb53-16" data-line-number="16">         <span class="dt">wantmore =</span> <span class="kw">ifelse</span>(v605<span class="op">%in%</span><span class="kw">c</span>(<span class="dv">1</span>,<span class="dv">2</span>), <span class="dv">1</span>, <span class="dv">0</span>))<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb53-17" data-line-number="17"><span class="st">  </span><span class="kw">filter</span>(currpreg<span class="op">==</span><span class="dv">0</span>, v536<span class="op">&gt;</span><span class="dv">0</span>, v701<span class="op">!=</span><span class="dv">9</span>)<span class="op">%&gt;%</span><span class="st"> </span><span class="co">#notpreg, sex active</span></a>
<a class="sourceLine" id="cb53-18" data-line-number="18"><span class="st">  </span>dplyr<span class="op">::</span><span class="kw">select</span>(caseid, region, modcontra,age, age2,livchildren, educ, knowmodern, rural, wantmore, partnered,wealth, work)</a></code></pre></div>
<div class="sourceCode" id="cb54"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb54-1" data-line-number="1">knitr<span class="op">::</span><span class="kw">kable</span>(<span class="kw">head</span>(model.dat2))</a></code></pre></div>
<table>
<colgroup>
<col width="7%" />
<col width="6%" />
<col width="9%" />
<col width="11%" />
<col width="4%" />
<col width="11%" />
<col width="4%" />
<col width="10%" />
<col width="5%" />
<col width="8%" />
<col width="9%" />
<col width="6%" />
<col width="4%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">caseid</th>
<th align="right">region</th>
<th align="right">modcontra</th>
<th align="left">age</th>
<th align="right">age2</th>
<th align="right">livchildren</th>
<th align="right">educ</th>
<th align="right">knowmodern</th>
<th align="right">rural</th>
<th align="right">wantmore</th>
<th align="right">partnered</th>
<th align="left">wealth</th>
<th align="right">work</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">1 1 2</td>
<td align="right">2</td>
<td align="right">0</td>
<td align="left">(28.6,35.4]</td>
<td align="right">900</td>
<td align="right">4</td>
<td align="right">0</td>
<td align="right">1</td>
<td align="right">1</td>
<td align="right">1</td>
<td align="right">0</td>
<td align="left">1</td>
<td align="right">1</td>
</tr>
<tr class="even">
<td align="left">1 4 2</td>
<td align="right">2</td>
<td align="right">0</td>
<td align="left">(35.4,42.2]</td>
<td align="right">1764</td>
<td align="right">2</td>
<td align="right">0</td>
<td align="right">1</td>
<td align="right">1</td>
<td align="right">0</td>
<td align="right">0</td>
<td align="left">3</td>
<td align="right">1</td>
</tr>
<tr class="odd">
<td align="left">1 4 3</td>
<td align="right">2</td>
<td align="right">0</td>
<td align="left">(21.8,28.6]</td>
<td align="right">625</td>
<td align="right">3</td>
<td align="right">1</td>
<td align="right">1</td>
<td align="right">1</td>
<td align="right">0</td>
<td align="right">0</td>
<td align="left">3</td>
<td align="right">1</td>
</tr>
<tr class="even">
<td align="left">1 5 1</td>
<td align="right">2</td>
<td align="right">0</td>
<td align="left">(21.8,28.6]</td>
<td align="right">625</td>
<td align="right">2</td>
<td align="right">2</td>
<td align="right">1</td>
<td align="right">1</td>
<td align="right">1</td>
<td align="right">0</td>
<td align="left">2</td>
<td align="right">1</td>
</tr>
<tr class="odd">
<td align="left">1 6 2</td>
<td align="right">2</td>
<td align="right">0</td>
<td align="left">(35.4,42.2]</td>
<td align="right">1369</td>
<td align="right">2</td>
<td align="right">0</td>
<td align="right">1</td>
<td align="right">1</td>
<td align="right">1</td>
<td align="right">0</td>
<td align="left">3</td>
<td align="right">1</td>
</tr>
<tr class="even">
<td align="left">1 7 2</td>
<td align="right">2</td>
<td align="right">0</td>
<td align="left">(15,21.8]</td>
<td align="right">441</td>
<td align="right">1</td>
<td align="right">0</td>
<td align="right">1</td>
<td align="right">1</td>
<td align="right">1</td>
<td align="right">0</td>
<td align="left">1</td>
<td align="right">1</td>
</tr>
</tbody>
</table>
<div class="sourceCode" id="cb55"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb55-1" data-line-number="1"><span class="kw">library</span>(caret)</a></code></pre></div>
<pre><code>## Loading required package: lattice</code></pre>
<div class="sourceCode" id="cb57"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb57-1" data-line-number="1"><span class="kw">set.seed</span>(<span class="dv">1115</span>)</a>
<a class="sourceLine" id="cb57-2" data-line-number="2">train&lt;-<span class="st"> </span><span class="kw">createDataPartition</span>(<span class="dt">y =</span> model.dat2<span class="op">$</span>modcontra , <span class="dt">p =</span> <span class="fl">.80</span>, <span class="dt">list=</span>F)</a>
<a class="sourceLine" id="cb57-3" data-line-number="3"></a>
<a class="sourceLine" id="cb57-4" data-line-number="4">dtrain&lt;-model.dat2[train,]</a></code></pre></div>
<pre><code>## Warning: The `i` argument of ``[`()` can&#39;t be a matrix as of tibble 3.0.0.
## Convert to a vector.
## This warning is displayed once every 8 hours.
## Call `lifecycle::last_warnings()` to see where this warning was generated.</code></pre>
<div class="sourceCode" id="cb59"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb59-1" data-line-number="1">dtest&lt;-model.dat2[<span class="op">-</span>train,]</a></code></pre></div>
</div>
<div id="logistic-regression" class="section level2">
<h2>Logistic regression</h2>
<div class="sourceCode" id="cb60"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb60-1" data-line-number="1"><span class="kw">library</span>(glmnet)</a>
<a class="sourceLine" id="cb60-2" data-line-number="2"><span class="kw">library</span>(MASS)</a>
<a class="sourceLine" id="cb60-3" data-line-number="3">fm&lt;-<span class="kw">glm</span>(modcontra<span class="op">~</span>region<span class="op">+</span><span class="kw">factor</span>(age)<span class="op">+</span>livchildren<span class="op">+</span>wantmore<span class="op">+</span>rural<span class="op">+</span><span class="kw">factor</span>(educ)<span class="op">+</span>partnered<span class="op">+</span>work<span class="op">+</span>wealth, <span class="dt">data =</span> dtrain, <span class="dt">family=</span>binomial) </a>
<a class="sourceLine" id="cb60-4" data-line-number="4"><span class="kw">summary</span>(fm)</a></code></pre></div>
<pre><code>## 
## Call:
## glm(formula = modcontra ~ region + factor(age) + livchildren + 
##     wantmore + rural + factor(educ) + partnered + work + wealth, 
##     family = binomial, data = dtrain)
## 
## Deviance Residuals: 
##     Min       1Q   Median       3Q      Max  
## -1.3727  -0.6498  -0.4970  -0.3590   2.5673  
## 
## Coefficients:
##                        Estimate Std. Error z value             Pr(&gt;|z|)    
## (Intercept)            -2.80866    0.26506 -10.596 &lt; 0.0000000000000002 ***
## region                  0.15475    0.03772   4.103       0.000040825388 ***
## factor(age)(21.8,28.6]  0.44413    0.18439   2.409              0.01601 *  
## factor(age)(28.6,35.4]  0.54804    0.19226   2.850              0.00437 ** 
## factor(age)(35.4,42.2]  0.14667    0.20988   0.699              0.48468    
## factor(age)(42.2,49]   -0.63063    0.23623  -2.670              0.00760 ** 
## livchildren             0.18805    0.02976   6.318       0.000000000264 ***
## wantmore               -0.16630    0.10204  -1.630              0.10316    
## rural                  -0.62567    0.11323  -5.525       0.000000032859 ***
## factor(educ)1           0.30769    0.13045   2.359              0.01834 *  
## factor(educ)2           0.59775    0.13288   4.498       0.000006849378 ***
## factor(educ)3           0.62670    0.24653   2.542              0.01102 *  
## partnered               0.28746    0.10467   2.746              0.00603 ** 
## work                    0.16342    0.10386   1.573              0.11561    
## wealth2                 0.13635    0.14047   0.971              0.33170    
## wealth3                 0.17350    0.14114   1.229              0.21898    
## wealth4                 0.24257    0.15013   1.616              0.10614    
## wealth5                 0.17383    0.18138   0.958              0.33786    
## ---
## Signif. codes:  0 &#39;***&#39; 0.001 &#39;**&#39; 0.01 &#39;*&#39; 0.05 &#39;.&#39; 0.1 &#39; &#39; 1
## 
## (Dispersion parameter for binomial family taken to be 1)
## 
##     Null deviance: 3818.3  on 4128  degrees of freedom
## Residual deviance: 3536.4  on 4111  degrees of freedom
## AIC: 3572.4
## 
## Number of Fisher Scoring iterations: 5</code></pre>
</div>
<div id="stepwise-logistic" class="section level2">
<h2>stepwise logistic</h2>
<div class="sourceCode" id="cb62"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb62-1" data-line-number="1">smod&lt;-fm<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb62-2" data-line-number="2"><span class="st">  </span><span class="kw">stepAIC</span>(<span class="dt">trace =</span> T, <span class="dt">direction =</span> <span class="st">&quot;both&quot;</span>)</a></code></pre></div>
<pre><code>## Start:  AIC=3572.39
## modcontra ~ region + factor(age) + livchildren + wantmore + rural + 
##     factor(educ) + partnered + work + wealth
## 
##                Df Deviance    AIC
## - wealth        4   3539.2 3567.2
## &lt;none&gt;              3536.4 3572.4
## - work          1   3538.9 3572.9
## - wantmore      1   3539.0 3573.0
## - partnered     1   3543.8 3577.8
## - region        1   3553.0 3587.0
## - factor(educ)  3   3559.4 3589.4
## - rural         1   3566.7 3600.7
## - livchildren   1   3577.1 3611.1
## - factor(age)   4   3609.4 3637.4
## 
## Step:  AIC=3567.21
## modcontra ~ region + factor(age) + livchildren + wantmore + rural + 
##     factor(educ) + partnered + work
## 
##                Df Deviance    AIC
## &lt;none&gt;              3539.2 3567.2
## - work          1   3541.7 3567.7
## - wantmore      1   3541.8 3567.8
## + wealth        4   3536.4 3572.4
## - partnered     1   3547.9 3573.9
## - region        1   3555.0 3581.0
## - factor(educ)  3   3564.0 3586.0
## - livchildren   1   3579.4 3605.4
## - rural         1   3590.0 3616.0
## - factor(age)   4   3611.8 3631.8</code></pre>
<div class="sourceCode" id="cb64"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb64-1" data-line-number="1"><span class="kw">coef</span>(smod)</a></code></pre></div>
<pre><code>##            (Intercept)                 region factor(age)(21.8,28.6] 
##             -2.6270672              0.1501583              0.4524232 
## factor(age)(28.6,35.4] factor(age)(35.4,42.2]   factor(age)(42.2,49] 
##              0.5612389              0.1621533             -0.6103720 
##            livchildren               wantmore                  rural 
##              0.1861566             -0.1651203             -0.6915328 
##          factor(educ)1          factor(educ)2          factor(educ)3 
##              0.3178917              0.6052986              0.6167321 
##              partnered                   work 
##              0.3043424              0.1621220</code></pre>
<div id="best-subset-regression" class="section level3">
<h3>Best subset regression</h3>
<div class="sourceCode" id="cb66"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb66-1" data-line-number="1">y&lt;-dtrain<span class="op">$</span>modcontra</a>
<a class="sourceLine" id="cb66-2" data-line-number="2">x&lt;-<span class="kw">model.matrix</span>(<span class="op">~</span><span class="kw">factor</span>(region)<span class="op">+</span><span class="kw">factor</span>(age)<span class="op">+</span>livchildren<span class="op">+</span>rural<span class="op">+</span>wantmore<span class="op">+</span><span class="kw">factor</span>(educ)<span class="op">+</span>partnered<span class="op">+</span>work<span class="op">+</span><span class="kw">factor</span>(wealth), <span class="dt">data=</span>dtrain)</a>
<a class="sourceLine" id="cb66-3" data-line-number="3">x&lt;-(x)[,<span class="op">-</span><span class="dv">1</span>]</a>
<a class="sourceLine" id="cb66-4" data-line-number="4"></a>
<a class="sourceLine" id="cb66-5" data-line-number="5">yx&lt;-<span class="kw">as.data.frame</span>(<span class="kw">cbind</span>(x[,<span class="op">-</span><span class="dv">1</span>],y))</a>
<a class="sourceLine" id="cb66-6" data-line-number="6"><span class="kw">names</span>(yx)</a></code></pre></div>
<pre><code>##  [1] &quot;factor(region)3&quot;        &quot;factor(region)4&quot;        &quot;factor(age)(21.8,28.6]&quot;
##  [4] &quot;factor(age)(28.6,35.4]&quot; &quot;factor(age)(35.4,42.2]&quot; &quot;factor(age)(42.2,49]&quot;  
##  [7] &quot;livchildren&quot;            &quot;rural&quot;                  &quot;wantmore&quot;              
## [10] &quot;factor(educ)1&quot;          &quot;factor(educ)2&quot;          &quot;factor(educ)3&quot;         
## [13] &quot;partnered&quot;              &quot;work&quot;                   &quot;factor(wealth)2&quot;       
## [16] &quot;factor(wealth)3&quot;        &quot;factor(wealth)4&quot;        &quot;factor(wealth)5&quot;       
## [19] &quot;y&quot;</code></pre>
<div class="sourceCode" id="cb68"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb68-1" data-line-number="1"><span class="co">#names(yx)&lt;-c(paste(&quot;X&quot;,1:13, sep=&quot;&quot;),&quot;y&quot;)</span></a></code></pre></div>
<div class="sourceCode" id="cb69"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb69-1" data-line-number="1"><span class="kw">library</span>(bestglm)</a>
<a class="sourceLine" id="cb69-2" data-line-number="2"></a>
<a class="sourceLine" id="cb69-3" data-line-number="3">b1&lt;-<span class="kw">bestglm</span>(yx, <span class="dt">family=</span>binomial)</a>
<a class="sourceLine" id="cb69-4" data-line-number="4">b1<span class="op">$</span>BestModels</a>
<a class="sourceLine" id="cb69-5" data-line-number="5"><span class="kw">summary</span>(b1<span class="op">$</span>BestModel)</a></code></pre></div>
</div>
</div>
<div id="logistic-ridge-regression" class="section level2">
<h2>Logistic ridge regression</h2>
<div class="sourceCode" id="cb70"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb70-1" data-line-number="1">s1&lt;-<span class="kw">cv.glmnet</span>(<span class="dt">x =</span> x,<span class="dt">y =</span> y, <span class="dt">data =</span> dtrain, <span class="dt">family=</span><span class="st">&quot;binomial&quot;</span>, <span class="dt">alpha =</span> <span class="dv">0</span>)</a>
<a class="sourceLine" id="cb70-2" data-line-number="2"><span class="kw">plot</span>(s1)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/Lasso_intro_files/figure-html/unnamed-chunk-22-1.png" /><!-- --></p>
<div class="sourceCode" id="cb71"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb71-1" data-line-number="1">rmod&lt;-<span class="kw">glmnet</span>(x,y, <span class="dt">data =</span> dtrain, <span class="dt">family=</span><span class="st">&quot;binomial&quot;</span>, <span class="dt">alpha =</span> <span class="dv">0</span>)</a>
<a class="sourceLine" id="cb71-2" data-line-number="2"><span class="kw">plot</span>(rmod)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/Lasso_intro_files/figure-html/unnamed-chunk-23-1.png" /><!-- --></p>
<div class="sourceCode" id="cb72"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb72-1" data-line-number="1">rmod1&lt;-<span class="kw">glmnet</span>(x,y, <span class="dt">data =</span> dtrain, <span class="dt">family=</span><span class="st">&quot;binomial&quot;</span>, <span class="dt">alpha =</span> <span class="dv">0</span>, <span class="dt">lambda =</span> s1<span class="op">$</span>lambda<span class="fl">.1</span>se)</a>
<a class="sourceLine" id="cb72-2" data-line-number="2">rmod1<span class="op">$</span>beta</a></code></pre></div>
<pre><code>## 19 x 1 sparse Matrix of class &quot;dgCMatrix&quot;
##                                 s0
## factor(region)2         0.11736906
## factor(region)3         0.20097363
## factor(region)4         0.15952400
## factor(age)(21.8,28.6]  0.07831211
## factor(age)(28.6,35.4]  0.23721168
## factor(age)(35.4,42.2]  0.06382724
## factor(age)(42.2,49]   -0.27968879
## livchildren             0.06545045
## rural                  -0.33722253
## wantmore               -0.09296988
## factor(educ)1           0.13004018
## factor(educ)2           0.27349312
## factor(educ)3           0.28825836
## partnered               0.22401406
## work                    0.06631462
## factor(wealth)2        -0.02330795
## factor(wealth)3         0.00422731
## factor(wealth)4         0.13902383
## factor(wealth)5         0.16285245</code></pre>
</div>
<div id="logistic-lasso" class="section level2">
<h2>Logistic Lasso</h2>
<div class="sourceCode" id="cb74"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb74-1" data-line-number="1">s2&lt;-<span class="kw">cv.glmnet</span>(<span class="dt">x =</span> x,<span class="dt">y =</span> y, <span class="dt">data =</span> dtrain, <span class="dt">family=</span><span class="st">&quot;binomial&quot;</span>, <span class="dt">alpha =</span> <span class="dv">1</span>)</a>
<a class="sourceLine" id="cb74-2" data-line-number="2"><span class="kw">plot</span>(s2)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/Lasso_intro_files/figure-html/unnamed-chunk-25-1.png" /><!-- --></p>
<div class="sourceCode" id="cb75"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb75-1" data-line-number="1">rmod2&lt;-<span class="kw">glmnet</span>(x,y, <span class="dt">data =</span> dtrain, <span class="dt">family=</span><span class="st">&quot;binomial&quot;</span>, <span class="dt">alpha =</span> <span class="dv">1</span>)</a>
<a class="sourceLine" id="cb75-2" data-line-number="2"><span class="kw">plot</span>(rmod2)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/Lasso_intro_files/figure-html/unnamed-chunk-26-1.png" /><!-- --></p>
<div class="sourceCode" id="cb76"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb76-1" data-line-number="1">rmod3&lt;-<span class="kw">glmnet</span>(x,y, <span class="dt">data =</span> dtrain, <span class="dt">family=</span><span class="st">&quot;binomial&quot;</span>, <span class="dt">alpha =</span> <span class="dv">1</span>, <span class="dt">lambda =</span> s2<span class="op">$</span>lambda<span class="fl">.1</span>se)</a>
<a class="sourceLine" id="cb76-2" data-line-number="2">rmod3<span class="op">$</span>beta</a></code></pre></div>
<pre><code>## 19 x 1 sparse Matrix of class &quot;dgCMatrix&quot;
##                                 s0
## factor(region)2         .         
## factor(region)3         0.04882613
## factor(region)4         .         
## factor(age)(21.8,28.6]  .         
## factor(age)(28.6,35.4]  0.17720355
## factor(age)(35.4,42.2]  .         
## factor(age)(42.2,49]   -0.37140425
## livchildren             0.09635800
## rural                  -0.62095437
## wantmore                .         
## factor(educ)1           .         
## factor(educ)2           0.20666302
## factor(educ)3           .         
## partnered               0.26927535
## work                    .         
## factor(wealth)2         .         
## factor(wealth)3         .         
## factor(wealth)4         .         
## factor(wealth)5         .</code></pre>
</div>
<div id="classification-results" class="section level2">
<h2>classification results</h2>
<div id="regular-glm" class="section level3">
<h3>Regular GLM</h3>
<div class="sourceCode" id="cb78"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb78-1" data-line-number="1">probabilities &lt;-<span class="st"> </span>fm <span class="op">%&gt;%</span><span class="st"> </span><span class="kw">predict</span>(dtest, <span class="dt">type =</span> <span class="st">&quot;response&quot;</span>)</a>
<a class="sourceLine" id="cb78-2" data-line-number="2">predicted.classes &lt;-<span class="st"> </span><span class="kw">ifelse</span>(probabilities <span class="op">&gt;</span><span class="st"> </span><span class="kw">mean</span>(dtest<span class="op">$</span>modcontra, <span class="dt">na.rm=</span>T), <span class="dv">1</span>, <span class="dv">0</span>)</a>
<a class="sourceLine" id="cb78-3" data-line-number="3"><span class="co"># Prediction accuracy</span></a>
<a class="sourceLine" id="cb78-4" data-line-number="4">observed.classes &lt;-<span class="st"> </span>dtest<span class="op">$</span>modcontra</a>
<a class="sourceLine" id="cb78-5" data-line-number="5"><span class="kw">mean</span>(predicted.classes <span class="op">==</span><span class="st"> </span>observed.classes, <span class="dt">na.rm=</span>T)</a></code></pre></div>
<pre><code>## [1] 0.6889535</code></pre>
<div class="sourceCode" id="cb80"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb80-1" data-line-number="1"><span class="kw">table</span>(predicted.classes, observed.classes)</a></code></pre></div>
<pre><code>##                  observed.classes
## predicted.classes   0   1
##                 0 605 111
##                 1 210 106</code></pre>
</div>
<div id="stepwise-logistic-model" class="section level3">
<h3>stepwise logistic model</h3>
<div class="sourceCode" id="cb82"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb82-1" data-line-number="1">probabilities &lt;-<span class="st"> </span><span class="kw">predict</span>(smod, dtest, <span class="dt">type =</span> <span class="st">&quot;response&quot;</span>)</a>
<a class="sourceLine" id="cb82-2" data-line-number="2">predicted.classes &lt;-<span class="st"> </span><span class="kw">ifelse</span>(probabilities <span class="op">&gt;</span><span class="st"> </span><span class="kw">mean</span>(dtest<span class="op">$</span>modcontra, <span class="dt">na.rm=</span>T), <span class="dv">1</span>, <span class="dv">0</span>)</a>
<a class="sourceLine" id="cb82-3" data-line-number="3"><span class="co"># Prediction accuracy</span></a>
<a class="sourceLine" id="cb82-4" data-line-number="4">observed.classes &lt;-<span class="st"> </span>dtest<span class="op">$</span>modcontra</a>
<a class="sourceLine" id="cb82-5" data-line-number="5"><span class="kw">mean</span>(predicted.classes <span class="op">==</span><span class="st"> </span>observed.classes)</a></code></pre></div>
<pre><code>## [1] 0.6860465</code></pre>
<div class="sourceCode" id="cb84"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb84-1" data-line-number="1"><span class="kw">table</span>(predicted.classes, observed.classes)</a></code></pre></div>
<pre><code>##                  observed.classes
## predicted.classes   0   1
##                 0 605 114
##                 1 210 103</code></pre>
</div>
<div id="logistic-ridge-regression-1" class="section level3">
<h3>Logistic ridge regression</h3>
<div class="sourceCode" id="cb86"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb86-1" data-line-number="1">x.test &lt;-<span class="st"> </span><span class="kw">model.matrix</span>(<span class="op">~</span><span class="kw">factor</span>(region)<span class="op">+</span><span class="kw">factor</span>(age)<span class="op">+</span>livchildren<span class="op">+</span>rural<span class="op">+</span>wantmore<span class="op">+</span><span class="kw">factor</span>(educ)<span class="op">+</span>partnered<span class="op">+</span>work<span class="op">+</span><span class="kw">factor</span>(wealth), <span class="dt">data=</span>dtest)</a>
<a class="sourceLine" id="cb86-2" data-line-number="2">x.test&lt;-(x.test)[,<span class="op">-</span><span class="dv">1</span>]</a>
<a class="sourceLine" id="cb86-3" data-line-number="3">ytest&lt;-dtrain<span class="op">$</span>modcontra</a>
<a class="sourceLine" id="cb86-4" data-line-number="4"></a>
<a class="sourceLine" id="cb86-5" data-line-number="5">probabilities &lt;-<span class="st">   </span><span class="kw">predict</span>(<span class="dt">object=</span>rmod1, <span class="dt">newx =</span> x.test, <span class="dt">s=</span>s1<span class="op">$</span>lambda<span class="fl">.1</span>se, <span class="dt">type=</span><span class="st">&quot;response&quot;</span>)</a>
<a class="sourceLine" id="cb86-6" data-line-number="6">predicted.classes &lt;-<span class="st"> </span><span class="kw">ifelse</span>(probabilities <span class="op">&gt;</span><span class="st"> </span><span class="kw">mean</span>(dtest<span class="op">$</span>modcontra), <span class="dv">1</span>,<span class="dv">0</span>)</a>
<a class="sourceLine" id="cb86-7" data-line-number="7"><span class="co"># Model accuracy</span></a>
<a class="sourceLine" id="cb86-8" data-line-number="8">observed.classes &lt;-<span class="st"> </span>dtest<span class="op">$</span>modcontra</a>
<a class="sourceLine" id="cb86-9" data-line-number="9"><span class="kw">mean</span>(predicted.classes <span class="op">==</span><span class="st"> </span>observed.classes)</a></code></pre></div>
<pre><code>## [1] 0.7131783</code></pre>
<div class="sourceCode" id="cb88"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb88-1" data-line-number="1"><span class="kw">table</span>(predicted.classes, observed.classes)</a></code></pre></div>
<pre><code>##                  observed.classes
## predicted.classes   0   1
##                 0 646 127
##                 1 169  90</code></pre>
</div>
<div id="logistic-lasso-1" class="section level3">
<h3>Logistic Lasso</h3>
<div class="sourceCode" id="cb90"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb90-1" data-line-number="1">probabilities &lt;-<span class="st">   </span><span class="kw">predict</span>(<span class="dt">object=</span>rmod3, <span class="dt">newx =</span> x.test, <span class="dt">s=</span>s2<span class="op">$</span>lambda<span class="fl">.1</span>se, <span class="dt">type=</span><span class="st">&quot;response&quot;</span>)</a>
<a class="sourceLine" id="cb90-2" data-line-number="2">predicted.classes &lt;-<span class="st"> </span><span class="kw">ifelse</span>(probabilities <span class="op">&gt;</span><span class="st"> </span><span class="kw">mean</span>(dtest<span class="op">$</span>modcontra), <span class="dv">1</span>,<span class="dv">0</span>)</a>
<a class="sourceLine" id="cb90-3" data-line-number="3"><span class="co"># Model accuracy</span></a>
<a class="sourceLine" id="cb90-4" data-line-number="4">observed.classes &lt;-<span class="st"> </span>dtest<span class="op">$</span>modcontra</a>
<a class="sourceLine" id="cb90-5" data-line-number="5"><span class="kw">mean</span>(predicted.classes <span class="op">==</span><span class="st"> </span>observed.classes)</a></code></pre></div>
<pre><code>## [1] 0.6879845</code></pre>
<div class="sourceCode" id="cb92"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb92-1" data-line-number="1"><span class="kw">table</span>(predicted.classes, observed.classes)</a></code></pre></div>
<pre><code>##                  observed.classes
## predicted.classes   0   1
##                 0 611 118
##                 1 204  99</code></pre>
</div>
<div id="so-what" class="section level3">
<h3>So what?</h3>
<p>By doing any type of regression variable selection, you are going to have the computer tell you what variables are going into your models. A lot of people don’t like this, but it is very common in the applied world in order to build the best models, especially in high-dimensional data.</p>
<div id="take-aways" class="section level4">
<h4>Take aways</h4>
<ol style="list-style-type: decimal">
<li>These methods will lead to more parsimonius models with better predictive power</li>
<li>The models are sensitive to their tuning parameters, so please don’t jump into this without tuning the model parameters.</li>
</ol>
</div>
</div>
</div>
</div>
</section>

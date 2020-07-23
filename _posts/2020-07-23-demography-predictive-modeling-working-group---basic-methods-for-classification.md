---
title: "Demography Predictive Modeling Working Group - Basic methods for classification"

author: "Corey Sparks, Ph.D."
date: "July 23, 2020"
layout: post
---


<section class="main-content">
<div id="classification-methods-and-models" class="section level2">
<h2>Classification methods and models</h2>
<p>In classification methods, we are typically interested in using some observed characteristics of a case to predict a binary categorical outcome. This can be extended to a multi-category outcome, but the largest number of applications involve a 1/0 outcome.</p>
<p>Below, we look at a few classic methods of doing this:</p>
<ul>
<li><p>Logistic regression</p></li>
<li><p>Regression/Partitioning Trees</p></li>
<li><p>Linear Discriminant Functions</p></li>
</ul>
<p>There are other methods that we will examine but these are probably the easiest to understand.</p>
<p>In these examples, we will use the Demographic and Health Survey <a href="https://dhsprogram.com/data/Model-Datasets.cfm">Model Data</a>. These are based on the DHS survey, but are publicly available and are used to practice using the DHS data sets, but don’t represent a real country.</p>
<p>In this example, we will use the outcome of contraceptive choice (modern vs other/none) as our outcome.</p>
<div class="sourceCode" id="cb1"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb1-1" data-line-number="1"><span class="kw">library</span>(haven)</a>
<a class="sourceLine" id="cb1-2" data-line-number="2">dat&lt;-<span class="kw">url</span>(<span class="st">&quot;https://github.com/coreysparks/data/blob/master/ZZIR62FL.DTA?raw=true&quot;</span>)</a>
<a class="sourceLine" id="cb1-3" data-line-number="3">model.dat&lt;-<span class="kw">read_dta</span>(dat)</a></code></pre></div>
<p>Here we recode some of our variables and limit our data to those women who are not currently pregnant and who are sexually active.</p>
<div class="sourceCode" id="cb2"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb2-1" data-line-number="1"><span class="kw">library</span>(dplyr)</a></code></pre></div>
<pre><code>## 
## Attaching package: &#39;dplyr&#39;</code></pre>
<pre><code>## The following objects are masked from &#39;package:stats&#39;:
## 
##     filter, lag</code></pre>
<pre><code>## The following objects are masked from &#39;package:base&#39;:
## 
##     intersect, setdiff, setequal, union</code></pre>
<div class="sourceCode" id="cb6"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb6-1" data-line-number="1">model.dat2&lt;-model.dat<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb6-2" data-line-number="2"><span class="st">  </span><span class="kw">mutate</span>(<span class="dt">region =</span> v024, </a>
<a class="sourceLine" id="cb6-3" data-line-number="3">         <span class="dt">modcontra=</span> <span class="kw">as.factor</span>(<span class="kw">ifelse</span>(v364 <span class="op">==</span><span class="dv">1</span>,<span class="dv">1</span>, <span class="dv">0</span>)),</a>
<a class="sourceLine" id="cb6-4" data-line-number="4">         <span class="dt">age =</span> v012, </a>
<a class="sourceLine" id="cb6-5" data-line-number="5">         <span class="dt">livchildren=</span>v218,</a>
<a class="sourceLine" id="cb6-6" data-line-number="6">         <span class="dt">educ =</span> v106,</a>
<a class="sourceLine" id="cb6-7" data-line-number="7">         <span class="dt">currpreg=</span>v213,</a>
<a class="sourceLine" id="cb6-8" data-line-number="8">         <span class="dt">knowmodern=</span><span class="kw">ifelse</span>(v301<span class="op">==</span><span class="dv">3</span>, <span class="dv">1</span>, <span class="dv">0</span>),</a>
<a class="sourceLine" id="cb6-9" data-line-number="9">         <span class="dt">age2=</span>v012<span class="op">^</span><span class="dv">2</span>)<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb6-10" data-line-number="10"><span class="st">  </span><span class="kw">filter</span>(currpreg<span class="op">==</span><span class="dv">0</span>, v536<span class="op">&gt;</span><span class="dv">0</span>)<span class="op">%&gt;%</span><span class="st"> </span><span class="co">#notpreg, sex active</span></a>
<a class="sourceLine" id="cb6-11" data-line-number="11"><span class="st">  </span>dplyr<span class="op">::</span><span class="kw">select</span>(caseid, region, modcontra,age, age2,livchildren, educ, knowmodern)</a></code></pre></div>
<div class="sourceCode" id="cb7"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb7-1" data-line-number="1">knitr<span class="op">::</span><span class="kw">kable</span>(<span class="kw">head</span>(model.dat2))</a></code></pre></div>
<table>
<thead>
<tr class="header">
<th align="left">caseid</th>
<th align="right">region</th>
<th align="left">modcontra</th>
<th align="right">age</th>
<th align="right">age2</th>
<th align="right">livchildren</th>
<th align="right">educ</th>
<th align="right">knowmodern</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">1 1 2</td>
<td align="right">2</td>
<td align="left">0</td>
<td align="right">30</td>
<td align="right">900</td>
<td align="right">4</td>
<td align="right">0</td>
<td align="right">1</td>
</tr>
<tr class="even">
<td align="left">1 4 2</td>
<td align="right">2</td>
<td align="left">0</td>
<td align="right">42</td>
<td align="right">1764</td>
<td align="right">2</td>
<td align="right">0</td>
<td align="right">1</td>
</tr>
<tr class="odd">
<td align="left">1 4 3</td>
<td align="right">2</td>
<td align="left">0</td>
<td align="right">25</td>
<td align="right">625</td>
<td align="right">3</td>
<td align="right">1</td>
<td align="right">1</td>
</tr>
<tr class="even">
<td align="left">1 5 1</td>
<td align="right">2</td>
<td align="left">0</td>
<td align="right">25</td>
<td align="right">625</td>
<td align="right">2</td>
<td align="right">2</td>
<td align="right">1</td>
</tr>
<tr class="odd">
<td align="left">1 6 2</td>
<td align="right">2</td>
<td align="left">0</td>
<td align="right">37</td>
<td align="right">1369</td>
<td align="right">2</td>
<td align="right">0</td>
<td align="right">1</td>
</tr>
<tr class="even">
<td align="left">1 6 3</td>
<td align="right">2</td>
<td align="left">0</td>
<td align="right">17</td>
<td align="right">289</td>
<td align="right">0</td>
<td align="right">2</td>
<td align="right">0</td>
</tr>
</tbody>
</table>
<div id="using-caret-to-create-training-and-test-sets." class="section level3">
<h3>using caret to create training and test sets.</h3>
<p>We use an 80% training fraction</p>
<div class="sourceCode" id="cb8"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb8-1" data-line-number="1"><span class="kw">library</span>(caret)</a></code></pre></div>
<pre><code>## Loading required package: lattice</code></pre>
<pre><code>## Loading required package: ggplot2</code></pre>
<div class="sourceCode" id="cb11"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb11-1" data-line-number="1"><span class="kw">set.seed</span>(<span class="dv">1115</span>)</a>
<a class="sourceLine" id="cb11-2" data-line-number="2">train&lt;-<span class="st"> </span><span class="kw">createDataPartition</span>(<span class="dt">y =</span> model.dat2<span class="op">$</span>modcontra , <span class="dt">p =</span> <span class="fl">.80</span>, <span class="dt">list=</span>F)</a>
<a class="sourceLine" id="cb11-3" data-line-number="3"></a>
<a class="sourceLine" id="cb11-4" data-line-number="4">model.dat2train&lt;-model.dat2[train,]</a></code></pre></div>
<pre><code>## Warning: The `i` argument of ``[`()` can&#39;t be a matrix as of tibble 3.0.0.
## Convert to a vector.
## This warning is displayed once every 8 hours.
## Call `lifecycle::last_warnings()` to see where this warning was generated.</code></pre>
<div class="sourceCode" id="cb13"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb13-1" data-line-number="1">model.dat2test&lt;-model.dat2[<span class="op">-</span>train,]</a>
<a class="sourceLine" id="cb13-2" data-line-number="2"></a>
<a class="sourceLine" id="cb13-3" data-line-number="3"><span class="kw">table</span>(model.dat2train<span class="op">$</span>modcontra)</a></code></pre></div>
<pre><code>## 
##    0    1 
## 4036 1409</code></pre>
<div class="sourceCode" id="cb15"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb15-1" data-line-number="1"><span class="kw">prop.table</span>(<span class="kw">table</span>(model.dat2train<span class="op">$</span>modcontra))</a></code></pre></div>
<pre><code>## 
##         0         1 
## 0.7412305 0.2587695</code></pre>
<div class="sourceCode" id="cb17"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb17-1" data-line-number="1"><span class="kw">summary</span>(model.dat2train)</a></code></pre></div>
<pre><code>##     caseid              region      modcontra      age             age2       
##  Length:5445        Min.   :1.000   0:4036    Min.   :15.00   Min.   : 225.0  
##  Class :character   1st Qu.:1.000   1:1409    1st Qu.:21.00   1st Qu.: 441.0  
##  Mode  :character   Median :2.000             Median :29.00   Median : 841.0  
##                     Mean   :2.164             Mean   :29.78   Mean   : 976.8  
##                     3rd Qu.:3.000             3rd Qu.:37.00   3rd Qu.:1369.0  
##                     Max.   :4.000             Max.   :49.00   Max.   :2401.0  
##   livchildren          educ          knowmodern    
##  Min.   : 0.000   Min.   :0.0000   Min.   :0.0000  
##  1st Qu.: 1.000   1st Qu.:0.0000   1st Qu.:1.0000  
##  Median : 2.000   Median :0.0000   Median :1.0000  
##  Mean   : 2.546   Mean   :0.7381   Mean   :0.9442  
##  3rd Qu.: 4.000   3rd Qu.:2.0000   3rd Qu.:1.0000  
##  Max.   :10.000   Max.   :3.0000   Max.   :1.0000</code></pre>
</div>
</div>
<div id="logistic-regression-for-classification" class="section level2">
<h2>Logistic regression for classification</h2>
<p>Here we use a basic binomial GLM to estimate the probability of a woman using modern contraception. We use information on their region of residence, age, number of living children and level of education.</p>
<p>This model can be written: <span class="math display">\[ln \left ( \frac{Pr(\text{Modern Contraception})}{1-Pr(\text{Modern Contraception})} \right ) = X&#39; \beta\]</span></p>
<p>Which can be converted to the probability scale via the inverse logit transform:</p>
<p><span class="math display">\[Pr(\text{Modern Contraception}) = \frac{1}{1+exp (-X&#39; \beta)}\]</span></p>
<div class="sourceCode" id="cb19"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb19-1" data-line-number="1">glm1&lt;-<span class="kw">glm</span>(modcontra<span class="op">~</span><span class="kw">factor</span>(region)<span class="op">+</span><span class="kw">scale</span>(age)<span class="op">+</span><span class="kw">scale</span>(age2)<span class="op">+</span><span class="kw">scale</span>(livchildren)<span class="op">+</span><span class="kw">factor</span>(educ), <span class="dt">data=</span>model.dat2train[,<span class="op">-</span><span class="dv">1</span>], <span class="dt">family =</span> binomial)</a>
<a class="sourceLine" id="cb19-2" data-line-number="2"><span class="kw">summary</span>(glm1)</a></code></pre></div>
<pre><code>## 
## Call:
## glm(formula = modcontra ~ factor(region) + scale(age) + scale(age2) + 
##     scale(livchildren) + factor(educ), family = binomial, data = model.dat2train[, 
##     -1])
## 
## Deviance Residuals: 
##     Min       1Q   Median       3Q      Max  
## -1.4073  -0.7103  -0.5734   1.0669   2.3413  
## 
## Coefficients:
##                    Estimate Std. Error z value Pr(&gt;|z|)    
## (Intercept)        -1.91240    0.06807 -28.095  &lt; 2e-16 ***
## factor(region)2     0.38755    0.08534   4.541 5.60e-06 ***
## factor(region)3     0.62565    0.09531   6.564 5.23e-11 ***
## factor(region)4     0.30066    0.09454   3.180 0.001471 ** 
## scale(age)          0.63678    0.26540   2.399 0.016425 *  
## scale(age2)        -0.98328    0.26194  -3.754 0.000174 ***
## scale(livchildren)  0.17004    0.05408   3.144 0.001665 ** 
## factor(educ)1       0.43835    0.10580   4.143 3.43e-05 ***
## factor(educ)2       1.38923    0.08646  16.068  &lt; 2e-16 ***
## factor(educ)3       1.54061    0.16086   9.577  &lt; 2e-16 ***
## ---
## Signif. codes:  0 &#39;***&#39; 0.001 &#39;**&#39; 0.01 &#39;*&#39; 0.05 &#39;.&#39; 0.1 &#39; &#39; 1
## 
## (Dispersion parameter for binomial family taken to be 1)
## 
##     Null deviance: 6226.5  on 5444  degrees of freedom
## Residual deviance: 5629.0  on 5435  degrees of freedom
## AIC: 5649
## 
## Number of Fisher Scoring iterations: 4</code></pre>
<p>We see that all the predictors are significantly related to our outcome</p>
<p>Next we see how the model performs in terms of accuracy of prediction. This is new comparison to how we typically use logistic regression.</p>
<p>We use the <code>predict()</code> function to get the estimated class probabilities for each case</p>
<div class="sourceCode" id="cb21"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb21-1" data-line-number="1">tr_pred&lt;-<span class="st"> </span><span class="kw">predict</span>(glm1, <span class="dt">newdata =</span> model.dat2train, <span class="dt">type =</span> <span class="st">&quot;response&quot;</span>)</a>
<a class="sourceLine" id="cb21-2" data-line-number="2"><span class="kw">head</span>(tr_pred)</a></code></pre></div>
<pre><code>##          1          2          3          4          5          6 
## 0.22002790 0.31137928 0.15091505 0.20389088 0.08726724 0.18808481</code></pre>
<p>These are the estimated probability that each of these women used modern contraception, based on the model.</p>
<p>In order to create classes (uses modern vs doesn’t use modern contraception) we have to use a <strong>decision rule</strong>. A decision rule is when we choose a cut off point, or <em>threshold</em> value of the probability to classify each observation as belonging to one class or the other.</p>
<p>A basic decision rule is if <span class="math inline">\(Pr(y=\text{Modern Contraception} |X) &gt;.5\)</span> Then classify the observation as a modern contraception user, and otherwise not. This is what we will use here.</p>
<div class="sourceCode" id="cb23"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb23-1" data-line-number="1">tr_predcl&lt;-<span class="kw">factor</span>(<span class="kw">ifelse</span>(tr_pred<span class="op">&gt;</span>.<span class="dv">5</span>, <span class="dv">1</span>, <span class="dv">0</span>))</a>
<a class="sourceLine" id="cb23-2" data-line-number="2"></a>
<a class="sourceLine" id="cb23-3" data-line-number="3"><span class="kw">library</span>(ggplot2)</a>
<a class="sourceLine" id="cb23-4" data-line-number="4"></a>
<a class="sourceLine" id="cb23-5" data-line-number="5">pred1&lt;-<span class="kw">data.frame</span>(<span class="dt">pr=</span>tr_pred, <span class="dt">gr=</span>tr_predcl, <span class="dt">modcon=</span>model.dat2train<span class="op">$</span>modcontra)</a>
<a class="sourceLine" id="cb23-6" data-line-number="6"></a>
<a class="sourceLine" id="cb23-7" data-line-number="7">pred1<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb23-8" data-line-number="8"><span class="st">  </span><span class="kw">ggplot</span>()<span class="op">+</span><span class="kw">geom_density</span>(<span class="kw">aes</span>(<span class="dt">x=</span>pr, <span class="dt">color=</span>gr, <span class="dt">group=</span>gr))<span class="op">+</span><span class="kw">ggtitle</span>(<span class="dt">label =</span> <span class="st">&quot;Probability of Modern Contraception&quot;</span>, <span class="dt">subtitle =</span> <span class="st">&quot;Threshold = .5&quot;</span>)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/Basic-Classification_files/figure-html/unnamed-chunk-8-1.png" /><!-- --></p>
<div class="sourceCode" id="cb24"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb24-1" data-line-number="1">pred1<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb24-2" data-line-number="2"><span class="st">  </span><span class="kw">ggplot</span>()<span class="op">+</span><span class="kw">geom_density</span>(<span class="kw">aes</span>(<span class="dt">x=</span>pr, <span class="dt">color=</span>modcon, <span class="dt">group=</span>modcon))<span class="op">+</span><span class="kw">ggtitle</span>(<span class="dt">label =</span> <span class="st">&quot;Probability of Modern Contraception&quot;</span>, <span class="dt">subtitle =</span> <span class="st">&quot;Truth&quot;</span>)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/Basic-Classification_files/figure-html/unnamed-chunk-8-2.png" /><!-- --></p>
<p>Next we need to see how we did. A simple cross tab of the observed classes versus the predicted classes is called the <strong>confusion matrix</strong>.</p>
<div class="sourceCode" id="cb25"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb25-1" data-line-number="1"><span class="kw">table</span>( tr_predcl,model.dat2train<span class="op">$</span>modcontra)</a></code></pre></div>
<pre><code>##          
## tr_predcl    0    1
##         0 3761 1142
##         1  275  267</code></pre>
<p>This is great, but typically it’s easier to understand the model’s predictive ability by converting these to proportions. The <code>confusionMatrix()</code> function in <code>caret</code> can do this, plus other stuff.</p>
<p>This provides lots of output summarizing the classification results. At its core is the matrix of observed classes versus predicted classes. I got one depiction of this <a href="https://www.geeksforgeeks.org/confusion-matrix-machine-learning/">here</a> and from the <a href="https://en.wikipedia.org/wiki/Confusion_matrix">Wikipedia page</a></p>
<!-- ![Confusion matrix](C:/Users/ozd504/OneDrive - University of Texas at San Antonio/predictive_workinggroup/images/cm1.PNG) -->
<p>Lots of information on the predictive accuracy can be found from this 2x2 table:</p>
<!-- ![Confusion matrix](C:/Users/ozd504/OneDrive - University of Texas at San Antonio/predictive_workinggroup/images/cm2.PNG) -->
<p>Generally, we are interested in overall accuracy, sensitivity and specificity.</p>
<div class="sourceCode" id="cb27"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb27-1" data-line-number="1"><span class="kw">confusionMatrix</span>(<span class="dt">data =</span> tr_predcl,model.dat2train<span class="op">$</span>modcontra )</a></code></pre></div>
<pre><code>## Confusion Matrix and Statistics
## 
##           Reference
## Prediction    0    1
##          0 3761 1142
##          1  275  267
##                                           
##                Accuracy : 0.7398          
##                  95% CI : (0.7279, 0.7514)
##     No Information Rate : 0.7412          
##     P-Value [Acc &gt; NIR] : 0.6046          
##                                           
##                   Kappa : 0.1517          
##                                           
##  Mcnemar&#39;s Test P-Value : &lt;2e-16          
##                                           
##             Sensitivity : 0.9319          
##             Specificity : 0.1895          
##          Pos Pred Value : 0.7671          
##          Neg Pred Value : 0.4926          
##              Prevalence : 0.7412          
##          Detection Rate : 0.6907          
##    Detection Prevalence : 0.9005          
##       Balanced Accuracy : 0.5607          
##                                           
##        &#39;Positive&#39; Class : 0               
## </code></pre>
<p>Overall the model has a 73.9% accuracy, which isn’t bad! What is bad is some of the other measures. The sensitivity is really low <code>267/(267+1142) = .189</code>, so we are only predicting the positive class (modern contraception) in 19% of cases correctly. In other word the model is pretty good at predicting if you don’t use modern contraception, <code>3761/(3761+275)= .931</code>, but not at predicting if you do.</p>
<p>We could try a different decision rule, in this case, I use the mean of the response as the cutoff value.</p>
<div class="sourceCode" id="cb29"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb29-1" data-line-number="1">tr_predcl&lt;-<span class="kw">factor</span>(<span class="kw">ifelse</span>(tr_pred<span class="op">&gt;</span>.<span class="dv">258</span>, <span class="dv">1</span>, <span class="dv">0</span>)) <span class="co">#mean of response</span></a>
<a class="sourceLine" id="cb29-2" data-line-number="2"></a>
<a class="sourceLine" id="cb29-3" data-line-number="3">pred2&lt;-<span class="kw">data.frame</span>(<span class="dt">pr=</span>tr_pred, <span class="dt">gr=</span>tr_predcl, <span class="dt">modcon=</span>model.dat2train<span class="op">$</span>modcontra)</a>
<a class="sourceLine" id="cb29-4" data-line-number="4">pred2<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb29-5" data-line-number="5"><span class="st">  </span><span class="kw">ggplot</span>()<span class="op">+</span><span class="kw">geom_density</span>(<span class="kw">aes</span>(<span class="dt">x=</span>pr, <span class="dt">color=</span>gr, <span class="dt">group=</span>gr))<span class="op">+</span><span class="kw">ggtitle</span>(<span class="dt">label =</span> <span class="st">&quot;Probability of Modern Contraception&quot;</span>, <span class="dt">subtitle =</span> <span class="st">&quot;Threshold = .258&quot;</span>)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/Basic-Classification_files/figure-html/unnamed-chunk-11-1.png" /><!-- --></p>
<div class="sourceCode" id="cb30"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb30-1" data-line-number="1">pred2<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb30-2" data-line-number="2"><span class="st">  </span><span class="kw">ggplot</span>()<span class="op">+</span><span class="kw">geom_density</span>(<span class="kw">aes</span>(<span class="dt">x=</span>pr, <span class="dt">color=</span>modcon, <span class="dt">group=</span>modcon))<span class="op">+</span><span class="kw">ggtitle</span>(<span class="dt">label =</span> <span class="st">&quot;Probability of Modern Contraception&quot;</span>, <span class="dt">subtitle =</span> <span class="st">&quot;Truth&quot;</span>)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/Basic-Classification_files/figure-html/unnamed-chunk-11-2.png" /><!-- --></p>
<div class="sourceCode" id="cb31"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb31-1" data-line-number="1"><span class="kw">confusionMatrix</span>(<span class="dt">data =</span> tr_predcl,model.dat2train<span class="op">$</span>modcontra, <span class="dt">positive =</span> <span class="st">&quot;1&quot;</span> )</a></code></pre></div>
<pre><code>## Confusion Matrix and Statistics
## 
##           Reference
## Prediction    0    1
##          0 2944  577
##          1 1092  832
##                                          
##                Accuracy : 0.6935         
##                  95% CI : (0.681, 0.7057)
##     No Information Rate : 0.7412         
##     P-Value [Acc &gt; NIR] : 1              
##                                          
##                   Kappa : 0.2859         
##                                          
##  Mcnemar&#39;s Test P-Value : &lt;2e-16         
##                                          
##             Sensitivity : 0.5905         
##             Specificity : 0.7294         
##          Pos Pred Value : 0.4324         
##          Neg Pred Value : 0.8361         
##              Prevalence : 0.2588         
##          Detection Rate : 0.1528         
##    Detection Prevalence : 0.3534         
##       Balanced Accuracy : 0.6600         
##                                          
##        &#39;Positive&#39; Class : 1              
## </code></pre>
<p>Which drops the accuracy a little, but increases the specificity at the cost of the sensitivity.</p>
<p>Next we do this on the test set to evaluate model performance outside of the training data</p>
<div class="sourceCode" id="cb33"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb33-1" data-line-number="1">pred_test&lt;-<span class="kw">predict</span>(glm1, <span class="dt">newdata=</span>model.dat2test, <span class="dt">type=</span><span class="st">&quot;response&quot;</span>)</a>
<a class="sourceLine" id="cb33-2" data-line-number="2">pred_cl&lt;-<span class="kw">factor</span>(<span class="kw">ifelse</span>(pred_test<span class="op">&gt;</span>.<span class="dv">28</span>, <span class="dv">1</span>, <span class="dv">0</span>))</a>
<a class="sourceLine" id="cb33-3" data-line-number="3"></a>
<a class="sourceLine" id="cb33-4" data-line-number="4"><span class="kw">table</span>(model.dat2test<span class="op">$</span>modcontra,pred_cl)</a></code></pre></div>
<pre><code>##    pred_cl
##       0   1
##   0 746 262
##   1 160 192</code></pre>
<div class="sourceCode" id="cb35"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb35-1" data-line-number="1"><span class="kw">confusionMatrix</span>(<span class="dt">data =</span> pred_cl,model.dat2test<span class="op">$</span>modcontra )</a></code></pre></div>
<pre><code>## Confusion Matrix and Statistics
## 
##           Reference
## Prediction   0   1
##          0 746 160
##          1 262 192
##                                           
##                Accuracy : 0.6897          
##                  95% CI : (0.6644, 0.7142)
##     No Information Rate : 0.7412          
##     P-Value [Acc &gt; NIR] : 1               
##                                           
##                   Kappa : 0.2609          
##                                           
##  Mcnemar&#39;s Test P-Value : 8.806e-07       
##                                           
##             Sensitivity : 0.7401          
##             Specificity : 0.5455          
##          Pos Pred Value : 0.8234          
##          Neg Pred Value : 0.4229          
##              Prevalence : 0.7412          
##          Detection Rate : 0.5485          
##    Detection Prevalence : 0.6662          
##       Balanced Accuracy : 0.6428          
##                                           
##        &#39;Positive&#39; Class : 0               
## </code></pre>
</div>
<div id="regression-partition-tree" class="section level2">
<h2>Regression partition tree</h2>
<p>As we saw in the first working group example, the regression tree is another common technique used in classification problems. Regression or classification trees attempt to</p>
<div class="sourceCode" id="cb37"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb37-1" data-line-number="1"><span class="kw">library</span>(rpart)</a>
<a class="sourceLine" id="cb37-2" data-line-number="2"><span class="kw">library</span>(rpart.plot)</a>
<a class="sourceLine" id="cb37-3" data-line-number="3"></a>
<a class="sourceLine" id="cb37-4" data-line-number="4">rp1&lt;-<span class="kw">rpart</span>(modcontra<span class="op">~</span><span class="kw">factor</span>(region)<span class="op">+</span>(age)<span class="op">+</span>livchildren<span class="op">+</span><span class="kw">factor</span>(educ), </a>
<a class="sourceLine" id="cb37-5" data-line-number="5">           <span class="dt">data=</span>model.dat2train,</a>
<a class="sourceLine" id="cb37-6" data-line-number="6">           <span class="dt">method =</span><span class="st">&quot;class&quot;</span>,</a>
<a class="sourceLine" id="cb37-7" data-line-number="7">           <span class="dt">control =</span> <span class="kw">rpart.control</span>(<span class="dt">minbucket =</span> <span class="dv">10</span>, <span class="dt">cp=</span>.<span class="dv">01</span>)) <span class="co">#lower CP parameter makes for more compliacted tree</span></a>
<a class="sourceLine" id="cb37-8" data-line-number="8"><span class="kw">summary</span>(rp1)</a></code></pre></div>
<pre><code>## Call:
## rpart(formula = modcontra ~ factor(region) + (age) + livchildren + 
##     factor(educ), data = model.dat2train, method = &quot;class&quot;, control = rpart.control(minbucket = 10, 
##     cp = 0.01))
##   n= 5445 
## 
##           CP nsplit rel error    xerror       xstd
## 1 0.04009936      0 1.0000000 1.0000000 0.02293618
## 2 0.01100071      2 0.9198013 0.9198013 0.02230305
## 3 0.01000000      4 0.8977999 0.9169624 0.02227934
## 
## Variable importance
##   factor(educ)    livchildren            age factor(region) 
##             58             23             19              1 
## 
## Node number 1: 5445 observations,    complexity param=0.04009936
##   predicted class=0  expected loss=0.2587695  P(node) =1
##     class counts:  4036  1409
##    probabilities: 0.741 0.259 
##   left son=2 (3862 obs) right son=3 (1583 obs)
##   Primary splits:
##       factor(educ)   splits as  LLRR,     improve=189.73590, (0 missing)
##       livchildren    &lt; 0.5  to the right, improve= 84.51811, (0 missing)
##       age            &lt; 23.5 to the right, improve= 52.42664, (0 missing)
##       factor(region) splits as  LLRL,     improve= 36.53020, (0 missing)
##   Surrogate splits:
##       livchildren    &lt; 0.5  to the right, agree=0.772, adj=0.215, (0 split)
##       age            &lt; 19.5 to the right, agree=0.753, adj=0.149, (0 split)
##       factor(region) splits as  LLRL,     agree=0.713, adj=0.014, (0 split)
## 
## Node number 2: 3862 observations
##   predicted class=0  expected loss=0.174262  P(node) =0.7092746
##     class counts:  3189   673
##    probabilities: 0.826 0.174 
## 
## Node number 3: 1583 observations,    complexity param=0.04009936
##   predicted class=0  expected loss=0.46494  P(node) =0.2907254
##     class counts:   847   736
##    probabilities: 0.535 0.465 
##   left son=6 (868 obs) right son=7 (715 obs)
##   Primary splits:
##       livchildren    &lt; 0.5  to the right, improve=33.940940, (0 missing)
##       age            &lt; 36.5 to the right, improve=20.441730, (0 missing)
##       factor(region) splits as  LRRL,     improve= 2.382434, (0 missing)
##       factor(educ)   splits as  --LR,     improve= 0.556353, (0 missing)
##   Surrogate splits:
##       age &lt; 20.5 to the right, agree=0.749, adj=0.443, (0 split)
## 
## Node number 6: 868 observations
##   predicted class=0  expected loss=0.3709677  P(node) =0.1594123
##     class counts:   546   322
##    probabilities: 0.629 0.371 
## 
## Node number 7: 715 observations,    complexity param=0.01100071
##   predicted class=1  expected loss=0.420979  P(node) =0.1313131
##     class counts:   301   414
##    probabilities: 0.421 0.579 
##   left son=14 (14 obs) right son=15 (701 obs)
##   Primary splits:
##       age            &lt; 32.5 to the right, improve=9.574909, (0 missing)
##       factor(educ)   splits as  --LR,     improve=1.650766, (0 missing)
##       factor(region) splits as  LRRL,     improve=1.324512, (0 missing)
## 
## Node number 14: 14 observations
##   predicted class=0  expected loss=0  P(node) =0.002571166
##     class counts:    14     0
##    probabilities: 1.000 0.000 
## 
## Node number 15: 701 observations,    complexity param=0.01100071
##   predicted class=1  expected loss=0.4094151  P(node) =0.128742
##     class counts:   287   414
##    probabilities: 0.409 0.591 
##   left son=30 (137 obs) right son=31 (564 obs)
##   Primary splits:
##       age            &lt; 16.5 to the left,  improve=7.933444, (0 missing)
##       factor(educ)   splits as  --LR,     improve=2.545437, (0 missing)
##       factor(region) splits as  LRRL,     improve=1.768127, (0 missing)
## 
## Node number 30: 137 observations
##   predicted class=0  expected loss=0.4379562  P(node) =0.0251607
##     class counts:    77    60
##    probabilities: 0.562 0.438 
## 
## Node number 31: 564 observations
##   predicted class=1  expected loss=0.3723404  P(node) =0.1035813
##     class counts:   210   354
##    probabilities: 0.372 0.628</code></pre>
<div class="sourceCode" id="cb39"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb39-1" data-line-number="1"><span class="kw">rpart.plot</span>(rp1, <span class="dt">type =</span> <span class="dv">4</span>,<span class="dt">extra=</span><span class="dv">4</span>, </a>
<a class="sourceLine" id="cb39-2" data-line-number="2"><span class="dt">box.palette=</span><span class="st">&quot;GnBu&quot;</span>,</a>
<a class="sourceLine" id="cb39-3" data-line-number="3"><span class="dt">shadow.col=</span><span class="st">&quot;gray&quot;</span>, </a>
<a class="sourceLine" id="cb39-4" data-line-number="4"><span class="dt">nn=</span><span class="ot">TRUE</span>, <span class="dt">main=</span><span class="st">&quot;Classification tree for using modern contraception&quot;</span>)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/Basic-Classification_files/figure-html/unnamed-chunk-14-1.png" /><!-- --></p>
<p>Each node box displays the classification, the probability of each class at that node (i.e. the probability of the class conditioned on the node) and the percentage of observations used at that node. <a href="https://blog.revolutionanalytics.com/2013/06/plotting-classification-and-regression-trees-with-plotrpart.html">From here</a>.</p>
<div class="sourceCode" id="cb40"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb40-1" data-line-number="1">predrp1&lt;-<span class="kw">predict</span>(rp1, <span class="dt">newdata=</span>model.dat2train, <span class="dt">type =</span> <span class="st">&quot;class&quot;</span>)</a>
<a class="sourceLine" id="cb40-2" data-line-number="2"><span class="kw">confusionMatrix</span>(<span class="dt">data =</span> predrp1,model.dat2train<span class="op">$</span>modcontra )</a></code></pre></div>
<pre><code>## Confusion Matrix and Statistics
## 
##           Reference
## Prediction    0    1
##          0 3826 1055
##          1  210  354
##                                           
##                Accuracy : 0.7677          
##                  95% CI : (0.7562, 0.7788)
##     No Information Rate : 0.7412          
##     P-Value [Acc &gt; NIR] : 3.566e-06       
##                                           
##                   Kappa : 0.2475          
##                                           
##  Mcnemar&#39;s Test P-Value : &lt; 2.2e-16       
##                                           
##             Sensitivity : 0.9480          
##             Specificity : 0.2512          
##          Pos Pred Value : 0.7839          
##          Neg Pred Value : 0.6277          
##              Prevalence : 0.7412          
##          Detection Rate : 0.7027          
##    Detection Prevalence : 0.8964          
##       Balanced Accuracy : 0.5996          
##                                           
##        &#39;Positive&#39; Class : 0               
## </code></pre>
<p>We see the regression tree is performing a little better than the logistic regression on the test case using the summary below:</p>
<div class="sourceCode" id="cb42"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb42-1" data-line-number="1">pred_testrp&lt;-<span class="kw">predict</span>(rp1, <span class="dt">newdata=</span>model.dat2test, <span class="dt">type=</span><span class="st">&quot;class&quot;</span>)</a>
<a class="sourceLine" id="cb42-2" data-line-number="2"></a>
<a class="sourceLine" id="cb42-3" data-line-number="3"><span class="kw">confusionMatrix</span>(<span class="dt">data =</span> pred_testrp,model.dat2test<span class="op">$</span>modcontra )</a></code></pre></div>
<pre><code>## Confusion Matrix and Statistics
## 
##           Reference
## Prediction   0   1
##          0 947 263
##          1  61  89
##                                           
##                Accuracy : 0.7618          
##                  95% CI : (0.7382, 0.7842)
##     No Information Rate : 0.7412          
##     P-Value [Acc &gt; NIR] : 0.0434          
##                                           
##                   Kappa : 0.2365          
##                                           
##  Mcnemar&#39;s Test P-Value : &lt;2e-16          
##                                           
##             Sensitivity : 0.9395          
##             Specificity : 0.2528          
##          Pos Pred Value : 0.7826          
##          Neg Pred Value : 0.5933          
##              Prevalence : 0.7412          
##          Detection Rate : 0.6963          
##    Detection Prevalence : 0.8897          
##       Balanced Accuracy : 0.5962          
##                                           
##        &#39;Positive&#39; Class : 0               
## </code></pre>
</div>
<div id="linear-discriminant-function" class="section level2">
<h2>Linear discriminant function</h2>
<p>Linear discriminant functions attempt to separate classes from each other using a strictly linear function of the variables. It attempts to reduce the dimensionality of the original data to a single linear function of the input variables, or the <em>discriminant function</em>. This is very similar to what PCA does when it creates a principal component, although in LDA, the function uses this linear transformation of the data to optimally separate classes.</p>
<p>In this case it performs better than the logistic regression but not as well as the regression tree.</p>
<div class="sourceCode" id="cb44"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb44-1" data-line-number="1"><span class="kw">library</span>(MASS)</a></code></pre></div>
<pre><code>## 
## Attaching package: &#39;MASS&#39;</code></pre>
<pre><code>## The following object is masked from &#39;package:dplyr&#39;:
## 
##     select</code></pre>
<div class="sourceCode" id="cb47"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb47-1" data-line-number="1">lda1&lt;-<span class="kw">lda</span>(modcontra<span class="op">~</span><span class="kw">factor</span>(region)<span class="op">+</span><span class="kw">scale</span>(age)<span class="op">+</span>livchildren<span class="op">+</span><span class="kw">factor</span>(educ), <span class="dt">data=</span>model.dat2train,<span class="dt">prior=</span><span class="kw">c</span>(.<span class="dv">74</span>, <span class="fl">.26</span>) , <span class="dt">CV=</span>T)</a>
<a class="sourceLine" id="cb47-2" data-line-number="2"></a>
<a class="sourceLine" id="cb47-3" data-line-number="3">pred_ld1&lt;-lda1<span class="op">$</span>class</a>
<a class="sourceLine" id="cb47-4" data-line-number="4"><span class="kw">head</span>(lda1<span class="op">$</span>posterior) <span class="co">#probabilities of membership in each group</span></a></code></pre></div>
<pre><code>##           0         1
## 1 0.8153664 0.1846336
## 2 0.7387134 0.2612866
## 3 0.8673284 0.1326716
## 4 0.8080069 0.1919931
## 5 0.8976027 0.1023973
## 6 0.8387015 0.1612985</code></pre>
<div class="sourceCode" id="cb49"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb49-1" data-line-number="1">ld1&lt;-<span class="kw">data.frame</span>(<span class="dt">ppmod=</span> lda1<span class="op">$</span>posterior[, <span class="dv">2</span>],<span class="dt">pred=</span>lda1<span class="op">$</span>class, <span class="dt">real=</span>model.dat2train<span class="op">$</span>modcontra)</a>
<a class="sourceLine" id="cb49-2" data-line-number="2">ld1<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb49-3" data-line-number="3"><span class="st">  </span><span class="kw">ggplot</span>()<span class="op">+</span><span class="kw">geom_density</span>(<span class="kw">aes</span>(<span class="dt">x=</span>ppmod, <span class="dt">group=</span>pred, <span class="dt">color=</span>pred))<span class="op">+</span><span class="kw">ggtitle</span>(<span class="dt">label =</span> <span class="st">&quot;Probabilities of class membership on the linear discriminant function&quot;</span>)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/Basic-Classification_files/figure-html/unnamed-chunk-17-1.png" /><!-- --></p>
<div class="sourceCode" id="cb50"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb50-1" data-line-number="1">ld1<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb50-2" data-line-number="2"><span class="st">  </span><span class="kw">ggplot</span>()<span class="op">+</span><span class="kw">geom_density</span>(<span class="kw">aes</span>(<span class="dt">x=</span>ppmod, <span class="dt">group=</span>real, <span class="dt">color=</span>real))<span class="op">+</span><span class="kw">ggtitle</span>(<span class="dt">label =</span> <span class="st">&quot;Probabilities of class membership and the real class&quot;</span>)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/Basic-Classification_files/figure-html/unnamed-chunk-17-2.png" /><!-- --></p>
<p>Accuracy on the training set</p>
<div class="sourceCode" id="cb51"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb51-1" data-line-number="1"><span class="kw">confusionMatrix</span>(pred_ld1,model.dat2train<span class="op">$</span>modcontra )</a></code></pre></div>
<pre><code>## Confusion Matrix and Statistics
## 
##           Reference
## Prediction    0    1
##          0 3625 1000
##          1  411  409
##                                          
##                Accuracy : 0.7409         
##                  95% CI : (0.729, 0.7525)
##     No Information Rate : 0.7412         
##     P-Value [Acc &gt; NIR] : 0.5318         
##                                          
##                   Kappa : 0.2181         
##                                          
##  Mcnemar&#39;s Test P-Value : &lt;2e-16         
##                                          
##             Sensitivity : 0.8982         
##             Specificity : 0.2903         
##          Pos Pred Value : 0.7838         
##          Neg Pred Value : 0.4988         
##              Prevalence : 0.7412         
##          Detection Rate : 0.6657         
##    Detection Prevalence : 0.8494         
##       Balanced Accuracy : 0.5942         
##                                          
##        &#39;Positive&#39; Class : 0              
## </code></pre>
<div class="sourceCode" id="cb53"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb53-1" data-line-number="1">lda1&lt;-<span class="kw">lda</span>(modcontra<span class="op">~</span><span class="kw">factor</span>(region)<span class="op">+</span><span class="kw">scale</span>(age)<span class="op">+</span>livchildren<span class="op">+</span><span class="kw">factor</span>(educ), <span class="dt">data=</span>model.dat2train,<span class="dt">prior=</span><span class="kw">c</span>(.<span class="dv">74</span>, <span class="fl">.26</span>) )</a>
<a class="sourceLine" id="cb53-2" data-line-number="2"></a>
<a class="sourceLine" id="cb53-3" data-line-number="3"><span class="co">#linear discriminant function</span></a>
<a class="sourceLine" id="cb53-4" data-line-number="4">lda1<span class="op">$</span>scaling</a></code></pre></div>
<pre><code>##                        LD1
## factor(region)2  0.4580587
## factor(region)3  0.8545973
## factor(region)4  0.3495414
## scale(age)      -0.3873869
## livchildren      0.1025140
## factor(educ)1    0.4535731
## factor(educ)2    1.9263226
## factor(educ)3    2.2956187</code></pre>
<p>Accuracy on the test set</p>
<div class="sourceCode" id="cb55"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb55-1" data-line-number="1">pred_ld2&lt;-<span class="kw">predict</span>(lda1, model.dat2test)</a>
<a class="sourceLine" id="cb55-2" data-line-number="2"></a>
<a class="sourceLine" id="cb55-3" data-line-number="3"><span class="kw">confusionMatrix</span>(pred_ld2<span class="op">$</span>class, model.dat2test<span class="op">$</span>modcontra)</a></code></pre></div>
<pre><code>## Confusion Matrix and Statistics
## 
##           Reference
## Prediction   0   1
##          0 906 254
##          1 102  98
##                                          
##                Accuracy : 0.7382         
##                  95% CI : (0.714, 0.7614)
##     No Information Rate : 0.7412         
##     P-Value [Acc &gt; NIR] : 0.6115         
##                                          
##                   Kappa : 0.2062         
##                                          
##  Mcnemar&#39;s Test P-Value : 1.214e-15      
##                                          
##             Sensitivity : 0.8988         
##             Specificity : 0.2784         
##          Pos Pred Value : 0.7810         
##          Neg Pred Value : 0.4900         
##              Prevalence : 0.7412         
##          Detection Rate : 0.6662         
##    Detection Prevalence : 0.8529         
##       Balanced Accuracy : 0.5886         
##                                          
##        &#39;Positive&#39; Class : 0              
## </code></pre>
</div>
</section>

---
title: "Demography Informal Methods Seminar - Classification and Regression Trees"

author: "Corey Sparks, Ph.D. - UTSA Department of Demography"
date: "June 30, 2020"
layout: post
---


<section class="main-content">
<div id="classification-models" class="section level2">
<h2>Classification models</h2>
<p>I would suggest you read section 5.1 of <a href="https://utsacloud-my.sharepoint.com/:b:/g/personal/corey_sparks_utsa_edu/EcwJ43xnPG5Jg2XquDnHdmkBPstgvNIyqTTugCfEeFGDFQ?e=SSlWyT">Introduction to Statistical Learning</a> to get a full treatment of this topic</p>
<p>In classification methods, we are typically interested in using some observed characteristics of a case to predict a binary categorical outcome. This can be extended to a multi-category outcome, but the largest number of applications involve a 1/0 outcome.</p>
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
<a class="sourceLine" id="cb6-3" data-line-number="3">         <span class="dt">modcontra=</span> <span class="kw">ifelse</span>(v364 <span class="op">==</span><span class="dv">1</span>,<span class="dv">1</span>, <span class="dv">0</span>),</a>
<a class="sourceLine" id="cb6-4" data-line-number="4">         <span class="dt">age =</span> <span class="kw">cut</span>(v012, <span class="dt">breaks =</span> <span class="dv">5</span>), </a>
<a class="sourceLine" id="cb6-5" data-line-number="5">         <span class="dt">livchildren=</span>v218,</a>
<a class="sourceLine" id="cb6-6" data-line-number="6">         <span class="dt">educ =</span> v106,</a>
<a class="sourceLine" id="cb6-7" data-line-number="7">         <span class="dt">currpreg=</span>v213,</a>
<a class="sourceLine" id="cb6-8" data-line-number="8">         <span class="dt">wealth =</span> <span class="kw">as.factor</span>(v190),</a>
<a class="sourceLine" id="cb6-9" data-line-number="9">         <span class="dt">partnered =</span> <span class="kw">ifelse</span>(v701<span class="op">&lt;=</span><span class="dv">1</span>, <span class="dv">0</span>, <span class="dv">1</span>),</a>
<a class="sourceLine" id="cb6-10" data-line-number="10">         <span class="dt">work =</span> <span class="kw">ifelse</span>(v731<span class="op">%in%</span><span class="kw">c</span>(<span class="dv">0</span>,<span class="dv">1</span>), <span class="dv">0</span>, <span class="dv">1</span>),</a>
<a class="sourceLine" id="cb6-11" data-line-number="11">         <span class="dt">knowmodern=</span><span class="kw">ifelse</span>(v301<span class="op">==</span><span class="dv">3</span>, <span class="dv">1</span>, <span class="dv">0</span>),</a>
<a class="sourceLine" id="cb6-12" data-line-number="12">         <span class="dt">age2=</span>v012<span class="op">^</span><span class="dv">2</span>, </a>
<a class="sourceLine" id="cb6-13" data-line-number="13">         <span class="dt">rural =</span> <span class="kw">ifelse</span>(v025<span class="op">==</span><span class="dv">2</span>, <span class="dv">1</span>,<span class="dv">0</span>),</a>
<a class="sourceLine" id="cb6-14" data-line-number="14">         <span class="dt">wantmore =</span> <span class="kw">ifelse</span>(v605<span class="op">%in%</span><span class="kw">c</span>(<span class="dv">1</span>,<span class="dv">2</span>), <span class="dv">1</span>, <span class="dv">0</span>))<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb6-15" data-line-number="15"><span class="st">  </span><span class="kw">filter</span>(currpreg<span class="op">==</span><span class="dv">0</span>, v536<span class="op">&gt;</span><span class="dv">0</span>, v701<span class="op">!=</span><span class="dv">9</span>)<span class="op">%&gt;%</span><span class="st"> </span><span class="co">#notpreg, sex active</span></a>
<a class="sourceLine" id="cb6-16" data-line-number="16"><span class="st">  </span>dplyr<span class="op">::</span><span class="kw">select</span>(caseid, region, modcontra,age, age2,livchildren, educ, knowmodern, rural, wantmore, partnered,wealth, work)</a></code></pre></div>
<div class="sourceCode" id="cb7"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb7-1" data-line-number="1">knitr<span class="op">::</span><span class="kw">kable</span>(<span class="kw">head</span>(model.dat2))</a></code></pre></div>
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
</div>
<div id="cross-validation-of-predictive-models" class="section level2">
<h2>Cross-validation of predictive models</h2>
<p>The term <a href="https://www.cs.cmu.edu/~schneide/tut5/node42.html">cross-validation</a> refers to fitting a model on a subset of data and then testing it on another subset of the data. Typically this process is repeated several times.</p>
<p>The simplest way of doing this is to leave out a single observation, refit the model without it in the data, then predict its value using the rest of the data. This is called <strong>hold out</strong> cross-validation.</p>
<p><strong>K-fold</strong> cross-validation is a process where you leave out a “group” of observations, it is as follows:</p>
<ol style="list-style-type: decimal">
<li>Randomize the data</li>
<li>Split the data into k groups, where k is an integer</li>
<li>For each of the k groups,
<ul>
<li>Take one of the groups as a hold out test set</li>
<li>Use the other k-1 groups as training data</li>
<li>Fit a model using the data on the k-1 groups, and test it on the hold out group</li>
<li>Measure predictive accuracy of that model, and <strong>throw the model away!</strong></li>
</ul></li>
<li>Summarize the model accuracy over the measured model accuracy metrics</li>
</ol>
<p>A further method is called <strong>leave one out, or LOO</strong> cross-validation. This combines hold out and k-fold cross-validation.</p>
</div>
<div id="why" class="section level1">
<h1>Why?</h1>
<p>By doing this, we can see how model accuracy is affected by particular individuals, and overall allows for model accuracy to be measured repeatedly so we can assess things such as model <strong>tuning parameters</strong>.</p>
<p>If you remember from <a href="https://rpubs.com/corey_sparks/631537">last time</a>, the Lasso analysis depended upon us choosing a good value for the <strong>penalty term</strong> <span class="math inline">\(\lambda\)</span>. In a cross-validation analysis, we can use the various resamplings of the data to examine the model’s accuracy sensitivity to alternative values of this parameter.</p>
<p>This evaluation can either be done systematically, along a grid, or using a random search.</p>
<div id="alternative-accuracy-measures" class="section level2">
<h2>Alternative accuracy measures</h2>
<p>We talked last time about using model accuracy as a measure of overall fit. This was calculated using the observed and predicted values of our outcome. For classification model, another commonly used metric of model predictive power is the Receiver Operating Characteristics (<strong>ROC</strong>) curve. This is a probability curve, and is often accompanied by the area under the curve (<strong>AUC</strong>) measure, which summarizes the separability of the classes. Together they tell you how capable the model is of determining difference between the classes in the data. The higher the values of these, the better, and they are both bound on (0,1).</p>
<p>A nice description of these are found <a href="https://towardsdatascience.com/understanding-auc-roc-curve-68b2303cc9c5">here</a>.</p>
</div>
<div id="regression-trees" class="section level2">
<h2>Regression trees</h2>
<p>Regression trees are a common technique used in classification problems. Regression or classification trees attempt to find optimal splits in the data so that the best classification of observations can be found. Chapter 8 of <a href="https://utsacloud-my.sharepoint.com/:b:/g/personal/corey_sparks_utsa_edu/EcwJ43xnPG5Jg2XquDnHdmkBPstgvNIyqTTugCfEeFGDFQ?e=SSlWyT">Introduction to Statistical Learning</a> is a good place to start with this.</p>
<p>Regression trees generate a set of splitting rules, which classify the data into a set of classes, based on combinations of the predictors. <img src="/home/corey/Documents/seminar/rp1.png" alt="regression partition" /></p>
<p>This example, from the text, shows a 3 region partition of data on baseball hitter data. The outcome here is salary in dollars. Region 1 is players who’ve played less than 4.5 years, they typically have lower salary. The other 2 regions consist of players who’ve played longer than 4.5 years, and who have either less than 117.5 or greater than 117.5 hits. Those with more hits have higher salary than those with lower hits.</p>
<p>The regions can be thought of as nodes (or leaves) on a tree.</p>
<p>Here is a regression tree for these data. The Nodes are the mean salary (in thousands) for players in that region. For example, if a player has less than 4.5 years experiences, and have less than 39.5 hits, their average salary is 676.5 thousand dollars.</p>
<div class="sourceCode" id="cb8"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb8-1" data-line-number="1"><span class="kw">library</span>(tree)</a>
<a class="sourceLine" id="cb8-2" data-line-number="2"><span class="kw">data</span>(Hitters, <span class="dt">package =</span> <span class="st">&quot;ISLR&quot;</span>)</a>
<a class="sourceLine" id="cb8-3" data-line-number="3"><span class="kw">head</span>(Hitters)</a></code></pre></div>
<pre><code>##                   AtBat Hits HmRun Runs RBI Walks Years CAtBat CHits CHmRun
## -Andy Allanson      293   66     1   30  29    14     1    293    66      1
## -Alan Ashby         315   81     7   24  38    39    14   3449   835     69
## -Alvin Davis        479  130    18   66  72    76     3   1624   457     63
## -Andre Dawson       496  141    20   65  78    37    11   5628  1575    225
## -Andres Galarraga   321   87    10   39  42    30     2    396   101     12
## -Alfredo Griffin    594  169     4   74  51    35    11   4408  1133     19
##                   CRuns CRBI CWalks League Division PutOuts Assists Errors
## -Andy Allanson       30   29     14      A        E     446      33     20
## -Alan Ashby         321  414    375      N        W     632      43     10
## -Alvin Davis        224  266    263      A        W     880      82     14
## -Andre Dawson       828  838    354      N        E     200      11      3
## -Andres Galarraga    48   46     33      N        E     805      40      4
## -Alfredo Griffin    501  336    194      A        W     282     421     25
##                   Salary NewLeague
## -Andy Allanson        NA         A
## -Alan Ashby        475.0         N
## -Alvin Davis       480.0         A
## -Andre Dawson      500.0         N
## -Andres Galarraga   91.5         N
## -Alfredo Griffin   750.0         A</code></pre>
<div class="sourceCode" id="cb10"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb10-1" data-line-number="1">fit1&lt;-<span class="kw">tree</span>(Salary <span class="op">~</span><span class="st"> </span>Years<span class="op">+</span>Hits, <span class="dt">data=</span>Hitters)</a>
<a class="sourceLine" id="cb10-2" data-line-number="2">fit1</a></code></pre></div>
<pre><code>## node), split, n, deviance, yval
##       * denotes terminal node
## 
##  1) root 263 53320000  535.9  
##    2) Years &lt; 4.5 90  6769000  225.8  
##      4) Hits &lt; 39.5 5  3131000  676.5 *
##      5) Hits &gt; 39.5 85  2564000  199.3  
##       10) Years &lt; 3.5 58   309400  138.8 *
##       11) Years &gt; 3.5 27  1586000  329.3 *
##    3) Years &gt; 4.5 173 33390000  697.2  
##      6) Hits &lt; 117.5 90  5312000  464.9  
##       12) Years &lt; 6.5 26   644100  334.7 *
##       13) Years &gt; 6.5 64  4048000  517.8 *
##      7) Hits &gt; 117.5 83 17960000  949.2  
##       14) Hits &lt; 185 76 13290000  914.3  
##         28) Years &lt; 5.5 8    82790  622.5 *
##         29) Years &gt; 5.5 68 12450000  948.7 *
##       15) Hits &gt; 185 7  3571000 1328.0 *</code></pre>
<div class="sourceCode" id="cb12"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb12-1" data-line-number="1"><span class="kw">plot</span>(fit1); <span class="kw">text</span>(fit1, <span class="dt">pretty=</span><span class="dv">1</span>)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/RandomForests_files/figure-html/unnamed-chunk-4-1.png" /><!-- --></p>
<p>The cut points are decided by minimizing the residual sums of squares for a particular region. So we identify regions of the predictor space, <span class="math inline">\(R_1, R_2, \dots, R_j\)</span> so that</p>
<p><span class="math display">\[\sum_j \sum_{\in R_j} \left ( y_i - \hat{y_{R_j}} \right )^2\]</span> where <span class="math inline">\(\hat{y_{R_j}}\)</span> is the mean for a particular region <em>j</em>.</p>
<p>Often this process may over-fit the data, meaning it creates too complicated of a tree (too many terminal nodes). It’s possible to <em>prune</em> the tree to arrive at a simpler tree split that may be easier to interpret.</p>
<p>We can tune the tree depth parameter by cross-validation of the data, across different tree depths. In this case a depth of 3 is optimal.</p>
<div class="sourceCode" id="cb13"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb13-1" data-line-number="1">cvt&lt;-<span class="kw">cv.tree</span>(fit1)</a>
<a class="sourceLine" id="cb13-2" data-line-number="2"><span class="kw">plot</span>(cvt<span class="op">$</span>size, cvt<span class="op">$</span>dev, <span class="dt">type=</span><span class="st">&quot;b&quot;</span>)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/RandomForests_files/figure-html/unnamed-chunk-5-1.png" /><!-- --></p>
<p>Then, we can prune the tree, to basically get the tree version of the figure from above</p>
<div class="sourceCode" id="cb14"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb14-1" data-line-number="1">tree2&lt;-<span class="kw">prune.tree</span>(fit1, <span class="dt">best=</span><span class="dv">3</span>)</a>
<a class="sourceLine" id="cb14-2" data-line-number="2"><span class="kw">plot</span>(tree2); <span class="kw">text</span>(tree2, <span class="dt">pretty=</span><span class="dv">1</span>)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/RandomForests_files/figure-html/unnamed-chunk-6-1.png" /><!-- --></p>
<div class="sourceCode" id="cb15"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb15-1" data-line-number="1"><span class="co"># plot(x=Hitters$Years, y=Hitters$Hits)</span></a>
<a class="sourceLine" id="cb15-2" data-line-number="2"><span class="co"># abline(v=4.5,  col=3, lwd=3)</span></a>
<a class="sourceLine" id="cb15-3" data-line-number="3"><span class="co"># abline(h=117.5, col=4, lwd=3)</span></a></code></pre></div>
<p>Prediction works by assigning the mean value from a region to an observation who matches the decision rule. For example, let’s make up a player who has 6 years experience and 200 hits</p>
<div class="sourceCode" id="cb16"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb16-1" data-line-number="1">new&lt;-<span class="kw">data.frame</span>(<span class="dt">Hits=</span><span class="dv">200</span>, <span class="dt">Years=</span><span class="dv">6</span>)</a>
<a class="sourceLine" id="cb16-2" data-line-number="2"></a>
<a class="sourceLine" id="cb16-3" data-line-number="3">pred&lt;-<span class="kw">predict</span>(fit1, <span class="dt">newdata =</span> new)</a>
<a class="sourceLine" id="cb16-4" data-line-number="4">pred</a></code></pre></div>
<pre><code>##      1 
## 1327.5</code></pre>
<div id="classification-trees" class="section level3">
<h3>Classification trees</h3>
<p>If our outcome is categorical, or binary, the tree will be a <em>classification tree</em>. Instead of the mean of a particular value being predicted, the classification tree predicts the value of the <em>most common class</em> at a particular terminal node. So in addition to the tree predicting the class at each node, it also gives the class proportions at each node. The <em>classification error rate</em> is the percent of of observations at a node that do not belong to the most common class.</p>
<p><span class="math display">\[Error = 1- max (\hat p_{mk})\]</span> This is not a good method for growing trees, and instead either the Gini index or the entropy is measured at each node:</p>
<p><span class="math display">\[Gini = \sum_k \hat p_{mk}(1-\hat p_{mk})\]</span> The Gini index is used as a measure of <em>node purity</em>, if a node only contains 1 class, it is considered <em>pure</em></p>
<p><span class="math display">\[Entropy = D = - \sum_k \hat p_{mk} \text{log} \hat p_{mk}\]</span></p>
</div>
<div id="bagging-and-random-forests" class="section level3">
<h3>Bagging and Random Forests</h3>
<p>The example above is a single “tree”, if we did this type of analysis a large number of times, then we would end up with a <em>forest</em> of such trees.</p>
<p><strong>Bagging</strong> is short for <em>bootstrap aggragation</em>. This is a general purpose procedure for reducing the variance in a statistical test, but it is also commonly used in regression tree contexts. How this works in this setting is the data are bootstrapped into a large number of training sets, each of the same size. The regression tree is fit to each of these large number of trees and not pruned. By averaging these bootstrapped trees, the accuracy is actually higher than for a single tree alone.</p>
<p><strong>Random forests</strong> not only bag the trees, but at each iteration a different set of predictors is chosen from the data, so not only do we arrive at a more accurate bagged tree, but we can also get an idea of how important any particular variable is, based on its averaged Gini impurity across all the trees considered.</p>
<pre><code>## Warning: Number of logged events: 651</code></pre>
</div>
<div id="simple-example-using-prb-data---regression-tree" class="section level3">
<h3>simple example using PRB data - Regression tree</h3>
<div class="sourceCode" id="cb19"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb19-1" data-line-number="1"><span class="co">#set up training set identifier</span></a>
<a class="sourceLine" id="cb19-2" data-line-number="2">train1&lt;-<span class="kw">sample</span>(<span class="dv">1</span><span class="op">:</span><span class="kw">dim</span>(prb2)[<span class="dv">1</span>], <span class="dt">size =</span> <span class="fl">.75</span><span class="op">*</span><span class="kw">dim</span>(prb2)[<span class="dv">1</span>], <span class="dt">replace=</span>T)</a>
<a class="sourceLine" id="cb19-3" data-line-number="3"></a>
<a class="sourceLine" id="cb19-4" data-line-number="4">fit&lt;-<span class="kw">tree</span>(e0total<span class="op">~</span>., <span class="dt">data=</span>prb2[train1,])</a>
<a class="sourceLine" id="cb19-5" data-line-number="5"><span class="kw">summary</span>(fit)</a></code></pre></div>
<pre><code>## 
## Regression tree:
## tree(formula = e0total ~ ., data = prb2[train1, ])
## Variables actually used in tree construction:
## [1] &quot;imr&quot; &quot;cdr&quot;
## Number of terminal nodes:  7 
## Residual mean deviance:  6.536 = 973.9 / 149 
## Distribution of residuals:
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##  -7.783  -1.300   0.000   0.000   1.750   8.000</code></pre>
<div class="sourceCode" id="cb21"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb21-1" data-line-number="1"><span class="kw">plot</span>(fit); <span class="kw">text</span>(fit, <span class="dt">pretty=</span><span class="dv">1</span>)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/RandomForests_files/figure-html/unnamed-chunk-10-1.png" /><!-- --></p>
<div class="sourceCode" id="cb22"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb22-1" data-line-number="1">cv.fit&lt;-<span class="kw">cv.tree</span>(fit)</a>
<a class="sourceLine" id="cb22-2" data-line-number="2"><span class="kw">plot</span>(cv.fit<span class="op">$</span>size, cv.fit<span class="op">$</span>dev, <span class="dt">type=</span><span class="st">&quot;b&quot;</span>)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/RandomForests_files/figure-html/unnamed-chunk-10-2.png" /><!-- --></p>
<div class="sourceCode" id="cb23"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb23-1" data-line-number="1">pt1&lt;-<span class="kw">prune.tree</span>(fit, <span class="dt">best=</span><span class="dv">7</span>)</a>
<a class="sourceLine" id="cb23-2" data-line-number="2"><span class="kw">plot</span>(pt1); <span class="kw">text</span>(pt1, <span class="dt">pretty=</span><span class="dv">1</span>)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/RandomForests_files/figure-html/unnamed-chunk-10-3.png" /><!-- --></p>
</div>
<div id="bagged-regression-tree-from-prb-data---100-trees---3-variables-each" class="section level3">
<h3>Bagged regression tree from PRB data - 100 trees - 3 variables each</h3>
<div class="sourceCode" id="cb24"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb24-1" data-line-number="1"><span class="kw">library</span>(randomForest)</a></code></pre></div>
<pre><code>## randomForest 4.6-14</code></pre>
<pre><code>## Type rfNews() to see new features/changes/bug fixes.</code></pre>
<pre><code>## 
## Attaching package: &#39;randomForest&#39;</code></pre>
<pre><code>## The following object is masked from &#39;package:dplyr&#39;:
## 
##     combine</code></pre>
<div class="sourceCode" id="cb29"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb29-1" data-line-number="1"><span class="kw">set.seed</span>(<span class="dv">1115</span>)</a>
<a class="sourceLine" id="cb29-2" data-line-number="2">t1&lt;-<span class="kw">tuneRF</span>(<span class="dt">y=</span>prb2<span class="op">$</span>e0total, <span class="dt">x=</span>prb2[,<span class="kw">c</span>(<span class="op">-</span><span class="dv">12</span>,<span class="op">-</span><span class="dv">23</span>)], <span class="dt">trace=</span>T, <span class="dt">stepFactor =</span> <span class="dv">2</span>, <span class="dt">ntreeTry =</span> <span class="dv">1000</span>, <span class="dt">plot=</span>T)</a></code></pre></div>
<pre><code>## mtry = 7  OOB error = 7.802162 
## Searching left ...
## mtry = 4     OOB error = 9.75998 
## -0.2509328 0.05 
## Searching right ...
## mtry = 14    OOB error = 6.484249 
## 0.1689164 0.05 
## mtry = 21    OOB error = 5.758013 
## 0.112 0.05</code></pre>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/RandomForests_files/figure-html/unnamed-chunk-11-1.png" /><!-- --></p>
<div class="sourceCode" id="cb31"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb31-1" data-line-number="1">t1 <span class="co">#gewt mtry</span></a></code></pre></div>
<pre><code>##    mtry OOBError
## 4     4 9.759980
## 7     7 7.802162
## 14   14 6.484249
## 21   21 5.758013</code></pre>
<div class="sourceCode" id="cb33"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb33-1" data-line-number="1">bag<span class="fl">.1</span>&lt;-<span class="kw">randomForest</span>(e0total<span class="op">~</span>., <span class="dt">data=</span>prb2[train1,<span class="op">-</span><span class="dv">23</span>], <span class="dt">mtry=</span><span class="dv">14</span>, <span class="dt">ntree=</span><span class="dv">100</span>,<span class="dt">importance=</span>T) <span class="co">#mtry = 3; choose 3 variables for each tree</span></a>
<a class="sourceLine" id="cb33-2" data-line-number="2">bag<span class="fl">.1</span></a></code></pre></div>
<pre><code>## 
## Call:
##  randomForest(formula = e0total ~ ., data = prb2[train1, -23],      mtry = 14, ntree = 100, importance = T) 
##                Type of random forest: regression
##                      Number of trees: 100
## No. of variables tried at each split: 14
## 
##           Mean of squared residuals: 5.190271
##                     % Var explained: 95.89</code></pre>
<div class="sourceCode" id="cb35"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb35-1" data-line-number="1"><span class="kw">plot</span>(bag<span class="fl">.1</span>)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/RandomForests_files/figure-html/unnamed-chunk-11-2.png" /><!-- --></p>
<div class="sourceCode" id="cb36"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb36-1" data-line-number="1"><span class="kw">importance</span>(bag<span class="fl">.1</span>)</a></code></pre></div>
<pre><code>##                                     %IncMSE IncNodePurity
## continent                          3.848523     405.32174
## population.                        2.447108      47.23318
## cbr                                2.757050     444.96134
## cdr                               12.544770    2713.17272
## rate.of.natural.increase           2.419710     156.97416
## net.migration.rate                 1.669214      51.52248
## imr                               16.149632   11064.00964
## womandlifetimeriskmaternaldeath    5.211886    2608.32904
## tfr                                2.133800     179.19983
## percpoplt15                        2.913135     486.07099
## percpopgt65                        4.272992      65.84541
## percurban                          1.708796      32.93916
## percpopinurbangt750k               2.316377      43.63507
## percpop1549hivaids2007             4.479647     493.30197
## percmarwomcontraall                3.889310      69.80177
## percmarwomcontramodern             2.990570      51.66386
## percppundernourished0204           3.274408      42.50857
## motorvehper1000pop0005             2.482953      31.30377
## percpopwaccessimprovedwatersource  3.444429      35.62867
## gnippppercapitausdollars           7.252401     532.61671
## popdenspersqkm                     2.862772      64.64063</code></pre>
<div class="sourceCode" id="cb38"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb38-1" data-line-number="1"><span class="kw">varImpPlot</span>(bag<span class="fl">.1</span>, <span class="dt">n.var =</span> <span class="dv">10</span>, <span class="dt">type=</span><span class="dv">1</span>)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/RandomForests_files/figure-html/unnamed-chunk-11-3.png" /><!-- --></p>
</div>
<div id="classification-tree-for-life-expectancy---low-or-high" class="section level3">
<h3>Classification tree for life expectancy - low or high</h3>
<div class="sourceCode" id="cb39"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb39-1" data-line-number="1">prb2<span class="op">$</span>lowe0&lt;-<span class="kw">as.factor</span>(<span class="kw">ifelse</span>(prb2<span class="op">$</span>e0total<span class="op">&lt;</span><span class="kw">median</span>(prb2<span class="op">$</span>e0total), <span class="st">&quot;low&quot;</span>, <span class="st">&quot;high&quot;</span>))</a>
<a class="sourceLine" id="cb39-2" data-line-number="2"></a>
<a class="sourceLine" id="cb39-3" data-line-number="3">fit&lt;-<span class="kw">tree</span>(lowe0<span class="op">~</span>., <span class="dt">data=</span>prb2[train1,<span class="op">-</span><span class="dv">12</span>])</a>
<a class="sourceLine" id="cb39-4" data-line-number="4"><span class="co">#fit</span></a>
<a class="sourceLine" id="cb39-5" data-line-number="5"><span class="kw">plot</span>(fit); <span class="kw">text</span>(fit, <span class="dt">pretty=</span><span class="dv">1</span>)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/RandomForests_files/figure-html/unnamed-chunk-12-1.png" /><!-- --></p>
<div class="sourceCode" id="cb40"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb40-1" data-line-number="1">cv.fit&lt;-<span class="kw">cv.tree</span>(fit)</a>
<a class="sourceLine" id="cb40-2" data-line-number="2">cv.fit</a></code></pre></div>
<pre><code>## $size
## [1] 6 5 4 3 2 1
## 
## $dev
## [1] 141.5133 126.0102 117.9069  92.0370 113.7485 220.3056
## 
## $k
## [1]      -Inf  10.52553  10.71965  14.55516  28.17398 136.27366
## 
## $method
## [1] &quot;deviance&quot;
## 
## attr(,&quot;class&quot;)
## [1] &quot;prune&quot;         &quot;tree.sequence&quot;</code></pre>
<div class="sourceCode" id="cb42"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb42-1" data-line-number="1"><span class="kw">plot</span>(cv.fit<span class="op">$</span>size, cv.fit<span class="op">$</span>dev, <span class="dt">type=</span><span class="st">&quot;b&quot;</span>)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/RandomForests_files/figure-html/unnamed-chunk-12-2.png" /><!-- --></p>
<div class="sourceCode" id="cb43"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb43-1" data-line-number="1">pt1&lt;-<span class="kw">prune.tree</span>(fit, <span class="dt">best=</span>cv.fit<span class="op">$</span>size[<span class="kw">which.min</span>(cv.fit<span class="op">$</span>dev)])</a>
<a class="sourceLine" id="cb43-2" data-line-number="2"><span class="kw">plot</span>(pt1); <span class="kw">text</span>(pt1, <span class="dt">pretty=</span><span class="dv">1</span>)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/RandomForests_files/figure-html/unnamed-chunk-12-3.png" /><!-- --></p>
</div>
<div id="random-forest-tree-for-prb-low-life-expectancy" class="section level3">
<h3>Random forest tree for PRB low life expectancy</h3>
<div class="sourceCode" id="cb44"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb44-1" data-line-number="1"><span class="co">#Tune to find best number of variables to try</span></a>
<a class="sourceLine" id="cb44-2" data-line-number="2">t1&lt;-<span class="kw">tuneRF</span>(<span class="dt">y=</span>prb2<span class="op">$</span>e0total, <span class="dt">x=</span>prb2[,<span class="kw">c</span>(<span class="op">-</span><span class="dv">12</span>,<span class="op">-</span><span class="dv">23</span>)], <span class="dt">trace=</span>T, <span class="dt">stepFactor =</span> <span class="dv">2</span>, <span class="dt">ntreeTry =</span> <span class="dv">1000</span>, <span class="dt">plot=</span>T)</a></code></pre></div>
<pre><code>## mtry = 7  OOB error = 7.789191 
## Searching left ...
## mtry = 4     OOB error = 9.541584 
## -0.2249776 0.05 
## Searching right ...
## mtry = 14    OOB error = 6.142687 
## 0.2113831 0.05 
## mtry = 21    OOB error = 5.816884 
## 0.05303921 0.05</code></pre>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/RandomForests_files/figure-html/unnamed-chunk-13-1.png" /><!-- --></p>
<div class="sourceCode" id="cb46"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb46-1" data-line-number="1">t1</a></code></pre></div>
<pre><code>##    mtry OOBError
## 4     4 9.541584
## 7     7 7.789191
## 14   14 6.142687
## 21   21 5.816884</code></pre>
<div class="sourceCode" id="cb48"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb48-1" data-line-number="1">y&lt;-prb2<span class="op">$</span>e0total[train1]</a>
<a class="sourceLine" id="cb48-2" data-line-number="2">bag<span class="fl">.2</span>&lt;-<span class="kw">randomForest</span>(<span class="dt">y=</span>y,<span class="dt">x=</span>prb2[train1,<span class="kw">c</span>(<span class="op">-</span><span class="dv">12</span>,<span class="op">-</span><span class="dv">23</span>)],  <span class="dt">mtry=</span><span class="dv">14</span>, <span class="dt">ntree=</span><span class="dv">500</span>,<span class="dt">importance=</span>T)</a>
<a class="sourceLine" id="cb48-3" data-line-number="3">bag<span class="fl">.2</span></a></code></pre></div>
<pre><code>## 
## Call:
##  randomForest(x = prb2[train1, c(-12, -23)], y = y, ntree = 500,      mtry = 14, importance = T) 
##                Type of random forest: regression
##                      Number of trees: 500
## No. of variables tried at each split: 14
## 
##           Mean of squared residuals: 5.127008
##                     % Var explained: 95.94</code></pre>
<div class="sourceCode" id="cb50"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb50-1" data-line-number="1"><span class="kw">plot</span>(bag<span class="fl">.2</span>)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/RandomForests_files/figure-html/unnamed-chunk-13-2.png" /><!-- --></p>
<div class="sourceCode" id="cb51"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb51-1" data-line-number="1"><span class="kw">importance</span>(bag<span class="fl">.2</span>,<span class="dt">scale =</span> T )</a></code></pre></div>
<pre><code>##                                     %IncMSE IncNodePurity
## continent                          8.113739     315.13346
## population.                        3.491805      47.75376
## cbr                                4.170075     237.33712
## cdr                               30.801400    2809.97899
## rate.of.natural.increase           6.466801     128.37004
## net.migration.rate                 3.123241      41.33661
## imr                               33.424467   10939.33769
## womandlifetimeriskmaternaldeath   11.242490    2433.13138
## tfr                                3.817190     126.53284
## percpoplt15                        6.053018     651.77864
## percpopgt65                        8.185953      79.82232
## percurban                          5.381906      35.32764
## percpopinurbangt750k               3.061482      48.14325
## percpop1549hivaids2007             9.939650     421.01757
## percmarwomcontraall                6.826742      58.17955
## percmarwomcontramodern             7.054051      51.61753
## percppundernourished0204           3.312644      47.19299
## motorvehper1000pop0005             3.973039      54.94647
## percpopwaccessimprovedwatersource  4.080611      82.97701
## gnippppercapitausdollars          15.504913     674.79257
## popdenspersqkm                     4.588320      58.80380</code></pre>
<div class="sourceCode" id="cb53"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb53-1" data-line-number="1"><span class="kw">varImpPlot</span>(bag<span class="fl">.2</span>, <span class="dt">n.var =</span> <span class="dv">10</span>, <span class="dt">type=</span><span class="dv">2</span>)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/RandomForests_files/figure-html/unnamed-chunk-13-3.png" /><!-- --></p>
<div class="sourceCode" id="cb54"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb54-1" data-line-number="1">pred&lt;-<span class="kw">predict</span>(bag<span class="fl">.2</span>, <span class="dt">newdata=</span>prb2[<span class="op">-</span>train1,])</a>
<a class="sourceLine" id="cb54-2" data-line-number="2"><span class="kw">table</span>(pred, prb2[<span class="op">-</span>train1, <span class="st">&quot;lowe0&quot;</span>])</a></code></pre></div>
<pre><code>##                   
## pred               high low
##   43.9116333333333    0   1
##   46.2554             0   1
##   46.6971666666667    0   1
##   46.8996666666667    0   1
##   48.5067             0   1
##   49.0209666666666    0   1
##   49.2719333333333    0   1
##   49.63               0   1
##   49.8501             0   1
##   49.9107666666667    0   1
##   50.2087666666666    0   1
##   50.3383             0   1
##   50.4601333333333    0   1
##   50.479              0   1
##   52.2138             0   1
##   52.9432333333333    0   1
##   52.9651             0   1
##   57.493              0   1
##   58.0111333333333    0   1
##   58.9252666666667    0   1
##   59.0438666666667    0   1
##   59.0600333333333    0   1
##   60.1836333333333    0   1
##   60.5708666666667    0   1
##   61.0191333333333    0   1
##   61.5247333333333    0   1
##   62.9420666666667    0   1
##   63.4492666666667    0   1
##   63.4620333333333    0   1
##   63.5176333333333    0   1
##   64.1848             0   1
##   64.6063666666666    0   1
##   66.7599             0   1
##   67.3326666666666    0   1
##   68.4877666666667    0   1
##   69.2124             0   1
##   69.5067666666667    1   0
##   69.8787             0   1
##   70.2796666666667    0   1
##   70.5176             0   1
##   70.5517333333333    1   0
##   70.7125             1   0
##   70.805              0   1
##   71.2856333333334    1   0
##   71.3414333333333    1   0
##   71.3880666666667    1   0
##   71.6605333333333    1   0
##   71.7564333333333    1   0
##   71.7668666666667    0   1
##   71.8388             1   0
##   71.8765             0   1
##   72.1625666666667    1   0
##   72.1743             1   0
##   72.2219             1   0
##   72.2382666666667    1   0
##   72.303              1   0
##   72.3485             0   1
##   72.4083666666667    0   1
##   72.6019             1   0
##   72.6527             1   0
##   72.8502             1   0
##   73.0262             0   1
##   73.0343333333333    1   0
##   73.0405666666667    1   0
##   73.0917666666667    1   0
##   73.1843             1   0
##   73.2967666666667    0   1
##   73.3403             1   0
##   73.3828333333333    1   0
##   73.4574666666667    0   1
##   73.6294             1   0
##   74.0595666666666    1   0
##   74.1802666666667    1   0
##   74.4077             1   0
##   74.4351333333333    1   0
##   74.5592666666666    1   0
##   74.8154333333333    1   0
##   74.8921             1   0
##   74.9333666666667    1   0
##   75.0284333333334    1   0
##   75.111              1   0
##   76.0959333333334    1   0
##   76.1659333333333    1   0
##   76.4618333333333    1   0
##   76.5154             1   0
##   77.3296666666667    1   0
##   78.1521666666667    1   0
##   78.5404             1   0
##   78.6370333333334    1   0
##   78.7316             1   0
##   78.8905             1   0
##   78.972              1   0
##   79.1079333333333    1   0
##   79.3042333333334    1   0
##   79.3422             1   0
##   79.3878333333333    1   0
##   79.5871333333333    1   0
##   79.7036666666667    1   0
##   80.1361666666667    1   0</code></pre>
<div class="sourceCode" id="cb56"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb56-1" data-line-number="1"><span class="kw">mean</span>(pred<span class="op">==</span>prb2[<span class="op">-</span>train1, <span class="st">&quot;lowe0&quot;</span>]) <span class="co">#accuracy</span></a></code></pre></div>
<pre><code>## [1] 0</code></pre>
</div>
</div>
<div id="more-complicated-example" class="section level2">
<h2>More complicated example</h2>
<div id="using-caret-to-create-training-and-test-sets." class="section level3">
<h3>using caret to create training and test sets.</h3>
<p>We use an 80% training fraction</p>
<div class="sourceCode" id="cb58"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb58-1" data-line-number="1"><span class="kw">library</span>(caret)</a></code></pre></div>
<pre><code>## Loading required package: lattice</code></pre>
<pre><code>## Loading required package: ggplot2</code></pre>
<pre><code>## 
## Attaching package: &#39;ggplot2&#39;</code></pre>
<pre><code>## The following object is masked from &#39;package:randomForest&#39;:
## 
##     margin</code></pre>
<div class="sourceCode" id="cb63"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb63-1" data-line-number="1"><span class="kw">set.seed</span>(<span class="dv">1115</span>)</a>
<a class="sourceLine" id="cb63-2" data-line-number="2">train&lt;-<span class="st"> </span><span class="kw">createDataPartition</span>(<span class="dt">y =</span> model.dat2<span class="op">$</span>modcontra , <span class="dt">p =</span> <span class="fl">.80</span>, <span class="dt">list=</span>F)</a>
<a class="sourceLine" id="cb63-3" data-line-number="3"></a>
<a class="sourceLine" id="cb63-4" data-line-number="4">dtrain&lt;-model.dat2[train,]</a></code></pre></div>
<pre><code>## Warning: The `i` argument of ``[`()` can&#39;t be a matrix as of tibble 3.0.0.
## Convert to a vector.
## This warning is displayed once every 8 hours.
## Call `lifecycle::last_warnings()` to see where this warning was generated.</code></pre>
<div class="sourceCode" id="cb65"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb65-1" data-line-number="1">dtest&lt;-model.dat2[<span class="op">-</span>train,]</a></code></pre></div>
</div>
<div id="create-design-matrix" class="section level3">
<h3>Create design matrix</h3>
<p>If we have a mixture of factor variables and continuous predictors in our analysis, it is best to set up the design matrix for our models before we run them. Many methods within <code>caret</code> won’t use factor variables correctly unless we set up the dummy variable representations first.</p>
<div class="sourceCode" id="cb66"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb66-1" data-line-number="1">y&lt;-dtrain<span class="op">$</span>modcontra</a>
<a class="sourceLine" id="cb66-2" data-line-number="2">y&lt;-<span class="kw">as.factor</span>(<span class="kw">ifelse</span>(y<span class="op">==</span><span class="dv">1</span>, <span class="st">&quot;mod&quot;</span>, <span class="st">&quot;notmod&quot;</span>))</a>
<a class="sourceLine" id="cb66-3" data-line-number="3">x&lt;-<span class="kw">model.matrix</span>(<span class="op">~</span><span class="kw">factor</span>(region)<span class="op">+</span><span class="kw">factor</span>(age)<span class="op">+</span>livchildren<span class="op">+</span><span class="kw">factor</span>(rural)<span class="op">+</span><span class="kw">factor</span>(wantmore)<span class="op">+</span><span class="kw">factor</span>(educ)<span class="op">+</span>partnered<span class="op">+</span><span class="kw">factor</span>(work)<span class="op">+</span><span class="kw">factor</span>(wealth), <span class="dt">data=</span>dtrain)</a>
<a class="sourceLine" id="cb66-4" data-line-number="4">x&lt;-<span class="kw">data.frame</span>(x)[,<span class="op">-</span><span class="dv">1</span>]</a>
<a class="sourceLine" id="cb66-5" data-line-number="5"></a>
<a class="sourceLine" id="cb66-6" data-line-number="6">km1&lt;-<span class="kw">kmeans</span>(x, <span class="dt">centers =</span> <span class="dv">2</span>, <span class="dt">nstart=</span><span class="dv">10</span>)</a>
<a class="sourceLine" id="cb66-7" data-line-number="7">km1</a></code></pre></div>
<pre><code>## K-means clustering with 2 clusters of sizes 1669, 2460
## 
## Cluster means:
##   factor.region.2 factor.region.3 factor.region.4 factor.age..21.8.28.6.
## 1       0.2636309      0.09466747       0.2013182             0.07010186
## 2       0.2524390      0.16829268       0.1959350             0.36951220
##   factor.age..28.6.35.4. factor.age..35.4.42.2. factor.age..42.2.49.
## 1              0.3349311              0.3133613           0.27980827
## 2              0.2317073              0.1317073           0.08658537
##   livchildren factor.rural.1 factor.wantmore.1 factor.educ.1 factor.educ.2
## 1    5.173158      0.7177951         0.2822049     0.1084482    0.06411025
## 2    1.876423      0.6235772         0.7199187     0.1284553    0.17560976
##   factor.educ.3 partnered factor.work.1 factor.wealth.2 factor.wealth.3
## 1    0.01018574 0.2150989     0.8106651       0.2348712       0.2264829
## 2    0.03455285 0.3337398     0.7365854       0.2060976       0.2126016
##   factor.wealth.4 factor.wealth.5
## 1       0.1995207      0.08568005
## 2       0.2008130      0.16138211
## 
## Clustering vector:
##    1    2    3    4    5    6    7    8    9   10   11   12   13   14   15   16 
##    1    2    2    2    1    2    1    2    1    2    1    2    2    2    2    1 
##   17   18   19   20   21   22   23   24   25   26   27   28   29   30   31   32 
##    2    1    2    2    1    1    2    2    2    2    2    2    2    2    2    1 
##   33   34   35   36   37   38   39   40   41   42   43   44   45   46   47   48 
##    1    2    1    2    1    1    1    1    1    1    1    2    1    2    2    2 
##   49   50   51   52   53   54   55   56   57   58   59   60   61   62   63   64 
##    2    1    1    1    2    2    2    1    1    2    2    1    2    1    2    2 
##   65   66   67   68   69   70   71   72   73   74   75   76   77   78   79   80 
##    2    2    2    2    2    2    1    2    2    1    1    1    2    2    1    2 
##   81   82   83   84   85   86   87   88   89   90   91   92   93   94   95   96 
##    2    2    1    2    1    1    2    2    2    1    1    2    1    2    2    1 
##   97   98   99  100  101  102  103  104  105  106  107  108  109  110  111  112 
##    2    1    2    2    2    2    2    1    1    2    2    1    1    2    1    2 
##  113  114  115  116  117  118  119  120  121  122  123  124  125  126  127  128 
##    2    1    2    1    1    1    1    2    2    1    1    2    2    1    2    1 
##  129  130  131  132  133  134  135  136  137  138  139  140  141  142  143  144 
##    2    2    2    2    2    1    1    2    2    2    2    2    2    2    2    1 
##  145  146  147  148  149  150  151  152  153  154  155  156  157  158  159  160 
##    2    2    2    2    1    1    1    1    2    1    1    2    2    2    1    2 
##  161  162  163  164  165  166  167  168  169  170  171  172  173  174  175  176 
##    2    2    2    2    1    1    2    1    2    2    2    2    1    2    1    2 
##  177  178  179  180  181  182  183  184  185  186  187  188  189  190  191  192 
##    1    2    2    1    1    2    2    2    2    2    2    2    2    2    2    1 
##  193  194  195  196  197  198  199  200  201  202  203  204  205  206  207  208 
##    2    2    1    2    1    2    2    2    1    2    2    1    2    2    2    1 
##  209  210  211  212  213  214  215  216  217  218  219  220  221  222  223  224 
##    2    2    2    2    2    1    2    1    2    2    2    2    2    2    2    2 
##  225  226  227  228  229  230  231  232  233  234  235  236  237  238  239  240 
##    2    1    2    1    2    2    2    1    2    2    2    1    2    1    1    1 
##  241  242  243  244  245  246  247  248  249  250  251  252  253  254  255  256 
##    1    2    2    2    2    2    2    2    1    2    1    2    2    2    2    2 
##  257  258  259  260  261  262  263  264  265  266  267  268  269  270  271  272 
##    1    2    2    2    2    1    1    1    2    2    2    2    2    2    2    1 
##  273  274  275  276  277  278  279  280  281  282  283  284  285  286  287  288 
##    2    2    1    1    1    1    2    1    2    1    2    2    2    2    2    1 
##  289  290  291  292  293  294  295  296  297  298  299  300  301  302  303  304 
##    1    1    1    1    2    2    2    1    2    2    2    1    2    2    1    2 
##  305  306  307  308  309  310  311  312  313  314  315  316  317  318  319  320 
##    2    2    2    1    2    1    2    2    1    2    2    2    2    2    2    2 
##  321  322  323  324  325  326  327  328  329  330  331  332  333  334  335  336 
##    1    2    2    2    1    2    2    1    2    1    1    1    2    1    2    2 
##  337  338  339  340  341  342  343  344  345  346  347  348  349  350  351  352 
##    2    1    2    2    1    1    1    1    2    2    1    2    2    1    2    1 
##  353  354  355  356  357  358  359  360  361  362  363  364  365  366  367  368 
##    2    1    1    2    2    2    2    2    2    2    2    1    2    1    2    2 
##  369  370  371  372  373  374  375  376  377  378  379  380  381  382  383  384 
##    1    2    1    2    1    2    2    2    2    2    1    1    2    1    2    1 
##  385  386  387  388  389  390  391  392  393  394  395  396  397  398  399  400 
##    2    1    2    2    2    2    2    1    2    2    1    1    2    2    2    2 
##  401  402  403  404  405  406  407  408  409  410  411  412  413  414  415  416 
##    2    2    2    2    1    1    2    2    2    2    1    1    1    2    1    2 
##  417  418  419  420  421  422  423  424  425  426  427  428  429  430  431  432 
##    2    2    1    1    1    2    2    1    2    1    1    1    2    2    2    1 
##  433  434  435  436  437  438  439  440  441  442  443  444  445  446  447  448 
##    2    1    1    1    2    1    1    1    1    2    2    2    1    2    1    2 
##  449  450  451  452  453  454  455  456  457  458  459  460  461  462  463  464 
##    1    1    2    2    2    2    1    2    1    2    2    1    2    2    2    2 
##  465  466  467  468  469  470  471  472  473  474  475  476  477  478  479  480 
##    2    2    2    2    2    2    1    2    2    1    2    2    2    1    2    2 
##  481  482  483  484  485  486  487  488  489  490  491  492  493  494  495  496 
##    2    1    2    2    2    2    2    1    1    1    2    1    1    2    1    2 
##  497  498  499  500  501  502  503  504  505  506  507  508  509  510  511  512 
##    2    2    2    1    1    2    2    2    1    1    1    2    2    2    1    2 
##  513  514  515  516  517  518  519  520  521  522  523  524  525  526  527  528 
##    2    1    1    2    1    1    1    1    2    1    2    1    1    1    2    1 
##  529  530  531  532  533  534  535  536  537  538  539  540  541  542  543  544 
##    2    2    1    2    2    1    1    2    2    1    1    2    1    2    2    2 
##  545  546  547  548  549  550  551  552  553  554  555  556  557  558  559  560 
##    2    1    1    2    2    2    2    1    1    2    1    2    2    1    2    1 
##  561  562  563  564  565  566  567  568  569  570  571  572  573  574  575  576 
##    2    1    1    2    2    2    1    1    2    2    1    2    2    2    2    2 
##  577  578  579  580  581  582  583  584  585  586  587  588  589  590  591  592 
##    1    2    1    2    2    2    2    2    2    1    1    2    1    2    2    1 
##  593  594  595  596  597  598  599  600  601  602  603  604  605  606  607  608 
##    2    2    2    1    2    2    2    2    2    2    1    2    1    1    2    2 
##  609  610  611  612  613  614  615  616  617  618  619  620  621  622  623  624 
##    1    2    2    2    2    2    2    1    2    2    2    2    1    1    2    2 
##  625  626  627  628  629  630  631  632  633  634  635  636  637  638  639  640 
##    2    1    2    2    2    2    2    1    1    2    2    2    2    1    1    2 
##  641  642  643  644  645  646  647  648  649  650  651  652  653  654  655  656 
##    1    1    2    1    2    1    1    1    2    1    1    1    2    1    2    1 
##  657  658  659  660  661  662  663  664  665  666  667  668  669  670  671  672 
##    2    1    2    1    1    1    1    1    1    2    1    2    2    1    2    2 
##  673  674  675  676  677  678  679  680  681  682  683  684  685  686  687  688 
##    1    1    2    2    1    1    2    2    2    2    2    2    2    1    2    2 
##  689  690  691  692  693  694  695  696  697  698  699  700  701  702  703  704 
##    2    2    2    2    1    2    2    2    2    2    2    2    2    2    2    2 
##  705  706  707  708  709  710  711  712  713  714  715  716  717  718  719  720 
##    2    1    1    2    2    2    2    2    1    2    2    1    2    2    1    2 
##  721  722  723  724  725  726  727  728  729  730  731  732  733  734  735  736 
##    1    1    1    2    1    1    2    1    2    2    2    1    1    2    1    1 
##  737  738  739  740  741  742  743  744  745  746  747  748  749  750  751  752 
##    2    1    2    2    2    2    1    2    2    2    1    1    2    1    2    2 
##  753  754  755  756  757  758  759  760  761  762  763  764  765  766  767  768 
##    2    1    2    2    2    2    2    2    2    2    2    2    2    2    2    1 
##  769  770  771  772  773  774  775  776  777  778  779  780  781  782  783  784 
##    2    2    1    2    1    2    1    2    2    1    1    1    1    2    2    2 
##  785  786  787  788  789  790  791  792  793  794  795  796  797  798  799  800 
##    1    1    2    2    2    2    2    1    2    2    2    2    2    2    2    2 
##  801  802  803  804  805  806  807  808  809  810  811  812  813  814  815  816 
##    1    1    2    2    1    2    2    1    2    2    2    1    2    2    1    2 
##  817  818  819  820  821  822  823  824  825  826  827  828  829  830  831  832 
##    2    2    1    2    2    2    1    2    2    1    2    1    2    2    2    2 
##  833  834  835  836  837  838  839  840  841  842  843  844  845  846  847  848 
##    1    2    2    2    2    1    2    1    1    1    1    1    2    2    2    1 
##  849  850  851  852  853  854  855  856  857  858  859  860  861  862  863  864 
##    2    2    1    2    1    2    2    2    1    2    2    2    2    2    2    2 
##  865  866  867  868  869  870  871  872  873  874  875  876  877  878  879  880 
##    2    2    2    2    2    2    2    1    2    2    1    2    2    2    1    2 
##  881  882  883  884  885  886  887  888  889  890  891  892  893  894  895  896 
##    2    2    2    2    2    2    1    1    1    2    1    2    2    2    2    2 
##  897  898  899  900  901  902  903  904  905  906  907  908  909  910  911  912 
##    1    1    1    2    1    2    2    2    2    1    2    1    2    2    2    2 
##  913  914  915  916  917  918  919  920  921  922  923  924  925  926  927  928 
##    1    1    2    2    1    2    2    1    1    2    1    2    2    2    2    1 
##  929  930  931  932  933  934  935  936  937  938  939  940  941  942  943  944 
##    2    2    2    2    2    2    2    2    2    2    2    2    1    1    2    2 
##  945  946  947  948  949  950  951  952  953  954  955  956  957  958  959  960 
##    2    2    2    2    2    2    2    1    1    2    2    2    2    2    2    1 
##  961  962  963  964  965  966  967  968  969  970  971  972  973  974  975  976 
##    2    2    2    1    1    1    2    1    1    2    1    1    1    2    2    1 
##  977  978  979  980  981  982  983  984  985  986  987  988  989  990  991  992 
##    1    1    2    2    1    2    1    2    2    2    2    1    2    1    2    1 
##  993  994  995  996  997  998  999 1000 1001 1002 1003 1004 1005 1006 1007 1008 
##    2    1    1    1    2    1    2    1    2    2    2    1    2    1    2    1 
## 1009 1010 1011 1012 1013 1014 1015 1016 1017 1018 1019 1020 1021 1022 1023 1024 
##    1    2    1    2    2    1    2    1    2    2    2    1    1    2    2    2 
## 1025 1026 1027 1028 1029 1030 1031 1032 1033 1034 1035 1036 1037 1038 1039 1040 
##    1    2    1    1    2    1    2    2    1    2    2    2    2    2    1    1 
## 1041 1042 1043 1044 1045 1046 1047 1048 1049 1050 1051 1052 1053 1054 1055 1056 
##    1    2    2    2    2    2    2    1    1    2    1    1    1    1    1    2 
## 1057 1058 1059 1060 1061 1062 1063 1064 1065 1066 1067 1068 1069 1070 1071 1072 
##    2    2    1    2    1    1    1    1    2    2    2    1    2    1    2    1 
## 1073 1074 1075 1076 1077 1078 1079 1080 1081 1082 1083 1084 1085 1086 1087 1088 
##    2    2    2    2    2    2    1    2    1    1    1    2    2    1    2    2 
## 1089 1090 1091 1092 1093 1094 1095 1096 1097 1098 1099 1100 1101 1102 1103 1104 
##    2    1    2    2    2    2    1    1    2    1    2    1    1    2    1    1 
## 1105 1106 1107 1108 1109 1110 1111 1112 1113 1114 1115 1116 1117 1118 1119 1120 
##    1    2    2    2    1    1    2    1    1    2    2    2    1    1    2    1 
## 1121 1122 1123 1124 1125 1126 1127 1128 1129 1130 1131 1132 1133 1134 1135 1136 
##    2    2    2    1    2    1    2    2    2    2    1    2    1    1    1    1 
## 1137 1138 1139 1140 1141 1142 1143 1144 1145 1146 1147 1148 1149 1150 1151 1152 
##    1    2    2    2    1    2    2    2    2    2    1    2    2    1    1    1 
## 1153 1154 1155 1156 1157 1158 1159 1160 1161 1162 1163 1164 1165 1166 1167 1168 
##    1    1    1    2    2    2    1    2    1    2    2    1    1    2    2    1 
## 1169 1170 1171 1172 1173 1174 1175 1176 1177 1178 1179 1180 1181 1182 1183 1184 
##    1    2    2    2    1    2    2    2    2    2    2    1    2    2    2    2 
## 1185 1186 1187 1188 1189 1190 1191 1192 1193 1194 1195 1196 1197 1198 1199 1200 
##    1    1    2    2    1    2    1    1    1    2    2    2    1    1    1    1 
## 1201 1202 1203 1204 1205 1206 1207 1208 1209 1210 1211 1212 1213 1214 1215 1216 
##    2    1    2    2    1    1    2    2    2    1    2    2    1    2    2    2 
## 1217 1218 1219 1220 1221 1222 1223 1224 1225 1226 1227 1228 1229 1230 1231 1232 
##    2    2    1    2    1    2    2    1    1    1    2    2    1    1    1    2 
## 1233 1234 1235 1236 1237 1238 1239 1240 1241 1242 1243 1244 1245 1246 1247 1248 
##    1    1    2    2    2    2    1    2    1    1    1    2    2    2    2    1 
## 1249 1250 1251 1252 1253 1254 1255 1256 1257 1258 1259 1260 1261 1262 1263 1264 
##    1    1    2    1    1    2    1    2    2    2    2    2    2    2    2    2 
## 1265 1266 1267 1268 1269 1270 1271 1272 1273 1274 1275 1276 1277 1278 1279 1280 
##    1    1    2    2    2    2    2    2    2    2    2    2    2    1    1    1 
## 1281 1282 1283 1284 1285 1286 1287 1288 1289 1290 1291 1292 1293 1294 1295 1296 
##    1    1    1    2    1    2    1    2    1    2    2    2    1    2    2    1 
## 1297 1298 1299 1300 1301 1302 1303 1304 1305 1306 1307 1308 1309 1310 1311 1312 
##    1    1    2    2    2    2    2    2    2    2    2    1    1    2    2    2 
## 1313 1314 1315 1316 1317 1318 1319 1320 1321 1322 1323 1324 1325 1326 1327 1328 
##    1    2    1    2    2    1    2    2    1    2    2    2    1    1    2    1 
## 1329 1330 1331 1332 1333 1334 1335 1336 1337 1338 1339 1340 1341 1342 1343 1344 
##    2    2    1    2    1    2    2    1    1    1    2    2    2    1    1    2 
## 1345 1346 1347 1348 1349 1350 1351 1352 1353 1354 1355 1356 1357 1358 1359 1360 
##    2    2    2    1    2    2    2    2    1    2    2    1    1    1    2    1 
## 1361 1362 1363 1364 1365 1366 1367 1368 1369 1370 1371 1372 1373 1374 1375 1376 
##    1    2    2    2    1    1    2    2    1    2    2    2    2    2    2    2 
## 1377 1378 1379 1380 1381 1382 1383 1384 1385 1386 1387 1388 1389 1390 1391 1392 
##    2    1    1    2    1    1    2    1    2    1    1    2    2    1    2    1 
## 1393 1394 1395 1396 1397 1398 1399 1400 1401 1402 1403 1404 1405 1406 1407 1408 
##    2    1    2    2    2    2    2    1    2    2    2    1    1    1    2    2 
## 1409 1410 1411 1412 1413 1414 1415 1416 1417 1418 1419 1420 1421 1422 1423 1424 
##    1    1    1    2    2    1    2    1    2    1    2    2    1    2    2    2 
## 1425 1426 1427 1428 1429 1430 1431 1432 1433 1434 1435 1436 1437 1438 1439 1440 
##    1    1    2    2    2    2    1    1    2    2    2    2    2    2    2    2 
## 1441 1442 1443 1444 1445 1446 1447 1448 1449 1450 1451 1452 1453 1454 1455 1456 
##    2    2    2    2    2    2    2    1    1    1    2    1    1    1    1    1 
## 1457 1458 1459 1460 1461 1462 1463 1464 1465 1466 1467 1468 1469 1470 1471 1472 
##    2    2    2    2    1    1    2    2    2    1    2    1    2    2    1    1 
## 1473 1474 1475 1476 1477 1478 1479 1480 1481 1482 1483 1484 1485 1486 1487 1488 
##    2    2    1    2    2    2    2    2    2    1    2    2    2    2    2    2 
## 1489 1490 1491 1492 1493 1494 1495 1496 1497 1498 1499 1500 1501 1502 1503 1504 
##    2    2    2    2    1    2    1    1    2    1    1    2    2    2    1    2 
## 1505 1506 1507 1508 1509 1510 1511 1512 1513 1514 1515 1516 1517 1518 1519 1520 
##    2    2    1    2    1    1    2    2    1    1    2    2    2    2    2    2 
## 1521 1522 1523 1524 1525 1526 1527 1528 1529 1530 1531 1532 1533 1534 1535 1536 
##    2    2    2    1    1    1    1    1    2    1    1    2    2    2    1    2 
## 1537 1538 1539 1540 1541 1542 1543 1544 1545 1546 1547 1548 1549 1550 1551 1552 
##    2    2    1    2    1    2    1    2    2    2    1    1    1    2    2    2 
## 1553 1554 1555 1556 1557 1558 1559 1560 1561 1562 1563 1564 1565 1566 1567 1568 
##    2    2    2    2    1    1    1    1    2    2    2    2    2    2    2    1 
## 1569 1570 1571 1572 1573 1574 1575 1576 1577 1578 1579 1580 1581 1582 1583 1584 
##    1    1    2    1    2    2    1    2    2    2    2    1    2    1    2    2 
## 1585 1586 1587 1588 1589 1590 1591 1592 1593 1594 1595 1596 1597 1598 1599 1600 
##    2    2    1    2    2    2    2    2    2    2    2    1    1    2    2    1 
## 1601 1602 1603 1604 1605 1606 1607 1608 1609 1610 1611 1612 1613 1614 1615 1616 
##    2    2    1    2    2    1    2    2    1    2    2    2    2    1    2    1 
## 1617 1618 1619 1620 1621 1622 1623 1624 1625 1626 1627 1628 1629 1630 1631 1632 
##    2    1    2    2    2    2    2    1    2    2    1    1    2    2    2    2 
## 1633 1634 1635 1636 1637 1638 1639 1640 1641 1642 1643 1644 1645 1646 1647 1648 
##    2    2    2    2    2    1    1    2    2    2    1    2    1    2    1    2 
## 1649 1650 1651 1652 1653 1654 1655 1656 1657 1658 1659 1660 1661 1662 1663 1664 
##    2    1    2    2    2    2    2    2    1    1    2    2    2    2    2    2 
## 1665 1666 1667 1668 1669 1670 1671 1672 1673 1674 1675 1676 1677 1678 1679 1680 
##    2    2    2    1    2    2    1    1    2    1    2    1    1    2    2    1 
## 1681 1682 1683 1684 1685 1686 1687 1688 1689 1690 1691 1692 1693 1694 1695 1696 
##    2    2    1    2    2    1    1    1    2    2    2    2    2    2    1    2 
## 1697 1698 1699 1700 1701 1702 1703 1704 1705 1706 1707 1708 1709 1710 1711 1712 
##    1    2    2    1    1    1    2    1    2    2    2    1    1    1    2    1 
## 1713 1714 1715 1716 1717 1718 1719 1720 1721 1722 1723 1724 1725 1726 1727 1728 
##    2    1    2    2    2    1    1    1    2    2    1    1    1    1    2    1 
## 1729 1730 1731 1732 1733 1734 1735 1736 1737 1738 1739 1740 1741 1742 1743 1744 
##    2    2    2    1    2    1    2    1    1    1    1    2    2    2    2    1 
## 1745 1746 1747 1748 1749 1750 1751 1752 1753 1754 1755 1756 1757 1758 1759 1760 
##    1    2    1    1    1    2    2    2    2    2    1    1    1    2    1    2 
## 1761 1762 1763 1764 1765 1766 1767 1768 1769 1770 1771 1772 1773 1774 1775 1776 
##    2    2    1    1    2    1    1    2    1    2    1    1    2    2    2    2 
## 1777 1778 1779 1780 1781 1782 1783 1784 1785 1786 1787 1788 1789 1790 1791 1792 
##    2    2    2    2    2    2    1    2    1    1    2    2    2    2    1    2 
## 1793 1794 1795 1796 1797 1798 1799 1800 1801 1802 1803 1804 1805 1806 1807 1808 
##    1    1    2    1    1    1    2    2    2    1    1    2    2    1    2    1 
## 1809 1810 1811 1812 1813 1814 1815 1816 1817 1818 1819 1820 1821 1822 1823 1824 
##    1    1    1    2    1    2    2    2    1    2    1    1    1    2    2    1 
## 1825 1826 1827 1828 1829 1830 1831 1832 1833 1834 1835 1836 1837 1838 1839 1840 
##    1    2    2    1    1    2    1    2    2    1    2    2    1    1    1    1 
## 1841 1842 1843 1844 1845 1846 1847 1848 1849 1850 1851 1852 1853 1854 1855 1856 
##    2    1    1    1    1    2    2    2    2    1    1    1    1    1    2    2 
## 1857 1858 1859 1860 1861 1862 1863 1864 1865 1866 1867 1868 1869 1870 1871 1872 
##    2    2    1    1    2    2    1    2    2    2    2    1    1    1    2    2 
## 1873 1874 1875 1876 1877 1878 1879 1880 1881 1882 1883 1884 1885 1886 1887 1888 
##    2    2    2    2    1    2    2    2    2    2    2    2    2    1    2    2 
## 1889 1890 1891 1892 1893 1894 1895 1896 1897 1898 1899 1900 1901 1902 1903 1904 
##    2    2    1    1    1    2    2    2    2    2    2    2    1    2    2    2 
## 1905 1906 1907 1908 1909 1910 1911 1912 1913 1914 1915 1916 1917 1918 1919 1920 
##    1    1    2    1    2    2    1    2    2    1    2    1    2    1    2    2 
## 1921 1922 1923 1924 1925 1926 1927 1928 1929 1930 1931 1932 1933 1934 1935 1936 
##    1    2    1    2    1    1    1    1    2    2    2    1    2    2    2    2 
## 1937 1938 1939 1940 1941 1942 1943 1944 1945 1946 1947 1948 1949 1950 1951 1952 
##    2    1    1    2    2    2    2    2    2    2    2    2    1    1    1    2 
## 1953 1954 1955 1956 1957 1958 1959 1960 1961 1962 1963 1964 1965 1966 1967 1968 
##    1    2    1    2    2    1    1    1    1    2    2    1    2    1    2    1 
## 1969 1970 1971 1972 1973 1974 1975 1976 1977 1978 1979 1980 1981 1982 1983 1984 
##    1    2    2    2    1    2    2    1    2    2    2    1    2    1    1    2 
## 1985 1986 1987 1988 1989 1990 1991 1992 1993 1994 1995 1996 1997 1998 1999 2000 
##    2    2    1    2    2    2    1    1    2    1    2    2    2    2    2    2 
## 2001 2002 2003 2004 2005 2006 2007 2008 2009 2010 2011 2012 2013 2014 2015 2016 
##    2    2    1    2    2    1    2    2    2    2    2    2    2    2    2    1 
## 2017 2018 2019 2020 2021 2022 2023 2024 2025 2026 2027 2028 2029 2030 2031 2032 
##    2    1    1    1    2    1    2    1    2    2    1    2    1    2    2    2 
## 2033 2034 2035 2036 2037 2038 2039 2040 2041 2042 2043 2044 2045 2046 2047 2048 
##    2    1    2    2    2    2    2    1    2    2    1    2    2    2    2    2 
## 2049 2050 2051 2052 2053 2054 2055 2056 2057 2058 2059 2060 2061 2062 2063 2064 
##    1    1    1    1    2    2    1    2    1    2    2    2    1    1    1    1 
## 2065 2066 2067 2068 2069 2070 2071 2072 2073 2074 2075 2076 2077 2078 2079 2080 
##    2    2    1    1    2    2    1    2    2    1    1    2    1    2    2    1 
## 2081 2082 2083 2084 2085 2086 2087 2088 2089 2090 2091 2092 2093 2094 2095 2096 
##    2    1    2    2    1    1    2    1    2    1    1    2    2    1    2    1 
## 2097 2098 2099 2100 2101 2102 2103 2104 2105 2106 2107 2108 2109 2110 2111 2112 
##    2    2    2    2    2    2    1    1    1    2    2    2    2    2    2    1 
## 2113 2114 2115 2116 2117 2118 2119 2120 2121 2122 2123 2124 2125 2126 2127 2128 
##    2    2    1    2    1    2    1    1    2    1    1    2    1    1    2    2 
## 2129 2130 2131 2132 2133 2134 2135 2136 2137 2138 2139 2140 2141 2142 2143 2144 
##    1    1    1    2    2    1    2    2    2    1    1    2    1    1    2    1 
## 2145 2146 2147 2148 2149 2150 2151 2152 2153 2154 2155 2156 2157 2158 2159 2160 
##    2    2    1    2    2    2    2    2    2    2    1    2    1    1    1    1 
## 2161 2162 2163 2164 2165 2166 2167 2168 2169 2170 2171 2172 2173 2174 2175 2176 
##    2    2    2    2    1    1    2    2    2    2    2    1    1    2    2    2 
## 2177 2178 2179 2180 2181 2182 2183 2184 2185 2186 2187 2188 2189 2190 2191 2192 
##    2    1    1    2    1    2    1    2    1    1    1    2    1    1    2    2 
## 2193 2194 2195 2196 2197 2198 2199 2200 2201 2202 2203 2204 2205 2206 2207 2208 
##    2    2    1    1    2    1    1    2    1    2    2    2    1    1    2    1 
## 2209 2210 2211 2212 2213 2214 2215 2216 2217 2218 2219 2220 2221 2222 2223 2224 
##    2    1    2    2    1    1    1    1    2    2    2    1    1    2    1    2 
## 2225 2226 2227 2228 2229 2230 2231 2232 2233 2234 2235 2236 2237 2238 2239 2240 
##    2    1    2    2    2    1    1    1    1    2    2    2    2    2    2    2 
## 2241 2242 2243 2244 2245 2246 2247 2248 2249 2250 2251 2252 2253 2254 2255 2256 
##    2    1    1    1    2    2    2    1    1    2    2    1    1    1    1    2 
## 2257 2258 2259 2260 2261 2262 2263 2264 2265 2266 2267 2268 2269 2270 2271 2272 
##    2    1    2    1    2    1    2    2    2    1    1    1    1    2    1    2 
## 2273 2274 2275 2276 2277 2278 2279 2280 2281 2282 2283 2284 2285 2286 2287 2288 
##    1    1    2    1    1    1    2    1    2    2    1    2    1    2    1    2 
## 2289 2290 2291 2292 2293 2294 2295 2296 2297 2298 2299 2300 2301 2302 2303 2304 
##    2    2    1    2    2    2    2    2    2    2    2    1    2    1    1    2 
## 2305 2306 2307 2308 2309 2310 2311 2312 2313 2314 2315 2316 2317 2318 2319 2320 
##    2    1    2    2    2    2    1    1    1    1    1    1    2    2    2    1 
## 2321 2322 2323 2324 2325 2326 2327 2328 2329 2330 2331 2332 2333 2334 2335 2336 
##    2    2    1    2    2    2    2    1    2    2    2    2    2    2    2    1 
## 2337 2338 2339 2340 2341 2342 2343 2344 2345 2346 2347 2348 2349 2350 2351 2352 
##    2    1    2    2    2    1    2    1    1    2    2    2    2    1    2    2 
## 2353 2354 2355 2356 2357 2358 2359 2360 2361 2362 2363 2364 2365 2366 2367 2368 
##    1    2    2    1    2    2    2    2    1    1    1    1    1    1    1    1 
## 2369 2370 2371 2372 2373 2374 2375 2376 2377 2378 2379 2380 2381 2382 2383 2384 
##    1    1    1    1    1    2    1    2    2    2    1    1    2    2    2    1 
## 2385 2386 2387 2388 2389 2390 2391 2392 2393 2394 2395 2396 2397 2398 2399 2400 
##    2    2    1    1    1    1    1    2    1    2    2    2    2    2    1    2 
## 2401 2402 2403 2404 2405 2406 2407 2408 2409 2410 2411 2412 2413 2414 2415 2416 
##    2    1    2    2    2    2    2    1    2    1    1    2    2    1    2    1 
## 2417 2418 2419 2420 2421 2422 2423 2424 2425 2426 2427 2428 2429 2430 2431 2432 
##    1    1    1    1    2    1    1    1    2    2    2    1    2    1    1    1 
## 2433 2434 2435 2436 2437 2438 2439 2440 2441 2442 2443 2444 2445 2446 2447 2448 
##    2    2    2    1    1    1    2    1    1    1    2    2    2    1    1    2 
## 2449 2450 2451 2452 2453 2454 2455 2456 2457 2458 2459 2460 2461 2462 2463 2464 
##    2    2    2    2    1    1    2    2    2    1    2    2    2    2    2    2 
## 2465 2466 2467 2468 2469 2470 2471 2472 2473 2474 2475 2476 2477 2478 2479 2480 
##    1    2    2    2    2    1    1    1    2    2    1    1    1    2    1    1 
## 2481 2482 2483 2484 2485 2486 2487 2488 2489 2490 2491 2492 2493 2494 2495 2496 
##    2    2    2    1    2    2    2    2    2    2    2    2    1    2    1    1 
## 2497 2498 2499 2500 2501 2502 2503 2504 2505 2506 2507 2508 2509 2510 2511 2512 
##    1    1    2    2    2    2    2    2    2    2    1    1    2    1    1    2 
## 2513 2514 2515 2516 2517 2518 2519 2520 2521 2522 2523 2524 2525 2526 2527 2528 
##    2    2    2    1    2    2    2    1    2    2    2    2    1    2    2    1 
## 2529 2530 2531 2532 2533 2534 2535 2536 2537 2538 2539 2540 2541 2542 2543 2544 
##    1    2    1    1    1    1    1    2    2    2    2    1    1    1    2    1 
## 2545 2546 2547 2548 2549 2550 2551 2552 2553 2554 2555 2556 2557 2558 2559 2560 
##    2    2    1    2    1    1    2    1    2    2    1    2    2    2    2    1 
## 2561 2562 2563 2564 2565 2566 2567 2568 2569 2570 2571 2572 2573 2574 2575 2576 
##    2    1    2    1    1    2    1    1    2    2    2    1    2    2    1    2 
## 2577 2578 2579 2580 2581 2582 2583 2584 2585 2586 2587 2588 2589 2590 2591 2592 
##    1    1    1    2    2    2    2    1    2    2    1    2    2    1    1    1 
## 2593 2594 2595 2596 2597 2598 2599 2600 2601 2602 2603 2604 2605 2606 2607 2608 
##    2    1    2    2    1    2    2    2    2    2    2    2    2    1    1    2 
## 2609 2610 2611 2612 2613 2614 2615 2616 2617 2618 2619 2620 2621 2622 2623 2624 
##    1    2    2    2    1    1    2    2    1    2    1    1    1    1    1    2 
## 2625 2626 2627 2628 2629 2630 2631 2632 2633 2634 2635 2636 2637 2638 2639 2640 
##    2    2    1    1    2    2    1    2    2    2    2    2    1    2    1    2 
## 2641 2642 2643 2644 2645 2646 2647 2648 2649 2650 2651 2652 2653 2654 2655 2656 
##    2    2    2    2    1    1    1    2    2    2    2    1    2    2    1    1 
## 2657 2658 2659 2660 2661 2662 2663 2664 2665 2666 2667 2668 2669 2670 2671 2672 
##    2    2    1    1    2    2    2    2    2    2    1    1    1    1    1    2 
## 2673 2674 2675 2676 2677 2678 2679 2680 2681 2682 2683 2684 2685 2686 2687 2688 
##    2    2    2    2    2    2    1    2    2    1    2    2    2    2    2    2 
## 2689 2690 2691 2692 2693 2694 2695 2696 2697 2698 2699 2700 2701 2702 2703 2704 
##    2    1    1    2    2    2    2    2    1    1    2    1    1    1    2    1 
## 2705 2706 2707 2708 2709 2710 2711 2712 2713 2714 2715 2716 2717 2718 2719 2720 
##    2    1    2    2    1    2    2    1    2    2    2    2    2    1    2    2 
## 2721 2722 2723 2724 2725 2726 2727 2728 2729 2730 2731 2732 2733 2734 2735 2736 
##    1    1    1    1    1    1    2    2    2    2    2    2    2    2    2    2 
## 2737 2738 2739 2740 2741 2742 2743 2744 2745 2746 2747 2748 2749 2750 2751 2752 
##    2    2    2    1    2    2    2    2    2    2    2    2    2    2    2    2 
## 2753 2754 2755 2756 2757 2758 2759 2760 2761 2762 2763 2764 2765 2766 2767 2768 
##    1    1    2    2    1    2    1    2    2    2    2    1    2    1    2    2 
## 2769 2770 2771 2772 2773 2774 2775 2776 2777 2778 2779 2780 2781 2782 2783 2784 
##    1    2    1    1    2    2    2    2    2    2    2    1    1    1    1    2 
## 2785 2786 2787 2788 2789 2790 2791 2792 2793 2794 2795 2796 2797 2798 2799 2800 
##    1    1    2    1    1    1    1    1    2    2    2    1    2    2    2    1 
## 2801 2802 2803 2804 2805 2806 2807 2808 2809 2810 2811 2812 2813 2814 2815 2816 
##    1    1    2    2    2    1    2    2    2    2    1    1    2    2    1    1 
## 2817 2818 2819 2820 2821 2822 2823 2824 2825 2826 2827 2828 2829 2830 2831 2832 
##    2    2    1    1    1    2    2    2    2    2    2    2    1    2    2    2 
## 2833 2834 2835 2836 2837 2838 2839 2840 2841 2842 2843 2844 2845 2846 2847 2848 
##    2    2    2    2    2    1    2    2    2    1    1    1    2    1    2    2 
## 2849 2850 2851 2852 2853 2854 2855 2856 2857 2858 2859 2860 2861 2862 2863 2864 
##    2    2    1    2    2    2    2    1    2    2    2    2    2    2    1    2 
## 2865 2866 2867 2868 2869 2870 2871 2872 2873 2874 2875 2876 2877 2878 2879 2880 
##    2    2    2    2    2    2    1    1    1    1    2    1    2    2    2    2 
## 2881 2882 2883 2884 2885 2886 2887 2888 2889 2890 2891 2892 2893 2894 2895 2896 
##    2    1    2    2    2    2    1    2    2    2    2    2    1    2    2    2 
## 2897 2898 2899 2900 2901 2902 2903 2904 2905 2906 2907 2908 2909 2910 2911 2912 
##    2    2    1    2    1    2    2    1    1    1    1    2    2    1    2    1 
## 2913 2914 2915 2916 2917 2918 2919 2920 2921 2922 2923 2924 2925 2926 2927 2928 
##    2    1    2    2    2    2    1    1    2    1    1    1    1    1    2    2 
## 2929 2930 2931 2932 2933 2934 2935 2936 2937 2938 2939 2940 2941 2942 2943 2944 
##    2    1    2    2    2    2    2    1    2    1    2    2    1    2    2    2 
## 2945 2946 2947 2948 2949 2950 2951 2952 2953 2954 2955 2956 2957 2958 2959 2960 
##    1    2    2    2    2    2    2    1    2    2    1    1    1    2    1    1 
## 2961 2962 2963 2964 2965 2966 2967 2968 2969 2970 2971 2972 2973 2974 2975 2976 
##    1    2    2    1    1    2    1    2    2    1    2    1    2    1    1    2 
## 2977 2978 2979 2980 2981 2982 2983 2984 2985 2986 2987 2988 2989 2990 2991 2992 
##    1    1    1    1    2    1    2    2    2    2    1    1    1    2    2    1 
## 2993 2994 2995 2996 2997 2998 2999 3000 3001 3002 3003 3004 3005 3006 3007 3008 
##    1    2    1    2    1    2    2    1    1    1    1    2    2    1    2    2 
## 3009 3010 3011 3012 3013 3014 3015 3016 3017 3018 3019 3020 3021 3022 3023 3024 
##    2    1    1    2    1    2    1    1    1    1    1    2    1    1    2    2 
## 3025 3026 3027 3028 3029 3030 3031 3032 3033 3034 3035 3036 3037 3038 3039 3040 
##    2    2    1    1    2    2    2    2    2    2    2    1    1    2    1    2 
## 3041 3042 3043 3044 3045 3046 3047 3048 3049 3050 3051 3052 3053 3054 3055 3056 
##    2    2    1    1    2    1    2    2    1    2    2    2    1    1    2    1 
## 3057 3058 3059 3060 3061 3062 3063 3064 3065 3066 3067 3068 3069 3070 3071 3072 
##    2    2    2    2    2    2    1    2    1    2    1    1    1    2    2    2 
## 3073 3074 3075 3076 3077 3078 3079 3080 3081 3082 3083 3084 3085 3086 3087 3088 
##    1    2    2    1    1    2    2    2    2    2    1    2    1    2    2    1 
## 3089 3090 3091 3092 3093 3094 3095 3096 3097 3098 3099 3100 3101 3102 3103 3104 
##    1    2    2    1    1    2    2    2    2    2    1    2    1    1    2    2 
## 3105 3106 3107 3108 3109 3110 3111 3112 3113 3114 3115 3116 3117 3118 3119 3120 
##    1    1    1    1    1    2    2    2    1    1    2    1    2    1    1    1 
## 3121 3122 3123 3124 3125 3126 3127 3128 3129 3130 3131 3132 3133 3134 3135 3136 
##    2    2    2    2    1    1    1    2    2    1    2    2    2    2    2    1 
## 3137 3138 3139 3140 3141 3142 3143 3144 3145 3146 3147 3148 3149 3150 3151 3152 
##    2    1    1    1    2    1    2    1    2    1    1    2    1    1    1    2 
## 3153 3154 3155 3156 3157 3158 3159 3160 3161 3162 3163 3164 3165 3166 3167 3168 
##    1    1    2    1    1    1    2    2    1    1    2    2    2    2    2    1 
## 3169 3170 3171 3172 3173 3174 3175 3176 3177 3178 3179 3180 3181 3182 3183 3184 
##    2    2    2    2    2    2    2    2    1    1    1    1    1    1    1    2 
## 3185 3186 3187 3188 3189 3190 3191 3192 3193 3194 3195 3196 3197 3198 3199 3200 
##    2    2    1    2    2    2    1    2    1    1    1    1    2    1    1    1 
## 3201 3202 3203 3204 3205 3206 3207 3208 3209 3210 3211 3212 3213 3214 3215 3216 
##    2    1    1    1    2    2    1    2    2    2    1    2    1    1    2    1 
## 3217 3218 3219 3220 3221 3222 3223 3224 3225 3226 3227 3228 3229 3230 3231 3232 
##    2    1    1    1    2    2    1    2    2    2    2    2    2    2    1    2 
## 3233 3234 3235 3236 3237 3238 3239 3240 3241 3242 3243 3244 3245 3246 3247 3248 
##    1    2    2    1    2    1    2    2    1    2    2    1    1    2    1    1 
## 3249 3250 3251 3252 3253 3254 3255 3256 3257 3258 3259 3260 3261 3262 3263 3264 
##    2    2    2    1    2    2    2    1    1    2    2    1    1    1    1    1 
## 3265 3266 3267 3268 3269 3270 3271 3272 3273 3274 3275 3276 3277 3278 3279 3280 
##    1    2    2    1    2    1    1    1    2    1    1    1    1    2    1    1 
## 3281 3282 3283 3284 3285 3286 3287 3288 3289 3290 3291 3292 3293 3294 3295 3296 
##    2    2    1    2    1    1    2    2    2    1    2    2    2    1    2    1 
## 3297 3298 3299 3300 3301 3302 3303 3304 3305 3306 3307 3308 3309 3310 3311 3312 
##    2    2    2    1    1    1    1    1    1    2    2    2    1    2    1    2 
## 3313 3314 3315 3316 3317 3318 3319 3320 3321 3322 3323 3324 3325 3326 3327 3328 
##    1    2    1    1    2    2    2    1    2    2    1    2    2    1    2    1 
## 3329 3330 3331 3332 3333 3334 3335 3336 3337 3338 3339 3340 3341 3342 3343 3344 
##    1    1    2    2    2    1    2    1    2    2    2    2    1    2    1    2 
## 3345 3346 3347 3348 3349 3350 3351 3352 3353 3354 3355 3356 3357 3358 3359 3360 
##    2    1    2    2    1    1    1    1    2    2    2    2    1    2    1    1 
## 3361 3362 3363 3364 3365 3366 3367 3368 3369 3370 3371 3372 3373 3374 3375 3376 
##    2    1    2    1    1    2    2    2    1    2    2    1    2    2    1    2 
## 3377 3378 3379 3380 3381 3382 3383 3384 3385 3386 3387 3388 3389 3390 3391 3392 
##    2    2    2    2    2    2    2    2    2    1    2    2    2    2    2    2 
## 3393 3394 3395 3396 3397 3398 3399 3400 3401 3402 3403 3404 3405 3406 3407 3408 
##    2    2    2    1    1    2    1    1    1    1    2    2    1    1    2    1 
## 3409 3410 3411 3412 3413 3414 3415 3416 3417 3418 3419 3420 3421 3422 3423 3424 
##    2    2    1    2    1    1    1    2    1    2    2    1    2    1    2    2 
## 3425 3426 3427 3428 3429 3430 3431 3432 3433 3434 3435 3436 3437 3438 3439 3440 
##    1    2    1    1    2    2    1    2    2    2    1    2    1    1    1    1 
## 3441 3442 3443 3444 3445 3446 3447 3448 3449 3450 3451 3452 3453 3454 3455 3456 
##    2    2    1    2    2    2    1    1    2    1    2    1    2    2    1    1 
## 3457 3458 3459 3460 3461 3462 3463 3464 3465 3466 3467 3468 3469 3470 3471 3472 
##    2    2    1    2    2    2    1    2    2    2    1    2    1    2    1    1 
## 3473 3474 3475 3476 3477 3478 3479 3480 3481 3482 3483 3484 3485 3486 3487 3488 
##    1    1    2    2    2    2    2    2    2    1    2    2    2    1    1    2 
## 3489 3490 3491 3492 3493 3494 3495 3496 3497 3498 3499 3500 3501 3502 3503 3504 
##    1    1    2    1    2    2    1    2    2    2    1    2    2    2    2    1 
## 3505 3506 3507 3508 3509 3510 3511 3512 3513 3514 3515 3516 3517 3518 3519 3520 
##    2    2    2    2    2    1    1    1    2    2    2    2    2    2    2    2 
## 3521 3522 3523 3524 3525 3526 3527 3528 3529 3530 3531 3532 3533 3534 3535 3536 
##    1    1    1    1    2    2    1    2    2    1    2    1    1    2    2    2 
## 3537 3538 3539 3540 3541 3542 3543 3544 3545 3546 3547 3548 3549 3550 3551 3552 
##    2    2    2    1    2    2    1    1    1    2    1    2    1    2    2    2 
## 3553 3554 3555 3556 3557 3558 3559 3560 3561 3562 3563 3564 3565 3566 3567 3568 
##    2    2    2    1    1    1    2    2    1    1    2    1    2    2    1    2 
## 3569 3570 3571 3572 3573 3574 3575 3576 3577 3578 3579 3580 3581 3582 3583 3584 
##    1    2    1    2    2    2    2    1    1    2    2    1    1    1    2    1 
## 3585 3586 3587 3588 3589 3590 3591 3592 3593 3594 3595 3596 3597 3598 3599 3600 
##    2    2    1    1    1    2    2    1    1    1    1    1    1    1    2    1 
## 3601 3602 3603 3604 3605 3606 3607 3608 3609 3610 3611 3612 3613 3614 3615 3616 
##    2    2    1    1    1    2    2    1    2    2    1    1    1    2    2    1 
## 3617 3618 3619 3620 3621 3622 3623 3624 3625 3626 3627 3628 3629 3630 3631 3632 
##    1    1    1    1    2    1    2    1    2    1    1    2    1    2    2    2 
## 3633 3634 3635 3636 3637 3638 3639 3640 3641 3642 3643 3644 3645 3646 3647 3648 
##    2    2    2    1    1    1    2    1    1    2    2    1    2    1    2    1 
## 3649 3650 3651 3652 3653 3654 3655 3656 3657 3658 3659 3660 3661 3662 3663 3664 
##    1    1    2    2    1    1    1    1    2    1    1    1    2    1    1    2 
## 3665 3666 3667 3668 3669 3670 3671 3672 3673 3674 3675 3676 3677 3678 3679 3680 
##    2    1    1    1    1    1    1    1    2    2    2    2    1    2    2    1 
## 3681 3682 3683 3684 3685 3686 3687 3688 3689 3690 3691 3692 3693 3694 3695 3696 
##    1    2    2    1    2    1    1    1    2    2    1    1    2    2    1    1 
## 3697 3698 3699 3700 3701 3702 3703 3704 3705 3706 3707 3708 3709 3710 3711 3712 
##    2    2    2    2    2    2    2    2    2    1    1    1    2    1    1    2 
## 3713 3714 3715 3716 3717 3718 3719 3720 3721 3722 3723 3724 3725 3726 3727 3728 
##    1    1    2    1    2    1    1    1    2    2    1    1    1    2    1    1 
## 3729 3730 3731 3732 3733 3734 3735 3736 3737 3738 3739 3740 3741 3742 3743 3744 
##    1    1    2    2    2    1    1    2    1    2    2    2    2    2    1    2 
## 3745 3746 3747 3748 3749 3750 3751 3752 3753 3754 3755 3756 3757 3758 3759 3760 
##    2    2    1    1    2    2    2    2    1    1    2    2    2    2    2    2 
## 3761 3762 3763 3764 3765 3766 3767 3768 3769 3770 3771 3772 3773 3774 3775 3776 
##    2    2    1    2    1    1    2    2    1    2    2    1    2    2    2    2 
## 3777 3778 3779 3780 3781 3782 3783 3784 3785 3786 3787 3788 3789 3790 3791 3792 
##    2    1    1    2    1    2    2    1    1    1    2    2    2    1    2    1 
## 3793 3794 3795 3796 3797 3798 3799 3800 3801 3802 3803 3804 3805 3806 3807 3808 
##    2    2    1    1    1    1    1    2    2    1    2    1    2    2    1    2 
## 3809 3810 3811 3812 3813 3814 3815 3816 3817 3818 3819 3820 3821 3822 3823 3824 
##    1    1    1    1    2    1    1    1    2    2    2    2    1    2    1    1 
## 3825 3826 3827 3828 3829 3830 3831 3832 3833 3834 3835 3836 3837 3838 3839 3840 
##    1    2    2    1    1    2    2    2    2    2    1    1    2    1    2    2 
## 3841 3842 3843 3844 3845 3846 3847 3848 3849 3850 3851 3852 3853 3854 3855 3856 
##    2    1    1    2    2    2    2    2    2    2    1    2    2    1    2    2 
## 3857 3858 3859 3860 3861 3862 3863 3864 3865 3866 3867 3868 3869 3870 3871 3872 
##    2    2    2    2    1    2    2    1    1    1    1    2    1    2    1    2 
## 3873 3874 3875 3876 3877 3878 3879 3880 3881 3882 3883 3884 3885 3886 3887 3888 
##    1    2    2    2    2    1    2    2    1    1    1    2    1    2    1    2 
## 3889 3890 3891 3892 3893 3894 3895 3896 3897 3898 3899 3900 3901 3902 3903 3904 
##    1    1    1    1    1    1    2    2    1    1    1    2    2    2    2    2 
## 3905 3906 3907 3908 3909 3910 3911 3912 3913 3914 3915 3916 3917 3918 3919 3920 
##    1    2    2    1    2    2    1    2    2    1    2    1    2    2    2    2 
## 3921 3922 3923 3924 3925 3926 3927 3928 3929 3930 3931 3932 3933 3934 3935 3936 
##    2    2    2    1    1    2    2    2    2    1    2    2    1    1    1    2 
## 3937 3938 3939 3940 3941 3942 3943 3944 3945 3946 3947 3948 3949 3950 3951 3952 
##    1    2    1    2    1    2    1    1    2    1    2    2    1    2    1    1 
## 3953 3954 3955 3956 3957 3958 3959 3960 3961 3962 3963 3964 3965 3966 3967 3968 
##    1    2    2    1    1    1    2    1    1    2    2    2    1    2    1    2 
## 3969 3970 3971 3972 3973 3974 3975 3976 3977 3978 3979 3980 3981 3982 3983 3984 
##    1    1    1    1    2    1    1    1    2    1    2    1    2    1    1    2 
## 3985 3986 3987 3988 3989 3990 3991 3992 3993 3994 3995 3996 3997 3998 3999 4000 
##    2    1    2    1    2    2    2    2    2    2    1    1    2    1    2    2 
## 4001 4002 4003 4004 4005 4006 4007 4008 4009 4010 4011 4012 4013 4014 4015 4016 
##    2    2    2    1    1    1    1    1    1    2    1    2    1    2    1    2 
## 4017 4018 4019 4020 4021 4022 4023 4024 4025 4026 4027 4028 4029 4030 4031 4032 
##    1    1    1    1    2    1    2    2    2    2    2    2    2    1    1    1 
## 4033 4034 4035 4036 4037 4038 4039 4040 4041 4042 4043 4044 4045 4046 4047 4048 
##    2    1    2    2    2    2    2    2    1    2    1    2    1    1    1    1 
## 4049 4050 4051 4052 4053 4054 4055 4056 4057 4058 4059 4060 4061 4062 4063 4064 
##    2    2    1    2    1    1    2    1    1    2    2    2    2    1    2    2 
## 4065 4066 4067 4068 4069 4070 4071 4072 4073 4074 4075 4076 4077 4078 4079 4080 
##    2    2    1    1    2    2    2    1    1    2    1    2    2    2    1    1 
## 4081 4082 4083 4084 4085 4086 4087 4088 4089 4090 4091 4092 4093 4094 4095 4096 
##    1    2    2    1    2    2    2    2    1    1    1    1    1    2    1    2 
## 4097 4098 4099 4100 4101 4102 4103 4104 4105 4106 4107 4108 4109 4110 4111 4112 
##    1    2    1    2    2    2    2    2    1    1    1    1    1    2    2    2 
## 4113 4114 4115 4116 4117 4118 4119 4120 4121 4122 4123 4124 4125 4126 4127 4128 
##    2    1    1    2    2    2    2    2    1    2    1    1    1    1    2    1 
## 4129 
##    2 
## 
## Within cluster sum of squares by cluster:
## [1] 6942.451 9338.897
##  (between_SS / total_SS =  40.8 %)
## 
## Available components:
## 
## [1] &quot;cluster&quot;      &quot;centers&quot;      &quot;totss&quot;        &quot;withinss&quot;     &quot;tot.withinss&quot;
## [6] &quot;betweenss&quot;    &quot;size&quot;         &quot;iter&quot;         &quot;ifault&quot;</code></pre>
<div class="sourceCode" id="cb68"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb68-1" data-line-number="1">dtrain<span class="op">$</span>cluster&lt;-<span class="st"> </span>km1<span class="op">$</span>cluster</a>
<a class="sourceLine" id="cb68-2" data-line-number="2"></a>
<a class="sourceLine" id="cb68-3" data-line-number="3"><span class="kw">summary</span>(<span class="kw">glm</span>(modcontra<span class="op">~</span><span class="kw">factor</span>(cluster), <span class="dt">family =</span> binomial, <span class="dt">data=</span>dtrain))</a></code></pre></div>
<pre><code>## 
## Call:
## glm(formula = modcontra ~ factor(cluster), family = binomial, 
##     data = dtrain)
## 
## Deviance Residuals: 
##     Min       1Q   Median       3Q      Max  
## -0.6581  -0.6581  -0.5908  -0.5908   1.9139  
## 
## Coefficients:
##                  Estimate Std. Error z value Pr(&gt;|z|)    
## (Intercept)      -1.41958    0.06181  -22.96   &lt;2e-16 ***
## factor(cluster)2 -0.23744    0.08272   -2.87   0.0041 ** 
## ---
## Signif. codes:  0 &#39;***&#39; 0.001 &#39;**&#39; 0.01 &#39;*&#39; 0.05 &#39;.&#39; 0.1 &#39; &#39; 1
## 
## (Dispersion parameter for binomial family taken to be 1)
## 
##     Null deviance: 3818.3  on 4128  degrees of freedom
## Residual deviance: 3810.2  on 4127  degrees of freedom
## AIC: 3814.2
## 
## Number of Fisher Scoring iterations: 4</code></pre>
<div class="sourceCode" id="cb70"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb70-1" data-line-number="1"><span class="kw">head</span>(x)</a></code></pre></div>
<pre><code>##   factor.region.2 factor.region.3 factor.region.4 factor.age..21.8.28.6.
## 1               1               0               0                      0
## 2               1               0               0                      1
## 3               1               0               0                      0
## 4               1               0               0                      0
## 5               1               0               0                      0
## 6               1               0               0                      0
##   factor.age..28.6.35.4. factor.age..35.4.42.2. factor.age..42.2.49.
## 1                      1                      0                    0
## 2                      0                      0                    0
## 3                      0                      1                    0
## 4                      0                      0                    1
## 5                      1                      0                    0
## 6                      0                      0                    0
##   livchildren factor.rural.1 factor.wantmore.1 factor.educ.1 factor.educ.2
## 1           4              1                 1             0             0
## 2           3              1                 0             1             0
## 3           2              1                 1             0             0
## 4           2              1                 1             0             0
## 5           4              1                 1             0             0
## 6           1              1                 1             1             0
##   factor.educ.3 partnered factor.work.1 factor.wealth.2 factor.wealth.3
## 1             0         0             1               0               0
## 2             0         0             1               0               1
## 3             0         0             1               0               1
## 4             0         0             1               0               0
## 5             0         0             1               0               0
## 6             0         0             1               0               0
##   factor.wealth.4 factor.wealth.5
## 1               0               0
## 2               0               0
## 3               0               0
## 4               1               0
## 5               0               0
## 6               0               0</code></pre>
<div class="sourceCode" id="cb72"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb72-1" data-line-number="1"><span class="kw">table</span>(y)</a></code></pre></div>
<pre><code>## y
##    mod notmod 
##    719   3410</code></pre>
<div class="sourceCode" id="cb74"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb74-1" data-line-number="1"><span class="kw">prop.table</span>(<span class="kw">table</span>(y))</a></code></pre></div>
<pre><code>## y
##       mod    notmod 
## 0.1741342 0.8258658</code></pre>
<div class="sourceCode" id="cb76"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb76-1" data-line-number="1">xtest&lt;-<span class="kw">model.matrix</span>(<span class="op">~</span><span class="kw">factor</span>(region)<span class="op">+</span><span class="kw">factor</span>(age)<span class="op">+</span>livchildren<span class="op">+</span><span class="kw">factor</span>(rural)<span class="op">+</span><span class="kw">factor</span>(wantmore)<span class="op">+</span><span class="kw">factor</span>(educ)<span class="op">+</span>partnered<span class="op">+</span><span class="kw">factor</span>(work)<span class="op">+</span><span class="kw">factor</span>(wealth), <span class="dt">data=</span>dtest)</a>
<a class="sourceLine" id="cb76-2" data-line-number="2">xtest&lt;-xtest[,<span class="op">-</span><span class="dv">1</span>]</a>
<a class="sourceLine" id="cb76-3" data-line-number="3">xtest&lt;-<span class="kw">data.frame</span>(xtest)</a>
<a class="sourceLine" id="cb76-4" data-line-number="4"></a>
<a class="sourceLine" id="cb76-5" data-line-number="5">yt&lt;-dtest<span class="op">$</span>modcontra</a>
<a class="sourceLine" id="cb76-6" data-line-number="6">yt&lt;-<span class="kw">as.factor</span>(<span class="kw">ifelse</span>(yt<span class="op">==</span><span class="dv">1</span>, <span class="st">&quot;mod&quot;</span>, <span class="st">&quot;notmod&quot;</span>))</a>
<a class="sourceLine" id="cb76-7" data-line-number="7"><span class="kw">prop.table</span>(<span class="kw">table</span>(yt))</a></code></pre></div>
<pre><code>## yt
##       mod    notmod 
## 0.2102713 0.7897287</code></pre>
</div>
<div id="set-up-caret-for-repeated-10-fold-cross-validation" class="section level3">
<h3>Set up caret for repeated 10 fold cross-validation</h3>
<p>To set up the training controls for a caret model, we typically have to specify the type of re-sampling method, the number of resamplings, the number of repeats (if you’re doing repeated sampling). Here we will do a 10 fold cross-validation, 10 is often recommended as a choice for k based on experimental sensitivity analysis.</p>
<p>The other things we specify are:</p>
<ul>
<li>repeats - These are the number of times we wish to repeat the cross-validation, typically 3 or more is used</li>
<li>classProbs = TRUE - this is necessary to assess accuracy in the confusion matrix</li>
<li>search = “random” is used if you want to randomly search along the values of the tuning parameter</li>
<li>sampling - Here we can specify alternative sampling methods to account for unbalanced outcomes</li>
<li>SummaryFunction=twoClassSummary - keeps information on the two classes of the outcome</li>
<li>savePredictions = T - have the process save all the predicted values throughout the process, we need this for the ROC curves</li>
</ul>
<div class="sourceCode" id="cb78"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb78-1" data-line-number="1">fitctrl &lt;-<span class="st"> </span><span class="kw">trainControl</span>(<span class="dt">method=</span><span class="st">&quot;repeatedcv&quot;</span>, </a>
<a class="sourceLine" id="cb78-2" data-line-number="2">                        <span class="dt">number=</span><span class="dv">10</span>, </a>
<a class="sourceLine" id="cb78-3" data-line-number="3">                        <span class="dt">repeats=</span><span class="dv">5</span>,</a>
<a class="sourceLine" id="cb78-4" data-line-number="4">                        <span class="dt">classProbs =</span> <span class="ot">TRUE</span>,</a>
<a class="sourceLine" id="cb78-5" data-line-number="5">                     <span class="dt">search=</span><span class="st">&quot;random&quot;</span>, <span class="co">#randomly search on different values of the tuning parameters</span></a>
<a class="sourceLine" id="cb78-6" data-line-number="6">                     <span class="dt">sampling =</span> <span class="st">&quot;down&quot;</span>, <span class="co">#optional, but good for unbalanced outcomes like this one</span></a>
<a class="sourceLine" id="cb78-7" data-line-number="7">                     <span class="dt">summaryFunction=</span>twoClassSummary,</a>
<a class="sourceLine" id="cb78-8" data-line-number="8">                     <span class="dt">savePredictions =</span> <span class="st">&quot;all&quot;</span>)</a></code></pre></div>
</div>
<div id="train-regression-classification-models-using-caret" class="section level3">
<h3>Train regression classification models using caret</h3>
<p>Here we fit a basic regression classification tree using the <code>rpart()</code> function</p>
<div class="sourceCode" id="cb79"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb79-1" data-line-number="1">fitctrl &lt;-<span class="st"> </span><span class="kw">trainControl</span>(<span class="dt">method=</span><span class="st">&quot;cv&quot;</span>, </a>
<a class="sourceLine" id="cb79-2" data-line-number="2">                        <span class="dt">number=</span><span class="dv">10</span>, </a>
<a class="sourceLine" id="cb79-3" data-line-number="3">                        <span class="co">#repeats=5,</span></a>
<a class="sourceLine" id="cb79-4" data-line-number="4">                        <span class="dt">classProbs =</span> <span class="ot">TRUE</span>,</a>
<a class="sourceLine" id="cb79-5" data-line-number="5">                     <span class="dt">search=</span><span class="st">&quot;random&quot;</span>,</a>
<a class="sourceLine" id="cb79-6" data-line-number="6">                     <span class="dt">sampling =</span> <span class="st">&quot;down&quot;</span>,</a>
<a class="sourceLine" id="cb79-7" data-line-number="7">                     <span class="dt">summaryFunction=</span>twoClassSummary,</a>
<a class="sourceLine" id="cb79-8" data-line-number="8">                     <span class="dt">savePredictions =</span> <span class="st">&quot;all&quot;</span>)</a>
<a class="sourceLine" id="cb79-9" data-line-number="9"></a>
<a class="sourceLine" id="cb79-10" data-line-number="10">rp1&lt;-caret<span class="op">::</span><span class="kw">train</span>(<span class="dt">y=</span>y, <span class="dt">x=</span>x, </a>
<a class="sourceLine" id="cb79-11" data-line-number="11">           <span class="dt">metric=</span><span class="st">&quot;ROC&quot;</span>,</a>
<a class="sourceLine" id="cb79-12" data-line-number="12">           <span class="dt">method =</span><span class="st">&quot;rpart&quot;</span>,</a>
<a class="sourceLine" id="cb79-13" data-line-number="13">          <span class="dt">tuneLength=</span><span class="dv">20</span>, <span class="co">#try 20 random values of the tuning parameters</span></a>
<a class="sourceLine" id="cb79-14" data-line-number="14">           <span class="dt">trControl=</span>fitctrl,</a>
<a class="sourceLine" id="cb79-15" data-line-number="15">          <span class="dt">preProcess=</span><span class="kw">c</span>(<span class="st">&quot;center&quot;</span>, <span class="st">&quot;scale&quot;</span>))</a>
<a class="sourceLine" id="cb79-16" data-line-number="16"></a>
<a class="sourceLine" id="cb79-17" data-line-number="17">rp1</a></code></pre></div>
<pre><code>## CART 
## 
## 4129 samples
##   19 predictor
##    2 classes: &#39;mod&#39;, &#39;notmod&#39; 
## 
## Pre-processing: centered (19), scaled (19) 
## Resampling: Cross-Validated (10 fold) 
## Summary of sample sizes: 3716, 3716, 3716, 3716, 3716, 3716, ... 
## Addtional sampling using down-sampling prior to pre-processing
## 
## Resampling results across tuning parameters:
## 
##   cp            ROC        Sens       Spec     
##   0.0000000000  0.6387339  0.5883216  0.6005865
##   0.0002781641  0.6398112  0.5910994  0.6002933
##   0.0003477051  0.6393265  0.5938772  0.5979472
##   0.0004636069  0.6378236  0.5883216  0.6011730
##   0.0013908206  0.6414247  0.6202269  0.5944282
##   0.0018544274  0.6447474  0.6453052  0.6093842
## 
## ROC was used to select the optimal model using the largest value.
## The final value used for the model was cp = 0.001854427.</code></pre>
<div class="sourceCode" id="cb81"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb81-1" data-line-number="1"><span class="kw">library</span>(rpart.plot)</a></code></pre></div>
<pre><code>## Loading required package: rpart</code></pre>
<div class="sourceCode" id="cb83"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb83-1" data-line-number="1"><span class="kw">plot</span>(rp1)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/RandomForests_files/figure-html/unnamed-chunk-17-1.png" /><!-- --></p>
<div class="sourceCode" id="cb84"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb84-1" data-line-number="1"><span class="co">#plot(rp1$finalModel)</span></a>
<a class="sourceLine" id="cb84-2" data-line-number="2"><span class="kw">prp</span>(rp1<span class="op">$</span>finalModel,<span class="dt">type=</span><span class="dv">4</span>, <span class="dt">extra =</span> <span class="dv">4</span>, </a>
<a class="sourceLine" id="cb84-3" data-line-number="3">    <span class="dt">main=</span><span class="st">&quot;Classification tree for using modern contraception&quot;</span>)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/RandomForests_files/figure-html/unnamed-chunk-17-2.png" /><!-- --></p>
<div class="sourceCode" id="cb85"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb85-1" data-line-number="1"><span class="kw">varImp</span>(rp1)</a></code></pre></div>
<pre><code>## rpart variable importance
## 
##                        Overall
## livchildren            100.000
## partnered               79.283
## factor.age..28.6.35.4.  60.287
## factor.rural.1          54.849
## factor.educ.2           37.668
## factor.age..21.8.28.6.  33.258
## factor.region.3         32.785
## factor.wantmore.1       25.272
## factor.region.2         22.304
## factor.work.1           21.409
## factor.age..42.2.49.    20.856
## factor.region.4         19.453
## factor.wealth.4         17.237
## factor.wealth.3          9.969
## factor.age..35.4.42.2.   8.871
## factor.wealth.2          6.085
## factor.educ.1            5.067
## factor.wealth.5          3.287
## factor.educ.3            0.000</code></pre>
<div class="sourceCode" id="cb87"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb87-1" data-line-number="1"><span class="kw">plot</span>(<span class="kw">varImp</span>(rp1), <span class="dt">top=</span><span class="dv">10</span>)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/RandomForests_files/figure-html/unnamed-chunk-17-3.png" /><!-- --></p>
<div class="sourceCode" id="cb88"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb88-1" data-line-number="1"><span class="co">##Accuracy on training set</span></a>
<a class="sourceLine" id="cb88-2" data-line-number="2">pred1&lt;-<span class="kw">predict</span>(rp1, <span class="dt">newdata=</span>x)</a>
<a class="sourceLine" id="cb88-3" data-line-number="3"><span class="kw">confusionMatrix</span>(<span class="dt">data =</span> pred1,<span class="dt">reference =</span> y, <span class="dt">positive =</span> <span class="st">&quot;mod&quot;</span> )</a></code></pre></div>
<pre><code>## Confusion Matrix and Statistics
## 
##           Reference
## Prediction  mod notmod
##     mod     496   1186
##     notmod  223   2224
##                                           
##                Accuracy : 0.6588          
##                  95% CI : (0.6441, 0.6732)
##     No Information Rate : 0.8259          
##     P-Value [Acc &gt; NIR] : 1               
##                                           
##                   Kappa : 0.2238          
##                                           
##  Mcnemar&#39;s Test P-Value : &lt;2e-16          
##                                           
##             Sensitivity : 0.6898          
##             Specificity : 0.6522          
##          Pos Pred Value : 0.2949          
##          Neg Pred Value : 0.9089          
##              Prevalence : 0.1741          
##          Detection Rate : 0.1201          
##    Detection Prevalence : 0.4074          
##       Balanced Accuracy : 0.6710          
##                                           
##        &#39;Positive&#39; Class : mod             
## </code></pre>
<div class="sourceCode" id="cb90"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb90-1" data-line-number="1">predt1&lt;-<span class="kw">predict</span>(rp1, <span class="dt">newdata=</span>xtest)</a>
<a class="sourceLine" id="cb90-2" data-line-number="2"><span class="kw">confusionMatrix</span>(<span class="dt">data =</span> predt1, yt, <span class="dt">positive =</span> <span class="st">&quot;mod&quot;</span> )</a></code></pre></div>
<pre><code>## Confusion Matrix and Statistics
## 
##           Reference
## Prediction mod notmod
##     mod    132    302
##     notmod  85    513
##                                           
##                Accuracy : 0.625           
##                  95% CI : (0.5947, 0.6546)
##     No Information Rate : 0.7897          
##     P-Value [Acc &gt; NIR] : 1               
##                                           
##                   Kappa : 0.1739          
##                                           
##  Mcnemar&#39;s Test P-Value : &lt;2e-16          
##                                           
##             Sensitivity : 0.6083          
##             Specificity : 0.6294          
##          Pos Pred Value : 0.3041          
##          Neg Pred Value : 0.8579          
##              Prevalence : 0.2103          
##          Detection Rate : 0.1279          
##    Detection Prevalence : 0.4205          
##       Balanced Accuracy : 0.6189          
##                                           
##        &#39;Positive&#39; Class : mod             
## </code></pre>
</div>
<div id="bagged-tree-model" class="section level3">
<h3>Bagged tree model</h3>
<div class="sourceCode" id="cb92"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb92-1" data-line-number="1">fitctrl &lt;-<span class="st"> </span><span class="kw">trainControl</span>(<span class="dt">method=</span><span class="st">&quot;cv&quot;</span>, </a>
<a class="sourceLine" id="cb92-2" data-line-number="2">                        <span class="dt">number=</span><span class="dv">10</span>, </a>
<a class="sourceLine" id="cb92-3" data-line-number="3">                        <span class="co">#repeats=5,</span></a>
<a class="sourceLine" id="cb92-4" data-line-number="4">                        <span class="dt">classProbs =</span> <span class="ot">TRUE</span>,</a>
<a class="sourceLine" id="cb92-5" data-line-number="5">                     <span class="dt">search=</span><span class="st">&quot;random&quot;</span>,</a>
<a class="sourceLine" id="cb92-6" data-line-number="6">                     <span class="dt">sampling =</span> <span class="st">&quot;down&quot;</span>,</a>
<a class="sourceLine" id="cb92-7" data-line-number="7">                     <span class="dt">summaryFunction=</span>twoClassSummary,</a>
<a class="sourceLine" id="cb92-8" data-line-number="8">                     <span class="dt">savePredictions =</span> <span class="st">&quot;all&quot;</span>)</a>
<a class="sourceLine" id="cb92-9" data-line-number="9"></a>
<a class="sourceLine" id="cb92-10" data-line-number="10">bt1&lt;-caret<span class="op">::</span><span class="kw">train</span>(<span class="dt">y=</span>y, <span class="dt">x=</span>x, </a>
<a class="sourceLine" id="cb92-11" data-line-number="11">           <span class="dt">metric=</span><span class="st">&quot;ROC&quot;</span>,</a>
<a class="sourceLine" id="cb92-12" data-line-number="12">           <span class="dt">method =</span><span class="st">&quot;treebag&quot;</span>,</a>
<a class="sourceLine" id="cb92-13" data-line-number="13">          <span class="dt">tuneLength=</span><span class="dv">20</span>, <span class="co">#try 20 random values of the tuning parameters</span></a>
<a class="sourceLine" id="cb92-14" data-line-number="14">           <span class="dt">trControl=</span>fitctrl,</a>
<a class="sourceLine" id="cb92-15" data-line-number="15">          <span class="dt">preProcess=</span><span class="kw">c</span>(<span class="st">&quot;center&quot;</span>, <span class="st">&quot;scale&quot;</span>))</a>
<a class="sourceLine" id="cb92-16" data-line-number="16"></a>
<a class="sourceLine" id="cb92-17" data-line-number="17"><span class="kw">print</span>(bt1)</a></code></pre></div>
<pre><code>## Bagged CART 
## 
## 4129 samples
##   19 predictor
##    2 classes: &#39;mod&#39;, &#39;notmod&#39; 
## 
## Pre-processing: centered (19), scaled (19) 
## Resampling: Cross-Validated (10 fold) 
## Summary of sample sizes: 3716, 3716, 3716, 3716, 3716, 3716, ... 
## Addtional sampling using down-sampling prior to pre-processing
## 
## Resampling results:
## 
##   ROC        Sens       Spec     
##   0.6289611  0.6132238  0.5721408</code></pre>
<div class="sourceCode" id="cb94"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb94-1" data-line-number="1"><span class="kw">plot</span>(<span class="kw">varImp</span>(bt1))</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/RandomForests_files/figure-html/unnamed-chunk-19-1.png" /><!-- --></p>
<div class="sourceCode" id="cb95"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb95-1" data-line-number="1"><span class="co">##Accuracy on training set</span></a>
<a class="sourceLine" id="cb95-2" data-line-number="2">pred1&lt;-<span class="kw">predict</span>(bt1, <span class="dt">newdata=</span>x)</a>
<a class="sourceLine" id="cb95-3" data-line-number="3"><span class="kw">confusionMatrix</span>(<span class="dt">data =</span> pred1,<span class="dt">reference =</span> y, <span class="dt">positive =</span> <span class="st">&quot;mod&quot;</span> )</a></code></pre></div>
<pre><code>## Confusion Matrix and Statistics
## 
##           Reference
## Prediction  mod notmod
##     mod     646   1201
##     notmod   73   2209
##                                           
##                Accuracy : 0.6915          
##                  95% CI : (0.6771, 0.7055)
##     No Information Rate : 0.8259          
##     P-Value [Acc &gt; NIR] : 1               
##                                           
##                   Kappa : 0.3374          
##                                           
##  Mcnemar&#39;s Test P-Value : &lt;2e-16          
##                                           
##             Sensitivity : 0.8985          
##             Specificity : 0.6478          
##          Pos Pred Value : 0.3498          
##          Neg Pred Value : 0.9680          
##              Prevalence : 0.1741          
##          Detection Rate : 0.1565          
##    Detection Prevalence : 0.4473          
##       Balanced Accuracy : 0.7731          
##                                           
##        &#39;Positive&#39; Class : mod             
## </code></pre>
<div class="sourceCode" id="cb97"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb97-1" data-line-number="1">predt1&lt;-<span class="kw">predict</span>(bt1, <span class="dt">newdata=</span>xtest)</a>
<a class="sourceLine" id="cb97-2" data-line-number="2"><span class="kw">confusionMatrix</span>(<span class="dt">data =</span> predt1,yt, <span class="dt">positive =</span> <span class="st">&quot;mod&quot;</span> )</a></code></pre></div>
<pre><code>## Confusion Matrix and Statistics
## 
##           Reference
## Prediction mod notmod
##     mod    135    329
##     notmod  82    486
##                                           
##                Accuracy : 0.6017          
##                  95% CI : (0.5711, 0.6318)
##     No Information Rate : 0.7897          
##     P-Value [Acc &gt; NIR] : 1               
##                                           
##                   Kappa : 0.1541          
##                                           
##  Mcnemar&#39;s Test P-Value : &lt;2e-16          
##                                           
##             Sensitivity : 0.6221          
##             Specificity : 0.5963          
##          Pos Pred Value : 0.2909          
##          Neg Pred Value : 0.8556          
##              Prevalence : 0.2103          
##          Detection Rate : 0.1308          
##    Detection Prevalence : 0.4496          
##       Balanced Accuracy : 0.6092          
##                                           
##        &#39;Positive&#39; Class : mod             
## </code></pre>
</div>
<div id="random-forest-model-using-caret" class="section level3">
<h3>Random forest model using caret</h3>
<div class="sourceCode" id="cb99"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb99-1" data-line-number="1"><span class="kw">library</span>(rpart)</a>
<a class="sourceLine" id="cb99-2" data-line-number="2">rf1&lt;-caret<span class="op">::</span><span class="kw">train</span>(<span class="dt">y=</span>y, <span class="dt">x=</span>x, </a>
<a class="sourceLine" id="cb99-3" data-line-number="3">           <span class="dt">data=</span>dtrain,</a>
<a class="sourceLine" id="cb99-4" data-line-number="4">           <span class="dt">metric=</span><span class="st">&quot;ROC&quot;</span>,</a>
<a class="sourceLine" id="cb99-5" data-line-number="5">           <span class="dt">method =</span><span class="st">&quot;rf&quot;</span>,</a>
<a class="sourceLine" id="cb99-6" data-line-number="6">          <span class="dt">tuneLength=</span><span class="dv">20</span>, <span class="co">#try 20 random values of the tuning parameters</span></a>
<a class="sourceLine" id="cb99-7" data-line-number="7">           <span class="dt">trControl=</span>fitctrl, </a>
<a class="sourceLine" id="cb99-8" data-line-number="8">          <span class="dt">preProcess=</span><span class="kw">c</span>(<span class="st">&quot;center&quot;</span>, <span class="st">&quot;scale&quot;</span>))</a>
<a class="sourceLine" id="cb99-9" data-line-number="9"></a>
<a class="sourceLine" id="cb99-10" data-line-number="10">rf1</a></code></pre></div>
<pre><code>## Random Forest 
## 
## 4129 samples
##   19 predictor
##    2 classes: &#39;mod&#39;, &#39;notmod&#39; 
## 
## Pre-processing: centered (19), scaled (19) 
## Resampling: Cross-Validated (10 fold) 
## Summary of sample sizes: 3716, 3716, 3716, 3716, 3716, 3716, ... 
## Addtional sampling using down-sampling prior to pre-processing
## 
## Resampling results across tuning parameters:
## 
##   mtry  ROC        Sens       Spec     
##    1    0.6737048  0.5508803  0.6994135
##    2    0.6727308  0.6023670  0.6571848
##    3    0.6770178  0.6357003  0.6357771
##    5    0.6598504  0.6286189  0.5958944
##    6    0.6531281  0.6091549  0.6011730
##    7    0.6432423  0.6091549  0.5909091
##    8    0.6339576  0.6147692  0.5809384
##    9    0.6425341  0.6426252  0.5809384
##   11    0.6283912  0.6258998  0.5733138
##   13    0.6313454  0.6271127  0.5759531
##   15    0.6371863  0.6439750  0.5712610
## 
## ROC was used to select the optimal model using the largest value.
## The final value used for the model was mtry = 3.</code></pre>
<div class="sourceCode" id="cb101"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb101-1" data-line-number="1"><span class="co">##Accuracy on training set</span></a>
<a class="sourceLine" id="cb101-2" data-line-number="2">predrf1&lt;-<span class="kw">predict</span>(rf1, <span class="dt">newdata=</span>x)</a>
<a class="sourceLine" id="cb101-3" data-line-number="3"><span class="kw">confusionMatrix</span>(<span class="dt">data =</span> predrf1,y, <span class="dt">positive =</span> <span class="st">&quot;mod&quot;</span> )</a></code></pre></div>
<pre><code>## Confusion Matrix and Statistics
## 
##           Reference
## Prediction  mod notmod
##     mod     529    972
##     notmod  190   2438
##                                           
##                Accuracy : 0.7186          
##                  95% CI : (0.7046, 0.7323)
##     No Information Rate : 0.8259          
##     P-Value [Acc &gt; NIR] : 1               
##                                           
##                   Kappa : 0.3154          
##                                           
##  Mcnemar&#39;s Test P-Value : &lt;2e-16          
##                                           
##             Sensitivity : 0.7357          
##             Specificity : 0.7150          
##          Pos Pred Value : 0.3524          
##          Neg Pred Value : 0.9277          
##              Prevalence : 0.1741          
##          Detection Rate : 0.1281          
##    Detection Prevalence : 0.3635          
##       Balanced Accuracy : 0.7254          
##                                           
##        &#39;Positive&#39; Class : mod             
## </code></pre>
<div class="sourceCode" id="cb103"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb103-1" data-line-number="1">predgl1&lt;-<span class="kw">predict</span>(rf1, <span class="dt">newdata=</span>xtest)</a>
<a class="sourceLine" id="cb103-2" data-line-number="2"><span class="kw">confusionMatrix</span>(<span class="dt">data =</span> predgl1,yt, <span class="dt">positive =</span> <span class="st">&quot;mod&quot;</span> )</a></code></pre></div>
<pre><code>## Confusion Matrix and Statistics
## 
##           Reference
## Prediction mod notmod
##     mod    129    266
##     notmod  88    549
##                                           
##                Accuracy : 0.657           
##                  95% CI : (0.6271, 0.6859)
##     No Information Rate : 0.7897          
##     P-Value [Acc &gt; NIR] : 1               
##                                           
##                   Kappa : 0.2061          
##                                           
##  Mcnemar&#39;s Test P-Value : &lt;2e-16          
##                                           
##             Sensitivity : 0.5945          
##             Specificity : 0.6736          
##          Pos Pred Value : 0.3266          
##          Neg Pred Value : 0.8619          
##              Prevalence : 0.2103          
##          Detection Rate : 0.1250          
##    Detection Prevalence : 0.3828          
##       Balanced Accuracy : 0.6340          
##                                           
##        &#39;Positive&#39; Class : mod             
## </code></pre>
<p>We see that by down sampling the more common level of the outcome, we end up with much more balanced accuracy in terms of specificity and sensitivity.</p>
<p>You see that the best fitting model is much more complicated than the previous one. Each node box displays the classification, the probability of each class at that node (i.e. the probability of the class conditioned on the node) and the percentage of observations used at that node. <a href="https://blog.revolutionanalytics.com/2013/06/plotting-classification-and-regression-trees-with-plotrpart.html">From here</a>.</p>
</div>
<div id="roc-curve" class="section level3">
<h3>ROC curve</h3>
<p>The ROC curve can be shown for the model:</p>
<div class="sourceCode" id="cb105"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb105-1" data-line-number="1"><span class="kw">library</span>(pROC)</a></code></pre></div>
<pre><code>## Type &#39;citation(&quot;pROC&quot;)&#39; for a citation.</code></pre>
<pre><code>## 
## Attaching package: &#39;pROC&#39;</code></pre>
<pre><code>## The following objects are masked from &#39;package:stats&#39;:
## 
##     cov, smooth, var</code></pre>
<div class="sourceCode" id="cb109"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb109-1" data-line-number="1"><span class="co"># Select a parameter setting</span></a>
<a class="sourceLine" id="cb109-2" data-line-number="2">mycp&lt;-rf1<span class="op">$</span>pred<span class="op">$</span>mtry<span class="op">==</span>rf1<span class="op">$</span>bestTune<span class="op">$</span>mtry</a>
<a class="sourceLine" id="cb109-3" data-line-number="3">selectedIndices &lt;-<span class="st"> </span>mycp<span class="op">==</span>T</a>
<a class="sourceLine" id="cb109-4" data-line-number="4"><span class="co"># Plot:</span></a>
<a class="sourceLine" id="cb109-5" data-line-number="5"><span class="kw">plot.roc</span>(rf1<span class="op">$</span>pred<span class="op">$</span>obs[selectedIndices], rf1<span class="op">$</span>pred<span class="op">$</span>mod[selectedIndices], <span class="dt">grid=</span>T)</a></code></pre></div>
<pre><code>## Setting levels: control = mod, case = notmod</code></pre>
<pre><code>## Setting direction: controls &gt; cases</code></pre>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/RandomForests_files/figure-html/unnamed-chunk-22-1.png" /><!-- --></p>
<div class="sourceCode" id="cb112"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb112-1" data-line-number="1"><span class="co">#Value of ROC and AUC</span></a>
<a class="sourceLine" id="cb112-2" data-line-number="2"><span class="kw">roc</span>(rf1<span class="op">$</span>pred<span class="op">$</span>obs[selectedIndices],  rf1<span class="op">$</span>pred<span class="op">$</span>mod[selectedIndices])</a></code></pre></div>
<pre><code>## Setting levels: control = mod, case = notmod
## Setting direction: controls &gt; cases</code></pre>
<pre><code>## 
## Call:
## roc.default(response = rf1$pred$obs[selectedIndices], predictor = rf1$pred$mod[selectedIndices])
## 
## Data: rf1$pred$mod[selectedIndices] in 719 controls (rf1$pred$obs[selectedIndices] mod) &gt; 3410 cases (rf1$pred$obs[selectedIndices] notmod).
## Area under the curve: 0.6774</code></pre>
<div class="sourceCode" id="cb115"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb115-1" data-line-number="1"><span class="kw">auc</span>(rf1<span class="op">$</span>pred<span class="op">$</span>obs[selectedIndices],  rf1<span class="op">$</span>pred<span class="op">$</span>mod[selectedIndices])</a></code></pre></div>
<pre><code>## Setting levels: control = mod, case = notmod
## Setting direction: controls &gt; cases</code></pre>
<pre><code>## Area under the curve: 0.6774</code></pre>
</div>
</div>
</div>
</section>

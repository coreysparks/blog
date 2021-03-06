---
title: "DEM 7283 - Example 1 - Survey Statistics using BRFSS data"

author: "Corey Sparks, PhD"
date: "February 1, 2021"
layout: post
---

<script src="{{ site.url }}{{ site.baseurl }}/knitr_files/EX2_surveystats_files/header-attrs-2.6/header-attrs.js"></script>

<section class="main-content">
<div id="what-is-a-survey" class="section level2">
<h2>What is a survey?</h2>
<p>A systematic method for gathering information from a sample of entities for the purposes of constructing quantitative descriptors of the attributes of the larger population of which the entities are members</p>
</div>
<div id="questions-when-identifying-a-survey-data-source" class="section level2">
<h2>Questions when identifying a survey data source:</h2>
<ol style="list-style-type: decimal">
<li>What is the target population?</li>
<li>What is the sampling frame</li>
</ol>
<ul>
<li>how do we know who <strong><em>could</em></strong> be included?</li>
</ul>
<ol start="3" style="list-style-type: decimal">
<li>What is the sample design?</li>
<li>What is the mode of data collection?</li>
<li>Is the survey ongoing or a one-time collection?</li>
</ol>
</div>
<div id="core-concepts" class="section level1">
<h1>Core Concepts</h1>
<p><strong><em>Sampling units</em></strong> - where information will be collected from <strong><em>Sampling frame</em></strong> - the set of sampling units containing distinct sets of population members</p>
</div>
<div id="weights-and-weighting" class="section level1">
<h1>Weights and weighting</h1>
<p>Surveys with complex sample designs will often have:</p>
<ul>
<li>Unequal probabilities of selection</li>
<li>Variation in response rates across groups</li>
<li>Differences in distributions of characteristics compared to the population</li>
</ul>
<div id="weights-are-used-to-compensate-for-these-features" class="section level3">
<h3>Weights are used to compensate for these features</h3>
</div>
<div id="what-is-a-weight" class="section level2">
<h2>What is a weight?</h2>
<ul>
<li><p>A weight is used to indicate the relative strength of an observation.</p></li>
<li><p>In the simplest case, each observation is counted equally.</p></li>
<li><p>For example, if we have five observations, and wish to calculate the mean, we just add up the values and divide by 5.</p></li>
</ul>
<div id="dataset-with-5-cases" class="section level3">
<h3>Dataset with 5 cases</h3>
<div class="sourceCode" id="cb1"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb1-1"><a href="#cb1-1" aria-hidden="true" tabindex="-1"></a><span class="fu">library</span>(htmlTable)</span>
<span id="cb1-2"><a href="#cb1-2" aria-hidden="true" tabindex="-1"></a>y<span class="ot">&lt;-</span><span class="fu">c</span>(<span class="dv">4</span>,<span class="dv">2</span>,<span class="dv">1</span>,<span class="dv">5</span>,<span class="dv">2</span>)</span>
<span id="cb1-3"><a href="#cb1-3" aria-hidden="true" tabindex="-1"></a>wt<span class="ot">&lt;-</span><span class="fu">c</span>(<span class="dv">1</span>,<span class="dv">2</span>,<span class="dv">4</span>,<span class="dv">1</span>,<span class="dv">2</span>)</span>
<span id="cb1-4"><a href="#cb1-4" aria-hidden="true" tabindex="-1"></a>dat<span class="ot">&lt;-</span><span class="fu">data.frame</span>(<span class="at">y=</span>y, <span class="at">weight=</span>wt)</span>
<span id="cb1-5"><a href="#cb1-5" aria-hidden="true" tabindex="-1"></a><span class="fu">htmlTable</span>(dat)</span></code></pre></div>
<table class="gmisc_table" style="border-collapse: collapse; margin-top: 1em; margin-bottom: 1em;">
<thead>
<tr>
<th style="border-bottom: 1px solid grey; border-top: 2px solid grey;">
</th>
<th style="font-weight: 900; border-bottom: 1px solid grey; border-top: 2px solid grey; text-align: center;">
y
</th>
<th style="font-weight: 900; border-bottom: 1px solid grey; border-top: 2px solid grey; text-align: center;">
weight
</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align: left;">
1
</td>
<td style="text-align: center;">
4
</td>
<td style="text-align: center;">
1
</td>
</tr>
<tr>
<td style="text-align: left;">
2
</td>
<td style="text-align: center;">
2
</td>
<td style="text-align: center;">
2
</td>
</tr>
<tr>
<td style="text-align: left;">
3
</td>
<td style="text-align: center;">
1
</td>
<td style="text-align: center;">
4
</td>
</tr>
<tr>
<td style="text-align: left;">
4
</td>
<td style="text-align: center;">
5
</td>
<td style="text-align: center;">
1
</td>
</tr>
<tr>
<td style="border-bottom: 2px solid grey; text-align: left;">
5
</td>
<td style="border-bottom: 2px solid grey; text-align: center;">
2
</td>
<td style="border-bottom: 2px solid grey; text-align: center;">
2
</td>
</tr>
</tbody>
</table>
<p>Unweighted sample mean</p>
<div class="sourceCode" id="cb2"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb2-1"><a href="#cb2-1" aria-hidden="true" tabindex="-1"></a><span class="fu">mean</span>(dat<span class="sc">$</span>y)</span></code></pre></div>
<pre><code>## [1] 2.8</code></pre>
<p>Weighted sample mean</p>
<div class="sourceCode" id="cb4"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb4-1"><a href="#cb4-1" aria-hidden="true" tabindex="-1"></a><span class="fu">library</span>(questionr)</span>
<span id="cb4-2"><a href="#cb4-2" aria-hidden="true" tabindex="-1"></a><span class="fu">wtd.mean</span>(<span class="at">x=</span>dat<span class="sc">$</span>y, <span class="at">weights=</span>dat<span class="sc">$</span>weight)</span></code></pre></div>
<pre><code>## [1] 2.1</code></pre>
</div>
</div>
<div id="difference-between-unweighted-and-weighted-data" class="section level2">
<h2>Difference between unweighted and weighted data</h2>
<ul>
<li><p>With unweighted data, each case is counted equally.</p></li>
<li><p>Unweighted data represent only those in the sample who provide data.</p></li>
<li><p>With weighted data, each case is counted relative to its representation in the population.</p></li>
<li><p>Weights allow analyses that represent the target population.</p></li>
</ul>
<p>Weights compensate for collecting data from a sample rather than the entire population and for using a complex sample design.</p>
<p>Weights adjust for differential selection probabilities and reduce bias associated with non-response by adjusting for differential nonresponse. Weights are used when estimating characteristics of the population.</p>
</div>
<div id="sample-variance-estimation-in-complex-designs" class="section level2">
<h2>Sample variance estimation in complex designs</h2>
<p><strong><em>Standard errors</em></strong> are produced for estimates from sample surveys. They are a measure of the variance in the estimates associated with the selected sample being one of many possible samples.</p>
<p>Standard errors are used to test hypotheses and to study group differences.</p>
<p>Using inaccurate standard errors can lead to identification of statistically significant results where none are present and vice versa</p>
</div>
<div id="complex-survey-designs-and-standard-errors" class="section level2">
<h2>Complex survey designs and standard errors</h2>
<ul>
<li><p>The usual standard error formula assumes a simple random sample.</p></li>
<li><p>Software packages designed for simple random samples tend to underestimate the standard errors for complex sample designs.</p></li>
<li><p>Standard errors for estimates from a complex sample must account for the within cluster/ across cluster variation.</p></li>
<li><p>Special software can make the adjustment, or this adjustment can be approximated using the design effect.</p></li>
</ul>
</div>
<div id="methods-for-sample-variance-estimation" class="section level2">
<h2>Methods for sample variance estimation</h2>
<p>There are basically 3 ways in which software estimates variances: 1) Naive method 2) Taylor series approximation 3) Balanced or Jackknife replication</p>
</div>
<div id="data-example" class="section level2">
<h2>Data Example</h2>
<p>This example will cover the use of R functions for analyzing complex survey data. Most social and health surveys are not simple random samples of the population, but instead consist of respondents from a complex survey design.</p>
<p>These designs often stratify the population based on one or more characteristics, including geography, race, age, etc. In addition the designs can be multi-stage, meaning that initial strata are created, then respondents are sampled from smaller units within those strata.</p>
<p>An example would be if a school district was chosen as a sample strata, and then schools were then chosen as the primary sampling units (PSUs) within the district. From this 2 stage design, we could further sample classrooms within the school (3 stage design) or simply sample students (or whatever our unit of interest is).</p>
</div>
<div id="multi-stage-sampling" class="section level2">
<h2>Multi-stage sampling</h2>
<ul>
<li>Non-random sampling</li>
<li>Population consists of known sub-groups called <em>clusters</em></li>
<li>A 2 -stage sample might be households within neighborhoods, or children within schools
<ul>
<li>We may choose a random sample of schools/neighborhoods at the first stage, and a random sample of people within each school/neighborhood as the second stage</li>
<li>We need to be <em>careful</em> because the observations in the second stage are not <em>independent</em> of one another</li>
<li>Increased probability of selection for children in a selected school</li>
</ul></li>
<li>This type of sampling leads to <em>dependent</em> observations</li>
</ul>
<p>Here’s a picture of this:</p>
<div class="figure">
<img src="C:/Users/ozd504/OneDrive%20-%20University%20of%20Texas%20at%20San%20Antonio/classes/dem7473/lectures/Rpresentations/multistage.png" alt="" />
<p class="caption">Multistage Sampling - From Kawachi and Berkman, 2003</p>
</div>
<p>A second feature of survey data we often want to account for is differential respondent weighting. This means that each respondent is given a weight to represent how common that particular respondent is within the population. This reflects the differenital probability of sampling based on respondent characteristics.</p>
<p>As demographers, we are also often interested in making inference for the population, not just the sample, so our results must be generalizable to the population at large. Sample weights are used in the process as well.</p>
<p>When such data are analyzed, we must take into account this nesting structure (sample design) as well as the respondent sample weight in order to make valid estimates of <strong>ANY</strong> statistical parameter. If we do not account for design, the parameter standard errors will be incorrect, and if we do not account for weighting, the parameters themselves will be incorrect and biased.</p>
<p>In general there are typically three things we need to find in our survey data codebooks: The sample strata identifier, the sample primary sampling unit identifier (often called a cluster identifier) and the respondent survey weight. These will typically have one of these names and should be easily identifiable in the codebook.</p>
<p>Statistical software will have special routines for analyzing these types of data and you must be aware that the diversity of statistical routines that generally exists will be lower for analyzing complex survey data, and some forms of analysis <em>may not be available!</em></p>
<p>See <a href="https://onlinelibrary.wiley.com/doi/book/10.1002/9780470580066">Thomas Lumley’s Book</a> on this!</p>
<p>Below I illustrate the use of survey characteristics when conducting descriptive analysis of a survey data set and a linear regression model estimated from that data. For this example I am using 2016 CDC Behavioral Risk Factor Surveillance System (BRFSS) SMART metro area survey data. <a href="https://www.cdc.gov/brfss/smart/smart_2016.html">Link</a></p>
<div class="sourceCode" id="cb6"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb6-1"><a href="#cb6-1" aria-hidden="true" tabindex="-1"></a><span class="co">#load brfss</span></span>
<span id="cb6-2"><a href="#cb6-2" aria-hidden="true" tabindex="-1"></a><span class="fu">library</span>(car)</span></code></pre></div>
<pre><code>## Loading required package: carData</code></pre>
<div class="sourceCode" id="cb8"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb8-1"><a href="#cb8-1" aria-hidden="true" tabindex="-1"></a><span class="fu">library</span>(stargazer)</span></code></pre></div>
<pre><code>## 
## Please cite as:</code></pre>
<pre><code>##  Hlavac, Marek (2018). stargazer: Well-Formatted Regression and Summary Statistics Tables.</code></pre>
<pre><code>##  R package version 5.2.2. https://CRAN.R-project.org/package=stargazer</code></pre>
<div class="sourceCode" id="cb12"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb12-1"><a href="#cb12-1" aria-hidden="true" tabindex="-1"></a><span class="fu">library</span>(survey)</span></code></pre></div>
<pre><code>## Loading required package: grid</code></pre>
<pre><code>## Loading required package: Matrix</code></pre>
<pre><code>## Loading required package: survival</code></pre>
<pre><code>## 
## Attaching package: &#39;survey&#39;</code></pre>
<pre><code>## The following object is masked from &#39;package:graphics&#39;:
## 
##     dotchart</code></pre>
<div class="sourceCode" id="cb18"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb18-1"><a href="#cb18-1" aria-hidden="true" tabindex="-1"></a><span class="fu">library</span>(questionr)</span>
<span id="cb18-2"><a href="#cb18-2" aria-hidden="true" tabindex="-1"></a><span class="fu">library</span>(dplyr)</span></code></pre></div>
<pre><code>## 
## Attaching package: &#39;dplyr&#39;</code></pre>
<pre><code>## The following object is masked from &#39;package:car&#39;:
## 
##     recode</code></pre>
<pre><code>## The following objects are masked from &#39;package:stats&#39;:
## 
##     filter, lag</code></pre>
<pre><code>## The following objects are masked from &#39;package:base&#39;:
## 
##     intersect, setdiff, setequal, union</code></pre>
<div class="sourceCode" id="cb23"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb23-1"><a href="#cb23-1" aria-hidden="true" tabindex="-1"></a><span class="fu">load</span>(<span class="fu">url</span>(<span class="st">&quot;https://github.com/coreysparks/data/blob/master/brfss_2017.Rdata?raw=true&quot;</span>))</span></code></pre></div>
<div id="fix-variable-names" class="section level3">
<h3>Fix variable names</h3>
<div class="sourceCode" id="cb24"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb24-1"><a href="#cb24-1" aria-hidden="true" tabindex="-1"></a><span class="co">#The names in the data are very ugly, so I make them less ugly</span></span>
<span id="cb24-2"><a href="#cb24-2" aria-hidden="true" tabindex="-1"></a>nams<span class="ot">&lt;-</span><span class="fu">names</span>(brfss_17)</span>
<span id="cb24-3"><a href="#cb24-3" aria-hidden="true" tabindex="-1"></a><span class="fu">head</span>(nams, <span class="at">n=</span><span class="dv">10</span>)</span></code></pre></div>
<pre><code>##  [1] &quot;dispcode&quot; &quot;statere1&quot; &quot;safetime&quot; &quot;hhadult&quot;  &quot;genhlth&quot;  &quot;physhlth&quot;
##  [7] &quot;menthlth&quot; &quot;poorhlth&quot; &quot;hlthpln1&quot; &quot;persdoc2&quot;</code></pre>
<div class="sourceCode" id="cb26"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb26-1"><a href="#cb26-1" aria-hidden="true" tabindex="-1"></a><span class="co">#we see some names are lower case, some are upper and some have a little _ in the first position. This is a nightmare.</span></span>
<span id="cb26-2"><a href="#cb26-2" aria-hidden="true" tabindex="-1"></a>newnames<span class="ot">&lt;-</span><span class="fu">tolower</span>(<span class="fu">gsub</span>(<span class="at">pattern =</span> <span class="st">&quot;_&quot;</span>,<span class="at">replacement =</span>  <span class="st">&quot;&quot;</span>,<span class="at">x =</span>  nams))</span>
<span id="cb26-3"><a href="#cb26-3" aria-hidden="true" tabindex="-1"></a><span class="fu">names</span>(brfss_17)<span class="ot">&lt;-</span>newnames</span></code></pre></div>
</div>
<div id="recoding-of-variables" class="section level3">
<h3>Recoding of variables</h3>
<p>Be sure to always check your codebooks!</p>
<div class="sourceCode" id="cb27"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb27-1"><a href="#cb27-1" aria-hidden="true" tabindex="-1"></a><span class="co">#Poor or fair self rated health</span></span>
<span id="cb27-2"><a href="#cb27-2" aria-hidden="true" tabindex="-1"></a>brfss_17<span class="sc">$</span>badhealth<span class="ot">&lt;-</span><span class="fu">Recode</span>(brfss_17<span class="sc">$</span>genhlth, <span class="at">recodes=</span><span class="st">&quot;4:5=1; 1:3=0; else=NA&quot;</span>)</span>
<span id="cb27-3"><a href="#cb27-3" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb27-4"><a href="#cb27-4" aria-hidden="true" tabindex="-1"></a><span class="co">#sex</span></span>
<span id="cb27-5"><a href="#cb27-5" aria-hidden="true" tabindex="-1"></a>brfss_17<span class="sc">$</span>male<span class="ot">&lt;-</span><span class="fu">as.factor</span>(<span class="fu">ifelse</span>(brfss_17<span class="sc">$</span>sex<span class="sc">==</span><span class="dv">1</span>, <span class="st">&quot;Male&quot;</span>, <span class="st">&quot;Female&quot;</span>))</span>
<span id="cb27-6"><a href="#cb27-6" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb27-7"><a href="#cb27-7" aria-hidden="true" tabindex="-1"></a><span class="co">#race/ethnicity</span></span>
<span id="cb27-8"><a href="#cb27-8" aria-hidden="true" tabindex="-1"></a>brfss_17<span class="sc">$</span>black<span class="ot">&lt;-</span><span class="fu">Recode</span>(brfss_17<span class="sc">$</span>racegr3, <span class="at">recodes=</span><span class="st">&quot;2=1; 9=NA; else=0&quot;</span>)</span>
<span id="cb27-9"><a href="#cb27-9" aria-hidden="true" tabindex="-1"></a>brfss_17<span class="sc">$</span>white<span class="ot">&lt;-</span><span class="fu">Recode</span>(brfss_17<span class="sc">$</span>racegr3, <span class="at">recodes=</span><span class="st">&quot;1=1; 9=NA; else=0&quot;</span>)</span>
<span id="cb27-10"><a href="#cb27-10" aria-hidden="true" tabindex="-1"></a>brfss_17<span class="sc">$</span>other<span class="ot">&lt;-</span><span class="fu">Recode</span>(brfss_17<span class="sc">$</span>racegr3, <span class="at">recodes=</span><span class="st">&quot;3:4=1; 9=NA; else=0&quot;</span>)</span>
<span id="cb27-11"><a href="#cb27-11" aria-hidden="true" tabindex="-1"></a>brfss_17<span class="sc">$</span>hispanic<span class="ot">&lt;-</span><span class="fu">Recode</span>(brfss_17<span class="sc">$</span>racegr3, <span class="at">recodes=</span><span class="st">&quot;5=1; 9=NA; else=0&quot;</span>)</span>
<span id="cb27-12"><a href="#cb27-12" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb27-13"><a href="#cb27-13" aria-hidden="true" tabindex="-1"></a><span class="co">#insurance</span></span>
<span id="cb27-14"><a href="#cb27-14" aria-hidden="true" tabindex="-1"></a>brfss_17<span class="sc">$</span>ins<span class="ot">&lt;-</span><span class="fu">Recode</span>(brfss_17<span class="sc">$</span>hlthpln1, <span class="at">recodes =</span><span class="st">&quot;7:9=NA; 1=1;2=0&quot;</span>)</span>
<span id="cb27-15"><a href="#cb27-15" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb27-16"><a href="#cb27-16" aria-hidden="true" tabindex="-1"></a><span class="co">#income grouping</span></span>
<span id="cb27-17"><a href="#cb27-17" aria-hidden="true" tabindex="-1"></a>brfss_17<span class="sc">$</span>inc<span class="ot">&lt;-</span><span class="fu">ifelse</span>(brfss_17<span class="sc">$</span>incomg<span class="sc">==</span><span class="dv">9</span>, <span class="cn">NA</span>, brfss_17<span class="sc">$</span>incomg)</span>
<span id="cb27-18"><a href="#cb27-18" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb27-19"><a href="#cb27-19" aria-hidden="true" tabindex="-1"></a><span class="co">#education level</span></span>
<span id="cb27-20"><a href="#cb27-20" aria-hidden="true" tabindex="-1"></a>brfss_17<span class="sc">$</span>educ<span class="ot">&lt;-</span><span class="fu">Recode</span>(brfss_17<span class="sc">$</span>educa, <span class="at">recodes=</span><span class="st">&quot;1:2=&#39;0Prim&#39;; 3=&#39;1somehs&#39;; 4=&#39;2hsgrad&#39;; 5=&#39;3somecol&#39;; 6=&#39;4colgrad&#39;;9=NA&quot;</span>, <span class="at">as.factor=</span>T)</span>
<span id="cb27-21"><a href="#cb27-21" aria-hidden="true" tabindex="-1"></a>brfss_17<span class="sc">$</span>educ<span class="ot">&lt;-</span><span class="fu">relevel</span>(brfss_17<span class="sc">$</span>educ, <span class="at">ref=</span><span class="st">&#39;2hsgrad&#39;</span>)</span>
<span id="cb27-22"><a href="#cb27-22" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb27-23"><a href="#cb27-23" aria-hidden="true" tabindex="-1"></a><span class="co">#employment</span></span>
<span id="cb27-24"><a href="#cb27-24" aria-hidden="true" tabindex="-1"></a>brfss_17<span class="sc">$</span>employ<span class="ot">&lt;-</span><span class="fu">Recode</span>(brfss_17<span class="sc">$</span>employ1, <span class="at">recodes=</span><span class="st">&quot;1:2=&#39;Employed&#39;; 2:6=&#39;nilf&#39;; 7=&#39;retired&#39;; 8=&#39;unable&#39;; else=NA&quot;</span>, <span class="at">as.factor=</span>T)</span>
<span id="cb27-25"><a href="#cb27-25" aria-hidden="true" tabindex="-1"></a>brfss_17<span class="sc">$</span>employ<span class="ot">&lt;-</span><span class="fu">relevel</span>(brfss_17<span class="sc">$</span>employ, <span class="at">ref=</span><span class="st">&#39;Employed&#39;</span>)</span>
<span id="cb27-26"><a href="#cb27-26" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb27-27"><a href="#cb27-27" aria-hidden="true" tabindex="-1"></a><span class="co">#marital status</span></span>
<span id="cb27-28"><a href="#cb27-28" aria-hidden="true" tabindex="-1"></a>brfss_17<span class="sc">$</span>marst<span class="ot">&lt;-</span><span class="fu">Recode</span>(brfss_17<span class="sc">$</span>marital, <span class="at">recodes=</span><span class="st">&quot;1=&#39;married&#39;; 2=&#39;divorced&#39;; 3=&#39;widowed&#39;; 4=&#39;separated&#39;; 5=&#39;nm&#39;;6=&#39;cohab&#39;; else=NA&quot;</span>, <span class="at">as.factor=</span>T)</span>
<span id="cb27-29"><a href="#cb27-29" aria-hidden="true" tabindex="-1"></a>brfss_17<span class="sc">$</span>marst<span class="ot">&lt;-</span><span class="fu">relevel</span>(brfss_17<span class="sc">$</span>marst, <span class="at">ref=</span><span class="st">&#39;married&#39;</span>)</span>
<span id="cb27-30"><a href="#cb27-30" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb27-31"><a href="#cb27-31" aria-hidden="true" tabindex="-1"></a><span class="co">#Age cut into intervals</span></span>
<span id="cb27-32"><a href="#cb27-32" aria-hidden="true" tabindex="-1"></a>brfss_17<span class="sc">$</span>agec<span class="ot">&lt;-</span><span class="fu">cut</span>(brfss_17<span class="sc">$</span>age80, <span class="at">breaks=</span><span class="fu">c</span>(<span class="dv">0</span>,<span class="dv">24</span>,<span class="dv">39</span>,<span class="dv">59</span>,<span class="dv">79</span>,<span class="dv">99</span>))</span>
<span id="cb27-33"><a href="#cb27-33" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb27-34"><a href="#cb27-34" aria-hidden="true" tabindex="-1"></a><span class="co">#BMI, in the brfss_17a the bmi variable has 2 implied decimal places, so we must divide by 100 to get real bmi&#39;s</span></span>
<span id="cb27-35"><a href="#cb27-35" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb27-36"><a href="#cb27-36" aria-hidden="true" tabindex="-1"></a>brfss_17<span class="sc">$</span>bmi<span class="ot">&lt;-</span>brfss_17<span class="sc">$</span>bmi5<span class="sc">/</span><span class="dv">100</span></span>
<span id="cb27-37"><a href="#cb27-37" aria-hidden="true" tabindex="-1"></a>brfss_17<span class="sc">$</span>obese<span class="ot">&lt;-</span><span class="fu">ifelse</span>(brfss_17<span class="sc">$</span>bmi<span class="sc">&gt;=</span><span class="dv">30</span>, <span class="dv">1</span>, <span class="dv">0</span>)</span></code></pre></div>
</div>
<div id="filter-cases" class="section level3">
<h3>Filter cases</h3>
<div class="sourceCode" id="cb28"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb28-1"><a href="#cb28-1" aria-hidden="true" tabindex="-1"></a>brfss_17<span class="ot">&lt;-</span>brfss_17<span class="sc">%&gt;%</span></span>
<span id="cb28-2"><a href="#cb28-2" aria-hidden="true" tabindex="-1"></a>  <span class="fu">filter</span>(sex<span class="sc">!=</span><span class="dv">9</span>, <span class="fu">is.na</span>(educ)<span class="sc">==</span>F)</span></code></pre></div>
</div>
<div id="analysis" class="section level3">
<h3>Analysis</h3>
<p>First, we will do some descriptive analysis, such as means and cross tabulations.</p>
<div class="sourceCode" id="cb29"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb29-1"><a href="#cb29-1" aria-hidden="true" tabindex="-1"></a><span class="co">#First we will do some tables</span></span>
<span id="cb29-2"><a href="#cb29-2" aria-hidden="true" tabindex="-1"></a><span class="co">#Raw frequencies</span></span>
<span id="cb29-3"><a href="#cb29-3" aria-hidden="true" tabindex="-1"></a><span class="fu">table</span>(brfss_17<span class="sc">$</span>badhealth, brfss_17<span class="sc">$</span>educ)</span></code></pre></div>
<pre><code>##    
##     2hsgrad 0Prim 1somehs 3somecol 4colgrad
##   0   42204  2657    5581    50592    87642
##   1   13533  2412    3843    11544     9177</code></pre>
<div class="sourceCode" id="cb31"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb31-1"><a href="#cb31-1" aria-hidden="true" tabindex="-1"></a><span class="co">#column percentages</span></span>
<span id="cb31-2"><a href="#cb31-2" aria-hidden="true" tabindex="-1"></a><span class="fu">prop.table</span>(<span class="fu">table</span>(brfss_17<span class="sc">$</span>badhealth, brfss_17<span class="sc">$</span>educ), <span class="at">margin=</span><span class="dv">2</span>)</span></code></pre></div>
<pre><code>##    
##        2hsgrad      0Prim    1somehs   3somecol   4colgrad
##   0 0.75719899 0.52416650 0.59221138 0.81421398 0.90521489
##   1 0.24280101 0.47583350 0.40778862 0.18578602 0.09478511</code></pre>
<div class="sourceCode" id="cb33"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb33-1"><a href="#cb33-1" aria-hidden="true" tabindex="-1"></a><span class="co">#basic chi square test of independence</span></span>
<span id="cb33-2"><a href="#cb33-2" aria-hidden="true" tabindex="-1"></a><span class="fu">chisq.test</span>(<span class="fu">table</span>(brfss_17<span class="sc">$</span>badhealth, brfss_17<span class="sc">$</span>educ))</span></code></pre></div>
<pre><code>## 
##  Pearson&#39;s Chi-squared test
## 
## data:  table(brfss_17$badhealth, brfss_17$educ)
## X-squared = 12749, df = 4, p-value &lt; 2.2e-16</code></pre>
<p>So basically all of these numbers are incorrect, since they all assume random sampling. Now, we must tell R what the survey design is and what the weight variable is, then we can re-do these so they are correct.</p>
</div>
<div id="create-a-survey-design-object" class="section level3">
<h3>Create a survey design object</h3>
<p>Now we identify the survey design. <em>ids</em> = PSU identifers, <em>strata</em>=strata identifiers, <em>weights</em>=sampling weights, <em>data</em>= the data frame where these variables are located. Lastly, I only include respondents with NON-MISSING case weights.</p>
<p>I first try to get only cities in the state of Texas by looking for “TX” in the MSA’s name field in the data.</p>
<div class="sourceCode" id="cb35"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb35-1"><a href="#cb35-1" aria-hidden="true" tabindex="-1"></a>brfss_17<span class="sc">$</span>tx<span class="ot">&lt;-</span><span class="cn">NA</span></span>
<span id="cb35-2"><a href="#cb35-2" aria-hidden="true" tabindex="-1"></a>brfss_17<span class="sc">$</span>tx[<span class="fu">grep</span>(<span class="at">pattern =</span> <span class="st">&quot;TX&quot;</span>, brfss_17<span class="sc">$</span>mmsaname)]<span class="ot">&lt;-</span><span class="dv">1</span></span></code></pre></div>
<p>Now I make the survey design object. You may be required to specify two options here:</p>
<ol style="list-style-type: decimal">
<li><p><code>survey.lonely.psu</code> This means that some of the strata only have 1 PSU within them. This does not allow for within strata variance to be calculated. So we often have to tell the computer to do something here. Two valid options are “adjust”, to center the stratum at the population mean rather than the stratum mean, and “average” to replace the variance contribution of the stratum by the average variance contribution across strata. (from ?surveyoptions)</p></li>
<li><p>Nesting of PSU within strata. By default, PSUs have numeric identifiers that can overlap between strata. By specifying <code>nest=T</code>, we tell R to re-lable the PSUs so they are unique across strata. If your survey requires this, it will throw a warning message.</p></li>
</ol>
<div class="sourceCode" id="cb36"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb36-1"><a href="#cb36-1" aria-hidden="true" tabindex="-1"></a>brfss_17<span class="ot">&lt;-</span>brfss_17<span class="sc">%&gt;%</span></span>
<span id="cb36-2"><a href="#cb36-2" aria-hidden="true" tabindex="-1"></a>  <span class="fu">filter</span>(tx<span class="sc">==</span><span class="dv">1</span>, <span class="fu">is.na</span>(mmsawt)<span class="sc">==</span>F, <span class="fu">is.na</span>(badhealth)<span class="sc">==</span>F)</span>
<span id="cb36-3"><a href="#cb36-3" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb36-4"><a href="#cb36-4" aria-hidden="true" tabindex="-1"></a><span class="co">#</span></span>
<span id="cb36-5"><a href="#cb36-5" aria-hidden="true" tabindex="-1"></a><span class="fu">options</span>(<span class="at">survey.lonely.psu =</span> <span class="st">&quot;adjust&quot;</span>)</span>
<span id="cb36-6"><a href="#cb36-6" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb36-7"><a href="#cb36-7" aria-hidden="true" tabindex="-1"></a>des<span class="ot">&lt;-</span><span class="fu">svydesign</span>(<span class="at">ids=</span><span class="sc">~</span><span class="dv">1</span>, <span class="at">strata=</span><span class="sc">~</span>ststr, <span class="at">weights=</span><span class="sc">~</span>mmsawt, <span class="at">data =</span> brfss_17 )</span></code></pre></div>
<p>###Simple weighted analysis Now , we re-do the analysis from above using only weights:</p>
<div class="sourceCode" id="cb37"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb37-1"><a href="#cb37-1" aria-hidden="true" tabindex="-1"></a><span class="co">#counts</span></span>
<span id="cb37-2"><a href="#cb37-2" aria-hidden="true" tabindex="-1"></a>cat<span class="ot">&lt;-</span><span class="fu">wtd.table</span>(brfss_17<span class="sc">$</span>badhealth, brfss_17<span class="sc">$</span>educ, <span class="at">weights =</span> brfss_17<span class="sc">$</span>mmsawt)</span>
<span id="cb37-3"><a href="#cb37-3" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb37-4"><a href="#cb37-4" aria-hidden="true" tabindex="-1"></a><span class="co">#proportions</span></span>
<span id="cb37-5"><a href="#cb37-5" aria-hidden="true" tabindex="-1"></a><span class="fu">prop.table</span>(<span class="fu">wtd.table</span>(brfss_17<span class="sc">$</span>badhealth, brfss_17<span class="sc">$</span>educ, <span class="at">weights =</span> brfss_17<span class="sc">$</span>mmsawt), <span class="at">margin=</span><span class="dv">2</span>)</span></code></pre></div>
<pre><code>##      2hsgrad      0Prim    1somehs   3somecol   4colgrad
## 0 0.75785067 0.60034582 0.66897765 0.83478907 0.91149676
## 1 0.24214933 0.39965418 0.33102235 0.16521093 0.08850324</code></pre>
<div class="sourceCode" id="cb39"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb39-1"><a href="#cb39-1" aria-hidden="true" tabindex="-1"></a><span class="co">#compare that with the original, unweighted proportions</span></span>
<span id="cb39-2"><a href="#cb39-2" aria-hidden="true" tabindex="-1"></a><span class="fu">prop.table</span>(<span class="fu">table</span>(brfss_17<span class="sc">$</span>badhealth, brfss_17<span class="sc">$</span>educ), <span class="at">margin=</span><span class="dv">2</span>)</span></code></pre></div>
<pre><code>##    
##       2hsgrad     0Prim   1somehs  3somecol  4colgrad
##   0 0.7090336 0.5686813 0.6226013 0.7797297 0.8837339
##   1 0.2909664 0.4313187 0.3773987 0.2202703 0.1162661</code></pre>
<p>There <strong>are</strong> differences, notably that the prevalence of poor SRH is <em>higher in the sample than the population</em>. This is important!</p>
<p>Let’s say we also want the standard errors of these percentages. This can be found for a proportion by: <span class="math inline">\(s.e. (p)={\sqrt {p(1-p)} \over {n}}\)</span></p>
<p>So we need to get n and p, that’s easy:</p>
<div class="sourceCode" id="cb41"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb41-1"><a href="#cb41-1" aria-hidden="true" tabindex="-1"></a>n<span class="ot">&lt;-</span><span class="fu">table</span>(<span class="fu">is.na</span>(brfss_17<span class="sc">$</span>badhealth)<span class="sc">==</span>F)</span>
<span id="cb41-2"><a href="#cb41-2" aria-hidden="true" tabindex="-1"></a>n</span></code></pre></div>
<p>TRUE 8535</p>
<div class="sourceCode" id="cb42"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb42-1"><a href="#cb42-1" aria-hidden="true" tabindex="-1"></a>p<span class="ot">&lt;-</span><span class="fu">prop.table</span>(<span class="fu">wtd.table</span>(brfss_17<span class="sc">$</span>badhealth, brfss_17<span class="sc">$</span>educ, <span class="at">weights =</span> brfss_17<span class="sc">$</span>mmsawt), <span class="at">margin=</span><span class="dv">2</span>)</span>
<span id="cb42-2"><a href="#cb42-2" aria-hidden="true" tabindex="-1"></a><span class="fu">t</span>(p)</span></code></pre></div>
<pre><code>              0          1</code></pre>
<p>2hsgrad 0.75785067 0.24214933 0Prim 0.60034582 0.39965418 1somehs 0.66897765 0.33102235 3somecol 0.83478907 0.16521093 4colgrad 0.91149676 0.08850324</p>
<div class="sourceCode" id="cb44"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb44-1"><a href="#cb44-1" aria-hidden="true" tabindex="-1"></a>p<span class="ot">&lt;-</span><span class="fu">prop.table</span>(<span class="fu">wtd.table</span>(brfss_17<span class="sc">$</span>badhealth, brfss_17<span class="sc">$</span>male, <span class="at">weights =</span> brfss_17<span class="sc">$</span>mmsawt), <span class="at">margin=</span><span class="dv">2</span>)</span>
<span id="cb44-2"><a href="#cb44-2" aria-hidden="true" tabindex="-1"></a><span class="fu">t</span>(p)</span></code></pre></div>
<pre><code>           0         1</code></pre>
<p>Female 0.7913522 0.2086478 Male 0.8189935 0.1810065</p>
<div class="sourceCode" id="cb46"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb46-1"><a href="#cb46-1" aria-hidden="true" tabindex="-1"></a>se<span class="ot">&lt;-</span>(p<span class="sc">*</span>(<span class="dv">1</span><span class="sc">-</span>p))<span class="sc">/</span>n[<span class="dv">2</span>]</span>
<span id="cb46-2"><a href="#cb46-2" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb46-3"><a href="#cb46-3" aria-hidden="true" tabindex="-1"></a>p<span class="ot">&lt;-</span></span>
<span id="cb46-4"><a href="#cb46-4" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb46-5"><a href="#cb46-5" aria-hidden="true" tabindex="-1"></a>knitr<span class="sc">::</span><span class="fu">kable</span>(<span class="fu">data.frame</span>(<span class="at">proportion=</span>p, <span class="at">se=</span><span class="fu">sqrt</span>(se))[, <span class="fu">c</span>(<span class="dv">1</span>,<span class="dv">2</span>,<span class="dv">3</span>,<span class="dv">6</span>)], <span class="at">format =</span> <span class="st">&quot;html&quot;</span>, <span class="at">caption =</span> <span class="st">&quot;Frequencies assuming random sampling&quot;</span>)</span></code></pre></div>
<p>Which shows us the errors in the estimates based on the weighted proportions. That’s nice, but since we basically inflated the n to be the population of the US, these standard errors are too small. This is another example of using survey statistical methods, to get the right standard error for a statistic.</p>
</div>
<div id="proper-survey-design-analysis" class="section level3">
<h3>Proper survey design analysis</h3>
<div class="sourceCode" id="cb47"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb47-1"><a href="#cb47-1" aria-hidden="true" tabindex="-1"></a><span class="co">#Now consider the full sample design + weights</span></span>
<span id="cb47-2"><a href="#cb47-2" aria-hidden="true" tabindex="-1"></a>cat<span class="ot">&lt;-</span><span class="fu">svyby</span>(<span class="at">formula =</span> <span class="sc">~</span>badhealth, <span class="at">by=</span><span class="sc">~</span>male, <span class="at">design =</span> des, <span class="at">FUN=</span>svymean)</span>
<span id="cb47-3"><a href="#cb47-3" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb47-4"><a href="#cb47-4" aria-hidden="true" tabindex="-1"></a><span class="fu">svychisq</span>(<span class="sc">~</span>badhealth<span class="sc">+</span>male, <span class="at">design =</span> des)</span></code></pre></div>
<pre><code>Pearson&#39;s X^2: Rao &amp; Scott adjustment</code></pre>
<p>data: svychisq(~badhealth + male, design = des) F = 2.2685, ndf = 1, ddf = 8486, p-value = 0.1321</p>
<div class="sourceCode" id="cb49"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb49-1"><a href="#cb49-1" aria-hidden="true" tabindex="-1"></a>knitr<span class="sc">::</span><span class="fu">kable</span>(cat,</span>
<span id="cb49-2"><a href="#cb49-2" aria-hidden="true" tabindex="-1"></a>      <span class="at">caption =</span> <span class="st">&quot;Survey Estimates of Poor SRH by Sex&quot;</span>,</span>
<span id="cb49-3"><a href="#cb49-3" aria-hidden="true" tabindex="-1"></a>      <span class="at">align =</span> <span class="st">&#39;c&#39;</span>,  </span>
<span id="cb49-4"><a href="#cb49-4" aria-hidden="true" tabindex="-1"></a>      <span class="at">format =</span> <span class="st">&quot;html&quot;</span>)</span></code></pre></div>
<table>
<caption>
Survey Estimates of Poor SRH by Sex
</caption>
<thead>
<tr>
<th style="text-align:left;">
</th>
<th style="text-align:center;">
male
</th>
<th style="text-align:center;">
badhealth
</th>
<th style="text-align:center;">
se
</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:left;">
Female
</td>
<td style="text-align:center;">
Female
</td>
<td style="text-align:center;">
0.2086478
</td>
<td style="text-align:center;">
0.0129755
</td>
</tr>
<tr>
<td style="text-align:left;">
Male
</td>
<td style="text-align:center;">
Male
</td>
<td style="text-align:center;">
0.1810065
</td>
<td style="text-align:center;">
0.0128833
</td>
</tr>
</tbody>
</table>
<p>Which gives the same %’s as the weighted table above, but we also want the correct standard errors for our bad health prevalence.</p>
<p>The <code>svyby()</code> function will calculate statistics by groups, in this case we want the % in bad health by each level of education. The %’s can be gotten using the <code>svymean()</code> function, which finds means of variables using survey design:</p>
<div class="sourceCode" id="cb50"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb50-1"><a href="#cb50-1" aria-hidden="true" tabindex="-1"></a>sv.table<span class="ot">&lt;-</span><span class="fu">svyby</span>(<span class="at">formula =</span> <span class="sc">~</span>badhealth, <span class="at">by =</span> <span class="sc">~</span>educ, <span class="at">design =</span> des, <span class="at">FUN =</span> svymean, <span class="at">na.rm=</span>T)</span>
<span id="cb50-2"><a href="#cb50-2" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb50-3"><a href="#cb50-3" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb50-4"><a href="#cb50-4" aria-hidden="true" tabindex="-1"></a>knitr<span class="sc">::</span><span class="fu">kable</span>(sv.table,</span>
<span id="cb50-5"><a href="#cb50-5" aria-hidden="true" tabindex="-1"></a>      <span class="at">caption =</span> <span class="st">&quot;Survey Estimates of Poor SRH by Education&quot;</span>,</span>
<span id="cb50-6"><a href="#cb50-6" aria-hidden="true" tabindex="-1"></a>      <span class="at">align =</span> <span class="st">&#39;c&#39;</span>,  </span>
<span id="cb50-7"><a href="#cb50-7" aria-hidden="true" tabindex="-1"></a>      <span class="at">format =</span> <span class="st">&quot;html&quot;</span>)</span></code></pre></div>
<table>
<caption>
Survey Estimates of Poor SRH by Education
</caption>
<thead>
<tr>
<th style="text-align:left;">
</th>
<th style="text-align:center;">
educ
</th>
<th style="text-align:center;">
badhealth
</th>
<th style="text-align:center;">
se
</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:left;">
2hsgrad
</td>
<td style="text-align:center;">
2hsgrad
</td>
<td style="text-align:center;">
0.2421493
</td>
<td style="text-align:center;">
0.0193807
</td>
</tr>
<tr>
<td style="text-align:left;">
0Prim
</td>
<td style="text-align:center;">
0Prim
</td>
<td style="text-align:center;">
0.3996542
</td>
<td style="text-align:center;">
0.0483989
</td>
</tr>
<tr>
<td style="text-align:left;">
1somehs
</td>
<td style="text-align:center;">
1somehs
</td>
<td style="text-align:center;">
0.3310224
</td>
<td style="text-align:center;">
0.0425548
</td>
</tr>
<tr>
<td style="text-align:left;">
3somecol
</td>
<td style="text-align:center;">
3somecol
</td>
<td style="text-align:center;">
0.1652109
</td>
<td style="text-align:center;">
0.0147720
</td>
</tr>
<tr>
<td style="text-align:left;">
4colgrad
</td>
<td style="text-align:center;">
4colgrad
</td>
<td style="text-align:center;">
0.0885032
</td>
<td style="text-align:center;">
0.0094730
</td>
</tr>
</tbody>
</table>
<div class="sourceCode" id="cb51"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb51-1"><a href="#cb51-1" aria-hidden="true" tabindex="-1"></a><span class="co">#Make a survey design that is random sampling - no survey information</span></span>
<span id="cb51-2"><a href="#cb51-2" aria-hidden="true" tabindex="-1"></a>nodes<span class="ot">&lt;-</span><span class="fu">svydesign</span>(<span class="at">ids =</span> <span class="sc">~</span><span class="dv">1</span>,  <span class="at">weights =</span> <span class="sc">~</span><span class="dv">1</span>, <span class="at">data =</span> brfss_17)</span>
<span id="cb51-3"><a href="#cb51-3" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb51-4"><a href="#cb51-4" aria-hidden="true" tabindex="-1"></a>sv.table<span class="ot">&lt;-</span><span class="fu">svyby</span>(<span class="at">formula =</span> <span class="sc">~</span>badhealth, <span class="at">by =</span> <span class="sc">~</span>educ, <span class="at">design =</span> nodes, <span class="at">FUN =</span> svymean, <span class="at">na.rm=</span>T)</span>
<span id="cb51-5"><a href="#cb51-5" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb51-6"><a href="#cb51-6" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb51-7"><a href="#cb51-7" aria-hidden="true" tabindex="-1"></a>knitr<span class="sc">::</span><span class="fu">kable</span>(sv.table,</span>
<span id="cb51-8"><a href="#cb51-8" aria-hidden="true" tabindex="-1"></a>      <span class="at">caption =</span> <span class="st">&quot;Estimates of Poor SRH by Education - No survey design&quot;</span>,</span>
<span id="cb51-9"><a href="#cb51-9" aria-hidden="true" tabindex="-1"></a>      <span class="at">align =</span> <span class="st">&#39;c&#39;</span>,  </span>
<span id="cb51-10"><a href="#cb51-10" aria-hidden="true" tabindex="-1"></a>      <span class="at">format =</span> <span class="st">&quot;html&quot;</span>)</span></code></pre></div>
<table>
<caption>
Estimates of Poor SRH by Education - No survey design
</caption>
<thead>
<tr>
<th style="text-align:left;">
</th>
<th style="text-align:center;">
educ
</th>
<th style="text-align:center;">
badhealth
</th>
<th style="text-align:center;">
se
</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:left;">
2hsgrad
</td>
<td style="text-align:center;">
2hsgrad
</td>
<td style="text-align:center;">
0.2909664
</td>
<td style="text-align:center;">
0.0104099
</td>
</tr>
<tr>
<td style="text-align:left;">
0Prim
</td>
<td style="text-align:center;">
0Prim
</td>
<td style="text-align:center;">
0.4313187
</td>
<td style="text-align:center;">
0.0259602
</td>
</tr>
<tr>
<td style="text-align:left;">
1somehs
</td>
<td style="text-align:center;">
1somehs
</td>
<td style="text-align:center;">
0.3773987
</td>
<td style="text-align:center;">
0.0223843
</td>
</tr>
<tr>
<td style="text-align:left;">
3somecol
</td>
<td style="text-align:center;">
3somecol
</td>
<td style="text-align:center;">
0.2202703
</td>
<td style="text-align:center;">
0.0087963
</td>
</tr>
<tr>
<td style="text-align:left;">
4colgrad
</td>
<td style="text-align:center;">
4colgrad
</td>
<td style="text-align:center;">
0.1162661
</td>
<td style="text-align:center;">
0.0053591
</td>
</tr>
</tbody>
</table>
<p>And we see the same point estimates of our prevalences as in the simple weighted table, but the standard errors have now been adjusted for survey design as well, so they are also correct. You also see they are much larger than the ones we computed above, which assumed random sampling.</p>
</div>
<div id="another-way" class="section level3">
<h3>Another way</h3>
<p>There’s this great R package, <code>tableone</code> that does this stuff very nicely and incorporates survey design too. Here’s an example of using it to generate your bi-variate tests like above:</p>
<div class="sourceCode" id="cb52"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb52-1"><a href="#cb52-1" aria-hidden="true" tabindex="-1"></a><span class="fu">library</span>(tableone)</span>
<span id="cb52-2"><a href="#cb52-2" aria-hidden="true" tabindex="-1"></a><span class="co">#not using survey design</span></span>
<span id="cb52-3"><a href="#cb52-3" aria-hidden="true" tabindex="-1"></a>t1<span class="ot">&lt;-</span><span class="fu">CreateTableOne</span>(<span class="at">vars =</span> <span class="fu">c</span>(<span class="st">&quot;educ&quot;</span>, <span class="st">&quot;marst&quot;</span>, <span class="st">&quot;male&quot;</span>), <span class="at">strata =</span> <span class="st">&quot;badhealth&quot;</span>, <span class="at">test =</span> T, <span class="at">data =</span> brfss_17)</span>
<span id="cb52-4"><a href="#cb52-4" aria-hidden="true" tabindex="-1"></a><span class="co">#t1&lt;-print(t1, format=&quot;p&quot;)</span></span>
<span id="cb52-5"><a href="#cb52-5" aria-hidden="true" tabindex="-1"></a><span class="fu">print</span>(t1,<span class="at">format=</span><span class="st">&quot;p&quot;</span>)</span></code></pre></div>
<pre><code>##                  Stratified by badhealth
##                   0    1    p      test
##   n               6742 1793            
##   educ (%)                  &lt;0.001     
##      2hsgrad      20.0 30.9            
##      0Prim         3.1  8.8            
##      1somehs       4.3  9.9            
##      3somecol     25.7 27.3            
##      4colgrad     46.9 23.2            
##   marst (%)                 &lt;0.001     
##      married      53.4 41.1            
##      cohab         3.4  2.5            
##      divorced     11.9 16.7            
##      nm           16.1 13.8            
##      separated     2.0  5.1            
##      widowed      13.2 20.7            
##   male = Male (%) 43.0 36.9 &lt;0.001</code></pre>
<div class="sourceCode" id="cb54"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb54-1"><a href="#cb54-1" aria-hidden="true" tabindex="-1"></a><span class="co">#using survey design</span></span>
<span id="cb54-2"><a href="#cb54-2" aria-hidden="true" tabindex="-1"></a>st1<span class="ot">&lt;-</span><span class="fu">svyCreateTableOne</span>(<span class="at">vars =</span> <span class="fu">c</span>(<span class="st">&quot;educ&quot;</span>, <span class="st">&quot;marst&quot;</span>, <span class="st">&quot;male&quot;</span>), <span class="at">strata =</span> <span class="st">&quot;badhealth&quot;</span>, <span class="at">test =</span> T, <span class="at">data =</span> des)</span>
<span id="cb54-3"><a href="#cb54-3" aria-hidden="true" tabindex="-1"></a><span class="co">#st1&lt;-print(st1, format=&quot;p&quot;)</span></span>
<span id="cb54-4"><a href="#cb54-4" aria-hidden="true" tabindex="-1"></a><span class="fu">print</span>(st1, <span class="at">format=</span><span class="st">&quot;p&quot;</span>)</span></code></pre></div>
<pre><code>##                  Stratified by badhealth
##                   0          1         p      test
##   n               12270182.9 2975288.5            
##   educ (%)                             &lt;0.001     
##      2hsgrad            23.3      30.8            
##      0Prim               6.1      16.7            
##      1somehs             6.6      13.4            
##      3somecol           32.3      26.4            
##      4colgrad           31.6      12.7            
##   marst (%)                            &lt;0.001     
##      married            52.9      46.8            
##      cohab               5.7       3.3            
##      divorced            9.5      14.4            
##      nm                 24.9      21.1            
##      separated           2.1       7.1            
##      widowed             4.9       7.3            
##   male = Male (%)       49.7      45.3  0.132</code></pre>
</div>
</div>
<div id="regression-example" class="section level2">
<h2>Regression example</h2>
<p>Next we apply this logic to a regression case. First we fit the OLS model for our BMI outcome using education and age as predictors:</p>
<div class="sourceCode" id="cb56"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb56-1"><a href="#cb56-1" aria-hidden="true" tabindex="-1"></a>fit1<span class="ot">&lt;-</span><span class="fu">lm</span>(bmi<span class="sc">~</span>educ<span class="sc">+</span>agec, <span class="at">data=</span>brfss_17)</span></code></pre></div>
<p>Next we incorporate case weights</p>
<div class="sourceCode" id="cb57"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb57-1"><a href="#cb57-1" aria-hidden="true" tabindex="-1"></a>fit2<span class="ot">&lt;-</span><span class="fu">lm</span>(bmi<span class="sc">~</span>educ<span class="sc">+</span>agec, <span class="at">data=</span>brfss_17, <span class="at">weights =</span> mmsawt)</span></code></pre></div>
<p>Now we will incorporate design effects as well:</p>
<div class="sourceCode" id="cb58"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb58-1"><a href="#cb58-1" aria-hidden="true" tabindex="-1"></a>fit3<span class="ot">&lt;-</span><span class="fu">svyglm</span>(bmi<span class="sc">~</span>educ<span class="sc">+</span>agec,des, <span class="at">family=</span>gaussian)</span></code></pre></div>
<p>Now I make a table to show the results of the three models:</p>
<div class="sourceCode" id="cb59"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb59-1"><a href="#cb59-1" aria-hidden="true" tabindex="-1"></a><span class="fu">stargazer</span>(fit1, fit2, fit3, <span class="at">style=</span><span class="st">&quot;demography&quot;</span>, <span class="at">type=</span><span class="st">&quot;html&quot;</span>,</span>
<span id="cb59-2"><a href="#cb59-2" aria-hidden="true" tabindex="-1"></a>          <span class="at">column.labels =</span> <span class="fu">c</span>(<span class="st">&quot;OLS&quot;</span>, <span class="st">&quot;Weights Only&quot;</span>, <span class="st">&quot;Survey Design&quot;</span>),</span>
<span id="cb59-3"><a href="#cb59-3" aria-hidden="true" tabindex="-1"></a>          <span class="at">title =</span> <span class="st">&quot;Regression models for BMI using survey data - BRFSS 2016&quot;</span>, </span>
<span id="cb59-4"><a href="#cb59-4" aria-hidden="true" tabindex="-1"></a>          <span class="at">covariate.labels=</span><span class="fu">c</span>(<span class="st">&quot;PrimarySchool&quot;</span>, <span class="st">&quot;SomeHS&quot;</span>, <span class="st">&quot;SomeColl&quot;</span>, <span class="st">&quot;CollGrad&quot;</span>, <span class="st">&quot;Age 24-39&quot;</span>,<span class="st">&quot;Age 39-59&quot;</span> ,<span class="st">&quot;Age 59-79&quot;</span>, <span class="st">&quot;Age 80+&quot;</span>), </span>
<span id="cb59-5"><a href="#cb59-5" aria-hidden="true" tabindex="-1"></a>          <span class="at">keep.stat=</span><span class="st">&quot;n&quot;</span>, <span class="at">model.names=</span>F, <span class="at">align=</span>T, <span class="at">ci=</span>T)</span></code></pre></div>
<table style="text-align:center">
<caption>
<strong>Regression models for BMI using survey data - BRFSS 2016</strong>
</caption>
<tr>
<td colspan="4" style="border-bottom: 1px solid black">
</td>
</tr>
<tr>
<td style="text-align:left">
</td>
<td colspan="3">
bmi
</td>
</tr>
<tr>
<td style="text-align:left">
</td>
<td>
OLS
</td>
<td>
Weights Only
</td>
<td>
Survey Design
</td>
</tr>
<tr>
<td style="text-align:left">
</td>
<td>
Model 1
</td>
<td>
Model 2
</td>
<td>
Model 3
</td>
</tr>
<tr>
<td colspan="4" style="border-bottom: 1px solid black">
</td>
</tr>
<tr>
<td style="text-align:left">
PrimarySchool
</td>
<td>
0.114
</td>
<td>
-0.521
</td>
<td>
-0.521
</td>
</tr>
<tr>
<td style="text-align:left">
</td>
<td>
(-0.671, 0.899)
</td>
<td>
(-1.088, 0.046)
</td>
<td>
(-1.798, 0.756)
</td>
</tr>
<tr>
<td style="text-align:left">
SomeHS
</td>
<td>
-0.006
</td>
<td>
-0.699<sup>*</sup>
</td>
<td>
-0.699
</td>
</tr>
<tr>
<td style="text-align:left">
</td>
<td>
(-0.687, 0.674)
</td>
<td>
(-1.266, -0.132)
</td>
<td>
(-2.080, 0.682)
</td>
</tr>
<tr>
<td style="text-align:left">
SomeColl
</td>
<td>
-0.456<sup>*</sup>
</td>
<td>
-1.255<sup>***</sup>
</td>
<td>
-1.255<sup>**</sup>
</td>
</tr>
<tr>
<td style="text-align:left">
</td>
<td>
(-0.860, -0.052)
</td>
<td>
(-1.619, -0.892)
</td>
<td>
(-2.094, -0.417)
</td>
</tr>
<tr>
<td style="text-align:left">
CollGrad
</td>
<td>
-1.972<sup>***</sup>
</td>
<td>
-2.463<sup>***</sup>
</td>
<td>
-2.463<sup>***</sup>
</td>
</tr>
<tr>
<td style="text-align:left">
</td>
<td>
(-2.342, -1.602)
</td>
<td>
(-2.839, -2.086)
</td>
<td>
(-3.228, -1.697)
</td>
</tr>
<tr>
<td style="text-align:left">
Age 24-39
</td>
<td>
2.943<sup>***</sup>
</td>
<td>
3.221<sup>***</sup>
</td>
<td>
3.221<sup>***</sup>
</td>
</tr>
<tr>
<td style="text-align:left">
</td>
<td>
(2.263, 3.624)
</td>
<td>
(2.771, 3.672)
</td>
<td>
(2.345, 4.097)
</td>
</tr>
<tr>
<td style="text-align:left">
Age 39-59
</td>
<td>
4.388<sup>***</sup>
</td>
<td>
4.745<sup>***</sup>
</td>
<td>
4.745<sup>***</sup>
</td>
</tr>
<tr>
<td style="text-align:left">
</td>
<td>
(3.742, 5.034)
</td>
<td>
(4.306, 5.183)
</td>
<td>
(3.868, 5.621)
</td>
</tr>
<tr>
<td style="text-align:left">
Age 59-79
</td>
<td>
3.476<sup>***</sup>
</td>
<td>
4.005<sup>***</sup>
</td>
<td>
4.005<sup>***</sup>
</td>
</tr>
<tr>
<td style="text-align:left">
</td>
<td>
(2.846, 4.107)
</td>
<td>
(3.529, 4.481)
</td>
<td>
(3.039, 4.971)
</td>
</tr>
<tr>
<td style="text-align:left">
Age 80+
</td>
<td>
0.810<sup>*</sup>
</td>
<td>
0.819
</td>
<td>
0.819
</td>
</tr>
<tr>
<td style="text-align:left">
</td>
<td>
(0.076, 1.543)
</td>
<td>
(-0.051, 1.688)
</td>
<td>
(-0.496, 2.133)
</td>
</tr>
<tr>
<td style="text-align:left">
Constant
</td>
<td>
26.045<sup>***</sup>
</td>
<td>
26.179<sup>***</sup>
</td>
<td>
26.179<sup>***</sup>
</td>
</tr>
<tr>
<td style="text-align:left">
</td>
<td>
(25.423, 26.667)
</td>
<td>
(25.761, 26.597)
</td>
<td>
(25.338, 27.019)
</td>
</tr>
<tr>
<td style="text-align:left">
<em>N</em>
</td>
<td>
7,840
</td>
<td>
7,840
</td>
<td>
7,840
</td>
</tr>
<tr>
<td colspan="4" style="border-bottom: 1px solid black">
</td>
</tr>
<tr>
<td colspan="4" style="text-align:left">
<sup><em></sup>p &lt; .05; <sup><strong></sup>p &lt; .01; <sup></strong></em></sup>p &lt; .001
</td>
</tr>
</table>
<p>Notice, the results for the education levels are much <em>less</em> significant than the were with either of the other two analysis. This is because those models had standard errors for the parameters that were too small. You see all the standard errors are larger and the T statistics are smaller.</p>
<p>Which shows the same <span class="math inline">\(\beta\)</span>’s between the survey design model and the weighted model but the standard errors are larger in the survey model, so the test statistics are more conservative (smaller t statistics).</p>
<p>While in this simple model, our overall interpretation of the effects do not change (positive effects of education, negative effects of age), it is entirely possible that they could once we include our survey design effects.</p>
<p>It may be informative to plot the results of the models to see how different the coefficients are from one another:</p>
<div class="sourceCode" id="cb60"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb60-1"><a href="#cb60-1" aria-hidden="true" tabindex="-1"></a><span class="fu">library</span>(ggplot2)</span>
<span id="cb60-2"><a href="#cb60-2" aria-hidden="true" tabindex="-1"></a><span class="fu">library</span>(dplyr)</span>
<span id="cb60-3"><a href="#cb60-3" aria-hidden="true" tabindex="-1"></a>coefs<span class="ot">&lt;-</span><span class="fu">data.frame</span>(<span class="at">coefs=</span><span class="fu">c</span>(<span class="fu">coef</span>(fit1)[<span class="sc">-</span><span class="dv">1</span>], <span class="fu">coef</span>(fit3)[<span class="sc">-</span><span class="dv">1</span>]),</span>
<span id="cb60-4"><a href="#cb60-4" aria-hidden="true" tabindex="-1"></a>                  <span class="at">mod=</span><span class="fu">c</span>(<span class="fu">rep</span>(<span class="st">&quot;Non Survey Model&quot;</span>, <span class="dv">8</span>),<span class="fu">rep</span>(<span class="st">&quot;Survey Model&quot;</span>, <span class="dv">8</span>)),</span>
<span id="cb60-5"><a href="#cb60-5" aria-hidden="true" tabindex="-1"></a>                  <span class="at">effect=</span><span class="fu">rep</span>(<span class="fu">names</span>(<span class="fu">coef</span>(fit1)[<span class="sc">-</span><span class="dv">1</span>]), <span class="dv">2</span>))</span>
<span id="cb60-6"><a href="#cb60-6" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb60-7"><a href="#cb60-7" aria-hidden="true" tabindex="-1"></a>coefs<span class="sc">%&gt;%</span></span>
<span id="cb60-8"><a href="#cb60-8" aria-hidden="true" tabindex="-1"></a>  <span class="fu">ggplot</span>()<span class="sc">+</span></span>
<span id="cb60-9"><a href="#cb60-9" aria-hidden="true" tabindex="-1"></a>  <span class="fu">geom_point</span>(<span class="fu">aes</span>( <span class="at">x=</span>effect, <span class="at">y=</span>coefs, <span class="at">group=</span>effect,<span class="at">color=</span>effect, <span class="at">shape=</span>mod),</span>
<span id="cb60-10"><a href="#cb60-10" aria-hidden="true" tabindex="-1"></a>             <span class="at">position=</span><span class="fu">position_jitterdodge</span>(<span class="at">jitter.width =</span> <span class="dv">1</span>),</span>
<span id="cb60-11"><a href="#cb60-11" aria-hidden="true" tabindex="-1"></a>             <span class="at">size=</span><span class="dv">2</span>)<span class="sc">+</span></span>
<span id="cb60-12"><a href="#cb60-12" aria-hidden="true" tabindex="-1"></a>  <span class="fu">ylab</span>(<span class="st">&quot;Regression Coefficient&quot;</span>)<span class="sc">+</span></span>
<span id="cb60-13"><a href="#cb60-13" aria-hidden="true" tabindex="-1"></a>  <span class="fu">xlab</span>(<span class="st">&quot;Beta&quot;</span>)<span class="sc">+</span></span>
<span id="cb60-14"><a href="#cb60-14" aria-hidden="true" tabindex="-1"></a>  <span class="fu">geom_abline</span>(<span class="at">intercept =</span> <span class="dv">0</span>, <span class="at">slope=</span><span class="dv">0</span>)<span class="sc">+</span></span>
<span id="cb60-15"><a href="#cb60-15" aria-hidden="true" tabindex="-1"></a>  <span class="fu">theme</span>(<span class="at">axis.text.x =</span> <span class="fu">element_text</span>(<span class="at">angle =</span> <span class="dv">45</span>, <span class="at">hjust =</span> <span class="dv">1</span>))<span class="sc">+</span></span>
<span id="cb60-16"><a href="#cb60-16" aria-hidden="true" tabindex="-1"></a>  <span class="fu">ggtitle</span>(<span class="at">label =</span> <span class="st">&quot;Comparison of Survey and Non-Survey Regression effects&quot;</span>)</span></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/EX2_surveystats_files/figure-html/unnamed-chunk-21-1.png" /><!-- --></p>
<p>Which shows us that the betas are similar but have some differences between the two models.</p>
</div>
<div id="creating-survey-estimates-for-places" class="section level2">
<h2>Creating Survey estimates for places</h2>
<p>One of the coolest ways to use the BRFSS is to calculate estimates for places, and by demographic characteristics withing places. Again, we use <code>svyby()</code> to do this, but now we calculate obesity rates by sex within cities.</p>
<div class="sourceCode" id="cb61"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb61-1"><a href="#cb61-1" aria-hidden="true" tabindex="-1"></a>citytab<span class="ot">&lt;-</span><span class="fu">svyby</span>(<span class="sc">~</span>obese, <span class="sc">~</span>mmsaname, <span class="at">design=</span>des,<span class="at">FUN =</span> svymean, <span class="at">na.rm=</span>T )</span>
<span id="cb61-2"><a href="#cb61-2" aria-hidden="true" tabindex="-1"></a>knitr<span class="sc">::</span><span class="fu">kable</span>(citytab, <span class="at">type=</span><span class="st">&quot;html&quot;</span>, <span class="at">digits=</span><span class="dv">3</span>,<span class="at">caption =</span> <span class="st">&quot;Obesity Estimats for TX MSAs&quot;</span>)</span></code></pre></div>
<table>
<caption>Obesity Estimats for TX MSAs</caption>
<colgroup>
<col width="45%" />
<col width="45%" />
<col width="4%" />
<col width="4%" />
</colgroup>
<thead>
<tr class="header">
<th align="left"></th>
<th align="left">mmsaname</th>
<th align="right">obese</th>
<th align="right">se</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Austin-Round Rock, TX, Metropolitan Statistical Area</td>
<td align="left">Austin-Round Rock, TX, Metropolitan Statistical Area</td>
<td align="right">0.294</td>
<td align="right">0.021</td>
</tr>
<tr class="even">
<td align="left">College Station-Bryan, TX, Metropolitan Statistical Area</td>
<td align="left">College Station-Bryan, TX, Metropolitan Statistical Area</td>
<td align="right">0.308</td>
<td align="right">0.052</td>
</tr>
<tr class="odd">
<td align="left">Corpus Christi, TX, Metropolitan Statistical Area</td>
<td align="left">Corpus Christi, TX, Metropolitan Statistical Area</td>
<td align="right">0.362</td>
<td align="right">0.034</td>
</tr>
<tr class="even">
<td align="left">Dallas-Plano-Irving, TX, Metropolitan Division</td>
<td align="left">Dallas-Plano-Irving, TX, Metropolitan Division</td>
<td align="right">0.286</td>
<td align="right">0.025</td>
</tr>
<tr class="odd">
<td align="left">El Paso, TX, Metropolitan Statistical Area</td>
<td align="left">El Paso, TX, Metropolitan Statistical Area</td>
<td align="right">0.347</td>
<td align="right">0.029</td>
</tr>
<tr class="even">
<td align="left">Fort Worth-Arlington, TX, Metropolitan Division</td>
<td align="left">Fort Worth-Arlington, TX, Metropolitan Division</td>
<td align="right">0.367</td>
<td align="right">0.028</td>
</tr>
<tr class="odd">
<td align="left">Houston-The Woodlands-Sugar Land, TX, Metropolitan Statistical Area</td>
<td align="left">Houston-The Woodlands-Sugar Land, TX, Metropolitan Statistical Area</td>
<td align="right">0.307</td>
<td align="right">0.022</td>
</tr>
<tr class="even">
<td align="left">San Antonio-New Braunfels, TX, Metropolitan Statistical Area</td>
<td align="left">San Antonio-New Braunfels, TX, Metropolitan Statistical Area</td>
<td align="right">0.308</td>
<td align="right">0.033</td>
</tr>
<tr class="odd">
<td align="left">Wichita Falls, TX, Metropolitan Statistical Area</td>
<td align="left">Wichita Falls, TX, Metropolitan Statistical Area</td>
<td align="right">0.425</td>
<td align="right">0.055</td>
</tr>
</tbody>
</table>
<div id="using-srvyr" class="section level3">
<h3>Using srvyr</h3>
<p>There’s a new package called <code>srvyr</code> that incorporates the survey analysis stuff into the <code>dplyr</code> universe:</p>
<div class="sourceCode" id="cb62"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb62-1"><a href="#cb62-1" aria-hidden="true" tabindex="-1"></a><span class="fu">library</span>(srvyr)</span>
<span id="cb62-2"><a href="#cb62-2" aria-hidden="true" tabindex="-1"></a>brfsurv<span class="ot">&lt;-</span>brfss_17<span class="sc">%&gt;%</span></span>
<span id="cb62-3"><a href="#cb62-3" aria-hidden="true" tabindex="-1"></a>  <span class="fu">as_survey_design</span>(<span class="dv">1</span>,<span class="at">strata=</span>ststr,<span class="at">weights=</span>mmsawt )</span>
<span id="cb62-4"><a href="#cb62-4" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb62-5"><a href="#cb62-5" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb62-6"><a href="#cb62-6" aria-hidden="true" tabindex="-1"></a>brfsurv<span class="sc">%&gt;%</span></span>
<span id="cb62-7"><a href="#cb62-7" aria-hidden="true" tabindex="-1"></a>  <span class="fu">group_by</span>(mmsaname)<span class="sc">%&gt;%</span></span>
<span id="cb62-8"><a href="#cb62-8" aria-hidden="true" tabindex="-1"></a>    <span class="fu">summarise</span>(<span class="at">obprev =</span> <span class="fu">survey_mean</span>(obese, <span class="at">na.rm=</span>T))</span></code></pre></div>
</div>
</div>
<div id="replicate-weights" class="section level2">
<h2>Replicate Weights</h2>
<p>If your dataset comes with <em>replicate weights</em>, you have to specify the survey design slightly differently. Here is an example using the IPUMS CPS data. For this data, you can get information <a href="https://cps.ipums.org/cps/repwt.shtml">here</a>, but you must consult your specific data source for the appropriate information for your data.</p>
<div class="sourceCode" id="cb63"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb63-1"><a href="#cb63-1" aria-hidden="true" tabindex="-1"></a><span class="fu">load</span>(<span class="fu">url</span>(<span class="st">&quot;https://github.com/coreysparks/data/blob/master/cpsmar10tx.Rdata?raw=true&quot;</span>))</span>
<span id="cb63-2"><a href="#cb63-2" aria-hidden="true" tabindex="-1"></a><span class="fu">names</span>(cpsmar10tx)</span></code></pre></div>
<pre><code>##   [1] &quot;year&quot;     &quot;serial&quot;   &quot;hwtsupp&quot;  &quot;repwt&quot;    &quot;statefip&quot; &quot;metarea&quot; 
##   [7] &quot;foodstmp&quot; &quot;REPWT1&quot;   &quot;REPWT2&quot;   &quot;REPWT3&quot;   &quot;REPWT4&quot;   &quot;REPWT5&quot;  
##  [13] &quot;REPWT6&quot;   &quot;REPWT7&quot;   &quot;REPWT8&quot;   &quot;REPWT9&quot;   &quot;REPWT10&quot;  &quot;REPWT11&quot; 
##  [19] &quot;REPWT12&quot;  &quot;REPWT13&quot;  &quot;REPWT14&quot;  &quot;REPWT15&quot;  &quot;REPWT16&quot;  &quot;REPWT17&quot; 
##  [25] &quot;REPWT18&quot;  &quot;REPWT19&quot;  &quot;REPWT20&quot;  &quot;REPWT21&quot;  &quot;REPWT22&quot;  &quot;REPWT23&quot; 
##  [31] &quot;REPWT24&quot;  &quot;REPWT25&quot;  &quot;REPWT26&quot;  &quot;REPWT27&quot;  &quot;REPWT28&quot;  &quot;REPWT29&quot; 
##  [37] &quot;REPWT30&quot;  &quot;REPWT31&quot;  &quot;REPWT32&quot;  &quot;REPWT33&quot;  &quot;REPWT34&quot;  &quot;REPWT35&quot; 
##  [43] &quot;REPWT36&quot;  &quot;REPWT37&quot;  &quot;REPWT38&quot;  &quot;REPWT39&quot;  &quot;REPWT40&quot;  &quot;REPWT41&quot; 
##  [49] &quot;REPWT42&quot;  &quot;REPWT43&quot;  &quot;REPWT44&quot;  &quot;REPWT45&quot;  &quot;REPWT46&quot;  &quot;REPWT47&quot; 
##  [55] &quot;REPWT48&quot;  &quot;REPWT49&quot;  &quot;REPWT50&quot;  &quot;REPWT51&quot;  &quot;REPWT52&quot;  &quot;REPWT53&quot; 
##  [61] &quot;REPWT54&quot;  &quot;REPWT55&quot;  &quot;REPWT56&quot;  &quot;REPWT57&quot;  &quot;REPWT58&quot;  &quot;REPWT59&quot; 
##  [67] &quot;REPWT60&quot;  &quot;REPWT61&quot;  &quot;REPWT62&quot;  &quot;REPWT63&quot;  &quot;REPWT64&quot;  &quot;REPWT65&quot; 
##  [73] &quot;REPWT66&quot;  &quot;REPWT67&quot;  &quot;REPWT68&quot;  &quot;REPWT69&quot;  &quot;REPWT70&quot;  &quot;REPWT71&quot; 
##  [79] &quot;REPWT72&quot;  &quot;REPWT73&quot;  &quot;REPWT74&quot;  &quot;REPWT75&quot;  &quot;REPWT76&quot;  &quot;REPWT77&quot; 
##  [85] &quot;REPWT78&quot;  &quot;REPWT79&quot;  &quot;REPWT80&quot;  &quot;REPWT81&quot;  &quot;REPWT82&quot;  &quot;REPWT83&quot; 
##  [91] &quot;REPWT84&quot;  &quot;REPWT85&quot;  &quot;REPWT86&quot;  &quot;REPWT87&quot;  &quot;REPWT88&quot;  &quot;REPWT89&quot; 
##  [97] &quot;REPWT90&quot;  &quot;REPWT91&quot;  &quot;REPWT92&quot;  &quot;REPWT93&quot;  &quot;REPWT94&quot;  &quot;REPWT95&quot; 
## [103] &quot;REPWT96&quot;  &quot;REPWT97&quot;  &quot;REPWT98&quot;  &quot;REPWT99&quot;  &quot;REPWT100&quot; &quot;REPWT101&quot;
## [109] &quot;REPWT102&quot; &quot;REPWT103&quot; &quot;REPWT104&quot; &quot;REPWT105&quot; &quot;REPWT106&quot; &quot;REPWT107&quot;
## [115] &quot;REPWT108&quot; &quot;REPWT109&quot; &quot;REPWT110&quot; &quot;REPWT111&quot; &quot;REPWT112&quot; &quot;REPWT113&quot;
## [121] &quot;REPWT114&quot; &quot;REPWT115&quot; &quot;REPWT116&quot; &quot;REPWT117&quot; &quot;REPWT118&quot; &quot;REPWT119&quot;
## [127] &quot;REPWT120&quot; &quot;REPWT121&quot; &quot;REPWT122&quot; &quot;REPWT123&quot; &quot;REPWT124&quot; &quot;REPWT125&quot;
## [133] &quot;REPWT126&quot; &quot;REPWT127&quot; &quot;REPWT128&quot; &quot;REPWT129&quot; &quot;REPWT130&quot; &quot;REPWT131&quot;
## [139] &quot;REPWT132&quot; &quot;REPWT133&quot; &quot;REPWT134&quot; &quot;REPWT135&quot; &quot;REPWT136&quot; &quot;REPWT137&quot;
## [145] &quot;REPWT138&quot; &quot;REPWT139&quot; &quot;REPWT140&quot; &quot;REPWT141&quot; &quot;REPWT142&quot; &quot;REPWT143&quot;
## [151] &quot;REPWT144&quot; &quot;REPWT145&quot; &quot;REPWT146&quot; &quot;REPWT147&quot; &quot;REPWT148&quot; &quot;REPWT149&quot;
## [157] &quot;REPWT150&quot; &quot;REPWT151&quot; &quot;REPWT152&quot; &quot;REPWT153&quot; &quot;REPWT154&quot; &quot;REPWT155&quot;
## [163] &quot;REPWT156&quot; &quot;REPWT157&quot; &quot;REPWT158&quot; &quot;REPWT159&quot; &quot;REPWT160&quot; &quot;month&quot;   
## [169] &quot;pernum&quot;   &quot;wtsupp&quot;   &quot;relate&quot;   &quot;age&quot;      &quot;sex&quot;      &quot;race&quot;    
## [175] &quot;marst&quot;    &quot;offpov&quot;   &quot;MIGRATE1&quot;</code></pre>
<p>So we see the replicate weights are in columns 8 through 167 in the data</p>
<div class="sourceCode" id="cb65"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb65-1"><a href="#cb65-1" aria-hidden="true" tabindex="-1"></a><span class="co">#simple binary outcome</span></span>
<span id="cb65-2"><a href="#cb65-2" aria-hidden="true" tabindex="-1"></a>cpsmar10tx<span class="sc">$</span>poverty<span class="ot">&lt;-</span><span class="fu">ifelse</span>(cpsmar10tx<span class="sc">$</span>offpov<span class="sc">==</span><span class="dv">1</span>,<span class="dv">1</span>,<span class="dv">0</span>)</span>
<span id="cb65-3"><a href="#cb65-3" aria-hidden="true" tabindex="-1"></a></span>
<span id="cb65-4"><a href="#cb65-4" aria-hidden="true" tabindex="-1"></a><span class="co"># Replicate weight design - I got these details from the data source, you should too</span></span>
<span id="cb65-5"><a href="#cb65-5" aria-hidden="true" tabindex="-1"></a>des2<span class="ot">&lt;-</span><span class="fu">svrepdesign</span>( <span class="at">data =</span> cpsmar10tx,<span class="at">repweights =</span> cpsmar10tx[, <span class="fu">c</span>(<span class="dv">8</span><span class="sc">:</span><span class="dv">167</span>)]  ,</span>
<span id="cb65-6"><a href="#cb65-6" aria-hidden="true" tabindex="-1"></a>                   <span class="at">weights =</span> <span class="sc">~</span>wtsupp ,</span>
<span id="cb65-7"><a href="#cb65-7" aria-hidden="true" tabindex="-1"></a>                   <span class="at">type=</span><span class="st">&quot;JK1&quot;</span>,</span>
<span id="cb65-8"><a href="#cb65-8" aria-hidden="true" tabindex="-1"></a>                   <span class="at">scale=</span>.<span class="dv">025</span>)</span>
<span id="cb65-9"><a href="#cb65-9" aria-hidden="true" tabindex="-1"></a>des2</span></code></pre></div>
<pre><code>## Call: svrepdesign.default(data = cpsmar10tx, repweights = cpsmar10tx[, 
##     c(8:167)], weights = ~wtsupp, type = &quot;JK1&quot;, scale = 0.025)
## Unstratified cluster jacknife (JK1) with 160 replicates.</code></pre>
<div class="sourceCode" id="cb67"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb67-1"><a href="#cb67-1" aria-hidden="true" tabindex="-1"></a><span class="co">#Without design</span></span>
<span id="cb67-2"><a href="#cb67-2" aria-hidden="true" tabindex="-1"></a><span class="fu">prop.table</span>(<span class="fu">table</span>(cpsmar10tx<span class="sc">$</span>poverty))</span></code></pre></div>
<pre><code>## 
##         0         1 
## 0.8374617 0.1625383</code></pre>
<div class="sourceCode" id="cb69"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb69-1"><a href="#cb69-1" aria-hidden="true" tabindex="-1"></a><span class="co">#with design</span></span>
<span id="cb69-2"><a href="#cb69-2" aria-hidden="true" tabindex="-1"></a><span class="fu">prop.table</span>(<span class="fu">svytable</span>(<span class="sc">~</span>poverty, <span class="at">design =</span> des2))</span></code></pre></div>
<pre><code>## poverty
##         0         1 
## 0.8481106 0.1518894</code></pre>
<div class="sourceCode" id="cb71"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb71-1"><a href="#cb71-1" aria-hidden="true" tabindex="-1"></a><span class="co">#Again, using the mean</span></span>
<span id="cb71-2"><a href="#cb71-2" aria-hidden="true" tabindex="-1"></a><span class="fu">mean</span>(cpsmar10tx<span class="sc">$</span>poverty)</span></code></pre></div>
<pre><code>## [1] 0.1625383</code></pre>
<div class="sourceCode" id="cb73"><pre class="sourceCode r"><code class="sourceCode r"><span id="cb73-1"><a href="#cb73-1" aria-hidden="true" tabindex="-1"></a><span class="co">#Using the design. This would be an official estimate of poverty in TX in 2010:</span></span>
<span id="cb73-2"><a href="#cb73-2" aria-hidden="true" tabindex="-1"></a><span class="fu">svymean</span>(<span class="sc">~</span>poverty, <span class="at">design=</span>des2)</span></code></pre></div>
<pre><code>##            mean    SE
## poverty 0.15189 0.007</code></pre>
</div>
</div>
</section>

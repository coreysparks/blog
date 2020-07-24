---
title: "Survey Statistics using BRFSS data"

author: "Corey Sparks, PhD"
date: "February 3, 2020"
layout: post
---


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
<div class="sourceCode" id="cb1"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb1-1" data-line-number="1"><span class="kw">library</span>(htmlTable)</a>
<a class="sourceLine" id="cb1-2" data-line-number="2">y&lt;-<span class="kw">c</span>(<span class="dv">4</span>,<span class="dv">2</span>,<span class="dv">1</span>,<span class="dv">5</span>,<span class="dv">2</span>)</a>
<a class="sourceLine" id="cb1-3" data-line-number="3">wt&lt;-<span class="kw">c</span>(<span class="dv">1</span>,<span class="dv">2</span>,<span class="dv">4</span>,<span class="dv">1</span>,<span class="dv">2</span>)</a>
<a class="sourceLine" id="cb1-4" data-line-number="4">dat&lt;-<span class="kw">data.frame</span>(<span class="dt">y=</span>y, <span class="dt">weight=</span>wt)</a>
<a class="sourceLine" id="cb1-5" data-line-number="5"><span class="kw">htmlTable</span>(dat)</a></code></pre></div>
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
<div class="sourceCode" id="cb2"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb2-1" data-line-number="1"><span class="kw">mean</span>(dat<span class="op">$</span>y)</a></code></pre></div>
<pre><code>## [1] 2.8</code></pre>
<p>Weighted sample mean</p>
<div class="sourceCode" id="cb4"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb4-1" data-line-number="1"><span class="kw">library</span>(questionr)</a>
<a class="sourceLine" id="cb4-2" data-line-number="2"><span class="kw">wtd.mean</span>(<span class="dt">x=</span>dat<span class="op">$</span>y, <span class="dt">weights=</span>dat<span class="op">$</span>weight)</a></code></pre></div>
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
<img src="/media/ozd504/extra/gdrive/classes/dem7473/lectures//Rpresentations/multistage.png" alt="Multistage Sampling - From Kawachi and Berkman, 2003" />
<p class="caption">Multistage Sampling - From Kawachi and Berkman, 2003</p>
</div>
<p>A second feature of survey data we often want to account for is differential respondent weighting. This means that each respondent is given a weight to represent how common that particular respondent is within the population. This reflects the differenital probability of sampling based on respondent characteristics.</p>
<p>As demographers, we are also often interested in making inference for the population, not just the sample, so our results must be generalizable to the population at large. Sample weights are used in the process as well.</p>
<p>When such data are analyzed, we must take into account this nesting structure (sample design) as well as the respondent sample weight in order to make valid estimates of <strong>ANY</strong> statistical parameter. If we do not account for design, the parameter standard errors will be incorrect, and if we do not account for weighting, the parameters themselves will be incorrect and biased.</p>
<p>In general there are typically three things we need to find in our survey data codebooks: The sample strata identifier, the sample primary sampling unit identifier (often called a cluster identifier) and the respondent survey weight. These will typically have one of these names and should be easily identifiable in the codebook.</p>
<p>Statistical software will have special routines for analyzing these types of data and you must be aware that the diversity of statistical routines that generally exists will be lower for analyzing complex survey data, and some forms of analysis <em>may not be available!</em></p>
<p>See <a href="https://onlinelibrary.wiley.com/doi/book/10.1002/9780470580066">Thomas Lumley’s Book</a> on this!</p>
<p>Below I illustrate the use of survey characteristics when conducting descriptive analysis of a survey data set and a linear regression model estimated from that data. For this example I am using 2016 CDC Behavioral Risk Factor Surveillance System (BRFSS) SMART metro area survey data. <a href="https://www.cdc.gov/brfss/smart/smart_2016.html">Link</a></p>
<div class="sourceCode" id="cb6"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb6-1" data-line-number="1"><span class="co">#load brfss</span></a>
<a class="sourceLine" id="cb6-2" data-line-number="2"><span class="kw">library</span>(car)</a></code></pre></div>
<pre><code>## Loading required package: carData</code></pre>
<div class="sourceCode" id="cb8"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb8-1" data-line-number="1"><span class="kw">library</span>(stargazer)</a></code></pre></div>
<pre><code>## 
## Please cite as:</code></pre>
<pre><code>##  Hlavac, Marek (2018). stargazer: Well-Formatted Regression and Summary Statistics Tables.</code></pre>
<pre><code>##  R package version 5.2.2. https://CRAN.R-project.org/package=stargazer</code></pre>
<div class="sourceCode" id="cb12"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb12-1" data-line-number="1"><span class="kw">library</span>(survey)</a></code></pre></div>
<pre><code>## Loading required package: grid</code></pre>
<pre><code>## Loading required package: Matrix</code></pre>
<pre><code>## Loading required package: survival</code></pre>
<pre><code>## 
## Attaching package: &#39;survey&#39;</code></pre>
<pre><code>## The following object is masked from &#39;package:graphics&#39;:
## 
##     dotchart</code></pre>
<div class="sourceCode" id="cb18"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb18-1" data-line-number="1"><span class="kw">library</span>(questionr)</a>
<a class="sourceLine" id="cb18-2" data-line-number="2"><span class="kw">library</span>(dplyr)</a></code></pre></div>
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
<div class="sourceCode" id="cb23"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb23-1" data-line-number="1"><span class="kw">load</span>(<span class="kw">url</span>(<span class="st">&quot;https://github.com/coreysparks/data/blob/master/brfss_2017.Rdata?raw=true&quot;</span>))</a></code></pre></div>
<div id="recoding-of-variables" class="section level3">
<h3>Recoding of variables</h3>
<p>Be sure to always check your codebooks!</p>
<div class="sourceCode" id="cb24"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb24-1" data-line-number="1"><span class="co">#The names in the data are very ugly, so I make them less ugly</span></a>
<a class="sourceLine" id="cb24-2" data-line-number="2">nams&lt;-<span class="kw">names</span>(brfss_<span class="dv">17</span>)</a>
<a class="sourceLine" id="cb24-3" data-line-number="3"><span class="kw">head</span>(nams, <span class="dt">n=</span><span class="dv">10</span>)</a></code></pre></div>
<pre><code>##  [1] &quot;dispcode&quot; &quot;statere1&quot; &quot;safetime&quot; &quot;hhadult&quot;  &quot;genhlth&quot;  &quot;physhlth&quot;
##  [7] &quot;menthlth&quot; &quot;poorhlth&quot; &quot;hlthpln1&quot; &quot;persdoc2&quot;</code></pre>
<div class="sourceCode" id="cb26"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb26-1" data-line-number="1"><span class="co">#we see some names are lower case, some are upper and some have a little _ in the first position. This is a nightmare.</span></a>
<a class="sourceLine" id="cb26-2" data-line-number="2">newnames&lt;-<span class="kw">tolower</span>(<span class="kw">gsub</span>(<span class="dt">pattern =</span> <span class="st">&quot;_&quot;</span>,<span class="dt">replacement =</span>  <span class="st">&quot;&quot;</span>,<span class="dt">x =</span>  nams))</a>
<a class="sourceLine" id="cb26-3" data-line-number="3"><span class="kw">names</span>(brfss_<span class="dv">17</span>)&lt;-newnames</a>
<a class="sourceLine" id="cb26-4" data-line-number="4"></a>
<a class="sourceLine" id="cb26-5" data-line-number="5"><span class="co">#Poor or fair self rated health</span></a>
<a class="sourceLine" id="cb26-6" data-line-number="6">brfss_<span class="dv">17</span><span class="op">$</span>badhealth&lt;-<span class="kw">Recode</span>(brfss_<span class="dv">17</span><span class="op">$</span>genhlth, <span class="dt">recodes=</span><span class="st">&quot;4:5=1; 1:3=0; else=NA&quot;</span>)</a>
<a class="sourceLine" id="cb26-7" data-line-number="7"></a>
<a class="sourceLine" id="cb26-8" data-line-number="8"><span class="co">#sex</span></a>
<a class="sourceLine" id="cb26-9" data-line-number="9">brfss_<span class="dv">17</span><span class="op">$</span>male&lt;-<span class="kw">as.factor</span>(<span class="kw">ifelse</span>(brfss_<span class="dv">17</span><span class="op">$</span>sex<span class="op">==</span><span class="dv">1</span>, <span class="st">&quot;Male&quot;</span>, <span class="st">&quot;Female&quot;</span>))</a>
<a class="sourceLine" id="cb26-10" data-line-number="10"></a>
<a class="sourceLine" id="cb26-11" data-line-number="11"><span class="co">#race/ethnicity</span></a>
<a class="sourceLine" id="cb26-12" data-line-number="12">brfss_<span class="dv">17</span><span class="op">$</span>black&lt;-<span class="kw">Recode</span>(brfss_<span class="dv">17</span><span class="op">$</span>racegr3, <span class="dt">recodes=</span><span class="st">&quot;2=1; 9=NA; else=0&quot;</span>)</a>
<a class="sourceLine" id="cb26-13" data-line-number="13">brfss_<span class="dv">17</span><span class="op">$</span>white&lt;-<span class="kw">Recode</span>(brfss_<span class="dv">17</span><span class="op">$</span>racegr3, <span class="dt">recodes=</span><span class="st">&quot;1=1; 9=NA; else=0&quot;</span>)</a>
<a class="sourceLine" id="cb26-14" data-line-number="14">brfss_<span class="dv">17</span><span class="op">$</span>other&lt;-<span class="kw">Recode</span>(brfss_<span class="dv">17</span><span class="op">$</span>racegr3, <span class="dt">recodes=</span><span class="st">&quot;3:4=1; 9=NA; else=0&quot;</span>)</a>
<a class="sourceLine" id="cb26-15" data-line-number="15">brfss_<span class="dv">17</span><span class="op">$</span>hispanic&lt;-<span class="kw">Recode</span>(brfss_<span class="dv">17</span><span class="op">$</span>racegr3, <span class="dt">recodes=</span><span class="st">&quot;5=1; 9=NA; else=0&quot;</span>)</a>
<a class="sourceLine" id="cb26-16" data-line-number="16"></a>
<a class="sourceLine" id="cb26-17" data-line-number="17"><span class="co">#insurance</span></a>
<a class="sourceLine" id="cb26-18" data-line-number="18">brfss_<span class="dv">17</span><span class="op">$</span>ins&lt;-<span class="kw">Recode</span>(brfss_<span class="dv">17</span><span class="op">$</span>hlthpln1, <span class="dt">recodes =</span><span class="st">&quot;7:9=NA; 1=1;2=0&quot;</span>)</a>
<a class="sourceLine" id="cb26-19" data-line-number="19"></a>
<a class="sourceLine" id="cb26-20" data-line-number="20"><span class="co">#income grouping</span></a>
<a class="sourceLine" id="cb26-21" data-line-number="21">brfss_<span class="dv">17</span><span class="op">$</span>inc&lt;-<span class="kw">ifelse</span>(brfss_<span class="dv">17</span><span class="op">$</span>incomg<span class="op">==</span><span class="dv">9</span>, <span class="ot">NA</span>, brfss_<span class="dv">17</span><span class="op">$</span>incomg)</a>
<a class="sourceLine" id="cb26-22" data-line-number="22"></a>
<a class="sourceLine" id="cb26-23" data-line-number="23"><span class="co">#education level</span></a>
<a class="sourceLine" id="cb26-24" data-line-number="24">brfss_<span class="dv">17</span><span class="op">$</span>educ&lt;-<span class="kw">Recode</span>(brfss_<span class="dv">17</span><span class="op">$</span>educa, <span class="dt">recodes=</span><span class="st">&quot;1:2=&#39;0Prim&#39;; 3=&#39;1somehs&#39;; 4=&#39;2hsgrad&#39;; 5=&#39;3somecol&#39;; 6=&#39;4colgrad&#39;;9=NA&quot;</span>, <span class="dt">as.factor=</span>T)</a>
<a class="sourceLine" id="cb26-25" data-line-number="25">brfss_<span class="dv">17</span><span class="op">$</span>educ&lt;-<span class="kw">relevel</span>(brfss_<span class="dv">17</span><span class="op">$</span>educ, <span class="dt">ref=</span><span class="st">&#39;2hsgrad&#39;</span>)</a>
<a class="sourceLine" id="cb26-26" data-line-number="26"></a>
<a class="sourceLine" id="cb26-27" data-line-number="27"><span class="co">#employment</span></a>
<a class="sourceLine" id="cb26-28" data-line-number="28">brfss_<span class="dv">17</span><span class="op">$</span>employ&lt;-<span class="kw">Recode</span>(brfss_<span class="dv">17</span><span class="op">$</span>employ1, <span class="dt">recodes=</span><span class="st">&quot;1:2=&#39;Employed&#39;; 2:6=&#39;nilf&#39;; 7=&#39;retired&#39;; 8=&#39;unable&#39;; else=NA&quot;</span>, <span class="dt">as.factor=</span>T)</a>
<a class="sourceLine" id="cb26-29" data-line-number="29">brfss_<span class="dv">17</span><span class="op">$</span>employ&lt;-<span class="kw">relevel</span>(brfss_<span class="dv">17</span><span class="op">$</span>employ, <span class="dt">ref=</span><span class="st">&#39;Employed&#39;</span>)</a>
<a class="sourceLine" id="cb26-30" data-line-number="30"></a>
<a class="sourceLine" id="cb26-31" data-line-number="31"><span class="co">#marital status</span></a>
<a class="sourceLine" id="cb26-32" data-line-number="32">brfss_<span class="dv">17</span><span class="op">$</span>marst&lt;-<span class="kw">Recode</span>(brfss_<span class="dv">17</span><span class="op">$</span>marital, <span class="dt">recodes=</span><span class="st">&quot;1=&#39;married&#39;; 2=&#39;divorced&#39;; 3=&#39;widowed&#39;; 4=&#39;separated&#39;; 5=&#39;nm&#39;;6=&#39;cohab&#39;; else=NA&quot;</span>, <span class="dt">as.factor=</span>T)</a>
<a class="sourceLine" id="cb26-33" data-line-number="33">brfss_<span class="dv">17</span><span class="op">$</span>marst&lt;-<span class="kw">relevel</span>(brfss_<span class="dv">17</span><span class="op">$</span>marst, <span class="dt">ref=</span><span class="st">&#39;married&#39;</span>)</a>
<a class="sourceLine" id="cb26-34" data-line-number="34"></a>
<a class="sourceLine" id="cb26-35" data-line-number="35"><span class="co">#Age cut into intervals</span></a>
<a class="sourceLine" id="cb26-36" data-line-number="36">brfss_<span class="dv">17</span><span class="op">$</span>agec&lt;-<span class="kw">cut</span>(brfss_<span class="dv">17</span><span class="op">$</span>age80, <span class="dt">breaks=</span><span class="kw">c</span>(<span class="dv">0</span>,<span class="dv">24</span>,<span class="dv">39</span>,<span class="dv">59</span>,<span class="dv">79</span>,<span class="dv">99</span>))</a>
<a class="sourceLine" id="cb26-37" data-line-number="37"></a>
<a class="sourceLine" id="cb26-38" data-line-number="38"><span class="co">#BMI, in the brfss_17a the bmi variable has 2 implied decimal places, so we must divide by 100 to get real bmi&#39;s</span></a>
<a class="sourceLine" id="cb26-39" data-line-number="39"></a>
<a class="sourceLine" id="cb26-40" data-line-number="40">brfss_<span class="dv">17</span><span class="op">$</span>bmi&lt;-brfss_<span class="dv">17</span><span class="op">$</span>bmi5<span class="op">/</span><span class="dv">100</span></a>
<a class="sourceLine" id="cb26-41" data-line-number="41">brfss_<span class="dv">17</span><span class="op">$</span>obese&lt;-<span class="kw">ifelse</span>(brfss_<span class="dv">17</span><span class="op">$</span>bmi<span class="op">&gt;=</span><span class="dv">30</span>, <span class="dv">1</span>, <span class="dv">0</span>)</a></code></pre></div>
</div>
<div id="analysis" class="section level3">
<h3>Analysis</h3>
<p>First, we will do some descriptive analysis, such as means and cross tabulations.</p>
<div class="sourceCode" id="cb27"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb27-1" data-line-number="1"><span class="co">#First we will do some tables</span></a>
<a class="sourceLine" id="cb27-2" data-line-number="2"><span class="co">#Raw frequencies</span></a>
<a class="sourceLine" id="cb27-3" data-line-number="3"><span class="kw">table</span>(brfss_<span class="dv">17</span><span class="op">$</span>badhealth, brfss_<span class="dv">17</span><span class="op">$</span>educ)</a></code></pre></div>
<pre><code>##    
##     2hsgrad 0Prim 1somehs 3somecol 4colgrad
##   0   42226  2660    5583    50627    87690
##   1   13542  2413    3846    11555     9181</code></pre>
<div class="sourceCode" id="cb29"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb29-1" data-line-number="1"><span class="co">#column percentages</span></a>
<a class="sourceLine" id="cb29-2" data-line-number="2"><span class="kw">prop.table</span>(<span class="kw">table</span>(brfss_<span class="dv">17</span><span class="op">$</span>badhealth, brfss_<span class="dv">17</span><span class="op">$</span>educ), <span class="dt">margin=</span><span class="dv">2</span>)</a></code></pre></div>
<pre><code>##    
##        2hsgrad      0Prim    1somehs   3somecol   4colgrad
##   0 0.75717257 0.52434457 0.59210945 0.81417452 0.90522447
##   1 0.24282743 0.47565543 0.40789055 0.18582548 0.09477553</code></pre>
<div class="sourceCode" id="cb31"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb31-1" data-line-number="1"><span class="co">#basic chi square test of independence</span></a>
<a class="sourceLine" id="cb31-2" data-line-number="2"><span class="kw">chisq.test</span>(<span class="kw">table</span>(brfss_<span class="dv">17</span><span class="op">$</span>badhealth, brfss_<span class="dv">17</span><span class="op">$</span>educ))</a></code></pre></div>
<pre><code>## 
##  Pearson&#39;s Chi-squared test
## 
## data:  table(brfss_17$badhealth, brfss_17$educ)
## X-squared = 12758, df = 4, p-value &lt; 2.2e-16</code></pre>
<p>So basically all of these numbers are incorrect, since they all assume random sampling. Now, we must tell R what the survey design is and what the weight variable is, then we can re-do these so they are correct.</p>
</div>
<div id="create-a-survey-design-object" class="section level3">
<h3>Create a survey design object</h3>
<p>Now we identify the survey design. ids = PSU identifers, strata=strata identifiers, weights=case weights, data= the data frame where these variables are located. Lastly, I only include respondents with NON-MISSING case weights.</p>
<p>I first try to get only cities in the state of Texas by looking for “TX” in the MSA’s name field in the data.</p>
<div class="sourceCode" id="cb33"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb33-1" data-line-number="1">brfss_<span class="dv">17</span><span class="op">$</span>tx&lt;-<span class="ot">NA</span></a>
<a class="sourceLine" id="cb33-2" data-line-number="2">brfss_<span class="dv">17</span><span class="op">$</span>tx[<span class="kw">grep</span>(<span class="dt">pattern =</span> <span class="st">&quot;TX&quot;</span>, brfss_<span class="dv">17</span><span class="op">$</span>mmsaname)]&lt;-<span class="dv">1</span></a></code></pre></div>
<p>Now I make the survey design object. You may be required to specify two options here:</p>
<ol style="list-style-type: decimal">
<li><p><code>survey.lonely.psu</code> This means that some of the strata only have 1 PSU within them. This does not allow for within strata variance to be calculated. So we often have to tell the computer to do something here. Two valid options are “adjust”, to center the stratum at the population mean rather than the stratum mean, and “average” to replace the variance contribution of the stratum by the average variance contribution across strata. (from ?surveyoptions)</p></li>
<li><p>Nesting of PSU within strata. By default, PSUs have numeric identifiers that can overlap between strata. By specifying <code>nest=T</code>, we tell R to relable the PSUs so they are unique across strata. If your survey requires this, it will throw a warning message.</p></li>
</ol>
<div class="sourceCode" id="cb34"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb34-1" data-line-number="1">brfss_<span class="dv">17</span>&lt;-brfss_<span class="dv">17</span><span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb34-2" data-line-number="2"><span class="st">  </span><span class="kw">filter</span>(tx<span class="op">==</span><span class="dv">1</span>, <span class="kw">is.na</span>(mmsawt)<span class="op">==</span>F)</a>
<a class="sourceLine" id="cb34-3" data-line-number="3"></a>
<a class="sourceLine" id="cb34-4" data-line-number="4"><span class="co">#</span></a>
<a class="sourceLine" id="cb34-5" data-line-number="5"><span class="kw">options</span>(<span class="dt">survey.lonely.psu =</span> <span class="st">&quot;adjust&quot;</span>)</a>
<a class="sourceLine" id="cb34-6" data-line-number="6"></a>
<a class="sourceLine" id="cb34-7" data-line-number="7">des&lt;-<span class="kw">svydesign</span>(<span class="dt">ids=</span><span class="op">~</span><span class="dv">1</span>, <span class="dt">strata=</span><span class="op">~</span>ststr, <span class="dt">weights=</span><span class="op">~</span>mmsawt, <span class="dt">data =</span> brfss_<span class="dv">17</span> )</a></code></pre></div>
<p>###simple weighted analysis Now , we re-do the analysis from above using only weights:</p>
<div class="sourceCode" id="cb35"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb35-1" data-line-number="1">cat&lt;-<span class="kw">wtd.table</span>(brfss_<span class="dv">17</span><span class="op">$</span>badhealth, brfss_<span class="dv">17</span><span class="op">$</span>educ, <span class="dt">weights =</span> brfss_<span class="dv">17</span><span class="op">$</span>mmsawt)</a>
<a class="sourceLine" id="cb35-2" data-line-number="2"><span class="kw">prop.table</span>(<span class="kw">wtd.table</span>(brfss_<span class="dv">17</span><span class="op">$</span>badhealth, brfss_<span class="dv">17</span><span class="op">$</span>educ, <span class="dt">weights =</span> brfss_<span class="dv">17</span><span class="op">$</span>mmsawt), <span class="dt">margin=</span><span class="dv">2</span>)</a></code></pre></div>
<pre><code>##      2hsgrad      0Prim    1somehs   3somecol   4colgrad
## 0 0.75772036 0.60034582 0.65504645 0.83488335 0.91152544
## 1 0.24227964 0.39965418 0.34495355 0.16511665 0.08847456</code></pre>
<div class="sourceCode" id="cb37"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb37-1" data-line-number="1"><span class="co">#compare that with the original</span></a>
<a class="sourceLine" id="cb37-2" data-line-number="2"><span class="kw">prop.table</span>(<span class="kw">table</span>(brfss_<span class="dv">17</span><span class="op">$</span>badhealth, brfss_<span class="dv">17</span><span class="op">$</span>educ), <span class="dt">margin=</span><span class="dv">2</span>)</a></code></pre></div>
<pre><code>##    
##       2hsgrad     0Prim   1somehs  3somecol  4colgrad
##   0 0.7089670 0.5686813 0.6220807 0.7798289 0.8838313
##   1 0.2910330 0.4313187 0.3779193 0.2201711 0.1161687</code></pre>
<p>There <strong>are</strong> differences, notably that the prevalence of poor SRH is <em>higher in the sample than the population</em>. This is important!</p>
<p>Let’s say we also want the standard errors of these percentages. This can be found for a proportion by: <span class="math inline">\(s.e. (p)={\sqrt {p(1-p)} \over {n}}\)</span></p>
<p>So we need to get n and p, that’s easy:</p>
<div class="sourceCode" id="cb39"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb39-1" data-line-number="1">n&lt;-<span class="kw">table</span>(<span class="kw">is.na</span>(brfss_<span class="dv">17</span><span class="op">$</span>badhealth)<span class="op">==</span>F)</a>
<a class="sourceLine" id="cb39-2" data-line-number="2">n</a></code></pre></div>
<p>FALSE TRUE 49 8584</p>
<div class="sourceCode" id="cb40"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb40-1" data-line-number="1">p&lt;-<span class="kw">prop.table</span>(<span class="kw">wtd.table</span>(brfss_<span class="dv">17</span><span class="op">$</span>badhealth, brfss_<span class="dv">17</span><span class="op">$</span>educ, <span class="dt">weights =</span> brfss_<span class="dv">17</span><span class="op">$</span>mmsawt), <span class="dt">margin=</span><span class="dv">2</span>)</a>
<a class="sourceLine" id="cb40-2" data-line-number="2"><span class="kw">t</span>(p)</a></code></pre></div>
<pre><code>              0          1</code></pre>
<p>2hsgrad 0.75772036 0.24227964 0Prim 0.60034582 0.39965418 1somehs 0.65504645 0.34495355 3somecol 0.83488335 0.16511665 4colgrad 0.91152544 0.08847456</p>
<div class="sourceCode" id="cb42"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb42-1" data-line-number="1">p&lt;-<span class="kw">prop.table</span>(<span class="kw">wtd.table</span>(brfss_<span class="dv">17</span><span class="op">$</span>badhealth, brfss_<span class="dv">17</span><span class="op">$</span>male, <span class="dt">weights =</span> brfss_<span class="dv">17</span><span class="op">$</span>mmsawt), <span class="dt">margin=</span><span class="dv">2</span>)</a>
<a class="sourceLine" id="cb42-2" data-line-number="2"><span class="kw">t</span>(p)</a></code></pre></div>
<pre><code>           0         1</code></pre>
<p>Female 0.7874293 0.2125707 Male 0.8189765 0.1810235</p>
<div class="sourceCode" id="cb44"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb44-1" data-line-number="1">se&lt;-(p<span class="op">*</span>(<span class="dv">1</span><span class="op">-</span>p))<span class="op">/</span>n[<span class="dv">2</span>]</a>
<a class="sourceLine" id="cb44-2" data-line-number="2"></a>
<a class="sourceLine" id="cb44-3" data-line-number="3"><span class="kw">stargazer</span>(<span class="kw">data.frame</span>(<span class="dt">proportion=</span>p, <span class="dt">se=</span><span class="kw">sqrt</span>(se)), <span class="dt">summary =</span> F, <span class="dt">type =</span> <span class="st">&quot;html&quot;</span>, <span class="dt">digits =</span> <span class="dv">2</span>)</a></code></pre></div>
<table style="text-align:center">
<tr>
<td colspan="7" style="border-bottom: 1px solid black">
</td>
</tr>
<tr>
<td style="text-align:left">
</td>
<td>
proportion.Var1
</td>
<td>
proportion.Var2
</td>
<td>
proportion.Freq
</td>
<td>
se.Var1
</td>
<td>
se.Var2
</td>
<td>
se.Freq
</td>
</tr>
<tr>
<td colspan="7" style="border-bottom: 1px solid black">
</td>
</tr>
<tr>
<td style="text-align:left">
1
</td>
<td>
0
</td>
<td>
Female
</td>
<td>
0.79
</td>
<td>
0
</td>
<td>
Female
</td>
<td>
0.004
</td>
</tr>
<tr>
<td style="text-align:left">
2
</td>
<td>
1
</td>
<td>
Female
</td>
<td>
0.21
</td>
<td>
1
</td>
<td>
Female
</td>
<td>
0.004
</td>
</tr>
<tr>
<td style="text-align:left">
3
</td>
<td>
0
</td>
<td>
Male
</td>
<td>
0.82
</td>
<td>
0
</td>
<td>
Male
</td>
<td>
0.004
</td>
</tr>
<tr>
<td style="text-align:left">
4
</td>
<td>
1
</td>
<td>
Male
</td>
<td>
0.18
</td>
<td>
1
</td>
<td>
Male
</td>
<td>
0.004
</td>
</tr>
<tr>
<td colspan="7" style="border-bottom: 1px solid black">
</td>
</tr>
</table>
<p>Which shows us the errors in the estimates based on the weighted proportions. That’s nice, but since we basically inflated the n to be the population of the US, these standard errors are too small. This is another example of using survey statistical methods, to get the right standard error for a statistic.</p>
</div>
<div id="proper-survey-design-analysis" class="section level3">
<h3>Proper survey design analysis</h3>
<div class="sourceCode" id="cb45"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb45-1" data-line-number="1"><span class="co">#Now consider the full sample design + weights</span></a>
<a class="sourceLine" id="cb45-2" data-line-number="2">cat&lt;-<span class="kw">prop.table</span>(<span class="kw">svytable</span>(<span class="op">~</span>badhealth<span class="op">+</span>educ, <span class="dt">design =</span> des), <span class="dt">margin =</span> <span class="dv">2</span>)</a>
<a class="sourceLine" id="cb45-3" data-line-number="3">knitr<span class="op">::</span><span class="kw">kable</span>(<span class="kw">round</span>(<span class="dv">100</span><span class="op">*</span><span class="kw">t</span>(cat), <span class="dt">digits =</span> <span class="dv">3</span>))</a></code></pre></div>
<table>
<thead>
<tr class="header">
<th align="left"></th>
<th align="right">0</th>
<th align="right">1</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">2hsgrad</td>
<td align="right">75.772</td>
<td align="right">24.228</td>
</tr>
<tr class="even">
<td align="left">0Prim</td>
<td align="right">60.035</td>
<td align="right">39.965</td>
</tr>
<tr class="odd">
<td align="left">1somehs</td>
<td align="right">65.505</td>
<td align="right">34.495</td>
</tr>
<tr class="even">
<td align="left">3somecol</td>
<td align="right">83.488</td>
<td align="right">16.512</td>
</tr>
<tr class="odd">
<td align="left">4colgrad</td>
<td align="right">91.153</td>
<td align="right">8.847</td>
</tr>
</tbody>
</table>
<div class="sourceCode" id="cb46"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb46-1" data-line-number="1"><span class="kw">options</span>(<span class="dt">scipen=</span><span class="dv">999</span>)</a>
<a class="sourceLine" id="cb46-2" data-line-number="2"><span class="kw">svychisq</span>(<span class="op">~</span>badhealth<span class="op">+</span>educ, <span class="dt">design =</span> des)</a></code></pre></div>
<pre><code>Pearson&#39;s X^2: Rao &amp; Scott adjustment</code></pre>
<p>data: svychisq(~badhealth + educ, design = des) F = 25.872, ndf = 3.7069, ddf = 31819.8445, p-value &lt; 0.00000000000000022</p>
<div class="sourceCode" id="cb48"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb48-1" data-line-number="1"><span class="kw">stargazer</span>(<span class="kw">data.frame</span>(<span class="kw">prop.table</span>(<span class="kw">svytable</span>(<span class="op">~</span>badhealth<span class="op">+</span>educ, <span class="dt">design =</span> des), <span class="dt">margin =</span> <span class="dv">2</span>)),<span class="dt">summary =</span> F, <span class="dt">type =</span> <span class="st">&quot;html&quot;</span>, <span class="dt">digits=</span><span class="dv">3</span>)</a></code></pre></div>
<table style="text-align:center">
<tr>
<td colspan="4" style="border-bottom: 1px solid black">
</td>
</tr>
<tr>
<td style="text-align:left">
</td>
<td>
badhealth
</td>
<td>
educ
</td>
<td>
Freq
</td>
</tr>
<tr>
<td colspan="4" style="border-bottom: 1px solid black">
</td>
</tr>
<tr>
<td style="text-align:left">
1
</td>
<td>
0
</td>
<td>
2hsgrad
</td>
<td>
0.758
</td>
</tr>
<tr>
<td style="text-align:left">
2
</td>
<td>
1
</td>
<td>
2hsgrad
</td>
<td>
0.242
</td>
</tr>
<tr>
<td style="text-align:left">
3
</td>
<td>
0
</td>
<td>
0Prim
</td>
<td>
0.600
</td>
</tr>
<tr>
<td style="text-align:left">
4
</td>
<td>
1
</td>
<td>
0Prim
</td>
<td>
0.400
</td>
</tr>
<tr>
<td style="text-align:left">
5
</td>
<td>
0
</td>
<td>
1somehs
</td>
<td>
0.655
</td>
</tr>
<tr>
<td style="text-align:left">
6
</td>
<td>
1
</td>
<td>
1somehs
</td>
<td>
0.345
</td>
</tr>
<tr>
<td style="text-align:left">
7
</td>
<td>
0
</td>
<td>
3somecol
</td>
<td>
0.835
</td>
</tr>
<tr>
<td style="text-align:left">
8
</td>
<td>
1
</td>
<td>
3somecol
</td>
<td>
0.165
</td>
</tr>
<tr>
<td style="text-align:left">
9
</td>
<td>
0
</td>
<td>
4colgrad
</td>
<td>
0.912
</td>
</tr>
<tr>
<td style="text-align:left">
10
</td>
<td>
1
</td>
<td>
4colgrad
</td>
<td>
0.088
</td>
</tr>
<tr>
<td colspan="4" style="border-bottom: 1px solid black">
</td>
</tr>
</table>
<p>Which gives the same %’s as the weighted table above, but we also want the correct standard errors for our bad health prevalences.</p>
<p>The <code>svyby()</code> function will calculate statistics by groups, in this case we want the % in bad health by each level of education. The %’s can be gotten using the <code>svymean()</code> function, which finds means of variables using survey design:</p>
<div class="sourceCode" id="cb49"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb49-1" data-line-number="1">sv.table&lt;-<span class="kw">svyby</span>(<span class="dt">formula =</span> <span class="op">~</span>badhealth, <span class="dt">by =</span> <span class="op">~</span>educ, <span class="dt">design =</span> des, <span class="dt">FUN =</span> svymean, <span class="dt">na.rm=</span>T)</a>
<a class="sourceLine" id="cb49-2" data-line-number="2"><span class="kw">stargazer</span>(sv.table, <span class="dt">summary =</span> F, <span class="dt">type =</span> <span class="st">&quot;html&quot;</span>, <span class="dt">digits =</span> <span class="dv">2</span>)</a></code></pre></div>
<table style="text-align:center">
<tr>
<td colspan="4" style="border-bottom: 1px solid black">
</td>
</tr>
<tr>
<td style="text-align:left">
</td>
<td>
educ
</td>
<td>
badhealth
</td>
<td>
se
</td>
</tr>
<tr>
<td colspan="4" style="border-bottom: 1px solid black">
</td>
</tr>
<tr>
<td style="text-align:left">
2hsgrad
</td>
<td>
2hsgrad
</td>
<td>
0.24
</td>
<td>
0.02
</td>
</tr>
<tr>
<td style="text-align:left">
0Prim
</td>
<td>
0Prim
</td>
<td>
0.40
</td>
<td>
0.05
</td>
</tr>
<tr>
<td style="text-align:left">
1somehs
</td>
<td>
1somehs
</td>
<td>
0.34
</td>
<td>
0.04
</td>
</tr>
<tr>
<td style="text-align:left">
3somecol
</td>
<td>
3somecol
</td>
<td>
0.17
</td>
<td>
0.01
</td>
</tr>
<tr>
<td style="text-align:left">
4colgrad
</td>
<td>
4colgrad
</td>
<td>
0.09
</td>
<td>
0.01
</td>
</tr>
<tr>
<td colspan="4" style="border-bottom: 1px solid black">
</td>
</tr>
</table>
<p>And we see the same point estimates of our prevalences as in the simple weighted table, but the standard errors have now been adjusted for survey design as well, so they are also correct. You also see they are much larger than the ones we computed above, which assumed random sampling.</p>
</div>
<div id="another-way" class="section level3">
<h3>Another way</h3>
<p>There’s this great R package, <code>tableone</code> that does this stuff very nicely and incorporates survey design too. Here’s an example of using it to generate your bivariate tests like above:</p>
<div class="sourceCode" id="cb50"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb50-1" data-line-number="1"><span class="kw">library</span>(tableone)</a>
<a class="sourceLine" id="cb50-2" data-line-number="2"><span class="co">#not using survey design</span></a>
<a class="sourceLine" id="cb50-3" data-line-number="3">t1&lt;-<span class="kw">CreateTableOne</span>(<span class="dt">vars =</span> <span class="kw">c</span>(<span class="st">&quot;educ&quot;</span>, <span class="st">&quot;marst&quot;</span>, <span class="st">&quot;male&quot;</span>), <span class="dt">strata =</span> <span class="st">&quot;badhealth&quot;</span>, <span class="dt">test =</span> T, <span class="dt">data =</span> brfss_<span class="dv">17</span>)</a>
<a class="sourceLine" id="cb50-4" data-line-number="4"><span class="co">#t1&lt;-print(t1, format=&quot;p&quot;)</span></a>
<a class="sourceLine" id="cb50-5" data-line-number="5"><span class="kw">print</span>(t1,<span class="dt">format=</span><span class="st">&quot;p&quot;</span>)</a></code></pre></div>
<pre><code>##                  Stratified by badhealth
##                   0    1    p      test
##   n               6780 1804            
##   educ (%)                  &lt;0.001     
##      2hsgrad      20.0 30.9            
##      0Prim         3.1  8.7            
##      1somehs       4.3  9.9            
##      3somecol     25.7 27.2            
##      4colgrad     46.9 23.2            
##   marst (%)                 &lt;0.001     
##      married      53.4 41.1            
##      cohab         3.4  2.5            
##      divorced     11.9 16.6            
##      nm           16.1 13.9            
##      separated     2.0  5.1            
##      widowed      13.2 20.8            
##   male = Male (%) 43.0 36.8 &lt;0.001</code></pre>
<div class="sourceCode" id="cb52"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb52-1" data-line-number="1"><span class="co">#using survey design</span></a>
<a class="sourceLine" id="cb52-2" data-line-number="2">st1&lt;-<span class="kw">svyCreateTableOne</span>(<span class="dt">vars =</span> <span class="kw">c</span>(<span class="st">&quot;educ&quot;</span>, <span class="st">&quot;marst&quot;</span>, <span class="st">&quot;male&quot;</span>), <span class="dt">strata =</span> <span class="st">&quot;badhealth&quot;</span>, <span class="dt">test =</span> T, <span class="dt">data =</span> des)</a>
<a class="sourceLine" id="cb52-3" data-line-number="3"><span class="co">#st1&lt;-print(st1, format=&quot;p&quot;)</span></a>
<a class="sourceLine" id="cb52-4" data-line-number="4"><span class="kw">print</span>(st1, <span class="dt">format=</span><span class="st">&quot;p&quot;</span>)</a></code></pre></div>
<pre><code>##                  Stratified by badhealth
##                   0          1         p      test
##   n               12308745.3 3023838.2            
##   educ (%)                             &lt;0.001     
##      2hsgrad            23.4      30.5            
##      0Prim               6.1      16.6            
##      1somehs             6.6      14.2            
##      3somecol           32.3      26.1            
##      4colgrad           31.6      12.6            
##   marst (%)                            &lt;0.001     
##      married            52.9      46.4            
##      cohab               5.6       3.3            
##      divorced            9.5      15.0            
##      nm                 24.9      21.0            
##      separated           2.1       7.0            
##      widowed             4.9       7.3            
##   male = Male (%)       49.7      44.7  0.088</code></pre>
</div>
</div>
<div id="regression-example" class="section level2">
<h2>Regression example</h2>
<p>Next we apply this logic to a regression case. First we fit the OLS model for our BMI outcome using education and age as predictors:</p>
<div class="sourceCode" id="cb54"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb54-1" data-line-number="1">fit1&lt;-<span class="kw">lm</span>(bmi<span class="op">~</span>educ<span class="op">+</span>agec, <span class="dt">data=</span>brfss_<span class="dv">17</span>)</a></code></pre></div>
<p>Next we incorporate case weights</p>
<div class="sourceCode" id="cb55"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb55-1" data-line-number="1">fit2&lt;-<span class="kw">lm</span>(bmi<span class="op">~</span>educ<span class="op">+</span>agec, <span class="dt">data=</span>brfss_<span class="dv">17</span>, <span class="dt">weights =</span> mmsawt)</a></code></pre></div>
<p>Now we will incorporate design effects as well:</p>
<div class="sourceCode" id="cb56"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb56-1" data-line-number="1">fit3&lt;-<span class="kw">svyglm</span>(bmi<span class="op">~</span>educ<span class="op">+</span>agec,des, <span class="dt">family=</span>gaussian)</a></code></pre></div>
<p>Now I make a table to show the results of the three models:</p>
<div class="sourceCode" id="cb57"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb57-1" data-line-number="1"><span class="kw">stargazer</span>(fit1, fit2, fit3, <span class="dt">style=</span><span class="st">&quot;demography&quot;</span>, <span class="dt">type=</span><span class="st">&quot;html&quot;</span>,</a>
<a class="sourceLine" id="cb57-2" data-line-number="2">          <span class="dt">column.labels =</span> <span class="kw">c</span>(<span class="st">&quot;OLS&quot;</span>, <span class="st">&quot;Weights Only&quot;</span>, <span class="st">&quot;Survey&quot;</span>),</a>
<a class="sourceLine" id="cb57-3" data-line-number="3">          <span class="dt">title =</span> <span class="st">&quot;Regression models for BMI using survey data - BRFSS 2016&quot;</span>, </a>
<a class="sourceLine" id="cb57-4" data-line-number="4">          <span class="dt">covariate.labels=</span><span class="kw">c</span>(<span class="st">&quot;PrimarySchool&quot;</span>, <span class="st">&quot;SomeHS&quot;</span>, <span class="st">&quot;SomeColl&quot;</span>, <span class="st">&quot;CollGrad&quot;</span>, <span class="st">&quot;Age 24-39&quot;</span>,<span class="st">&quot;Age 39-59&quot;</span> ,<span class="st">&quot;Age 59-79&quot;</span>, <span class="st">&quot;Age 80+&quot;</span>), </a>
<a class="sourceLine" id="cb57-5" data-line-number="5">          <span class="dt">keep.stat=</span><span class="st">&quot;n&quot;</span>, <span class="dt">model.names=</span>F, <span class="dt">align=</span>T, <span class="dt">ci=</span>T)</a></code></pre></div>
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
Survey
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
0.134
</td>
<td>
-0.581<sup>*</sup>
</td>
<td>
-0.581
</td>
</tr>
<tr>
<td style="text-align:left">
</td>
<td>
(-0.642, 0.911)
</td>
<td>
(-1.143, -0.020)
</td>
<td>
(-1.841, 0.678)
</td>
</tr>
<tr>
<td style="text-align:left">
SomeHS
</td>
<td>
-0.023
</td>
<td>
-0.743<sup>**</sup>
</td>
<td>
-0.743
</td>
</tr>
<tr>
<td style="text-align:left">
</td>
<td>
(-0.699, 0.652)
</td>
<td>
(-1.302, -0.183)
</td>
<td>
(-2.093, 0.608)
</td>
</tr>
<tr>
<td style="text-align:left">
SomeColl
</td>
<td>
-0.477<sup>*</sup>
</td>
<td>
-1.305<sup>***</sup>
</td>
<td>
-1.305<sup>**</sup>
</td>
</tr>
<tr>
<td style="text-align:left">
</td>
<td>
(-0.880, -0.074)
</td>
<td>
(-1.668, -0.943)
</td>
<td>
(-2.146, -0.465)
</td>
</tr>
<tr>
<td style="text-align:left">
CollGrad
</td>
<td>
-1.997<sup>***</sup>
</td>
<td>
-2.513<sup>***</sup>
</td>
<td>
-2.513<sup>***</sup>
</td>
</tr>
<tr>
<td style="text-align:left">
</td>
<td>
(-2.365, -1.628)
</td>
<td>
(-2.889, -2.137)
</td>
<td>
(-3.281, -1.745)
</td>
</tr>
<tr>
<td style="text-align:left">
Age 24-39
</td>
<td>
2.930<sup>***</sup>
</td>
<td>
3.215<sup>***</sup>
</td>
<td>
3.215<sup>***</sup>
</td>
</tr>
<tr>
<td style="text-align:left">
</td>
<td>
(2.251, 3.609)
</td>
<td>
(2.765, 3.666)
</td>
<td>
(2.341, 4.089)
</td>
</tr>
<tr>
<td style="text-align:left">
Age 39-59
</td>
<td>
4.376<sup>***</sup>
</td>
<td>
4.773<sup>***</sup>
</td>
<td>
4.773<sup>***</sup>
</td>
</tr>
<tr>
<td style="text-align:left">
</td>
<td>
(3.731, 5.020)
</td>
<td>
(4.335, 5.211)
</td>
<td>
(3.897, 5.649)
</td>
</tr>
<tr>
<td style="text-align:left">
Age 59-79
</td>
<td>
3.467<sup>***</sup>
</td>
<td>
4.011<sup>***</sup>
</td>
<td>
4.011<sup>***</sup>
</td>
</tr>
<tr>
<td style="text-align:left">
</td>
<td>
(2.837, 4.096)
</td>
<td>
(3.537, 4.486)
</td>
<td>
(3.051, 4.971)
</td>
</tr>
<tr>
<td style="text-align:left">
Age 80+
</td>
<td>
0.792<sup>*</sup>
</td>
<td>
0.815
</td>
<td>
0.815
</td>
</tr>
<tr>
<td style="text-align:left">
</td>
<td>
(0.060, 1.524)
</td>
<td>
(-0.054, 1.684)
</td>
<td>
(-0.496, 2.126)
</td>
</tr>
<tr>
<td style="text-align:left">
Constant
</td>
<td>
26.082<sup>***</sup>
</td>
<td>
26.221<sup>***</sup>
</td>
<td>
26.221<sup>***</sup>
</td>
</tr>
<tr>
<td style="text-align:left">
</td>
<td>
(25.462, 26.702)
</td>
<td>
(25.804, 26.638)
</td>
<td>
(25.382, 27.060)
</td>
</tr>
<tr>
<td style="text-align:left">
<em>N</em>
</td>
<td>
7,891
</td>
<td>
7,891
</td>
<td>
7,891
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
<div class="sourceCode" id="cb58"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb58-1" data-line-number="1"><span class="kw">library</span>(ggplot2)</a>
<a class="sourceLine" id="cb58-2" data-line-number="2"><span class="kw">library</span>(dplyr)</a>
<a class="sourceLine" id="cb58-3" data-line-number="3">coefs&lt;-<span class="kw">data.frame</span>(<span class="dt">coefs=</span><span class="kw">c</span>(<span class="kw">coef</span>(fit1)[<span class="op">-</span><span class="dv">1</span>], <span class="kw">coef</span>(fit3)[<span class="op">-</span><span class="dv">1</span>]),</a>
<a class="sourceLine" id="cb58-4" data-line-number="4">                  <span class="dt">mod=</span><span class="kw">c</span>(<span class="kw">rep</span>(<span class="st">&quot;Non Survey Model&quot;</span>, <span class="dv">8</span>),<span class="kw">rep</span>(<span class="st">&quot;Survey Model&quot;</span>, <span class="dv">8</span>)),</a>
<a class="sourceLine" id="cb58-5" data-line-number="5">                  <span class="dt">effect=</span><span class="kw">rep</span>(<span class="kw">names</span>(<span class="kw">coef</span>(fit1)[<span class="op">-</span><span class="dv">1</span>]), <span class="dv">2</span>))</a>
<a class="sourceLine" id="cb58-6" data-line-number="6"></a>
<a class="sourceLine" id="cb58-7" data-line-number="7">coefs<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb58-8" data-line-number="8"><span class="st">  </span><span class="kw">ggplot</span>()<span class="op">+</span></a>
<a class="sourceLine" id="cb58-9" data-line-number="9"><span class="st">  </span><span class="kw">geom_point</span>(<span class="kw">aes</span>( <span class="dt">x=</span>effect, <span class="dt">y=</span>coefs, <span class="dt">group=</span>effect,<span class="dt">color=</span>effect, <span class="dt">shape=</span>mod),</a>
<a class="sourceLine" id="cb58-10" data-line-number="10">             <span class="dt">position=</span><span class="kw">position_jitterdodge</span>(<span class="dt">jitter.width =</span> <span class="dv">1</span>),</a>
<a class="sourceLine" id="cb58-11" data-line-number="11">             <span class="dt">size=</span><span class="dv">2</span>)<span class="op">+</span></a>
<a class="sourceLine" id="cb58-12" data-line-number="12"><span class="st">  </span><span class="kw">ylab</span>(<span class="st">&quot;Regression Coefficient&quot;</span>)<span class="op">+</span></a>
<a class="sourceLine" id="cb58-13" data-line-number="13"><span class="st">  </span><span class="kw">xlab</span>(<span class="st">&quot;Beta&quot;</span>)<span class="op">+</span></a>
<a class="sourceLine" id="cb58-14" data-line-number="14"><span class="st">  </span><span class="kw">geom_abline</span>(<span class="dt">intercept =</span> <span class="dv">0</span>, <span class="dt">slope=</span><span class="dv">0</span>)<span class="op">+</span></a>
<a class="sourceLine" id="cb58-15" data-line-number="15"><span class="st">  </span><span class="kw">theme</span>(<span class="dt">axis.text.x =</span> <span class="kw">element_text</span>(<span class="dt">angle =</span> <span class="dv">45</span>, <span class="dt">hjust =</span> <span class="dv">1</span>))<span class="op">+</span></a>
<a class="sourceLine" id="cb58-16" data-line-number="16"><span class="st">  </span><span class="kw">ggtitle</span>(<span class="dt">label =</span> <span class="st">&quot;Comparison of Survey and Non-Survey Regression effects&quot;</span>)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/survey_stat_basics_files/figure-html/unnamed-chunk-19-1.png" /><!-- --></p>
<p>Which shows us that the betas are similar but have some differences between the two models.</p>
</div>
<div id="creating-survey-estimates-for-places" class="section level2">
<h2>Creating Survey estimates for places</h2>
<p>One of the coolest ways to use the BRFSS is to calculate estimates for places, and by demographic characteristics withing places. Again, we use <code>svyby()</code> to do this, but now we calculate obesity rates by sex within cities.</p>
<div class="sourceCode" id="cb59"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb59-1" data-line-number="1">citytab&lt;-<span class="kw">svyby</span>(<span class="op">~</span>obese, <span class="op">~</span>mmsaname<span class="op">+</span>male, <span class="dt">design=</span>des,<span class="dt">FUN =</span> svymean, <span class="dt">na.rm=</span>T )</a>
<a class="sourceLine" id="cb59-2" data-line-number="2"><span class="kw">stargazer</span>(citytab, <span class="dt">type=</span><span class="st">&quot;html&quot;</span>, <span class="dt">digits=</span><span class="dv">3</span>, <span class="dt">summary =</span> F, <span class="dt">rownames =</span> F)</a></code></pre></div>
<table style="text-align:center">
<tr>
<td colspan="4" style="border-bottom: 1px solid black">
</td>
</tr>
<tr>
<td style="text-align:left">
mmsaname
</td>
<td>
male
</td>
<td>
obese
</td>
<td>
se
</td>
</tr>
<tr>
<td colspan="4" style="border-bottom: 1px solid black">
</td>
</tr>
<tr>
<td style="text-align:left">
Austin-Round Rock, TX, Metropolitan Statistical Area
</td>
<td>
Female
</td>
<td>
0.271
</td>
<td>
0.028
</td>
</tr>
<tr>
<td style="text-align:left">
College Station-Bryan, TX, Metropolitan Statistical Area
</td>
<td>
Female
</td>
<td>
0.351
</td>
<td>
0.075
</td>
</tr>
<tr>
<td style="text-align:left">
Corpus Christi, TX, Metropolitan Statistical Area
</td>
<td>
Female
</td>
<td>
0.336
</td>
<td>
0.041
</td>
</tr>
<tr>
<td style="text-align:left">
Dallas-Plano-Irving, TX, Metropolitan Division
</td>
<td>
Female
</td>
<td>
0.253
</td>
<td>
0.034
</td>
</tr>
<tr>
<td style="text-align:left">
El Paso, TX, Metropolitan Statistical Area
</td>
<td>
Female
</td>
<td>
0.304
</td>
<td>
0.035
</td>
</tr>
<tr>
<td style="text-align:left">
Fort Worth-Arlington, TX, Metropolitan Division
</td>
<td>
Female
</td>
<td>
0.328
</td>
<td>
0.040
</td>
</tr>
<tr>
<td style="text-align:left">
Houston-The Woodlands-Sugar Land, TX, Metropolitan Statistical Area
</td>
<td>
Female
</td>
<td>
0.313
</td>
<td>
0.033
</td>
</tr>
<tr>
<td style="text-align:left">
San Antonio-New Braunfels, TX, Metropolitan Statistical Area
</td>
<td>
Female
</td>
<td>
0.310
</td>
<td>
0.047
</td>
</tr>
<tr>
<td style="text-align:left">
Wichita Falls, TX, Metropolitan Statistical Area
</td>
<td>
Female
</td>
<td>
0.388
</td>
<td>
0.068
</td>
</tr>
<tr>
<td style="text-align:left">
Austin-Round Rock, TX, Metropolitan Statistical Area
</td>
<td>
Male
</td>
<td>
0.313
</td>
<td>
0.030
</td>
</tr>
<tr>
<td style="text-align:left">
College Station-Bryan, TX, Metropolitan Statistical Area
</td>
<td>
Male
</td>
<td>
0.267
</td>
<td>
0.070
</td>
</tr>
<tr>
<td style="text-align:left">
Corpus Christi, TX, Metropolitan Statistical Area
</td>
<td>
Male
</td>
<td>
0.413
</td>
<td>
0.057
</td>
</tr>
<tr>
<td style="text-align:left">
Dallas-Plano-Irving, TX, Metropolitan Division
</td>
<td>
Male
</td>
<td>
0.316
</td>
<td>
0.035
</td>
</tr>
<tr>
<td style="text-align:left">
El Paso, TX, Metropolitan Statistical Area
</td>
<td>
Male
</td>
<td>
0.390
</td>
<td>
0.046
</td>
</tr>
<tr>
<td style="text-align:left">
Fort Worth-Arlington, TX, Metropolitan Division
</td>
<td>
Male
</td>
<td>
0.406
</td>
<td>
0.040
</td>
</tr>
<tr>
<td style="text-align:left">
Houston-The Woodlands-Sugar Land, TX, Metropolitan Statistical Area
</td>
<td>
Male
</td>
<td>
0.307
</td>
<td>
0.030
</td>
</tr>
<tr>
<td style="text-align:left">
San Antonio-New Braunfels, TX, Metropolitan Statistical Area
</td>
<td>
Male
</td>
<td>
0.303
</td>
<td>
0.046
</td>
</tr>
<tr>
<td style="text-align:left">
Wichita Falls, TX, Metropolitan Statistical Area
</td>
<td>
Male
</td>
<td>
0.453
</td>
<td>
0.080
</td>
</tr>
<tr>
<td colspan="4" style="border-bottom: 1px solid black">
</td>
</tr>
</table>
<div id="using-srvyr" class="section level3">
<h3>Using srvyr</h3>
<p>There’s a new package called <code>srvyr</code> that incorporates the survey analysis stuff into the <code>dplyr</code> universe:</p>
<div class="sourceCode" id="cb60"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb60-1" data-line-number="1"><span class="kw">library</span>(srvyr)</a>
<a class="sourceLine" id="cb60-2" data-line-number="2">brfsurv&lt;-brfss_<span class="dv">17</span><span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb60-3" data-line-number="3"><span class="st">  </span><span class="kw">as_survey_design</span>(<span class="dv">1</span>,<span class="dt">strata=</span>ststr,<span class="dt">weights=</span>mmsawt )</a>
<a class="sourceLine" id="cb60-4" data-line-number="4"></a>
<a class="sourceLine" id="cb60-5" data-line-number="5"></a>
<a class="sourceLine" id="cb60-6" data-line-number="6">brfsurv<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb60-7" data-line-number="7"><span class="st">  </span><span class="kw">group_by</span>(mmsaname,male)<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb60-8" data-line-number="8"><span class="st">    </span><span class="kw">summarise</span>(<span class="dt">obprev =</span> <span class="kw">survey_mean</span>(obese, <span class="dt">na.rm=</span>T))</a></code></pre></div>
</div>
</div>
<div id="replicate-weights" class="section level2">
<h2>Replicate Weights</h2>
<p>If your dataset comes with <em>replicate weights</em>, you have to specify the survey design slightly differently. Here is an example using the IPUMS CPS data. For this data, you can get information <a href="https://cps.ipums.org/cps/repwt.shtml">here</a>, but you must consult your specific data source for the appropriate information for your data.</p>
<div class="sourceCode" id="cb61"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb61-1" data-line-number="1"><span class="kw">load</span>(<span class="kw">url</span>(<span class="st">&quot;https://github.com/coreysparks/data/blob/master/cpsmar10tx.Rdata?raw=true&quot;</span>))</a>
<a class="sourceLine" id="cb61-2" data-line-number="2"><span class="kw">names</span>(cpsmar10tx)</a></code></pre></div>
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
<div class="sourceCode" id="cb63"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb63-1" data-line-number="1"><span class="co">#simple binary outcome</span></a>
<a class="sourceLine" id="cb63-2" data-line-number="2">cpsmar10tx<span class="op">$</span>poverty&lt;-<span class="kw">ifelse</span>(cpsmar10tx<span class="op">$</span>offpov<span class="op">==</span><span class="dv">1</span>,<span class="dv">1</span>,<span class="dv">0</span>)</a>
<a class="sourceLine" id="cb63-3" data-line-number="3"></a>
<a class="sourceLine" id="cb63-4" data-line-number="4"><span class="co"># Replicate weight design</span></a>
<a class="sourceLine" id="cb63-5" data-line-number="5">des2&lt;-<span class="kw">svrepdesign</span>( <span class="dt">data =</span> cpsmar10tx,<span class="dt">repweights =</span> cpsmar10tx[, <span class="kw">c</span>(<span class="dv">8</span><span class="op">:</span><span class="dv">167</span>)]  , <span class="dt">weights =</span> <span class="op">~</span>wtsupp , <span class="dt">type=</span><span class="st">&quot;JK1&quot;</span>, <span class="dt">scale=</span>.<span class="dv">025</span>)</a>
<a class="sourceLine" id="cb63-6" data-line-number="6">des2</a></code></pre></div>
<pre><code>## Call: svrepdesign.default(data = cpsmar10tx, repweights = cpsmar10tx[, 
##     c(8:167)], weights = ~wtsupp, type = &quot;JK1&quot;, scale = 0.025)
## Unstratified cluster jacknife (JK1) with 160 replicates.</code></pre>
<div class="sourceCode" id="cb65"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb65-1" data-line-number="1"><span class="co">#Without design</span></a>
<a class="sourceLine" id="cb65-2" data-line-number="2"><span class="kw">prop.table</span>(<span class="kw">table</span>(cpsmar10tx<span class="op">$</span>poverty))</a></code></pre></div>
<pre><code>## 
##         0         1 
## 0.8374617 0.1625383</code></pre>
<div class="sourceCode" id="cb67"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb67-1" data-line-number="1"><span class="co">#with design</span></a>
<a class="sourceLine" id="cb67-2" data-line-number="2"><span class="kw">prop.table</span>(<span class="kw">svytable</span>(<span class="op">~</span>poverty, <span class="dt">design =</span> des2))</a></code></pre></div>
<pre><code>## poverty
##         0         1 
## 0.8481106 0.1518894</code></pre>
<div class="sourceCode" id="cb69"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb69-1" data-line-number="1"><span class="co">#Again, using the mean</span></a>
<a class="sourceLine" id="cb69-2" data-line-number="2"><span class="kw">mean</span>(cpsmar10tx<span class="op">$</span>poverty)</a></code></pre></div>
<pre><code>## [1] 0.1625383</code></pre>
<div class="sourceCode" id="cb71"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb71-1" data-line-number="1"><span class="co">#Using the design. This would be an official estimate of poverty in TX in 2010:</span></a>
<a class="sourceLine" id="cb71-2" data-line-number="2"><span class="kw">svymean</span>(<span class="op">~</span>poverty, <span class="dt">design=</span>des2)</a></code></pre></div>
<pre><code>##            mean    SE
## poverty 0.15189 0.007</code></pre>
<div class="sourceCode" id="cb73"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb73-1" data-line-number="1">fit&lt;-<span class="kw">svyglm</span>(poverty<span class="op">~</span><span class="kw">cut</span>(age, <span class="dt">breaks =</span> <span class="dv">5</span>), des2, <span class="dt">family=</span>binomial)</a></code></pre></div>
<pre><code>## Warning in eval(family$initialize): non-integer #successes in a binomial glm!

## Warning in eval(family$initialize): non-integer #successes in a binomial glm!

## Warning in eval(family$initialize): non-integer #successes in a binomial glm!

## Warning in eval(family$initialize): non-integer #successes in a binomial glm!

## Warning in eval(family$initialize): non-integer #successes in a binomial glm!

## Warning in eval(family$initialize): non-integer #successes in a binomial glm!

## Warning in eval(family$initialize): non-integer #successes in a binomial glm!

## Warning in eval(family$initialize): non-integer #successes in a binomial glm!

## Warning in eval(family$initialize): non-integer #successes in a binomial glm!

## Warning in eval(family$initialize): non-integer #successes in a binomial glm!

## Warning in eval(family$initialize): non-integer #successes in a binomial glm!

## Warning in eval(family$initialize): non-integer #successes in a binomial glm!

## Warning in eval(family$initialize): non-integer #successes in a binomial glm!

## Warning in eval(family$initialize): non-integer #successes in a binomial glm!

## Warning in eval(family$initialize): non-integer #successes in a binomial glm!

## Warning in eval(family$initialize): non-integer #successes in a binomial glm!

## Warning in eval(family$initialize): non-integer #successes in a binomial glm!

## Warning in eval(family$initialize): non-integer #successes in a binomial glm!

## Warning in eval(family$initialize): non-integer #successes in a binomial glm!</code></pre>
<div class="sourceCode" id="cb75"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb75-1" data-line-number="1"><span class="kw">summary</span>(fit)</a></code></pre></div>
<pre><code>## 
## Call:
## svyglm(formula = poverty ~ cut(age, breaks = 5), des2, family = binomial)
## 
## Survey design:
## svrepdesign.default(data = cpsmar10tx, repweights = cpsmar10tx[, 
##     c(8:167)], weights = ~wtsupp, type = &quot;JK1&quot;, scale = 0.025)
## 
## Coefficients:
##                               Estimate Std. Error t value             Pr(&gt;|t|)
## (Intercept)                    -1.0512     0.1147  -9.163 0.000000000000000288
## cut(age, breaks = 5)(29,43]    -0.7178     0.1405  -5.109 0.000000938524602554
## cut(age, breaks = 5)(43,57]    -0.7677     0.1412  -5.436 0.000000207282361670
## cut(age, breaks = 5)(57,71]    -1.1828     0.1690  -7.000 0.000000000072807386
## cut(age, breaks = 5)(71,85.1]  -0.8162     0.2249  -3.629             0.000385
##                                  
## (Intercept)                   ***
## cut(age, breaks = 5)(29,43]   ***
## cut(age, breaks = 5)(43,57]   ***
## cut(age, breaks = 5)(57,71]   ***
## cut(age, breaks = 5)(71,85.1] ***
## ---
## Signif. codes:  0 &#39;***&#39; 0.001 &#39;**&#39; 0.01 &#39;*&#39; 0.05 &#39;.&#39; 0.1 &#39; &#39; 1
## 
## (Dispersion parameter for binomial family taken to be 4239)
## 
## Number of Fisher Scoring iterations: 4</code></pre>
</div>
</div>
</section>

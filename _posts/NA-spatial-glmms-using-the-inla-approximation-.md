---
title: "Spatial GLMM(s) using the INLA Approximation "

author: "Corey S. Sparks, Ph.D."
date: "31 July, 2020"
layout: post
---


<section class="main-content">
<div id="the-inla-approach-to-bayesian-models" class="section level2">
<h2>The INLA Approach to Bayesian models</h2>
<p>The Integrated Nested Laplace Approximation, or INLA, approach is a recently developed, computationally simpler method for fitting Bayesian models [(Rue et al., <a href="http://onlinelibrary.wiley.com/store/10.1111/j.1467-9868.2008.00700.x/asset/j.1467-9868.2008.00700.x.pdf?v=1&amp;t=ih5b86ev&amp;s=9078c3b0adb48d4c15bc49ae3ededc6d1cd684c5">2009</a>, compared to traditional Markov Chain Monte Carlo (MCMC) approaches. INLA fits models that are classified as latent Gaussian models, which are applicable in many settings (Martino &amp; Rue, <a href="http://www.bias-project.org.uk/gmrfcourse/inla-program.pdf">2010</a>. In general, INLA fits a general form of additive models such as:</p>
<p><span class="math inline">\(\eta = \alpha + \sum_{j=1}^{nf} f^{(j)}(u_{ij}) + \sum_{k=1}^{n\beta}\beta_k z_{ki} + \epsilon_i\)</span></p>
<p>where <span class="math inline">\(\eta\)</span> is the linear predictor for a generalized linear model formula, and is composed of a linear function of some variables u, <span class="math inline">\(\beta\)</span> are the effects of covariates, z, and <span class="math inline">\(\epsilon\)</span> is an unstructured residual (Rue et al., 2009). As this model is often parameterized as a Bayesian one, we are interested in the posterior marginal distributions of all the model parameters. Rue and Martino <a href="http://www.sciencedirect.com/science/article/pii/S0378375807000845">(2007)</a> show that the posterior marginal for the random effects (x) in such models can be approximated as:</p>
<p><span class="math inline">\(\tilde{p}(x_i|y) = \sum_k \tilde{p}(x_i|\theta_k, y) \tilde{p}(\theta_k|y) \Delta_k\)</span></p>
<p>via numerical integration (Rue &amp; Martino, 2007; Schrodle &amp; Held, <a href="http://onlinelibrary.wiley.com/doi/10.1002/env.1065/full">2011a</a>, <a href="http://link.springer.com/article/10.1007/s00180-010-0208-2">2011b</a>). The posterior distribution of the hyperparameters (<span class="math inline">\(\theta\)</span>) of the model can also be approximated as:</p>
<p><span class="math inline">\(\tilde{p}(\theta | y)) \propto \frac{p(x, \theta, y)}{\tilde{p}G(x| \theta,y)} \mid _{x} = x^*(\theta)\)</span></p>
<p>, where G is a Gaussian approximation of the posterior and <span class="math inline">\(x^*(\theta)\)</span> is the mode of the conditional distribution of <span class="math inline">\(p(x|\theta,y)\)</span>. Thus, instead of using MCMC to find an iterative, sampling-based estimate of the posterior, it is arrived at numerically. This method of fitting the spatial models specified above has been presented by numerous authors (Blangiardo &amp; Cameletti, <a href="https://books.google.com/books?hl=en&amp;lr=&amp;id=--HuBgAAQBAJ&amp;oi=fnd&amp;pg=PA259&amp;dq=Blangiardo+%26+Cameletti,+2015&amp;ots=VSDEJ7wfM2&amp;sig=graudrEKTevK2HR7nozmZ-Y5N0Q#v=onepage&amp;q=Blangiardo%20%26%20Cameletti%2C%202015&amp;f=false">2015</a>; Blangiardo et al., <a href="http://www.sciencedirect.com/science/article/pii/S1877584513000336">2013</a>; Lindgren &amp; Rue, <a href="http://www.sciencedirect.com/science/article/pii/S2211675315000780">2015</a>; Martins et al., <a href="http://www.sciencedirect.com/science/article/pii/S0167947313001552">2013</a>; Schrodle &amp; Held, 2011a, 2011b), with comparable results to MCMC.</p>
<div id="libraries" class="section level3">
<h3>Libraries</h3>
<div class="sourceCode" id="cb1"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb1-1" data-line-number="1"><span class="co">#library(rgdal)</span></a>
<a class="sourceLine" id="cb1-2" data-line-number="2"><span class="kw">library</span>(spdep)</a>
<a class="sourceLine" id="cb1-3" data-line-number="3"><span class="kw">library</span>(RColorBrewer)</a>
<a class="sourceLine" id="cb1-4" data-line-number="4"><span class="kw">library</span>(lattice)</a>
<a class="sourceLine" id="cb1-5" data-line-number="5"><span class="kw">library</span>(INLA)</a>
<a class="sourceLine" id="cb1-6" data-line-number="6"><span class="kw">library</span>(tigris)</a>
<a class="sourceLine" id="cb1-7" data-line-number="7"><span class="kw">library</span>(tidycensus)</a>
<a class="sourceLine" id="cb1-8" data-line-number="8"><span class="kw">library</span>(ggplot2)</a>
<a class="sourceLine" id="cb1-9" data-line-number="9"><span class="kw">library</span>(dplyr)</a></code></pre></div>
</div>
<div id="data" class="section level3">
<h3>Data</h3>
<p>I have the data on my github site under the <a href="https://github.com/coreysparks/data/tree/master/nhgis_vs">nhgis_vs</a> page. These are data from the <a href="https://www.nhgis.org/">NHGIS</a> project by <a href="https://www.ipums.org/">IPUMS</a> who started providing birth and death data from the US Vital statistics program.</p>
<p>The data we will use here are infant mortality rates in US counties between 2000 and 2007.</p>
<div class="sourceCode" id="cb2"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb2-1" data-line-number="1">files&lt;-<span class="kw">list.files</span>(<span class="st">&quot;~/ExpanDrive/Google Drive/classes/dem7473/data/nhgis0022_csv/nhgis0022_csv/&quot;</span>, <span class="dt">pattern =</span> <span class="st">&quot;*.csv&quot;</span>, <span class="dt">full.names =</span> T)</a>
<a class="sourceLine" id="cb2-2" data-line-number="2">vital&lt;-<span class="kw">lapply</span>(files, read.csv, <span class="dt">header=</span>T)</a>
<a class="sourceLine" id="cb2-3" data-line-number="3"></a>
<a class="sourceLine" id="cb2-4" data-line-number="4"></a>
<a class="sourceLine" id="cb2-5" data-line-number="5"><span class="kw">library</span>(plyr)</a>
<a class="sourceLine" id="cb2-6" data-line-number="6">df &lt;-<span class="st"> </span><span class="kw">ldply</span>(vital, data.frame)</a>
<a class="sourceLine" id="cb2-7" data-line-number="7">df<span class="op">$</span>cofips&lt;-<span class="kw">paste</span>(<span class="kw">substr</span>(df<span class="op">$</span>GISJOIN, <span class="dv">2</span>,<span class="dv">3</span>), <span class="kw">substr</span>(df<span class="op">$</span>GISJOIN, <span class="dv">5</span>,<span class="dv">7</span>), <span class="dt">sep=</span><span class="st">&quot;&quot;</span>)</a>
<a class="sourceLine" id="cb2-8" data-line-number="8"></a>
<a class="sourceLine" id="cb2-9" data-line-number="9">df&lt;-df<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb2-10" data-line-number="10"><span class="st">  </span><span class="kw">filter</span>(YEAR <span class="op">%in%</span><span class="dv">2000</span><span class="op">:</span><span class="dv">2007</span>)<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb2-11" data-line-number="11"><span class="st">  </span><span class="kw">mutate</span>(<span class="dt">rate=</span><span class="kw">as.numeric</span>(AGWJ001) )<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb2-12" data-line-number="12"><span class="st">  </span><span class="kw">select</span>(YEAR, cofips,rate)</a>
<a class="sourceLine" id="cb2-13" data-line-number="13"><span class="kw">head</span>(df)</a></code></pre></div>
<pre><code>##   YEAR cofips rate
## 1 2000  01001   34
## 2 2000  01003   61
## 3 2000  01005  125
## 4 2000  01007   70
## 5 2000  01009   89
## 6 2000  01011  242</code></pre>
</div>
<div id="census-intercensus-population-estimates" class="section level3">
<h3>Census intercensus population estimates</h3>
<p>From the Census population estimates <a href="http://www2.census.gov/programs-surveys/popest/">program</a></p>
<div class="sourceCode" id="cb4"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb4-1" data-line-number="1">popurl&lt;-<span class="kw">url</span>(<span class="st">&quot;http://www2.census.gov/programs-surveys/popest/datasets/2000-2010/intercensal/county/co-est00int-tot.csv&quot;</span>)</a>
<a class="sourceLine" id="cb4-2" data-line-number="2">pops&lt;-<span class="kw">read.csv</span>(popurl)</a>
<a class="sourceLine" id="cb4-3" data-line-number="3"><span class="kw">names</span>(pops)&lt;-<span class="kw">tolower</span>(<span class="kw">names</span>(pops))</a>
<a class="sourceLine" id="cb4-4" data-line-number="4">pops&lt;-pops<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb4-5" data-line-number="5"><span class="st">  </span><span class="kw">mutate</span>(<span class="dt">cofips =</span> <span class="kw">paste</span>(<span class="kw">sprintf</span>(<span class="dt">fmt =</span> <span class="st">&quot;%02d&quot;</span>, state), <span class="kw">sprintf</span>(<span class="dt">fmt =</span> <span class="st">&quot;%03d&quot;</span>,county), <span class="dt">sep=</span><span class="st">&quot;&quot;</span>))<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb4-6" data-line-number="6"><span class="st">  </span><span class="kw">filter</span>(sumlev<span class="op">==</span><span class="dv">50</span>, <span class="op">!</span>state<span class="op">%in%</span><span class="kw">c</span>(<span class="dv">2</span>, <span class="dv">15</span>))</a>
<a class="sourceLine" id="cb4-7" data-line-number="7"></a>
<a class="sourceLine" id="cb4-8" data-line-number="8"><span class="kw">head</span>(pops)</a></code></pre></div>
<pre><code>##   sumlev region division state county  stname        ctyname estimatesbase2000
## 1     50      3        6     1      1 Alabama Autauga County             43751
## 2     50      3        6     1      3 Alabama Baldwin County            140416
## 3     50      3        6     1      5 Alabama Barbour County             29042
## 4     50      3        6     1      7 Alabama    Bibb County             19856
## 5     50      3        6     1      9 Alabama  Blount County             50982
## 6     50      3        6     1     11 Alabama Bullock County             11603
##   popestimate2000 popestimate2001 popestimate2002 popestimate2003
## 1           44021           44889           45909           46800
## 2          141342          144875          147957          151509
## 3           29015           28863           28653           28594
## 4           19913           21028           21199           21399
## 5           51107           51845           52551           53457
## 6           11581           11358           11256           11316
##   popestimate2004 popestimate2005 popestimate2006 popestimate2007
## 1           48366           49676           51328           52405
## 2          156266          162183          168121          172404
## 3           28287           28027           27861           27757
## 4           21721           22042           22099           22438
## 5           54124           54624           55485           56240
## 6           11056           11011           10776           11011
##   popestimate2008 popestimate2009 census2010pop popestimate2010 cofips
## 1           53277           54135         54571           54632  01001
## 2          175827          179406        182265          183195  01003
## 3           27808           27657         27457           27411  01005
## 4           22705           22941         22915           22867  01007
## 5           57055           57341         57322           57338  01009
## 6           10953           10987         10914           10890  01011</code></pre>
<div id="data-prep" class="section level4">
<h4>Data prep</h4>
<div class="sourceCode" id="cb6"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb6-1" data-line-number="1">pops.long&lt;-<span class="kw">reshape</span>(<span class="dt">data =</span> pops, <span class="dt">idvar =</span> <span class="st">&quot;cofips&quot;</span>, <span class="dt">varying =</span> <span class="kw">list</span>(<span class="kw">names</span>(pops)[<span class="dv">9</span><span class="op">:</span><span class="dv">16</span>]), <span class="dt">direction=</span><span class="st">&quot;long&quot;</span>, <span class="dt">drop =</span> <span class="kw">names</span>(pops)[<span class="kw">c</span>(<span class="dv">2</span>,<span class="dv">3</span>,<span class="dv">4</span>,<span class="dv">5</span>,<span class="dv">6</span>,<span class="dv">8</span>,<span class="dv">17</span>,<span class="dv">18</span>,<span class="dv">19</span>,<span class="dv">20</span>)], <span class="dt">v.names =</span> <span class="st">&quot;population&quot;</span>)</a>
<a class="sourceLine" id="cb6-2" data-line-number="2">pops.long<span class="op">$</span>year&lt;-pops.long<span class="op">$</span>time<span class="op">+</span><span class="dv">1999</span></a>
<a class="sourceLine" id="cb6-3" data-line-number="3"><span class="kw">head</span>(pops.long)</a></code></pre></div>
<pre><code>##         sumlev        ctyname cofips time population year
## 01001.1     50 Autauga County  01001    1      44021 2000
## 01003.1     50 Baldwin County  01003    1     141342 2000
## 01005.1     50 Barbour County  01005    1      29015 2000
## 01007.1     50    Bibb County  01007    1      19913 2000
## 01009.1     50  Blount County  01009    1      51107 2000
## 01011.1     50 Bullock County  01011    1      11581 2000</code></pre>
<div class="sourceCode" id="cb8"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb8-1" data-line-number="1">dat.long&lt;-<span class="kw">merge</span>(pops.long, df, <span class="dt">by.x=</span><span class="kw">c</span>(<span class="st">&quot;cofips&quot;</span>, <span class="st">&quot;year&quot;</span>), <span class="dt">by.y=</span><span class="kw">c</span>(<span class="st">&quot;cofips&quot;</span>, <span class="st">&quot;YEAR&quot;</span>))</a>
<a class="sourceLine" id="cb8-2" data-line-number="2"><span class="kw">head</span>(dat.long)</a></code></pre></div>
<pre><code>##   cofips year sumlev        ctyname time population rate
## 1  01001 2000     50 Autauga County    1      44021   34
## 2  01001 2001     50 Autauga County    2      44889   78
## 3  01001 2002     50 Autauga County    3      45909   83
## 4  01001 2003     50 Autauga County    4      46800   79
## 5  01001 2004     50 Autauga County    5      48366   76
## 6  01001 2005     50 Autauga County    6      49676  124</code></pre>
</div>
</div>
</div>
<div id="get-census-data-using-tidycensus" class="section level2">
<h2>Get census data using tidycensus</h2>
<p>Here I get data from the 2000 decennial census summary file 3</p>
<div class="sourceCode" id="cb10"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb10-1" data-line-number="1"><span class="co">#v00&lt;-load_variables(year=2000, dataset = &quot;sf3&quot;, cache = T)</span></a>
<a class="sourceLine" id="cb10-2" data-line-number="2">cov_dat&lt;-<span class="kw">get_decennial</span>(<span class="dt">geography =</span> <span class="st">&quot;county&quot;</span>, <span class="dt">year =</span> <span class="dv">2000</span>, <span class="dt">sumfile =</span> <span class="st">&quot;sf3&quot;</span>,</a>
<a class="sourceLine" id="cb10-3" data-line-number="3">                       <span class="dt">summary_var =</span> <span class="st">&quot;P001001&quot;</span>,</a>
<a class="sourceLine" id="cb10-4" data-line-number="4">                       <span class="dt">variables =</span> <span class="kw">c</span>(<span class="st">&quot;P007003&quot;</span>, <span class="st">&quot;P007004&quot;</span>,<span class="st">&quot;P007010&quot;</span>,<span class="st">&quot;P053001&quot;</span>, <span class="st">&quot;P089001&quot;</span>, <span class="st">&quot;P089002&quot;</span> ),</a>
<a class="sourceLine" id="cb10-5" data-line-number="5">                      <span class="dt">output =</span> <span class="st">&quot;wide&quot;</span>)</a></code></pre></div>
<pre><code>## Getting data from the 2000 decennial Census</code></pre>
<div class="sourceCode" id="cb12"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb12-1" data-line-number="1">cov_dat&lt;-cov_dat<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb12-2" data-line-number="2"><span class="st">  </span><span class="kw">mutate</span>(<span class="dt">cofips=</span>GEOID,<span class="dt">pwhite=</span>P007003<span class="op">/</span>summary_value, <span class="dt">pblack=</span>P007004<span class="op">/</span>summary_value, <span class="dt">phisp=</span>P007010<span class="op">/</span>summary_value,<span class="dt">medhhinc=</span><span class="kw">as.numeric</span>(<span class="kw">scale</span>(P053001)), <span class="dt">ppov=</span>P089002<span class="op">/</span>P089001)</a>
<a class="sourceLine" id="cb12-3" data-line-number="3"></a>
<a class="sourceLine" id="cb12-4" data-line-number="4"></a>
<a class="sourceLine" id="cb12-5" data-line-number="5">final.dat&lt;-<span class="kw">merge</span>(dat.long, cov_dat, <span class="dt">by=</span><span class="st">&quot;cofips&quot;</span>)</a>
<a class="sourceLine" id="cb12-6" data-line-number="6"><span class="kw">head</span>(final.dat)</a></code></pre></div>
<pre><code>##   cofips year sumlev        ctyname time population rate GEOID           NAME
## 1  01001 2006     50 Autauga County    7      51328   93 01001 Autauga County
## 2  01001 2003     50 Autauga County    4      46800   79 01001 Autauga County
## 3  01001 2004     50 Autauga County    5      48366   76 01001 Autauga County
## 4  01001 2005     50 Autauga County    6      49676  124 01001 Autauga County
## 5  01001 2000     50 Autauga County    1      44021   34 01001 Autauga County
## 6  01001 2007     50 Autauga County    8      52405   83 01001 Autauga County
##   P007003 P007004 P007010 P053001 P089001 P089002 summary_value    pwhite
## 1   34760    7450     394   42013   43377    4738         43671 0.7959515
## 2   34760    7450     394   42013   43377    4738         43671 0.7959515
## 3   34760    7450     394   42013   43377    4738         43671 0.7959515
## 4   34760    7450     394   42013   43377    4738         43671 0.7959515
## 5   34760    7450     394   42013   43377    4738         43671 0.7959515
## 6   34760    7450     394   42013   43377    4738         43671 0.7959515
##      pblack       phisp  medhhinc      ppov
## 1 0.1705938 0.009022005 0.7593459 0.1092284
## 2 0.1705938 0.009022005 0.7593459 0.1092284
## 3 0.1705938 0.009022005 0.7593459 0.1092284
## 4 0.1705938 0.009022005 0.7593459 0.1092284
## 5 0.1705938 0.009022005 0.7593459 0.1092284
## 6 0.1705938 0.009022005 0.7593459 0.1092284</code></pre>
<div id="create-expected-numbers-of-cases" class="section level3">
<h3>Create expected numbers of cases</h3>
<p>In count data models, and spatial epidemiology, we have to express the raw counts of events relative to some expected value, or population offset, see <a href="http://rpubs.com/corey_sparks/361894">this Rpub</a> for a reminder.</p>
<div class="sourceCode" id="cb14"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb14-1" data-line-number="1"><span class="co">#ratesyr&lt;-aggregate(rate~year, final.dat,mean) #in this case, we will standardize to the average IMR for the period</span></a>
<a class="sourceLine" id="cb14-2" data-line-number="2"><span class="co">#ratesyr$E&lt;-ratesyr$rate</span></a>
<a class="sourceLine" id="cb14-3" data-line-number="3"><span class="co">#final.dat&lt;-merge(final.dat, ratesyr[,-2], by=&quot;year&quot;)</span></a>
<a class="sourceLine" id="cb14-4" data-line-number="4"><span class="co">#rates&lt;-aggregate(rate~1, final.dat, mean)</span></a>
<a class="sourceLine" id="cb14-5" data-line-number="5">final.dat<span class="op">$</span>E_d&lt;-<span class="kw">mean</span>(final.dat<span class="op">$</span>rate)</a>
<a class="sourceLine" id="cb14-6" data-line-number="6"></a>
<a class="sourceLine" id="cb14-7" data-line-number="7">final.dat&lt;-final.dat[<span class="kw">order</span>(final.dat<span class="op">$</span>cofips, final.dat<span class="op">$</span>year),]</a>
<a class="sourceLine" id="cb14-8" data-line-number="8">final.dat<span class="op">$</span>id&lt;-<span class="dv">1</span><span class="op">:</span><span class="kw">dim</span>(final.dat)[<span class="dv">1</span>]</a>
<a class="sourceLine" id="cb14-9" data-line-number="9"></a>
<a class="sourceLine" id="cb14-10" data-line-number="10"><span class="kw">head</span>(final.dat)</a></code></pre></div>
<pre><code>##   cofips year sumlev        ctyname time population rate GEOID           NAME
## 5  01001 2000     50 Autauga County    1      44021   34 01001 Autauga County
## 8  01001 2001     50 Autauga County    2      44889   78 01001 Autauga County
## 7  01001 2002     50 Autauga County    3      45909   83 01001 Autauga County
## 2  01001 2003     50 Autauga County    4      46800   79 01001 Autauga County
## 3  01001 2004     50 Autauga County    5      48366   76 01001 Autauga County
## 4  01001 2005     50 Autauga County    6      49676  124 01001 Autauga County
##   P007003 P007004 P007010 P053001 P089001 P089002 summary_value    pwhite
## 5   34760    7450     394   42013   43377    4738         43671 0.7959515
## 8   34760    7450     394   42013   43377    4738         43671 0.7959515
## 7   34760    7450     394   42013   43377    4738         43671 0.7959515
## 2   34760    7450     394   42013   43377    4738         43671 0.7959515
## 3   34760    7450     394   42013   43377    4738         43671 0.7959515
## 4   34760    7450     394   42013   43377    4738         43671 0.7959515
##      pblack       phisp  medhhinc      ppov      E_d id
## 5 0.1705938 0.009022005 0.7593459 0.1092284 72.33683  1
## 8 0.1705938 0.009022005 0.7593459 0.1092284 72.33683  2
## 7 0.1705938 0.009022005 0.7593459 0.1092284 72.33683  3
## 2 0.1705938 0.009022005 0.7593459 0.1092284 72.33683  4
## 3 0.1705938 0.009022005 0.7593459 0.1092284 72.33683  5
## 4 0.1705938 0.009022005 0.7593459 0.1092284 72.33683  6</code></pre>
<div class="sourceCode" id="cb16"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb16-1" data-line-number="1"><span class="kw">options</span>(<span class="dt">scipen=</span><span class="dv">999</span>)</a></code></pre></div>
<p>Next we make the spatial information, we get the polygons from census directly using <code>counties</code> from the <code>tigris</code> package. We drop counties not in the contiguous 48 US states.</p>
<div class="sourceCode" id="cb17"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb17-1" data-line-number="1">us_co&lt;-<span class="kw">counties</span>( <span class="dt">cb =</span> T)</a>
<a class="sourceLine" id="cb17-2" data-line-number="2">us_co&lt;-us_co<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb17-3" data-line-number="3"><span class="st">  </span><span class="kw">subset</span>(<span class="op">!</span>STATEFP<span class="op">%in%</span><span class="kw">c</span>(<span class="st">&quot;02&quot;</span>, <span class="st">&quot;15&quot;</span>, <span class="st">&quot;60&quot;</span>, <span class="st">&quot;66&quot;</span>, <span class="st">&quot;69&quot;</span>, <span class="st">&quot;72&quot;</span>, <span class="st">&quot;78&quot;</span>))<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb17-4" data-line-number="4"><span class="st">  </span><span class="kw">filter</span>(STATEFP<span class="op">%in%</span><span class="kw">c</span>(<span class="st">&quot;01&quot;</span>, <span class="st">&quot;05&quot;</span>, <span class="st">&quot;12&quot;</span>, <span class="st">&quot;13&quot;</span>, <span class="st">&quot;21&quot;</span>, <span class="st">&quot;22&quot;</span>, <span class="st">&quot;28&quot;</span>, <span class="st">&quot;37&quot;</span>, <span class="st">&quot;45&quot;</span>, <span class="st">&quot;47&quot;</span>, <span class="st">&quot;48&quot;</span>, <span class="st">&quot;51&quot;</span>, <span class="st">&quot;40&quot;</span>))</a></code></pre></div>
</div>
</div>
<div id="construction-of-spatial-relationships" class="section level2">
<h2>Construction of spatial relationships:</h2>
<div id="contiguity-based-neighbors" class="section level4">
<h4>Contiguity based neighbors</h4>
<p>In a general sense, we can think of a square grid. Cells that share common elements of their geometry are said to be “neighbors”. There are several ways to describe these patterns, and for polygons, we generally use the rules of the chess board.</p>
<p><em>Rook adjacency</em> Neighbors must share a line segment</p>
<p><em>Queen adjacency</em> Neighbors must share a vertex or a line segment</p>
<p>If polygons share these boundaries (based on the specific definition: rook or queen), they are said to be “spatial neighbors” of one another. The figure below illustrates this principle.</p>
<p>For an observation of interest, the pink area, the Rood adjacent areas are those in green in the figure, because they share a line segment. For the second part of the figure on the right, the pink area has different sets of neighbors, compared to the Rook rule neighbors, because the area also shares vertices with other polygons, making them Queen neighbors.</p>
<div class="figure">
<img src="/home/corey/ExpanDrive/Google%20Drive/classes/dem7263/Rcode15/images/adj.png" alt="Adjacency using Chessboard Rules" />
<p class="caption">Adjacency using Chessboard Rules</p>
</div>
</div>
<div id="order-of-adjacency" class="section level3">
<h3>Order of adjacency</h3>
<p>The figure above also highlights the order of adjacency among observations. By order of adjacency, we simply men that observations are either immediate neighbors (the green areas), or they are neighbors of immediate neighbors. These are referred to as first and second order neighbors.</p>
<p>So, we can see, that the yellow polygons are the neighboring areas for this tract, which allows us to think about what the <em>spatial structure</em> of the area surrounding this part of campus.</p>
<p>For an example, let’s consider the case of San Antonio again. If our data are polygons, then there is a function in the <code>spdep</code> library in R, <code>poly2nb</code> that will take a polygon layer and find the neighbors of all areas using either a queen or rook rule. First we form the neighbors using the rook rule for all the tracts in Bexar County.</p>
</div>
<div id="distance-based-association" class="section level3">
<h3>Distance based association</h3>
<p>The queen and rook rules are useful for polygon features, but distance based contiguity is useful for all feature types (points, polygons, lines). The idea is similar to the polygon adjacency rule from above, but the distance rule is based on the calculated distance between areas. There are a variety of distance metrics that are used in statistics, but the most commonly assumed one is the Euclidean distance. The Euclidean distance between any two points is:</p>
<p><span class="math display">\[D^2 = \sqrt{\left (x_1 - x_2 \right)^2 + \left (y_1 - y_2 \right)^2 } \]</span> Where x and y are the coordinates of each of the two areas. For polygons, these coordinates are typically the centroid of the polygon (you may have noticed this above when we were plotting the neighbor lists), while for point features, these are the two dimensional geometry of the feature. The collection of these distances between all features forms what is known as the distance matrix between observations. This summarizes all distances between all features in the data.</p>
</div>
</div>
<div id="k-nearest-neighbors" class="section level1">
<h1>K nearest neighbors</h1>
<ul>
<li><p>A useful way to use distances is to construct a k-nearest neighbors set.</p></li>
<li><p>This will find the “k” closest observations for each observation, where k is some integer.</p></li>
<li><p>For instance if we find the k=3 nearest neighbors, then each observation will have 3 neighbors, which are the closest observations to it, <em>regardless of the distance between them</em> which is important.</p></li>
<li><p>Using the k nearest neighbor rule, two observations could potentially be very far apart and still be considered neighbors.</p></li>
</ul>
<div class="sourceCode" id="cb18"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb18-1" data-line-number="1"><span class="co">#In INLA, we don&#39;t need FIPS codes, we need a simple numeric index for our counties</span></a>
<a class="sourceLine" id="cb18-2" data-line-number="2">us_co<span class="op">$</span>struct&lt;-<span class="dv">1</span><span class="op">:</span><span class="kw">dim</span>(us_co)[<span class="dv">1</span>]</a>
<a class="sourceLine" id="cb18-3" data-line-number="3">nbs&lt;-<span class="kw">knearneigh</span>(<span class="kw">coordinates</span>(<span class="kw">as_Spatial</span>(us_co) ), <span class="dt">k =</span> <span class="dv">5</span>, <span class="dt">longlat =</span> T) <span class="co">#k=5 nearest neighbors</span></a>
<a class="sourceLine" id="cb18-4" data-line-number="4">nbs&lt;-<span class="kw">knn2nb</span>(nbs, <span class="dt">row.names =</span> us_co<span class="op">$</span>struct, <span class="dt">sym =</span> T) <span class="co">#force symmetry!!</span></a>
<a class="sourceLine" id="cb18-5" data-line-number="5">mat &lt;-<span class="st"> </span><span class="kw">nb2mat</span>(nbs, <span class="dt">style=</span><span class="st">&quot;B&quot;</span>,<span class="dt">zero.policy=</span><span class="ot">TRUE</span>)</a>
<a class="sourceLine" id="cb18-6" data-line-number="6"><span class="kw">colnames</span>(mat) &lt;-<span class="st"> </span><span class="kw">rownames</span>(mat) </a>
<a class="sourceLine" id="cb18-7" data-line-number="7">mat &lt;-<span class="st"> </span><span class="kw">as.matrix</span>(mat[<span class="dv">1</span><span class="op">:</span><span class="kw">dim</span>(mat)[<span class="dv">1</span>], <span class="dv">1</span><span class="op">:</span><span class="kw">dim</span>(mat)[<span class="dv">1</span>]])</a>
<a class="sourceLine" id="cb18-8" data-line-number="8"></a>
<a class="sourceLine" id="cb18-9" data-line-number="9"></a>
<a class="sourceLine" id="cb18-10" data-line-number="10"><span class="kw">nb2INLA</span>(<span class="st">&quot;cl_graph&quot;</span>,nbs)</a>
<a class="sourceLine" id="cb18-11" data-line-number="11">am_adj &lt;-<span class="kw">paste</span>(<span class="kw">getwd</span>(),<span class="st">&quot;/cl_graph&quot;</span>,<span class="dt">sep=</span><span class="st">&quot;&quot;</span>)</a>
<a class="sourceLine" id="cb18-12" data-line-number="12">H&lt;-<span class="kw">inla.read.graph</span>(<span class="dt">filename=</span><span class="st">&quot;cl_graph&quot;</span>)</a>
<a class="sourceLine" id="cb18-13" data-line-number="13"><span class="co">#image(inla.graph2matrix(H), xlab=&quot;&quot;, ylab=&quot;&quot;, main=&quot;&quot;)</span></a></code></pre></div>
<div id="plot-geographies" class="section level3">
<h3>Plot geographies</h3>
<div class="sourceCode" id="cb19"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb19-1" data-line-number="1"><span class="kw">library</span>(sf)</a>
<a class="sourceLine" id="cb19-2" data-line-number="2">us_co&lt;-<span class="kw">st_as_sf</span>(us_co)</a>
<a class="sourceLine" id="cb19-3" data-line-number="3">us_co<span class="op">$</span>cofips&lt;-<span class="kw">paste</span>(us_co<span class="op">$</span>STATEFP, us_co<span class="op">$</span>COUNTYFP, <span class="dt">sep=</span><span class="st">&quot;&quot;</span>)</a>
<a class="sourceLine" id="cb19-4" data-line-number="4">us_co<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb19-5" data-line-number="5"><span class="st">  </span><span class="kw">ggplot</span>()<span class="op">+</span><span class="kw">geom_sf</span>()<span class="op">+</span><span class="kw">coord_sf</span>(<span class="dt">crs =</span><span class="dv">7603</span>)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/INLA_Infant_Mortality_files/figure-html/unnamed-chunk-7-1.png" /><!-- --></p>
<div class="sourceCode" id="cb20"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb20-1" data-line-number="1">final.dat&lt;-<span class="kw">merge</span>( us_co,final.dat, <span class="dt">by=</span><span class="st">&quot;cofips&quot;</span>, <span class="dt">all.y=</span>F)</a>
<a class="sourceLine" id="cb20-2" data-line-number="2">final.dat&lt;-final.dat[<span class="kw">order</span>(final.dat<span class="op">$</span>cofips, final.dat<span class="op">$</span>year),]</a>
<a class="sourceLine" id="cb20-3" data-line-number="3">final.dat<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb20-4" data-line-number="4"><span class="st">  </span><span class="kw">filter</span>(year<span class="op">==</span><span class="dv">2000</span>)<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb20-5" data-line-number="5"><span class="st">  </span><span class="kw">ggplot</span>()<span class="op">+</span><span class="kw">geom_sf</span>(<span class="kw">aes</span>(<span class="dt">fill=</span>rate, <span class="dt">col=</span>rate))<span class="op">+</span><span class="kw">coord_sf</span>(<span class="dt">crs =</span><span class="dv">7603</span>)<span class="op">+</span><span class="kw">ggtitle</span>(<span class="st">&quot;Infant Mortality Rate - 2000&quot;</span>)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/INLA_Infant_Mortality_files/figure-html/unnamed-chunk-8-1.png" /><!-- --></p>
</div>
<div id="model-setup" class="section level2">
<h2>Model setup</h2>
<ul>
<li>We have a count outcome (deaths and births), in counties over time, and a set of time-constant covariates.</li>
<li><p>We have several options in the GLM framework with which to model these data, for example:</p></li>
<li><p>Binomial - <span class="math display">\[y_{ij} \sim Bin(\pi_{ij}) \text{:  } logit(\pi_{ij} ) = \beta_{0}+ x&#39;\beta_k \]</span></p></li>
<li><p>Poisson - <span class="math display">\[y_{ij} \sim Pois(\lambda_{ij} E_{ij}) \text{:  } log(\lambda_{ij} ) = log(E_{ij}) + \beta_{0}+ x&#39;\beta_k \]</span></p></li>
<li><p>Negative Binomial - <span class="math display">\[y_{ij} \sim \text{Neg Bin} (\mu_{ij}, \alpha, E_{ij}) \text{:  } log(\mu_{ij} ) = log(E_{ij}) + \beta_{0}+ x&#39;\beta_k \]</span></p></li>
<li><p>In addition to various zero-inflated versions of these data.</p></li>
</ul>
<div class="sourceCode" id="cb21"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb21-1" data-line-number="1"><span class="kw">ggplot</span>(<span class="dt">data =</span> final.dat)<span class="op">+</span><span class="kw">geom_histogram</span>(<span class="kw">aes</span>(<span class="dt">x =</span><span class="kw">log</span>(rate) , <span class="dt">y=</span><span class="fl">0.5</span><span class="op">*</span>..density..))<span class="op">+</span><span class="kw">facet_wrap</span>(<span class="op">~</span>year)<span class="op">+</span></a>
<a class="sourceLine" id="cb21-2" data-line-number="2"><span class="st">  </span><span class="kw">ggtitle</span>(<span class="dt">label =</span> <span class="st">&quot;Distribution of Infant Mortality Rate by Year&quot;</span>, <span class="dt">subtitle =</span> <span class="st">&quot;US Counties, 2000-2007&quot;</span>)</a></code></pre></div>
<pre><code>## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.</code></pre>
<pre><code>## Warning: Removed 1849 rows containing non-finite values (stat_bin).</code></pre>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/INLA_Infant_Mortality_files/figure-html/unnamed-chunk-9-1.png" /><!-- --></p>
<div class="sourceCode" id="cb24"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb24-1" data-line-number="1"><span class="kw">ggplot</span>(<span class="dt">data =</span> final.dat)<span class="op">+</span><span class="kw">geom_histogram</span>(<span class="kw">aes</span>(<span class="dt">x =</span><span class="kw">log</span>(rate<span class="op">/</span>E_d) , <span class="dt">y=</span><span class="fl">0.5</span><span class="op">*</span>..density..))<span class="op">+</span><span class="kw">facet_wrap</span>(<span class="op">~</span>year)<span class="op">+</span></a>
<a class="sourceLine" id="cb24-2" data-line-number="2"><span class="st">  </span><span class="kw">ggtitle</span>(<span class="dt">label =</span> <span class="st">&quot;Distribution of Infant Mortality Relative Risk by Year&quot;</span>, <span class="dt">subtitle =</span> <span class="st">&quot;US Counties, 2000-2007&quot;</span>)</a></code></pre></div>
<pre><code>## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.</code></pre>
<pre><code>## Warning: Removed 1849 rows containing non-finite values (stat_bin).</code></pre>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/INLA_Infant_Mortality_files/figure-html/unnamed-chunk-9-2.png" /><!-- --></p>
<div class="sourceCode" id="cb27"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb27-1" data-line-number="1">final.dat<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb27-2" data-line-number="2"><span class="st">  </span>dplyr<span class="op">::</span><span class="kw">group_by</span>(cofips)<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb27-3" data-line-number="3"><span class="st">  </span>dplyr<span class="op">::</span><span class="kw">summarise</span>(<span class="dt">meanrate=</span><span class="kw">mean</span>(rate), <span class="dt">meanE=</span><span class="kw">mean</span>(E_d))<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb27-4" data-line-number="4"><span class="st">  </span>dplyr<span class="op">::</span><span class="kw">mutate</span>(<span class="dt">rate_e=</span>meanrate<span class="op">/</span>meanE)<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb27-5" data-line-number="5"><span class="st">  </span><span class="co">#ungroup()</span></a>
<a class="sourceLine" id="cb27-6" data-line-number="6"><span class="st">  </span><span class="co"># filter(year%in%c(2000))%&gt;%</span></a>
<a class="sourceLine" id="cb27-7" data-line-number="7"><span class="st">  </span><span class="co"># mutate(rate_e=rate/E_d)%&gt;%</span></a>
<a class="sourceLine" id="cb27-8" data-line-number="8"><span class="st">   </span><span class="kw">mutate</span>(<span class="dt">qrr=</span><span class="kw">cut</span>(rate_e, <span class="dt">breaks =</span> <span class="kw">quantile</span>(rate_e, <span class="dt">p=</span><span class="kw">seq</span>(<span class="dv">0</span>,<span class="dv">1</span>,<span class="dt">length.out =</span> <span class="dv">7</span>)), <span class="dt">include.lowest =</span> T))<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb27-9" data-line-number="9"><span class="st">  </span><span class="kw">ggplot</span>()<span class="op">+</span></a>
<a class="sourceLine" id="cb27-10" data-line-number="10"><span class="st">  </span><span class="kw">geom_sf</span>(<span class="kw">aes</span>(<span class="dt">fill=</span>qrr))<span class="op">+</span></a>
<a class="sourceLine" id="cb27-11" data-line-number="11"><span class="st">  </span><span class="kw">scale_fill_brewer</span>(<span class="dt">type=</span><span class="st">&quot;seq&quot;</span>, <span class="dt">palette =</span> <span class="st">&quot;RdBu&quot;</span>)<span class="op">+</span></a>
<a class="sourceLine" id="cb27-12" data-line-number="12"><span class="st">  </span><span class="kw">ggtitle</span>(<span class="dt">label=</span><span class="st">&quot;Relative Risk Quartile - period average 2000 to 2007&quot;</span>)<span class="op">+</span></a>
<a class="sourceLine" id="cb27-13" data-line-number="13"><span class="st">  </span><span class="kw">coord_sf</span>(<span class="dt">crs =</span> <span class="dv">7603</span>)</a></code></pre></div>
<pre><code>## `summarise()` ungrouping output (override with `.groups` argument)</code></pre>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/INLA_Infant_Mortality_files/figure-html/unnamed-chunk-10-1.png" /><!-- --></p>
<p>We can fit these model using the Bayesian framework with INLA.</p>
<p>First, we consider the basic GLM for the mortality outcome, with out any hierarchical structure. We can write this model as a Negative Binomial model, for instance as:</p>
<p><span class="math display">\[\text{Deaths}_{ij} \sim NB(\mu_{ij}, \gamma)\]</span> <span class="math display">\[\mu_{ij} = \text{log(E_d)}_{ij} + X&#39; \beta\]</span></p>
<p>INLA will use vague Normal priors for the <span class="math inline">\(\beta\)</span>’s, and we have other parameters in the model to specify priors for. INLA does not require you to specify all priors, as all parameters have a default prior specification. In this example, I will use a <span class="math inline">\(Gamma(1, .5)\)</span> prior for all hierarchical variance terms.</p>
<div class="sourceCode" id="cb29"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb29-1" data-line-number="1"><span class="co">#Model specification:</span></a>
<a class="sourceLine" id="cb29-2" data-line-number="2">f1&lt;-rate<span class="op">~</span><span class="kw">scale</span>(pblack)<span class="op">+</span><span class="kw">scale</span>(phisp)<span class="op">+</span><span class="kw">scale</span>(ppov)<span class="op">+</span>year</a>
<a class="sourceLine" id="cb29-3" data-line-number="3"></a>
<a class="sourceLine" id="cb29-4" data-line-number="4"><span class="co">#Model fit</span></a>
<a class="sourceLine" id="cb29-5" data-line-number="5">mod1&lt;-<span class="kw">inla</span>(<span class="dt">formula =</span> f1,<span class="dt">data =</span> final.dat, <span class="co">#linear predictor - fixed effects</span></a>
<a class="sourceLine" id="cb29-6" data-line-number="6">           <span class="dt">family =</span> <span class="st">&quot;nbinomial&quot;</span>, <span class="dt">E =</span> E_d,  <span class="co">#marginal distribution for the outcome, expected count</span></a>
<a class="sourceLine" id="cb29-7" data-line-number="7">           <span class="dt">control.compute =</span> <span class="kw">list</span>(<span class="dt">waic=</span>T), <span class="co"># compute DIC or not?</span></a>
<a class="sourceLine" id="cb29-8" data-line-number="8">           <span class="dt">control.predictor =</span> <span class="kw">list</span>(<span class="dt">link=</span><span class="dv">1</span>), <span class="co">#estimate predicted values &amp; their marginals or not?</span></a>
<a class="sourceLine" id="cb29-9" data-line-number="9">           <span class="dt">num.threads =</span> <span class="dv">2</span>, </a>
<a class="sourceLine" id="cb29-10" data-line-number="10">               <span class="dt">verbose =</span> F)</a>
<a class="sourceLine" id="cb29-11" data-line-number="11"><span class="co">#model summary</span></a>
<a class="sourceLine" id="cb29-12" data-line-number="12"><span class="kw">summary</span>(mod1)</a></code></pre></div>
<pre><code>## 
## Call:
##    c(&quot;inla(formula = f1, family = \&quot;nbinomial\&quot;, data = final.dat, E = 
##    E_d, &quot;, &quot; verbose = F, control.compute = list(waic = T), 
##    control.predictor = list(link = 1), &quot;, &quot; num.threads = 2)&quot;) 
## Time used:
##     Pre = 0.859, Running = 25.1, Post = 0.806, Total = 26.7 
## Fixed effects:
##                 mean     sd 0.025quant 0.5quant 0.975quant   mode kld
## (Intercept)   -5.047 10.723    -26.102   -5.048     15.989 -5.047   0
## scale(pblack)  0.159  0.015      0.130    0.159      0.188  0.159   0
## scale(phisp)  -0.025  0.013     -0.050   -0.025      0.001 -0.025   0
## scale(ppov)    0.041  0.015      0.012    0.041      0.070  0.041   0
## year           0.003  0.005     -0.008    0.003      0.013  0.003   0
## 
## Model hyperparameters:
##                                                         mean    sd 0.025quant
## size for the nbinomial observations (1/overdispersion) 0.624 0.009      0.608
##                                                        0.5quant 0.975quant
## size for the nbinomial observations (1/overdispersion)    0.624      0.641
##                                                         mode
## size for the nbinomial observations (1/overdispersion) 0.624
## 
## Expected number of effective parameters(stdev): 5.04(0.001)
## Number of equivalent replicates : 2124.92 
## 
## Watanabe-Akaike information criterion (WAIC) ...: 114586.38
## Effective number of parameters .................: 10.27
## 
## Marginal log-Likelihood:  -57331.80 
## Posterior marginals for the linear predictor and
##  the fitted values are computed</code></pre>
<p>Plot our observed vs fitted values</p>
<div class="sourceCode" id="cb31"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb31-1" data-line-number="1"><span class="kw">plot</span>(<span class="dt">x=</span> mod1<span class="op">$</span>summary.fitted.values<span class="op">$</span>mean, <span class="dt">y=</span>final.dat<span class="op">$</span>rate<span class="op">/</span>final.dat<span class="op">$</span>E_d , <span class="dt">ylab=</span><span class="st">&quot;Observed&quot;</span>, <span class="dt">xlab=</span><span class="st">&quot;Estimated&quot;</span> )</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/INLA_Infant_Mortality_files/figure-html/unnamed-chunk-12-1.png" /><!-- --></p>
<div id="basic-county-level-random-intercept-model" class="section level3">
<h3>Basic county level random intercept model</h3>
<p>Now we add basic nesting of rates within counties, with a random intercept term for each county. This would allow there to be heterogeneity in the mortality rate for each county, over and above each county’s observed characteristics.</p>
<p>This model would be:</p>
<p><span class="math display">\[\text{Deaths}_{ij} \sim NB(\mu_{ij}, \gamma)\]</span> <span class="math display">\[\mu_{ij} = \text{log(E_d)}_{ij} + X&#39; \beta + u_j\]</span> <span class="math display">\[u_j \sim  \text{Normal} (0 , \tau_u)\]</span></p>
<p>where <span class="math inline">\(\tau_u\)</span> here is the precision, not the variance and <strong>precision = 1/variance.</strong> INLA puts a log-gamma prior on the the precision by default.</p>
<div class="sourceCode" id="cb32"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb32-1" data-line-number="1">f2&lt;-rate<span class="op">~</span><span class="kw">scale</span>(pblack)<span class="op">+</span><span class="kw">scale</span>(phisp)<span class="op">+</span><span class="kw">scale</span>(ppov)<span class="op">+</span>year<span class="op">+</span><span class="st"> </span><span class="co">#fixed effects</span></a>
<a class="sourceLine" id="cb32-2" data-line-number="2"><span class="st">  </span><span class="kw">f</span>(struct, <span class="dt">model =</span> <span class="st">&quot;iid&quot;</span>,<span class="dt">param=</span><span class="kw">c</span>(<span class="dv">1</span>,.<span class="dv">5</span>))  <span class="co">#random effects</span></a>
<a class="sourceLine" id="cb32-3" data-line-number="3"></a>
<a class="sourceLine" id="cb32-4" data-line-number="4">mod2&lt;-<span class="kw">inla</span>(<span class="dt">formula =</span> f2,<span class="dt">data =</span> final.dat,</a>
<a class="sourceLine" id="cb32-5" data-line-number="5">           <span class="dt">family =</span> <span class="st">&quot;nbinomial&quot;</span>, <span class="dt">E =</span> E_d, </a>
<a class="sourceLine" id="cb32-6" data-line-number="6">           <span class="dt">control.compute =</span> <span class="kw">list</span>(<span class="dt">waic=</span>T), </a>
<a class="sourceLine" id="cb32-7" data-line-number="7">           <span class="dt">control.predictor =</span> <span class="kw">list</span>(<span class="dt">link=</span><span class="dv">1</span>),</a>
<a class="sourceLine" id="cb32-8" data-line-number="8">           <span class="dt">num.threads =</span> <span class="dv">2</span>, </a>
<a class="sourceLine" id="cb32-9" data-line-number="9">               <span class="dt">verbose =</span> F)</a>
<a class="sourceLine" id="cb32-10" data-line-number="10"></a>
<a class="sourceLine" id="cb32-11" data-line-number="11"><span class="co">#total model summary</span></a>
<a class="sourceLine" id="cb32-12" data-line-number="12"><span class="kw">summary</span>(mod2)</a></code></pre></div>
<pre><code>## 
## Call:
##    c(&quot;inla(formula = f2, family = \&quot;nbinomial\&quot;, data = final.dat, E = 
##    E_d, &quot;, &quot; verbose = F, control.compute = list(waic = T), 
##    control.predictor = list(link = 1), &quot;, &quot; num.threads = 2)&quot;) 
## Time used:
##     Pre = 0.594, Running = 199, Post = 1.15, Total = 200 
## Fixed effects:
##                 mean     sd 0.025quant 0.5quant 0.975quant   mode kld
## (Intercept)   -2.824 10.757    -23.944   -2.824     18.278 -2.824   0
## scale(pblack)  0.158  0.015      0.128    0.158      0.189  0.158   0
## scale(phisp)  -0.041  0.014     -0.069   -0.041     -0.013 -0.041   0
## scale(ppov)    0.044  0.015      0.014    0.044      0.074  0.044   0
## year           0.001  0.005     -0.009    0.001      0.012  0.001   0
## 
## Random effects:
##   Name     Model
##     struct IID model
## 
## Model hyperparameters:
##                                                          mean    sd 0.025quant
## size for the nbinomial observations (1/overdispersion)  0.627 0.009      0.609
## Precision for struct                                   50.624 7.007     38.290
##                                                        0.5quant 0.975quant
## size for the nbinomial observations (1/overdispersion)    0.627      0.644
## Precision for struct                                     50.135     65.785
##                                                          mode
## size for the nbinomial observations (1/overdispersion)  0.626
## Precision for struct                                   49.168
## 
## Expected number of effective parameters(stdev): 125.34(15.34)
## Number of equivalent replicates : 85.47 
## 
## Watanabe-Akaike information criterion (WAIC) ...: 114610.09
## Effective number of parameters .................: 66.26
## 
## Marginal log-Likelihood:  -57375.58 
## Posterior marginals for the linear predictor and
##  the fitted values are computed</code></pre>
<div id="marginal-distributions-of-hyperparameters" class="section level4">
<h4>Marginal Distributions of hyperparameters</h4>
<p>We can plot the posterior marginal of the hyperparameter in this model, in this case <span class="math inline">\(\sigma_u = 1/\tau_u\)</span></p>
<div class="sourceCode" id="cb34"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb34-1" data-line-number="1">m2&lt;-<span class="st"> </span><span class="kw">inla.tmarginal</span>(</a>
<a class="sourceLine" id="cb34-2" data-line-number="2">        <span class="cf">function</span>(x) (<span class="dv">1</span><span class="op">/</span>x), <span class="co">#invert the precision to be on variance scale</span></a>
<a class="sourceLine" id="cb34-3" data-line-number="3">        mod2<span class="op">$</span>marginals.hyperpar<span class="op">$</span><span class="st">`</span><span class="dt">Precision for struct</span><span class="st">`</span>)</a>
<a class="sourceLine" id="cb34-4" data-line-number="4"><span class="co">#95% credible interval for the variance</span></a>
<a class="sourceLine" id="cb34-5" data-line-number="5"><span class="kw">inla.hpdmarginal</span>(.<span class="dv">95</span>, <span class="dt">marginal=</span>m2)</a></code></pre></div>
<pre><code>##                   low       high
## level:0.95 0.01491364 0.02565568</code></pre>
<div class="sourceCode" id="cb36"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb36-1" data-line-number="1"><span class="kw">plot</span>(m2, <span class="dt">type=</span><span class="st">&quot;l&quot;</span>, <span class="dt">main=</span><span class="kw">c</span>(<span class="st">&quot;Posterior distibution for between county variance&quot;</span>, <span class="st">&quot;- IID model -&quot;</span>))</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/INLA_Infant_Mortality_files/figure-html/unnamed-chunk-14-1.png" /><!-- --></p>
<div class="sourceCode" id="cb37"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb37-1" data-line-number="1">final.dat<span class="op">$</span>fitted_m2&lt;-mod2<span class="op">$</span>summary.fitted.values<span class="op">$</span>mean</a>
<a class="sourceLine" id="cb37-2" data-line-number="2"></a>
<a class="sourceLine" id="cb37-3" data-line-number="3">p1&lt;-final.dat<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb37-4" data-line-number="4"><span class="st">  </span><span class="kw">filter</span>(year<span class="op">%in%</span><span class="kw">c</span>(<span class="dv">2000</span>))<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb37-5" data-line-number="5"><span class="st">  </span><span class="kw">mutate</span>(<span class="dt">qrr=</span><span class="kw">cut</span>(fitted_m2, <span class="dt">breaks =</span> <span class="kw">quantile</span>(final.dat<span class="op">$</span>fitted_m2, <span class="dt">p=</span><span class="kw">seq</span>(<span class="dv">0</span>,<span class="dv">1</span>,<span class="dt">length.out =</span> <span class="dv">6</span>)), <span class="dt">include.lowest =</span> T))<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb37-6" data-line-number="6"><span class="st">  </span><span class="kw">ggplot</span>()<span class="op">+</span><span class="kw">geom_sf</span>(<span class="kw">aes</span>(<span class="dt">fill=</span>qrr))<span class="op">+</span><span class="kw">scale_colour_brewer</span>(<span class="dt">palette =</span> <span class="st">&quot;RdBu&quot;</span> )<span class="op">+</span><span class="kw">scale_fill_brewer</span>(<span class="dt">palette =</span> <span class="st">&quot;RdBu&quot;</span>, <span class="dt">na.value=</span><span class="st">&quot;grey&quot;</span>)<span class="op">+</span><span class="kw">guides</span>(<span class="dt">fill=</span><span class="kw">guide_legend</span>(<span class="dt">title=</span><span class="st">&quot;Relative Risk Quartile&quot;</span>))<span class="op">+</span><span class="kw">ggtitle</span>(<span class="dt">label=</span><span class="st">&quot;Relative Risk Quartile - IID Model, 2000&quot;</span>)<span class="op">+</span><span class="kw">coord_sf</span>(<span class="dt">crs =</span> <span class="dv">7603</span>)</a>
<a class="sourceLine" id="cb37-7" data-line-number="7">  </a>
<a class="sourceLine" id="cb37-8" data-line-number="8">p2&lt;-final.dat<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb37-9" data-line-number="9"><span class="st">  </span><span class="kw">filter</span>(year<span class="op">%in%</span><span class="kw">c</span>(<span class="dv">2007</span>))<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb37-10" data-line-number="10"><span class="st">  </span><span class="kw">mutate</span>(<span class="dt">qrr=</span><span class="kw">cut</span>(fitted_m2, <span class="dt">breaks =</span> <span class="kw">quantile</span>(final.dat<span class="op">$</span>fitted_m2, <span class="dt">p=</span><span class="kw">seq</span>(<span class="dv">0</span>,<span class="dv">1</span>,<span class="dt">length.out =</span> <span class="dv">6</span>)), <span class="dt">include.lowest =</span> T))<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb37-11" data-line-number="11"><span class="st">  </span><span class="kw">ggplot</span>()<span class="op">+</span><span class="kw">geom_sf</span>(<span class="kw">aes</span>(<span class="dt">fill=</span>qrr))<span class="op">+</span><span class="kw">scale_colour_brewer</span>(<span class="dt">palette =</span> <span class="st">&quot;RdBu&quot;</span> )<span class="op">+</span><span class="kw">scale_fill_brewer</span>(<span class="dt">palette =</span> <span class="st">&quot;RdBu&quot;</span>, <span class="dt">na.value=</span><span class="st">&quot;grey&quot;</span>)<span class="op">+</span><span class="kw">guides</span>(<span class="dt">fill=</span><span class="kw">guide_legend</span>(<span class="dt">title=</span><span class="st">&quot;Relative Risk Quartile&quot;</span>))<span class="op">+</span><span class="kw">ggtitle</span>(<span class="dt">label=</span><span class="st">&quot;Relative Risk Quartile - IID Model, 2007&quot;</span>)<span class="op">+</span><span class="kw">coord_sf</span>(<span class="dt">crs =</span> <span class="dv">7603</span>)</a>
<a class="sourceLine" id="cb37-12" data-line-number="12"></a>
<a class="sourceLine" id="cb37-13" data-line-number="13"><span class="kw">library</span>(gridExtra)</a></code></pre></div>
<pre><code>## 
## Attaching package: &#39;gridExtra&#39;</code></pre>
<pre><code>## The following object is masked from &#39;package:dplyr&#39;:
## 
##     combine</code></pre>
<div class="sourceCode" id="cb40"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb40-1" data-line-number="1">pall&lt;-<span class="kw">grid.arrange</span>(p1, p2, <span class="dt">nrow=</span><span class="dv">2</span>)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/INLA_Infant_Mortality_files/figure-html/unnamed-chunk-15-1.png" /><!-- --></p>
<div class="sourceCode" id="cb41"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb41-1" data-line-number="1">pall</a></code></pre></div>
<pre><code>## TableGrob (2 x 1) &quot;arrange&quot;: 2 grobs
##   z     cells    name           grob
## 1 1 (1-1,1-1) arrange gtable[layout]
## 2 2 (2-2,1-1) arrange gtable[layout]</code></pre>
<div class="sourceCode" id="cb43"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb43-1" data-line-number="1"><span class="co"># library(mapview)</span></a>
<a class="sourceLine" id="cb43-2" data-line-number="2"><span class="co"># </span></a>
<a class="sourceLine" id="cb43-3" data-line-number="3"><span class="co"># map1&lt;-final.dat%&gt;%</span></a>
<a class="sourceLine" id="cb43-4" data-line-number="4"><span class="co">#   filter(year%in%c(2007))%&gt;%</span></a>
<a class="sourceLine" id="cb43-5" data-line-number="5"><span class="co">#   mutate(qrr=cut(fitted_m2, breaks = quantile(fitted_m2, p=seq(0,1,length.out = 8))))</span></a>
<a class="sourceLine" id="cb43-6" data-line-number="6"><span class="co"># clrs &lt;- colorRampPalette(brewer.pal(8, &quot;RdBu&quot;))</span></a>
<a class="sourceLine" id="cb43-7" data-line-number="7"><span class="co"># mapView(as(map1, &quot;Spatial&quot;), zcol=&quot;qrr&quot;, legend=T, col.regions=clrs)</span></a></code></pre></div>
</div>
</div>
<div id="bym-model" class="section level3">
<h3>BYM Model</h3>
<p>Model with spatial correlation - Besag, York, and Mollie (1991) model and temporal heterogeneity <span class="math display">\[\text{Deaths}_{ij} \sim NB(\mu_{ij}, \gamma)\]</span> <span class="math display">\[\mu_{ij} = \text{log(E_d)}_{ij} + X&#39; \beta + u_j + v_j + \gamma_t\]</span></p>
<p>Which has two random effects, one an IID random effect and the second a spatially correlated random effect, specified as a conditionally auto-regressive prior for the <span class="math inline">\(v_j\)</span>’s. This is the Besag model:</p>
<p><span class="math display">\[v_j|v_{\neq j},\sim\text{Normal}(\frac{1}{n_i}\sum_{i\sim j}v_j,\frac{1}{n_i\tau})\]</span> and <span class="math inline">\(u_j\)</span> is an IID normal random effect, <span class="math inline">\(\gamma_t\)</span> is also given an IID Normal random effect specification, and there are now three hyperparameters, <span class="math inline">\(\tau_u\)</span> and <span class="math inline">\(\tau_v\)</span> and <span class="math inline">\(\tau_{\gamma}\)</span> and each are given log-gamma priors.</p>
<p>For the BYM model we must specify the spatial connectivity matrix in the random effect.</p>
<div class="sourceCode" id="cb44"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb44-1" data-line-number="1"><span class="co">#final.dat$year_c&lt;-final.dat$year - 2004</span></a>
<a class="sourceLine" id="cb44-2" data-line-number="2">f3&lt;-rate<span class="op">~</span><span class="kw">scale</span>(pblack)<span class="op">+</span><span class="kw">scale</span>(phisp)<span class="op">+</span><span class="kw">scale</span>(ppov)<span class="op">+</span></a>
<a class="sourceLine" id="cb44-3" data-line-number="3"><span class="st">  </span><span class="kw">f</span>(struct, <span class="dt">model =</span> <span class="st">&quot;bym&quot;</span>, <span class="dt">constr =</span> T, <span class="dt">scale.model =</span> T, <span class="dt">graph =</span> H,<span class="dt">param=</span><span class="kw">c</span>(<span class="dv">1</span>,.<span class="dv">5</span>))<span class="op">+</span></a>
<a class="sourceLine" id="cb44-4" data-line-number="4"><span class="st">  </span><span class="kw">f</span>(year, <span class="dt">model=</span><span class="st">&quot;iid&quot;</span>,<span class="dt">param=</span><span class="kw">c</span>(<span class="dv">1</span>,.<span class="dv">5</span>)) <span class="co">#temporal random effect</span></a>
<a class="sourceLine" id="cb44-5" data-line-number="5"></a>
<a class="sourceLine" id="cb44-6" data-line-number="6">mod3&lt;-<span class="kw">inla</span>(<span class="dt">formula =</span> f3,<span class="dt">data =</span> final.dat,</a>
<a class="sourceLine" id="cb44-7" data-line-number="7">           <span class="dt">family =</span> <span class="st">&quot;nbinomial&quot;</span>, <span class="dt">E =</span> E_d, </a>
<a class="sourceLine" id="cb44-8" data-line-number="8">           <span class="dt">control.compute =</span> <span class="kw">list</span>(<span class="dt">waic=</span>T), </a>
<a class="sourceLine" id="cb44-9" data-line-number="9">           <span class="dt">num.threads =</span> <span class="dv">2</span>, </a>
<a class="sourceLine" id="cb44-10" data-line-number="10">               <span class="dt">verbose =</span> F,</a>
<a class="sourceLine" id="cb44-11" data-line-number="11">           <span class="dt">control.predictor =</span> <span class="kw">list</span>(<span class="dt">link=</span><span class="dv">1</span>))</a>
<a class="sourceLine" id="cb44-12" data-line-number="12"></a>
<a class="sourceLine" id="cb44-13" data-line-number="13"><span class="co">#total model summary</span></a>
<a class="sourceLine" id="cb44-14" data-line-number="14"><span class="kw">summary</span>(mod3)</a></code></pre></div>
<pre><code>## 
## Call:
##    c(&quot;inla(formula = f3, family = \&quot;nbinomial\&quot;, data = final.dat, E = 
##    E_d, &quot;, &quot; verbose = F, control.compute = list(waic = T), 
##    control.predictor = list(link = 1), &quot;, &quot; num.threads = 2)&quot;) 
## Time used:
##     Pre = 0.774, Running = 165, Post = 1.32, Total = 167 
## Fixed effects:
##                 mean    sd 0.025quant 0.5quant 0.975quant   mode kld
## (Intercept)    0.115 0.129     -0.145    0.115      0.374  0.115   0
## scale(pblack)  0.157 0.016      0.126    0.158      0.189  0.158   0
## scale(phisp)  -0.039 0.016     -0.069   -0.039     -0.007 -0.040   0
## scale(ppov)    0.043 0.016      0.012    0.043      0.075  0.043   0
## 
## Random effects:
##   Name     Model
##     struct BYM model
##    year IID model
## 
## Model hyperparameters:
##                                                            mean       sd
## size for the nbinomial observations (1/overdispersion)    0.627    0.009
## Precision for struct (iid component)                     51.094    7.099
## Precision for struct (spatial component)               1974.289 1903.577
## Precision for year                                        8.760    4.130
##                                                        0.025quant 0.5quant
## size for the nbinomial observations (1/overdispersion)      0.609    0.627
## Precision for struct (iid component)                       38.602   50.591
## Precision for struct (spatial component)                  174.447 1425.658
## Precision for year                                          2.885    8.075
##                                                        0.975quant    mode
## size for the nbinomial observations (1/overdispersion)      0.644   0.628
## Precision for struct (iid component)                       66.447  49.595
## Precision for struct (spatial component)                 7055.730 496.592
## Precision for year                                         18.742   6.583
## 
## Expected number of effective parameters(stdev): 133.75(15.30)
## Number of equivalent replicates : 80.09 
## 
## Watanabe-Akaike information criterion (WAIC) ...: 114605.76
## Effective number of parameters .................: 69.81
## 
## Marginal log-Likelihood:  -56934.15 
## Posterior marginals for the linear predictor and
##  the fitted values are computed</code></pre>
<div class="sourceCode" id="cb46"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb46-1" data-line-number="1"><span class="kw">plot</span>(<span class="dt">y=</span>mod3<span class="op">$</span>summary.random<span class="op">$</span>year<span class="op">$</span>mean,<span class="dt">x=</span><span class="kw">unique</span>(final.dat<span class="op">$</span>year), <span class="dt">type=</span><span class="st">&quot;l&quot;</span>)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/INLA_Infant_Mortality_files/figure-html/unnamed-chunk-17-1.png" /><!-- --></p>
<div class="sourceCode" id="cb47"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb47-1" data-line-number="1">m3a&lt;-<span class="st"> </span><span class="kw">inla.tmarginal</span>(</a>
<a class="sourceLine" id="cb47-2" data-line-number="2">        <span class="cf">function</span>(x) (<span class="dv">1</span><span class="op">/</span>x),</a>
<a class="sourceLine" id="cb47-3" data-line-number="3">        mod3<span class="op">$</span>marginals.hyperpar<span class="op">$</span><span class="st">`</span><span class="dt">Precision for struct (iid component)</span><span class="st">`</span>)</a>
<a class="sourceLine" id="cb47-4" data-line-number="4">m3b&lt;-<span class="st"> </span><span class="kw">inla.tmarginal</span>(</a>
<a class="sourceLine" id="cb47-5" data-line-number="5">        <span class="cf">function</span>(x) (<span class="dv">1</span><span class="op">/</span>x),</a>
<a class="sourceLine" id="cb47-6" data-line-number="6">        mod3<span class="op">$</span>marginals.hyperpar<span class="op">$</span><span class="st">`</span><span class="dt">Precision for struct (spatial component)</span><span class="st">`</span>)</a>
<a class="sourceLine" id="cb47-7" data-line-number="7">m3c&lt;-<span class="st"> </span><span class="kw">inla.tmarginal</span>(</a>
<a class="sourceLine" id="cb47-8" data-line-number="8">        <span class="cf">function</span>(x) (<span class="dv">1</span><span class="op">/</span>x),</a>
<a class="sourceLine" id="cb47-9" data-line-number="9">        mod3<span class="op">$</span>marginals.hyperpar<span class="op">$</span><span class="st">`</span><span class="dt">Precision for year</span><span class="st">`</span>)</a>
<a class="sourceLine" id="cb47-10" data-line-number="10"></a>
<a class="sourceLine" id="cb47-11" data-line-number="11"><span class="kw">plot</span>(m3a, <span class="dt">type=</span><span class="st">&quot;l&quot;</span>, <span class="dt">main=</span><span class="kw">c</span>(<span class="st">&quot;Posterior distibution for between county variance&quot;</span>, <span class="st">&quot;- BYM model -&quot;</span>), <span class="dt">xlim=</span><span class="kw">c</span>(<span class="dv">0</span>, <span class="fl">.2</span>), <span class="dt">ylim=</span><span class="kw">c</span>(<span class="dv">0</span>, <span class="dv">300</span>))</a>
<a class="sourceLine" id="cb47-12" data-line-number="12"><span class="kw">lines</span>(m3b, <span class="dt">col=</span><span class="st">&quot;red&quot;</span>)</a>
<a class="sourceLine" id="cb47-13" data-line-number="13"><span class="kw">lines</span>(m3c, <span class="dt">col=</span><span class="st">&quot;green&quot;</span>)</a>
<a class="sourceLine" id="cb47-14" data-line-number="14"><span class="kw">legend</span>(<span class="st">&quot;topright&quot;</span>, <span class="dt">legend=</span><span class="kw">c</span>(<span class="st">&quot;BYM IID&quot;</span>, <span class="st">&quot;BYM Spatial&quot;</span>, <span class="st">&quot;Year&quot;</span>), <span class="dt">col=</span><span class="kw">c</span>(<span class="dv">1</span>, <span class="st">&quot;red&quot;</span>, <span class="st">&quot;green&quot;</span>), <span class="dt">lty=</span><span class="kw">c</span>(<span class="dv">1</span>,<span class="dv">1</span>,<span class="dv">1</span>))</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/INLA_Infant_Mortality_files/figure-html/unnamed-chunk-17-2.png" /><!-- --></p>
<div class="sourceCode" id="cb48"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb48-1" data-line-number="1"><span class="co">#HPD intervals</span></a>
<a class="sourceLine" id="cb48-2" data-line-number="2"><span class="kw">inla.hpdmarginal</span>(.<span class="dv">95</span>,m3a)</a></code></pre></div>
<pre><code>##                   low       high
## level:0.95 0.01475866 0.02544088</code></pre>
<div class="sourceCode" id="cb50"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb50-1" data-line-number="1"><span class="kw">inla.hpdmarginal</span>(.<span class="dv">95</span>,m3b)</a></code></pre></div>
<pre><code>##                      low        high
## level:0.95 0.00005416961 0.003970123</code></pre>
<div class="sourceCode" id="cb52"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb52-1" data-line-number="1"><span class="kw">inla.hpdmarginal</span>(.<span class="dv">95</span>,m3c)</a></code></pre></div>
<pre><code>##                   low      high
## level:0.95 0.03927999 0.2945931</code></pre>
<p>This indicates <strong>very</strong> low spatially correlated variance in these data.</p>
</div>
</div>
<div id="space-time-mapping-of-the-fitted-values" class="section level2">
<h2>Space-time mapping of the fitted values</h2>
<div class="sourceCode" id="cb54"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb54-1" data-line-number="1">final.dat<span class="op">$</span>fitted_m3&lt;-mod3<span class="op">$</span>summary.fitted.values<span class="op">$</span>mean</a>
<a class="sourceLine" id="cb54-2" data-line-number="2"></a>
<a class="sourceLine" id="cb54-3" data-line-number="3">p3&lt;-final.dat<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb54-4" data-line-number="4"><span class="st">  </span><span class="kw">filter</span>(year<span class="op">%in%</span><span class="kw">c</span>(<span class="dv">2000</span>))<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb54-5" data-line-number="5"><span class="st">  </span><span class="kw">mutate</span>(<span class="dt">qrr=</span><span class="kw">cut</span>(fitted_m3, <span class="dt">breaks =</span> <span class="kw">quantile</span>(final.dat<span class="op">$</span>fitted_m3, <span class="dt">p=</span><span class="kw">seq</span>(<span class="dv">0</span>,<span class="dv">1</span>,<span class="dt">length.out =</span> <span class="dv">6</span>)), <span class="dt">include.lowest =</span> T))<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb54-6" data-line-number="6"><span class="st">  </span><span class="kw">ggplot</span>()<span class="op">+</span><span class="kw">geom_sf</span>(<span class="kw">aes</span>(<span class="dt">fill=</span>qrr))<span class="op">+</span><span class="kw">scale_colour_brewer</span>(<span class="dt">palette =</span> <span class="st">&quot;RdBu&quot;</span> )<span class="op">+</span><span class="kw">scale_fill_brewer</span>(<span class="dt">palette =</span> <span class="st">&quot;RdBu&quot;</span>, <span class="dt">na.value=</span><span class="st">&quot;grey&quot;</span>)<span class="op">+</span><span class="kw">guides</span>(<span class="dt">fill=</span><span class="kw">guide_legend</span>(<span class="dt">title=</span><span class="st">&quot;Relative Risk Quartile&quot;</span>))<span class="op">+</span><span class="kw">ggtitle</span>(<span class="dt">label=</span><span class="st">&quot;Relative Risk Quartile - IID Model, 2000&quot;</span>)<span class="op">+</span><span class="kw">coord_sf</span>(<span class="dt">crs =</span> <span class="dv">7603</span>)</a>
<a class="sourceLine" id="cb54-7" data-line-number="7">  </a>
<a class="sourceLine" id="cb54-8" data-line-number="8">p4&lt;-final.dat<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb54-9" data-line-number="9"><span class="st">  </span><span class="kw">filter</span>(year<span class="op">%in%</span><span class="kw">c</span>(<span class="dv">2007</span>))<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb54-10" data-line-number="10"><span class="st">  </span><span class="kw">mutate</span>(<span class="dt">qrr=</span><span class="kw">cut</span>(fitted_m3, <span class="dt">breaks =</span> <span class="kw">quantile</span>(final.dat<span class="op">$</span>fitted_m3, <span class="dt">p=</span><span class="kw">seq</span>(<span class="dv">0</span>,<span class="dv">1</span>,<span class="dt">length.out =</span> <span class="dv">6</span>)), <span class="dt">include.lowest =</span> T))<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb54-11" data-line-number="11"><span class="st">  </span><span class="kw">ggplot</span>()<span class="op">+</span><span class="kw">geom_sf</span>(<span class="kw">aes</span>(<span class="dt">fill=</span>qrr))<span class="op">+</span><span class="kw">scale_colour_brewer</span>(<span class="dt">palette =</span> <span class="st">&quot;RdBu&quot;</span> )<span class="op">+</span><span class="kw">scale_fill_brewer</span>(<span class="dt">palette =</span> <span class="st">&quot;RdBu&quot;</span>, <span class="dt">na.value=</span><span class="st">&quot;grey&quot;</span>)<span class="op">+</span><span class="kw">guides</span>(<span class="dt">fill=</span><span class="kw">guide_legend</span>(<span class="dt">title=</span><span class="st">&quot;Relative Risk Quartile&quot;</span>))<span class="op">+</span><span class="kw">ggtitle</span>(<span class="dt">label=</span><span class="st">&quot;Relative Risk Quartile - IID Model, 2007&quot;</span>)<span class="op">+</span><span class="kw">coord_sf</span>(<span class="dt">crs =</span> <span class="dv">7603</span>)</a>
<a class="sourceLine" id="cb54-12" data-line-number="12"></a>
<a class="sourceLine" id="cb54-13" data-line-number="13">pall2&lt;-<span class="kw">grid.arrange</span>(p3, p4, <span class="dt">nrow=</span><span class="dv">2</span>)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/INLA_Infant_Mortality_files/figure-html/unnamed-chunk-18-1.png" /><!-- --></p>
<div class="sourceCode" id="cb55"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb55-1" data-line-number="1">pall2</a></code></pre></div>
<pre><code>## TableGrob (2 x 1) &quot;arrange&quot;: 2 grobs
##   z     cells    name           grob
## 1 1 (1-1,1-1) arrange gtable[layout]
## 2 2 (2-2,1-1) arrange gtable[layout]</code></pre>
<div class="sourceCode" id="cb57"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb57-1" data-line-number="1"><span class="co">#library(mapview)</span></a>
<a class="sourceLine" id="cb57-2" data-line-number="2"></a>
<a class="sourceLine" id="cb57-3" data-line-number="3"><span class="co">#map1&lt;-final.dat%&gt;%</span></a>
<a class="sourceLine" id="cb57-4" data-line-number="4"><span class="co">#  filter(year%in%c(2007))%&gt;%</span></a>
<a class="sourceLine" id="cb57-5" data-line-number="5"><span class="co">#  mutate(qrr=cut(fitted_m3, breaks = quantile(fitted_m3, p=seq(0,1,length.out = 8))))</span></a>
<a class="sourceLine" id="cb57-6" data-line-number="6"><span class="co">#clrs &lt;- colorRampPalette(brewer.pal(8, &quot;RdBu&quot;))</span></a>
<a class="sourceLine" id="cb57-7" data-line-number="7"><span class="co">#mapView(as(map1, &quot;Spatial&quot;), zcol=&quot;qrr&quot;, legend=T, col.regions=clrs)</span></a></code></pre></div>
</div>
<div id="map-of-spatial-random-effects" class="section level2">
<h2>Map of spatial random effects</h2>
<p>It is common to map the random effects from the BYM model to look for spatial trends, in this case, there are not strong spatial signals:</p>
<div class="sourceCode" id="cb58"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb58-1" data-line-number="1">us_co<span class="op">$</span>sp_re&lt;-mod3<span class="op">$</span>summary.random<span class="op">$</span>struct<span class="op">$</span>mean[<span class="dv">1</span><span class="op">:</span><span class="kw">length</span>(<span class="kw">unique</span>(final.dat<span class="op">$</span>cofips))]</a>
<a class="sourceLine" id="cb58-2" data-line-number="2">us_co<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb58-3" data-line-number="3"><span class="st">  </span><span class="kw">mutate</span>(<span class="dt">qse=</span><span class="kw">cut</span>(sp_re, <span class="dt">breaks =</span> <span class="kw">quantile</span>(sp_re, <span class="dt">p=</span><span class="kw">seq</span>(<span class="dv">0</span>,<span class="dv">1</span>,<span class="dt">length.out =</span> <span class="dv">6</span>)), <span class="dt">include.lowest =</span> T))<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb58-4" data-line-number="4"><span class="st">  </span><span class="kw">ggplot</span>()<span class="op">+</span><span class="kw">geom_sf</span>(<span class="kw">aes</span>(<span class="dt">fill=</span>qse))<span class="op">+</span><span class="kw">scale_colour_brewer</span>(<span class="dt">palette =</span> <span class="st">&quot;RdBu&quot;</span> )<span class="op">+</span><span class="kw">scale_fill_brewer</span>(<span class="dt">palette =</span> <span class="st">&quot;RdBu&quot;</span>, <span class="dt">na.value=</span><span class="st">&quot;grey&quot;</span>)<span class="op">+</span><span class="kw">guides</span>(<span class="dt">fill=</span><span class="kw">guide_legend</span>(<span class="dt">title=</span><span class="st">&quot;Spatial Excess Risk&quot;</span>))<span class="op">+</span><span class="kw">ggtitle</span>(<span class="dt">label=</span><span class="st">&quot;Spatial Random Effect - BYM Model&quot;</span>)<span class="op">+</span><span class="kw">coord_sf</span>(<span class="dt">crs =</span> <span class="dv">7603</span>)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/INLA_Infant_Mortality_files/figure-html/unnamed-chunk-19-1.png" /><!-- --></p>
</div>
<div id="exceedence-probabilities" class="section level2">
<h2>Exceedence probabilities</h2>
<p>In Bayesian spatial models that are centered on an epidemiological type of outcome, it is common to examine the data for spatial clustering. One way to do this is to examine the clustering in the relative risk from one of these GLMM models. For instance if <span class="math inline">\(\theta\)</span> is the relative risk <span class="math display">\[\theta = exp(\beta_0 + \beta_1*x_1 + u_j)\]</span> from one of our Negative binomial models above. We can use the posterior marginals of the relative risk to ask <span class="math inline">\(\theta \gt \theta^*\)</span> where <span class="math inline">\(\theta^*\)</span> is a specific level of excess risk, say 50% extra or <span class="math inline">\(\theta &gt; 1.25\)</span>. If the density, or <span class="math inline">\(\text{Pr}(\theta \gt \theta^*)\)</span> is high, then there is evidence that the excess risk is not only high, but <strong>significantly</strong> high.</p>
<p>To get the exceedence probabilities from one of our models, we can use the <code>inla.pmarginal()</code> function to ask if <span class="math inline">\(\text{Pr}(\theta \gt \theta^*)\)</span></p>
<div class="sourceCode" id="cb59"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb59-1" data-line-number="1">thetastar&lt;-<span class="fl">1.25</span><span class="co">#theta*</span></a>
<a class="sourceLine" id="cb59-2" data-line-number="2">inlaprob&lt;-<span class="st"> </span><span class="kw">unlist</span>(<span class="kw">lapply</span>(mod3<span class="op">$</span>marginals.fitted.values, <span class="cf">function</span>(X){</a>
<a class="sourceLine" id="cb59-3" data-line-number="3">   <span class="dv">1</span><span class="op">-</span><span class="kw">inla.pmarginal</span>(thetastar, X)</a>
<a class="sourceLine" id="cb59-4" data-line-number="4">}))</a>
<a class="sourceLine" id="cb59-5" data-line-number="5"><span class="kw">hist</span>(inlaprob)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/INLA_Infant_Mortality_files/figure-html/unnamed-chunk-20-1.png" /><!-- --></p>
<p>So, we see lots of occasions where the exceedence probability is greater than .9. We can visualize these in a map.</p>
<div class="sourceCode" id="cb60"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb60-1" data-line-number="1">final.dat<span class="op">$</span>exceedprob&lt;-inlaprob</a>
<a class="sourceLine" id="cb60-2" data-line-number="2"></a>
<a class="sourceLine" id="cb60-3" data-line-number="3">final.dat<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb60-4" data-line-number="4"><span class="st">  </span><span class="kw">filter</span>(year<span class="op">%in%</span><span class="kw">c</span>(<span class="dv">2007</span>))<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb60-5" data-line-number="5"><span class="st">  </span><span class="kw">mutate</span>(<span class="dt">qrr=</span><span class="kw">cut</span>(exceedprob, <span class="dt">breaks =</span> <span class="kw">c</span>(<span class="dv">0</span>, <span class="fl">.5</span>, <span class="fl">.9</span>, <span class="fl">.95</span>, <span class="fl">.99</span>, <span class="dv">1</span>), <span class="dt">include.lowest =</span> T))<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb60-6" data-line-number="6"><span class="st">  </span><span class="kw">ggplot</span>()<span class="op">+</span><span class="kw">geom_sf</span>(<span class="kw">aes</span>(<span class="dt">fill=</span>qrr))<span class="op">+</span><span class="kw">scale_colour_brewer</span>(<span class="dt">palette =</span> <span class="st">&quot;Blues&quot;</span> )<span class="op">+</span><span class="kw">scale_fill_brewer</span>(<span class="dt">palette =</span> <span class="st">&quot;Blues&quot;</span>, <span class="dt">na.value=</span><span class="st">&quot;grey&quot;</span>)<span class="op">+</span><span class="kw">guides</span>(<span class="dt">fill=</span><span class="kw">guide_legend</span>(<span class="dt">title=</span><span class="st">&quot;&quot;</span>))<span class="op">+</span><span class="kw">ggtitle</span>(<span class="dt">label=</span><span class="kw">expression</span>(<span class="kw">paste</span>(<span class="st">&quot;Exceedence Probability Relative Risk &quot;</span>,<span class="st">&quot;Pr( &quot;</span>,theta,<span class="st">&quot; &gt;1.25&quot;</span>,<span class="st">&quot; )  - 2007&quot;</span>) ))<span class="op">+</span><span class="kw">coord_sf</span>(<span class="dt">crs =</span> <span class="dv">7603</span>)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/INLA_Infant_Mortality_files/figure-html/unnamed-chunk-21-1.png" /><!-- --></p>
<div class="sourceCode" id="cb61"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb61-1" data-line-number="1"><span class="co">#library(mapview)</span></a>
<a class="sourceLine" id="cb61-2" data-line-number="2"></a>
<a class="sourceLine" id="cb61-3" data-line-number="3"><span class="co">#map1&lt;-final.dat%&gt;%</span></a>
<a class="sourceLine" id="cb61-4" data-line-number="4"> <span class="co"># filter(year%in%c(2007))%&gt;%</span></a>
<a class="sourceLine" id="cb61-5" data-line-number="5"><span class="co">#  mutate(qrr=cut(exceedprob, breaks = c(0, .5, .9, .95, .99, 1), include.lowest = T))</span></a>
<a class="sourceLine" id="cb61-6" data-line-number="6">  </a>
<a class="sourceLine" id="cb61-7" data-line-number="7"><span class="co">#clrs &lt;- colorRampPalette(brewer.pal(6, &quot;Blues&quot;))</span></a>
<a class="sourceLine" id="cb61-8" data-line-number="8"><span class="co">#mapView(as(map1, &quot;Spatial&quot;), zcol=&quot;qrr&quot;, legend=T, col.regions=clrs, map.types=&quot;OpenStreetMap&quot;)</span></a></code></pre></div>
<p>Which shows several areas of the south where risk the infant mortality rate is signficantly higher than the national rate, with high posterior probability.</p>
</div>
<div id="references" class="section level2">
<h2>References</h2>
<p>Besag, J., York, J., &amp; Mollie, a. (1991). Bayesian image-restoration, with 2 applications in spatial statistics. Annals of the Institute of Statistical Mathematics, 43(1), 1-20. <a href="https://doi.org/10.1007/BF00116466" class="uri">https://doi.org/10.1007/BF00116466</a></p>
</div>
</div>
</section>

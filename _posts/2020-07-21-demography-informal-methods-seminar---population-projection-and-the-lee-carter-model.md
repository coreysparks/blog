---
title: "Demography Informal Methods Seminar - Population Projection and the Lee-Carter Model"

author: "Corey Sparks, Ph.D. - UTSA Department of Demography"
date: "July 21, 2020"
layout: post
---


<section class="main-content">
<div id="population-projection" class="section level2">
<h2>Population Projection</h2>
<ul>
<li>One of the most fundamental forms of demographic analysis</li>
<li>Uses age-structured rates of fertility and mortality to project the population structure forward into time</li>
<li>Shows patterns of population growth and age composition in future populations</li>
<li>Further analysis can show population growth rates and sensitivity of the growth rate to the vital rates</li>
</ul>
<div id="methods" class="section level3">
<h3>Methods</h3>
<ul>
<li>Cohort component method</li>
<li><span class="math display">\[P_{t+n} = P_t + B_t - D_t + M_t\]</span></li>
<li><p><a href="https://www.measureevaluation.org/resources/training/online-courses-and-resources/non-certificate-courses-and-mini-tutorials/population-analysis-for-planners/lesson-8/lesson-8-the-cohort-component-population-projection-method">Example</a></p></li>
<li>Hamilton-Perry method</li>
<li>Very low data requirements</li>
<li>Uses cohort change ratios from two previous censuses to project the population forward</li>
<li><span class="math display">\[P_{t+n} = CCR_{t, t-n} * P_t\]</span> Where the <span class="math inline">\(CCR\)</span> is the ratio of the population age structure at the two previous censuses.</li>
<li>Good description is <a href="https://demographics.coopercenter.org/sites/demographics/files/2019-08/VAPopProj%20Methodology_2019_Updated.pdf">here</a> and <a href="https://demographics.texas.gov/Resources/Presentations/ADC/2014/ADC2014_2C_Swanson.pdf">here</a></li>
<li><p>Original article <a href="https://utsacloud-my.sharepoint.com/:b:/g/personal/corey_sparks_utsa_edu/EVRxaOrCh21FikL16bWklzkBJly961agP92K5j2JKvk-qA?e=a1eBaT">here</a></p></li>
<li>Leslie Matrix model <a href="https://www.jstor.org/stable/pdf/2332297.pdf?casa_token=jl7ggpaUHlgAAAAA:nBBAiLiNzOoobVn2vsrfnir6V0-0qYvwlDnmlCl8ofL-gXzNqZu6Q2hA5yNRBG3NluuZ3kQzZbRq1mELaDJO4Eyr9Wbmod9h_K6_rlmhFX4ZFNpJTG4">Leslie, 1945</a></li>
<li>Birth Pulse model</li>
<li>People reproduce at specific ages after surviving to that age</li>
<li><p>Very thorough treatment in <a href="https://utsacloud-my.sharepoint.com/:b:/g/personal/corey_sparks_utsa_edu/EYFWQYGEoXBCukP3O75Xj1kBBmnky2P9hLFbzCbWFZ-EOQ?e=DapuF5">Keyfitz and Caswell, 2005, Chapters 3, 7 and 9</a></p></li>
</ul>
<p><span class="math display">\[\begin{pmatrix}
n_1\\ 
n_2\\ 
n_3
\end{pmatrix} (t+1)=  
\begin{pmatrix}
F_1 &amp; F_2 &amp; F_3\\ 
P_1 &amp; 0 &amp; 0\\ 
0 &amp; P_2 &amp; 0
\end{pmatrix}
\begin{pmatrix}
n_1\\ 
n_2\\ 
n_3
\end{pmatrix}(t)\]</span></p>
<p>Or as: <span class="math display">\[n(t+1) = \mathbf{A} n(t)\]</span></p>
<p><span class="math inline">\(n_k\)</span> are the population sizes, <span class="math inline">\(F_k\)</span> are the reproductive values at each age, and <span class="math inline">\(P_k\)</span> are the survivorship ratios at each age</p>
<ul>
<li>Very flexible - accomodates both age and stage structure, more general population model than cohort component</li>
<li><p><a href="https://web.stanford.edu/~jhj1/teachingdocs/Jones-Leslie1-050208.pdf">Notes by James Holland Jones</a></p></li>
<li>Bayesian projection methods
<ul>
<li><a href="https://bayespop.csss.washington.edu/">Bayespop</a></li>
<li>Uses methods from Bayesian statistics to project TFR, <span class="math inline">\(e_0\)</span> and population structure</li>
<li>Incorporates uncertainty in all levels of analysis</li>
<li>Leads to projections with errors incorporated</li>
<li>Used by the <a href="https://www.un.org/development/desa/pd/#:~:text=The%20Population%20Division%20of%20the,area%20of%20population%20and%20development.">UN Population Division</a></li>
</ul></li>
</ul>
</div>
</div>
<div id="example---leslie-matrix-model" class="section level2">
<h2>Example - Leslie Matrix Model</h2>
<p>Below, I will illustrate how to use data from the <a href="https://www.mortality.org/">Human Mortality Database</a> and the <a href="https://www.humanfertility.org/cgi-bin/main.php">Human Fertility Database</a> for the US to create a Leslie Matrix model.</p>
<div class="sourceCode" id="cb1"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb1-1" data-line-number="1"><span class="kw">library</span>(tidyverse)</a></code></pre></div>
<pre><code>## ── Attaching packages ─────────────────────────────────────────────────────────────── tidyverse 1.3.0 ──</code></pre>
<pre><code>## ✓ ggplot2 3.3.2     ✓ purrr   0.3.4
## ✓ tibble  3.0.3     ✓ dplyr   1.0.0
## ✓ tidyr   1.1.0     ✓ stringr 1.4.0
## ✓ readr   1.3.1     ✓ forcats 0.5.0</code></pre>
<pre><code>## ── Conflicts ────────────────────────────────────────────────────────────────── tidyverse_conflicts() ──
## x dplyr::filter() masks stats::filter()
## x dplyr::lag()    masks stats::lag()</code></pre>
<div class="sourceCode" id="cb5"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb5-1" data-line-number="1"><span class="kw">library</span>(HMDHFDplus)</a>
<a class="sourceLine" id="cb5-2" data-line-number="2"><span class="kw">library</span>(tidycensus)</a></code></pre></div>
<div id="data-from-hmdhfd" class="section level3">
<h3>Data from HMD/HFD</h3>
<p>The human mortality and human fertility databases are excellent sources for national historic series of mortality and fertility rates. You need to register with them to get access to the data.</p>
<div class="sourceCode" id="cb6"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb6-1" data-line-number="1">UShmd&lt;-<span class="st"> </span><span class="kw">readHMDweb</span>(<span class="dt">CNTRY =</span> <span class="st">&quot;USA&quot;</span>, <span class="dt">item =</span><span class="st">&quot;fltper_5x5&quot;</span>, <span class="dt">password =</span>mypassword,<span class="dt">username =</span> myusername)</a>
<a class="sourceLine" id="cb6-2" data-line-number="2"><span class="co">#49 for item, 51 for US</span></a>
<a class="sourceLine" id="cb6-3" data-line-number="3"></a>
<a class="sourceLine" id="cb6-4" data-line-number="4">us_mort&lt;-UShmd<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb6-5" data-line-number="5"><span class="st">  </span><span class="kw">filter</span>(Year <span class="op">==</span><span class="st"> </span><span class="dv">2015</span>)</a>
<a class="sourceLine" id="cb6-6" data-line-number="6"></a>
<a class="sourceLine" id="cb6-7" data-line-number="7"><span class="co">#average qx and lx for ages 0 to 1 and 1 to 4</span></a>
<a class="sourceLine" id="cb6-8" data-line-number="8">us_mort<span class="op">$</span>qx[us_mort<span class="op">$</span>Age<span class="op">==</span><span class="dv">0</span>]&lt;-us_mort<span class="op">$</span>qx[us_mort<span class="op">$</span>Age<span class="op">==</span><span class="dv">0</span>]<span class="op">+</span>us_mort<span class="op">$</span>qx[us_mort<span class="op">$</span>Age<span class="op">==</span><span class="dv">1</span>]</a>
<a class="sourceLine" id="cb6-9" data-line-number="9">us_mort<span class="op">$</span>Lx[us_mort<span class="op">$</span>Age<span class="op">==</span><span class="dv">0</span>]&lt;-us_mort<span class="op">$</span>Lx[us_mort<span class="op">$</span>Age<span class="op">==</span><span class="dv">0</span>]<span class="op">+</span>us_mort<span class="op">$</span>Lx[us_mort<span class="op">$</span>Age<span class="op">==</span><span class="dv">1</span>]</a>
<a class="sourceLine" id="cb6-10" data-line-number="10"></a>
<a class="sourceLine" id="cb6-11" data-line-number="11">us_mort&lt;-us_mort[<span class="op">-</span><span class="dv">2</span>,]</a>
<a class="sourceLine" id="cb6-12" data-line-number="12">  </a>
<a class="sourceLine" id="cb6-13" data-line-number="13"><span class="kw">head</span>(us_mort)</a></code></pre></div>
<pre><code>##   Year Age      mx      qx   ax     lx  dx     Lx      Tx    ex OpenInterval
## 1 2015   0 0.00530 0.00615 0.14 100000 527 497227 8135585 81.36        FALSE
## 3 2015   5 0.00011 0.00053 2.40  99386  53 496790 7638358 76.86        FALSE
## 4 2015  10 0.00012 0.00061 2.81  99332  61 496529 7141568 71.90        FALSE
## 5 2015  15 0.00029 0.00147 2.85  99271 146 496043 6645039 66.94        FALSE
## 6 2015  20 0.00049 0.00246 2.61  99125 243 495045 6148996 62.03        FALSE
## 7 2015  25 0.00066 0.00329 2.61  98882 325 493632 5653951 57.18        FALSE</code></pre>
<p>Fertility data are by single year of age, so I aggregate to 5 year intervals</p>
<div class="sourceCode" id="cb8"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb8-1" data-line-number="1">UShfd&lt;-<span class="kw">readHFDweb</span>(<span class="dt">CNTRY=</span><span class="st">&quot;USA&quot;</span>,<span class="dt">username =</span> myusername,<span class="dt">password =</span>myotherpassword, <span class="dt">item =</span> <span class="st">&quot;asfrRR&quot;</span>)</a>
<a class="sourceLine" id="cb8-2" data-line-number="2"><span class="co">#30</span></a>
<a class="sourceLine" id="cb8-3" data-line-number="3">us_fert&lt;-UShfd<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb8-4" data-line-number="4"><span class="st">  </span><span class="kw">filter</span>(Year<span class="op">==</span><span class="dv">2015</span>)<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb8-5" data-line-number="5"><span class="st">  </span><span class="kw">mutate</span>(<span class="dt">age_grp =</span> <span class="kw">cut</span>(Age, <span class="dt">breaks =</span> <span class="kw">seq</span>(<span class="dv">10</span>, <span class="dv">55</span>, <span class="dv">5</span>), <span class="dt">include.lowest =</span> T))<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb8-6" data-line-number="6"><span class="st">  </span><span class="kw">group_by</span>(age_grp)<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb8-7" data-line-number="7"><span class="st">  </span><span class="kw">summarise</span>(<span class="dt">asfr5 =</span> <span class="kw">sum</span>(ASFR))<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb8-8" data-line-number="8"><span class="st">  </span><span class="kw">filter</span>(<span class="kw">is.na</span>(age_grp)<span class="op">==</span>F)<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb8-9" data-line-number="9"><span class="st">  </span><span class="kw">ungroup</span>()</a></code></pre></div>
<pre><code>## `summarise()` ungrouping output (override with `.groups` argument)</code></pre>
<div class="sourceCode" id="cb10"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb10-1" data-line-number="1">us_fert<span class="op">$</span>age5&lt;-<span class="kw">seq</span>(<span class="dv">10</span>,<span class="dv">50</span>,<span class="dv">5</span>)</a>
<a class="sourceLine" id="cb10-2" data-line-number="2"></a>
<a class="sourceLine" id="cb10-3" data-line-number="3"><span class="kw">head</span>(us_fert)</a></code></pre></div>
<pre><code>## # A tibble: 6 x 3
##   age_grp  asfr5  age5
##   &lt;fct&gt;    &lt;dbl&gt; &lt;dbl&gt;
## 1 [10,15] 0.0049    10
## 2 (15,20] 0.171     15
## 3 (20,25] 0.414     20
## 4 (25,30] 0.543     25
## 5 (30,35] 0.469     30
## 6 (35,40] 0.205     35</code></pre>
<p>Combine these together</p>
<div class="sourceCode" id="cb12"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb12-1" data-line-number="1">us_dem&lt;-<span class="kw">merge</span>(us_mort, us_fert, <span class="dt">by.x=</span><span class="st">&quot;Age&quot;</span>, <span class="dt">by.y=</span><span class="st">&quot;age5&quot;</span>, <span class="dt">all.x=</span>T)</a>
<a class="sourceLine" id="cb12-2" data-line-number="2">us_dem<span class="op">$</span>asfr5&lt;-<span class="kw">ifelse</span>(<span class="kw">is.na</span>(us_dem<span class="op">$</span>asfr5)<span class="op">==</span>T, <span class="dv">0</span>, us_dem<span class="op">$</span>asfr5)</a>
<a class="sourceLine" id="cb12-3" data-line-number="3"></a>
<a class="sourceLine" id="cb12-4" data-line-number="4"><span class="kw">head</span>(us_dem)</a></code></pre></div>
<pre><code>##   Age Year      mx      qx   ax     lx  dx     Lx      Tx    ex OpenInterval
## 1   0 2015 0.00530 0.00615 0.14 100000 527 497227 8135585 81.36        FALSE
## 2   5 2015 0.00011 0.00053 2.40  99386  53 496790 7638358 76.86        FALSE
## 3  10 2015 0.00012 0.00061 2.81  99332  61 496529 7141568 71.90        FALSE
## 4  15 2015 0.00029 0.00147 2.85  99271 146 496043 6645039 66.94        FALSE
## 5  20 2015 0.00049 0.00246 2.61  99125 243 495045 6148996 62.03        FALSE
## 6  25 2015 0.00066 0.00329 2.61  98882 325 493632 5653951 57.18        FALSE
##   age_grp   asfr5
## 1    &lt;NA&gt; 0.00000
## 2    &lt;NA&gt; 0.00000
## 3 [10,15] 0.00490
## 4 (15,20] 0.17101
## 5 (20,25] 0.41403
## 6 (25,30] 0.54271</code></pre>
<div class="sourceCode" id="cb14"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb14-1" data-line-number="1"><span class="kw">ggplot</span>(us_dem)<span class="op">+</span><span class="kw">geom_line</span>( <span class="kw">aes</span>(<span class="dt">x=</span>Age, <span class="dt">y=</span>asfr5), <span class="dt">color=</span><span class="st">&quot;red&quot;</span>)<span class="op">+</span><span class="kw">geom_line</span>( <span class="kw">aes</span>(<span class="dt">x=</span>Age, <span class="dt">y=</span>qx), <span class="dt">color=</span><span class="st">&quot;blue&quot;</span>)<span class="op">+</span><span class="kw">xlim</span>(<span class="dv">0</span>, <span class="dv">110</span>)<span class="op">+</span><span class="kw">ylab</span>(<span class="st">&quot;Rate&quot;</span>)<span class="op">+</span><span class="kw">xlab</span>(<span class="st">&quot;Age&quot;</span>)<span class="op">+</span><span class="kw">theme_minimal</span>()</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/Projection_files/figure-html/unnamed-chunk-7-1.png" /><!-- --></p>
</div>
</div>
<div id="population-data" class="section level2">
<h2>Population data</h2>
<p>I get the 2015 population age distribution from the census estimates using <code>tidycensus</code> You can see the various parameters <a href="https://www.census.gov/data/developers/data-sets/popest-popproj/popest/popest-vars.html">here</a></p>
<div class="sourceCode" id="cb15"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb15-1" data-line-number="1">us_popn&lt;-<span class="kw">get_estimates</span>(<span class="dt">geography=</span><span class="st">&quot;us&quot;</span>, <span class="dt">product =</span> <span class="st">&quot;characteristics&quot;</span>, <span class="dt">breakdown =</span> <span class="kw">c</span>(<span class="st">&quot;AGEGROUP&quot;</span>, <span class="st">&quot;SEX&quot;</span>), <span class="dt">year =</span> <span class="dv">2015</span>)</a>
<a class="sourceLine" id="cb15-2" data-line-number="2">us_popn&lt;-us_popn<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb15-3" data-line-number="3"><span class="st">  </span><span class="kw">filter</span>(AGEGROUP<span class="op">&gt;</span><span class="dv">0</span><span class="op">&amp;</span>AGEGROUP<span class="op">&lt;</span><span class="dv">19</span>, SEX<span class="op">==</span><span class="dv">2</span>)<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb15-4" data-line-number="4"><span class="st">  </span><span class="co">#mutate(case_when(.$AGEGROUP==1))%&gt;%</span></a>
<a class="sourceLine" id="cb15-5" data-line-number="5"><span class="st">  </span><span class="kw">arrange</span>(AGEGROUP)</a>
<a class="sourceLine" id="cb15-6" data-line-number="6"></a>
<a class="sourceLine" id="cb15-7" data-line-number="7"><span class="kw">head</span>(us_popn)</a></code></pre></div>
<pre><code>## # A tibble: 6 x 5
##   GEOID NAME             value AGEGROUP   SEX
##   &lt;chr&gt; &lt;chr&gt;            &lt;dbl&gt;    &lt;dbl&gt; &lt;dbl&gt;
## 1 1     United States  9729680        1     2
## 2 1     United States 10028044        2     2
## 3 1     United States 10101942        3     2
## 4 1     United States 10311036        4     2
## 5 1     United States 11071459        5     2
## 6 1     United States 11052155        6     2</code></pre>
<div id="leslie-matrix-construction" class="section level3">
<h3>Leslie Matrix Construction</h3>
<ul>
<li>Need matrix <span class="math inline">\(A\)</span>, that is # of ages by # of ages in size, in this case, it will be 18 x 18</li>
<li>The first row are the reproductive values, <span class="math inline">\(F_k\)</span> <strong>THESE ARE NOT AGE SPECIFIC FERTILITY RATES</strong>, they also have to incorporate the probability of surviving to the age of reproduction.</li>
</ul>
<p><span class="math display">\[F_k = l(.5) \left ( \frac{m_i+P_i m_{i+1}}{2} \right)\]</span></p>
<ul>
<li><p>The *sub-diagonal is the survival ratios, these are calculated as <span class="math inline">\(L_{x+1}/L_{x})\)</span></p></li>
<li><p>Another note is that this is a one-sex population model, in this case, for females only.</p></li>
<li><p>I use code from <a href="https://raw.githubusercontent.com/AppliedDemogToolbox/Hunsinger_BasicProjection/master/BasicProjectionCode.R">Eddie Hunsinger’s Leslie Matrix Code</a>, so the good ideas here belong to him.</p></li>
</ul>
<div class="sourceCode" id="cb17"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb17-1" data-line-number="1">A&lt;-<span class="kw">matrix</span>(<span class="dv">0</span>, <span class="dt">nrow=</span><span class="kw">dim</span>(us_popn)[<span class="dv">1</span>], <span class="dt">ncol=</span><span class="kw">dim</span>(us_popn)[<span class="dv">1</span>])</a>
<a class="sourceLine" id="cb17-2" data-line-number="2">A</a></code></pre></div>
<pre><code>##       [,1] [,2] [,3] [,4] [,5] [,6] [,7] [,8] [,9] [,10] [,11] [,12] [,13]
##  [1,]    0    0    0    0    0    0    0    0    0     0     0     0     0
##  [2,]    0    0    0    0    0    0    0    0    0     0     0     0     0
##  [3,]    0    0    0    0    0    0    0    0    0     0     0     0     0
##  [4,]    0    0    0    0    0    0    0    0    0     0     0     0     0
##  [5,]    0    0    0    0    0    0    0    0    0     0     0     0     0
##  [6,]    0    0    0    0    0    0    0    0    0     0     0     0     0
##  [7,]    0    0    0    0    0    0    0    0    0     0     0     0     0
##  [8,]    0    0    0    0    0    0    0    0    0     0     0     0     0
##  [9,]    0    0    0    0    0    0    0    0    0     0     0     0     0
## [10,]    0    0    0    0    0    0    0    0    0     0     0     0     0
## [11,]    0    0    0    0    0    0    0    0    0     0     0     0     0
## [12,]    0    0    0    0    0    0    0    0    0     0     0     0     0
## [13,]    0    0    0    0    0    0    0    0    0     0     0     0     0
## [14,]    0    0    0    0    0    0    0    0    0     0     0     0     0
## [15,]    0    0    0    0    0    0    0    0    0     0     0     0     0
## [16,]    0    0    0    0    0    0    0    0    0     0     0     0     0
## [17,]    0    0    0    0    0    0    0    0    0     0     0     0     0
## [18,]    0    0    0    0    0    0    0    0    0     0     0     0     0
##       [,14] [,15] [,16] [,17] [,18]
##  [1,]     0     0     0     0     0
##  [2,]     0     0     0     0     0
##  [3,]     0     0     0     0     0
##  [4,]     0     0     0     0     0
##  [5,]     0     0     0     0     0
##  [6,]     0     0     0     0     0
##  [7,]     0     0     0     0     0
##  [8,]     0     0     0     0     0
##  [9,]     0     0     0     0     0
## [10,]     0     0     0     0     0
## [11,]     0     0     0     0     0
## [12,]     0     0     0     0     0
## [13,]     0     0     0     0     0
## [14,]     0     0     0     0     0
## [15,]     0     0     0     0     0
## [16,]     0     0     0     0     0
## [17,]     0     0     0     0     0
## [18,]     0     0     0     0     0</code></pre>
<div class="sourceCode" id="cb19"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb19-1" data-line-number="1">size&lt;-<span class="kw">dim</span>(A)[<span class="dv">1</span>]</a>
<a class="sourceLine" id="cb19-2" data-line-number="2">sxf1&lt;-<span class="kw">array</span>(<span class="dv">0</span>,<span class="kw">c</span>(size<span class="dv">-1</span>))</a>
<a class="sourceLine" id="cb19-3" data-line-number="3">Lxf&lt;-us_dem<span class="op">$</span>Lx<span class="op">/</span><span class="dv">100000</span></a>
<a class="sourceLine" id="cb19-4" data-line-number="4"><span class="cf">for</span> (i <span class="cf">in</span> <span class="dv">1</span><span class="op">:</span>size<span class="dv">-1</span>){sxf1[i]&lt;-(Lxf[i<span class="op">+</span><span class="dv">1</span>]<span class="op">/</span>Lxf[i])}</a>
<a class="sourceLine" id="cb19-5" data-line-number="5"></a>
<a class="sourceLine" id="cb19-6" data-line-number="6"><span class="co">#make matrix with survivals on off diagonal</span></a>
<a class="sourceLine" id="cb19-7" data-line-number="7">sf1&lt;-<span class="kw">rbind</span>(<span class="dv">0</span>,<span class="kw">cbind</span>(<span class="kw">diag</span>(sxf1),<span class="dv">0</span>))</a>
<a class="sourceLine" id="cb19-8" data-line-number="8"></a>
<a class="sourceLine" id="cb19-9" data-line-number="9"><span class="co">##SPECIAL CALCULATION FOR OPEN-ENDED AGE GROUP OF LESLIE MATRICES</span></a>
<a class="sourceLine" id="cb19-10" data-line-number="10">sf1[size,size]&lt;-sf1[size,size<span class="dv">-1</span>]&lt;-Lxf[size]<span class="op">/</span>(Lxf[size]<span class="op">+</span>Lxf[size<span class="dv">-1</span>])</a>
<a class="sourceLine" id="cb19-11" data-line-number="11"></a>
<a class="sourceLine" id="cb19-12" data-line-number="12"><span class="co"># proportion of female births</span></a>
<a class="sourceLine" id="cb19-13" data-line-number="13">ffab&lt;-<span class="fl">0.4882846</span></a>
<a class="sourceLine" id="cb19-14" data-line-number="14"></a>
<a class="sourceLine" id="cb19-15" data-line-number="15"><span class="co">##MAKE THE LESLIE MATRICES FOR FEMALES</span></a>
<a class="sourceLine" id="cb19-16" data-line-number="16"><span class="co">#convert fertilities to proportions</span></a>
<a class="sourceLine" id="cb19-17" data-line-number="17">fert&lt;-us_dem<span class="op">$</span>asfr5<span class="co">#/sum(us_dem$asfr5)</span></a>
<a class="sourceLine" id="cb19-18" data-line-number="18"></a>
<a class="sourceLine" id="cb19-19" data-line-number="19"><span class="co">##Make first row of matrix - reproductive values</span></a>
<a class="sourceLine" id="cb19-20" data-line-number="20"><span class="cf">for</span>(j <span class="cf">in</span> <span class="dv">1</span><span class="op">:</span>size<span class="dv">-1</span>)</a>
<a class="sourceLine" id="cb19-21" data-line-number="21">{sf1[<span class="dv">1</span>,j]&lt;-(Lxf[<span class="dv">1</span>]<span class="op">/</span><span class="dv">10</span>)<span class="op">*</span>(fert[j]<span class="op">+</span>fert[j<span class="op">+</span><span class="dv">1</span>]<span class="op">*</span>(sxf1[j]))<span class="op">*</span>ffab}</a>
<a class="sourceLine" id="cb19-22" data-line-number="22">sf1</a></code></pre></div>
<pre><code>##            [,1]        [,2]       [,3]      [,4]      [,5]      [,6]      [,7]
##  [1,] 0.0000000 0.001189038 0.04266825 0.1418386 0.2319092 0.2450830 0.1632889
##  [2,] 0.9991211 0.000000000 0.00000000 0.0000000 0.0000000 0.0000000 0.0000000
##  [3,] 0.0000000 0.999474627 0.00000000 0.0000000 0.0000000 0.0000000 0.0000000
##  [4,] 0.0000000 0.000000000 0.99902121 0.0000000 0.0000000 0.0000000 0.0000000
##  [5,] 0.0000000 0.000000000 0.00000000 0.9979881 0.0000000 0.0000000 0.0000000
##  [6,] 0.0000000 0.000000000 0.00000000 0.0000000 0.9971457 0.0000000 0.0000000
##  [7,] 0.0000000 0.000000000 0.00000000 0.0000000 0.0000000 0.9961611 0.0000000
##  [8,] 0.0000000 0.000000000 0.00000000 0.0000000 0.0000000 0.0000000 0.9948631
##  [9,] 0.0000000 0.000000000 0.00000000 0.0000000 0.0000000 0.0000000 0.0000000
## [10,] 0.0000000 0.000000000 0.00000000 0.0000000 0.0000000 0.0000000 0.0000000
## [11,] 0.0000000 0.000000000 0.00000000 0.0000000 0.0000000 0.0000000 0.0000000
## [12,] 0.0000000 0.000000000 0.00000000 0.0000000 0.0000000 0.0000000 0.0000000
## [13,] 0.0000000 0.000000000 0.00000000 0.0000000 0.0000000 0.0000000 0.0000000
## [14,] 0.0000000 0.000000000 0.00000000 0.0000000 0.0000000 0.0000000 0.0000000
## [15,] 0.0000000 0.000000000 0.00000000 0.0000000 0.0000000 0.0000000 0.0000000
## [16,] 0.0000000 0.000000000 0.00000000 0.0000000 0.0000000 0.0000000 0.0000000
## [17,] 0.0000000 0.000000000 0.00000000 0.0000000 0.0000000 0.0000000 0.0000000
## [18,] 0.0000000 0.000000000 0.00000000 0.0000000 0.0000000 0.0000000 0.0000000
##             [,8]        [,9]        [,10]        [,11]     [,12]     [,13]
##  [1,] 0.05840419 0.009203672 0.0005699971 3.641824e-05 0.0000000 0.0000000
##  [2,] 0.00000000 0.000000000 0.0000000000 0.000000e+00 0.0000000 0.0000000
##  [3,] 0.00000000 0.000000000 0.0000000000 0.000000e+00 0.0000000 0.0000000
##  [4,] 0.00000000 0.000000000 0.0000000000 0.000000e+00 0.0000000 0.0000000
##  [5,] 0.00000000 0.000000000 0.0000000000 0.000000e+00 0.0000000 0.0000000
##  [6,] 0.00000000 0.000000000 0.0000000000 0.000000e+00 0.0000000 0.0000000
##  [7,] 0.00000000 0.000000000 0.0000000000 0.000000e+00 0.0000000 0.0000000
##  [8,] 0.00000000 0.000000000 0.0000000000 0.000000e+00 0.0000000 0.0000000
##  [9,] 0.99317268 0.000000000 0.0000000000 0.000000e+00 0.0000000 0.0000000
## [10,] 0.00000000 0.990100253 0.0000000000 0.000000e+00 0.0000000 0.0000000
## [11,] 0.00000000 0.000000000 0.9847503747 0.000000e+00 0.0000000 0.0000000
## [12,] 0.00000000 0.000000000 0.0000000000 9.768769e-01 0.0000000 0.0000000
## [13,] 0.00000000 0.000000000 0.0000000000 0.000000e+00 0.9669513 0.0000000
## [14,] 0.00000000 0.000000000 0.0000000000 0.000000e+00 0.0000000 0.9531395
## [15,] 0.00000000 0.000000000 0.0000000000 0.000000e+00 0.0000000 0.0000000
## [16,] 0.00000000 0.000000000 0.0000000000 0.000000e+00 0.0000000 0.0000000
## [17,] 0.00000000 0.000000000 0.0000000000 0.000000e+00 0.0000000 0.0000000
## [18,] 0.00000000 0.000000000 0.0000000000 0.000000e+00 0.0000000 0.0000000
##          [,14]     [,15]    [,16]     [,17]     [,18]
##  [1,] 0.000000 0.0000000 0.000000 0.0000000 0.0000000
##  [2,] 0.000000 0.0000000 0.000000 0.0000000 0.0000000
##  [3,] 0.000000 0.0000000 0.000000 0.0000000 0.0000000
##  [4,] 0.000000 0.0000000 0.000000 0.0000000 0.0000000
##  [5,] 0.000000 0.0000000 0.000000 0.0000000 0.0000000
##  [6,] 0.000000 0.0000000 0.000000 0.0000000 0.0000000
##  [7,] 0.000000 0.0000000 0.000000 0.0000000 0.0000000
##  [8,] 0.000000 0.0000000 0.000000 0.0000000 0.0000000
##  [9,] 0.000000 0.0000000 0.000000 0.0000000 0.0000000
## [10,] 0.000000 0.0000000 0.000000 0.0000000 0.0000000
## [11,] 0.000000 0.0000000 0.000000 0.0000000 0.0000000
## [12,] 0.000000 0.0000000 0.000000 0.0000000 0.0000000
## [13,] 0.000000 0.0000000 0.000000 0.0000000 0.0000000
## [14,] 0.000000 0.0000000 0.000000 0.0000000 0.0000000
## [15,] 0.928972 0.0000000 0.000000 0.0000000 0.0000000
## [16,] 0.000000 0.8878485 0.000000 0.0000000 0.0000000
## [17,] 0.000000 0.0000000 0.819659 0.0000000 0.0000000
## [18,] 0.000000 0.0000000 0.000000 0.4147917 0.4147917</code></pre>
<div class="sourceCode" id="cb21"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb21-1" data-line-number="1"><span class="co">#assemble matrix</span></a>
<a class="sourceLine" id="cb21-2" data-line-number="2">sf1[size,size]&lt;-<span class="dv">0</span></a>
<a class="sourceLine" id="cb21-3" data-line-number="3">A&lt;-sf1</a>
<a class="sourceLine" id="cb21-4" data-line-number="4"><span class="kw">plot</span>(<span class="dt">x=</span><span class="kw">seq</span>(<span class="dv">0</span>, <span class="dv">80</span>, <span class="dv">5</span>), <span class="dt">y=</span><span class="kw">diag</span>(sf1[<span class="op">-</span><span class="dv">1</span>,<span class="op">-</span><span class="kw">ncol</span>(sf1)]), <span class="dt">type=</span><span class="st">&quot;l&quot;</span>, <span class="dt">ylim=</span><span class="kw">c</span>(<span class="dv">0</span>, <span class="dv">1</span>), <span class="dt">ylab=</span><span class="st">&quot;Rate&quot;</span>, <span class="dt">xlab=</span><span class="st">&quot;Age&quot;</span>)</a>
<a class="sourceLine" id="cb21-5" data-line-number="5"><span class="kw">lines</span>(<span class="dt">x=</span><span class="kw">seq</span>(<span class="dv">0</span>, <span class="dv">80</span>, <span class="dv">5</span>), <span class="dt">y =</span> sf1[<span class="dv">1</span>, <span class="op">-</span>size], <span class="dt">col=</span><span class="dv">2</span>)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/Projection_files/figure-html/unnamed-chunk-9-1.png" /><!-- --></p>
</div>
</div>
<div id="project-the-population" class="section level2">
<h2>Project the population</h2>
<p>Since these data represent 5 year age intervals, the projections will move the population forward in increments of 5 years. Below, I project the population forward by 10 periods, or 50 years. Given that we start at 2015 using data from the HMD/HFD, this will take us to 2065.</p>
<p>The weakness of this model is that it does not incorporate migration, so this is an incomplete model, but reflects the extrapolation of the population using current and unchanging rates of fertility and mortality. The model can be expanded to incorporate segmented populations, however, with exchanges between areas.</p>
<div class="sourceCode" id="cb22"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb22-1" data-line-number="1"><span class="co">##MAKE ARRAYS TO HOLD THE DATA</span></a>
<a class="sourceLine" id="cb22-2" data-line-number="2">nproj&lt;-<span class="dv">10</span></a>
<a class="sourceLine" id="cb22-3" data-line-number="3">newpop&lt;-<span class="kw">matrix</span>(<span class="dv">0</span>, <span class="dt">nrow=</span>size, <span class="dt">ncol=</span>nproj)</a>
<a class="sourceLine" id="cb22-4" data-line-number="4">newpop[,<span class="dv">1</span>]&lt;-us_popn<span class="op">$</span>value <span class="co">#first column is current population size</span></a>
<a class="sourceLine" id="cb22-5" data-line-number="5"></a>
<a class="sourceLine" id="cb22-6" data-line-number="6"><span class="co">#loop over the new periods</span></a>
<a class="sourceLine" id="cb22-7" data-line-number="7"><span class="cf">for</span>(i <span class="cf">in</span> <span class="dv">2</span><span class="op">:</span>nproj){</a>
<a class="sourceLine" id="cb22-8" data-line-number="8">newpop[,i]&lt;-(A<span class="op">%*%</span>newpop[,i<span class="dv">-1</span>])}</a>
<a class="sourceLine" id="cb22-9" data-line-number="9"></a>
<a class="sourceLine" id="cb22-10" data-line-number="10"><span class="kw">head</span>(newpop)</a></code></pre></div>
<pre><code>##          [,1]     [,2]     [,3]     [,4]    [,5]    [,6]    [,7]    [,8]
## [1,]  9729680  9638886  9486594  9236391 8992077 8801727 8641051 8481188
## [2,] 10028044  9721129  9630414  9478257 9228274 8984174 8793992 8633456
## [3,] 10101942 10022776  9716022  9625355 9473277 9223425 8979454 8789372
## [4,] 10311036 10092054 10012965  9706512 9615934 9464005 9214397 8970665
## [5,] 11071459 10290291 10071750  9992820 9686983 9596587 9444964 9195859
## [6,] 11052155 11039858 10260920 10043002 9964298 9659333 9569196 9418005
##         [,9]   [,10]
## [1,] 8310317 8126435
## [2,] 8473734 8303014
## [3,] 8628921 8469282
## [4,] 8780769 8620475
## [5,] 8952617 8763102
## [6,] 9169611 8927063</code></pre>
<div class="sourceCode" id="cb24"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb24-1" data-line-number="1"><span class="co">#Plot the new total population sizes</span></a>
<a class="sourceLine" id="cb24-2" data-line-number="2"><span class="kw">options</span>(<span class="dt">scipen=</span><span class="dv">999</span>)</a>
<a class="sourceLine" id="cb24-3" data-line-number="3"><span class="kw">plot</span>(<span class="dt">y =</span> <span class="kw">apply</span>(newpop, <span class="dv">2</span>, sum), <span class="dt">x=</span> <span class="kw">seq</span>(<span class="dv">2015</span>, <span class="dv">2060</span>, <span class="dv">5</span>) , <span class="dt">main=</span><span class="st">&quot;Total Female Population Size to 2060&quot;</span>)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/Projection_files/figure-html/unnamed-chunk-10-1.png" /><!-- --></p>
<div id="further-analysis-of-the-matrix" class="section level3">
<h3>Further analysis of the matrix…</h3>
<p>See <a href="https://web.stanford.edu/~jhj1/teachingdocs/Jones-Leslie1-050208.pdf">Jamie’s Notes</a> and <a href="https://utsacloud-my.sharepoint.com/:b:/g/personal/corey_sparks_utsa_edu/EYFWQYGEoXBCukP3O75Xj1kBBmnky2P9hLFbzCbWFZ-EOQ?e=DapuF5">Ch 13 of Keyfitz and Caswell</a> for more details on this.</p>
<p>We can do a eigenvalue decomposition of the matrix <span class="math inline">\(A\)</span>, and recover the population growth rate from the log of the first eigenvalue. The <em>stable age structure</em> can also be recovered by the standardized first eigenvector of the matrix.</p>
<div class="sourceCode" id="cb25"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb25-1" data-line-number="1">e&lt;-<span class="kw">eigen</span>(A)</a>
<a class="sourceLine" id="cb25-2" data-line-number="2">e<span class="op">$</span>values[<span class="dv">1</span>]</a></code></pre></div>
<pre><code>## [1] 0.9791456+0i</code></pre>
<div class="sourceCode" id="cb27"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb27-1" data-line-number="1"><span class="kw">log</span>(e<span class="op">$</span>values[<span class="dv">1</span>])</a></code></pre></div>
<pre><code>## [1] -0.02107494+0i</code></pre>
<div class="sourceCode" id="cb29"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb29-1" data-line-number="1">arv&lt;-<span class="kw">abs</span>(<span class="kw">Re</span>(e<span class="op">$</span>vectors[,<span class="dv">1</span>]))</a>
<a class="sourceLine" id="cb29-2" data-line-number="2">stableage&lt;-arv<span class="op">/</span><span class="kw">sum</span>(arv)</a>
<a class="sourceLine" id="cb29-3" data-line-number="3"><span class="kw">plot</span>(stableage, <span class="dt">ylim=</span><span class="kw">c</span>(<span class="dv">0</span>, <span class="fl">.1</span>))</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/Projection_files/figure-html/unnamed-chunk-11-1.png" /><!-- --></p>
<p>Even more analysis….</p>
<div class="sourceCode" id="cb30"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb30-1" data-line-number="1"><span class="kw">library</span>(demogR)</a>
<a class="sourceLine" id="cb30-2" data-line-number="2">A2&lt;-<span class="kw">leslie.matrix</span>(<span class="dt">lx=</span>us_dem<span class="op">$</span>lx<span class="op">/</span><span class="dv">100000</span>, <span class="dt">mx=</span>us_dem<span class="op">$</span>asfr5, <span class="dt">one.sex =</span> T, <span class="dt">SRB =</span> <span class="fl">1.048</span>, <span class="dt">infant.class =</span> F)</a>
<a class="sourceLine" id="cb30-3" data-line-number="3">ea&lt;-<span class="kw">eigen.analysis</span>(A2)</a>
<a class="sourceLine" id="cb30-4" data-line-number="4">ea</a></code></pre></div>
<pre><code>## $lambda1
## [1] 0.9788133
## 
## $rho
## [1] 2.393615
## 
## $sensitivities
##            [,1]       [,2]       [,3]       [,4]       [,5]       [,6]
##  [1,] 0.0000000 0.09003766 0.09193005 0.09378177 0.09557683 0.09732468
##  [2,] 0.3475641 0.00000000 0.00000000 0.00000000 0.00000000 0.00000000
##  [3,] 0.0000000 0.17304440 0.00000000 0.00000000 0.00000000 0.00000000
##  [4,] 0.0000000 0.00000000 0.16532057 0.00000000 0.00000000 0.00000000
##  [5,] 0.0000000 0.00000000 0.00000000 0.13875608 0.00000000 0.00000000
##  [6,] 0.0000000 0.00000000 0.00000000 0.00000000 0.09429642 0.00000000
##  [7,] 0.0000000 0.00000000 0.00000000 0.00000000 0.00000000 0.04638306
##  [8,] 0.0000000 0.00000000 0.00000000 0.00000000 0.00000000 0.00000000
##  [9,] 0.0000000 0.00000000 0.00000000 0.00000000 0.00000000 0.00000000
## [10,] 0.0000000 0.00000000 0.00000000 0.00000000 0.00000000 0.00000000
## [11,] 0.0000000 0.00000000 0.00000000 0.00000000 0.00000000 0.00000000
##            [,7]        [,8]         [,9]          [,10]    [,11]
##  [1,] 0.0989884 0.100539402 0.1018889841 0.102833546869 0.103081
##  [2,] 0.0000000 0.000000000 0.0000000000 0.000000000000 0.000000
##  [3,] 0.0000000 0.000000000 0.0000000000 0.000000000000 0.000000
##  [4,] 0.0000000 0.000000000 0.0000000000 0.000000000000 0.000000
##  [5,] 0.0000000 0.000000000 0.0000000000 0.000000000000 0.000000
##  [6,] 0.0000000 0.000000000 0.0000000000 0.000000000000 0.000000
##  [7,] 0.0000000 0.000000000 0.0000000000 0.000000000000 0.000000
##  [8,] 0.0138569 0.000000000 0.0000000000 0.000000000000 0.000000
##  [9,] 0.0000000 0.002021231 0.0000000000 0.000000000000 0.000000
## [10,] 0.0000000 0.000000000 0.0001265769 0.000000000000 0.000000
## [11,] 0.0000000 0.000000000 0.0000000000 0.000007694781 0.000000
## attr(,&quot;class&quot;)
## [1] &quot;leslie.matrix&quot;
## 
## $elasticities
##            [,1]         [,2]       [,3]      [,4]       [,5]       [,6]
##  [1,] 0.0000000 0.0002192746 0.00803085 0.0272386 0.04539112 0.04884489
##  [2,] 0.1769007 0.0000000000 0.00000000 0.0000000 0.00000000 0.00000000
##  [3,] 0.0000000 0.1766814188 0.00000000 0.0000000 0.00000000 0.00000000
##  [4,] 0.0000000 0.0000000000 0.16865057 0.0000000 0.00000000 0.00000000
##  [5,] 0.0000000 0.0000000000 0.00000000 0.1414120 0.00000000 0.00000000
##  [6,] 0.0000000 0.0000000000 0.00000000 0.0000000 0.09602085 0.00000000
##  [7,] 0.0000000 0.0000000000 0.00000000 0.0000000 0.00000000 0.04717596
##  [8,] 0.0000000 0.0000000000 0.00000000 0.0000000 0.00000000 0.00000000
##  [9,] 0.0000000 0.0000000000 0.00000000 0.0000000 0.00000000 0.00000000
## [10,] 0.0000000 0.0000000000 0.00000000 0.0000000 0.00000000 0.00000000
## [11,] 0.0000000 0.0000000000 0.00000000 0.0000000 0.00000000 0.00000000
##             [,7]        [,8]         [,9]          [,10]          [,11]
##  [1,] 0.03310194 0.012025654 0.0019206122 0.000120037015 0.000007713299
##  [2,] 0.00000000 0.000000000 0.0000000000 0.000000000000 0.000000000000
##  [3,] 0.00000000 0.000000000 0.0000000000 0.000000000000 0.000000000000
##  [4,] 0.00000000 0.000000000 0.0000000000 0.000000000000 0.000000000000
##  [5,] 0.00000000 0.000000000 0.0000000000 0.000000000000 0.000000000000
##  [6,] 0.00000000 0.000000000 0.0000000000 0.000000000000 0.000000000000
##  [7,] 0.00000000 0.000000000 0.0000000000 0.000000000000 0.000000000000
##  [8,] 0.01407402 0.000000000 0.0000000000 0.000000000000 0.000000000000
##  [9,] 0.00000000 0.002048363 0.0000000000 0.000000000000 0.000000000000
## [10,] 0.00000000 0.000000000 0.0001277503 0.000000000000 0.000000000000
## [11,] 0.00000000 0.000000000 0.0000000000 0.000007713299 0.000000000000
## attr(,&quot;class&quot;)
## [1] &quot;leslie.matrix&quot;
## 
## $stable.age
##  [1] 0.15344201 0.07809782 0.07973927 0.08134544 0.08290245 0.08441852
##  [7] 0.08586161 0.08720694 0.08837755 0.08919686 0.08941152
## 
## $repro.value
##  [1] 1.00000000000 1.96474122445 1.92191138920 1.79832990425 1.47956336158
##  [6] 0.98660333067 0.47658070739 0.13998508461 0.02010386664 0.00124230193
## [11] 0.00007482753</code></pre>
<div class="sourceCode" id="cb32"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb32-1" data-line-number="1"><span class="kw">plot</span>(ea<span class="op">$</span>stable.age)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/Projection_files/figure-html/unnamed-chunk-12-1.png" /><!-- --></p>
<div class="sourceCode" id="cb33"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb33-1" data-line-number="1"><span class="kw">plot</span>(ea<span class="op">$</span>sensitivities)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/Projection_files/figure-html/unnamed-chunk-12-2.png" /><!-- --></p>
<div class="sourceCode" id="cb34"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb34-1" data-line-number="1"><span class="kw">plot</span>(ea<span class="op">$</span>elasticities)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/Projection_files/figure-html/unnamed-chunk-12-3.png" /><!-- --></p>
<div class="sourceCode" id="cb35"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb35-1" data-line-number="1"><span class="kw">plot</span>(ea<span class="op">$</span>repro.value)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/Projection_files/figure-html/unnamed-chunk-12-4.png" /><!-- --></p>
<p>While this may seem strange, there are <a href="https://www.census.gov/content/dam/Census/library/publications/2020/demo/p25-1146.pdf">other analyses</a> that show a decline in the US population in the absence of migration. Also see <a href="https://www.washingtonpost.com/local/us-population-will-decline-faster-without-steady-immigration-census-report-says/2020/02/13/1ccff6d6-4ea7-11ea-b721-9f4cdc90bc1c_story.html">this report</a> in the Washington Post.</p>
</div>
</div>
<div id="lee-carter-method-for-mortality-forecasting" class="section level2">
<h2>Lee-Carter Method for Mortality Forecasting</h2>
<p>This method originates with <a href="https://www.tandfonline.com/doi/pdf/10.1080/01621459.1992.10475265?casa_token=eyudM2tuE8EAAAAA:FMWxjbOeaJ2NzvSQI5QkYO7xD4WA1UCIFhc47DypkjFDOsYoa2_8WMpSQNffz-v-WgwQoHYv8d-y">Lee &amp; Carter, 1992</a></p>
<p>They describe a method of forecasting age-specific mortality rates. Their method takes a series of age-specific mortality rates and writes it as a decomposition into an age specific average mortality, a vector of age specific changes in mortality and a period level vector of mortality trends.</p>
<p><span class="math display">\[ln(m_{x,t} = \alpha_x + \beta_x k_t +\epsilon_{x,t})\]</span></p>
<p>The solution to this equation comes from an eigenvalue decomposition of the <span class="math inline">\(m_{x,t}\)</span> matrix. Several additions and extensions of the method have been published over the years, and is an active area of research.</p>
<div class="sourceCode" id="cb36"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb36-1" data-line-number="1"><span class="kw">library</span>(demography)</a></code></pre></div>
<pre><code>## Loading required package: forecast</code></pre>
<pre><code>## Registered S3 method overwritten by &#39;quantmod&#39;:
##   method            from
##   as.zoo.data.frame zoo</code></pre>
<pre><code>## Registered S3 methods overwritten by &#39;demography&#39;:
##   method      from 
##   print.lca   e1071
##   summary.lca e1071</code></pre>
<pre><code>## This is demography 1.22</code></pre>
<div class="sourceCode" id="cb41"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb41-1" data-line-number="1"><span class="kw">library</span>(StMoMo)</a></code></pre></div>
<pre><code>## Loading required package: gnm</code></pre>
<div class="sourceCode" id="cb43"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb43-1" data-line-number="1"><span class="kw">library</span>(fds)</a></code></pre></div>
<pre><code>## Loading required package: rainbow</code></pre>
<pre><code>## Loading required package: MASS</code></pre>
<pre><code>## 
## Attaching package: &#39;MASS&#39;</code></pre>
<pre><code>## The following object is masked from &#39;package:dplyr&#39;:
## 
##     select</code></pre>
<pre><code>## Loading required package: pcaPP</code></pre>
<pre><code>## Loading required package: RCurl</code></pre>
<pre><code>## 
## Attaching package: &#39;RCurl&#39;</code></pre>
<pre><code>## The following object is masked from &#39;package:tidyr&#39;:
## 
##     complete</code></pre>
</div>
<div id="data" class="section level2">
<h2>data</h2>
<p>Again, get data from the HMD, here for the US <strong>Don’t use my password!!</strong></p>
<div class="sourceCode" id="cb52"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb52-1" data-line-number="1">usdat&lt;-<span class="kw">hmd.mx</span>(<span class="dt">country =</span> <span class="st">&quot;USA&quot;</span>, <span class="dt">username =</span> myusername, <span class="dt">password =</span> mypassword, <span class="dt">label=</span><span class="st">&quot;USA&quot;</span>)</a></code></pre></div>
<pre><code>## Warning in hmd.mx(country = &quot;USA&quot;, username = myusername, password =
## mypassword, : NAs introduced by coercion</code></pre>
<div class="sourceCode" id="cb54"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb54-1" data-line-number="1">usdat&lt;-<span class="kw">extract.years</span>(usdat, <span class="dt">years=</span><span class="dv">1940</span><span class="op">:</span><span class="dv">2017</span>)</a>
<a class="sourceLine" id="cb54-2" data-line-number="2"></a>
<a class="sourceLine" id="cb54-3" data-line-number="3"><span class="co">#male data</span></a>
<a class="sourceLine" id="cb54-4" data-line-number="4">usdatm&lt;-<span class="kw">demogdata</span>(<span class="dt">data=</span>usdat<span class="op">$</span>rate<span class="op">$</span>male,<span class="dt">pop=</span>usdat<span class="op">$</span>pop<span class="op">$</span>male,<span class="dt">ages =</span> usdat<span class="op">$</span>age,<span class="dt">years=</span>usdat<span class="op">$</span>year,<span class="dt">label =</span> usdat<span class="op">$</span>label,<span class="dt">name=</span><span class="st">&quot;male&quot;</span>, <span class="dt">type=</span><span class="st">&quot;mortality&quot;</span>)</a>
<a class="sourceLine" id="cb54-5" data-line-number="5"></a>
<a class="sourceLine" id="cb54-6" data-line-number="6"><span class="co">#Female data</span></a>
<a class="sourceLine" id="cb54-7" data-line-number="7">usdatf&lt;-<span class="kw">demogdata</span>(<span class="dt">data=</span>usdat<span class="op">$</span>rate<span class="op">$</span>female,<span class="dt">pop=</span>usdat<span class="op">$</span>pop<span class="op">$</span>female,<span class="dt">ages =</span> usdat<span class="op">$</span>age,<span class="dt">years=</span>usdat<span class="op">$</span>year,<span class="dt">label =</span> usdat<span class="op">$</span>label,<span class="dt">name=</span><span class="st">&quot;female&quot;</span>, <span class="dt">type=</span><span class="st">&quot;mortality&quot;</span>)</a>
<a class="sourceLine" id="cb54-8" data-line-number="8"></a>
<a class="sourceLine" id="cb54-9" data-line-number="9"><span class="kw">summary</span>(usdatf)</a></code></pre></div>
<pre><code>## Mortality data for USA
##     Series: female
##     Years: 1940 - 2017
##     Ages:  0 - 110</code></pre>
<div id="fit-lee---carter-model" class="section level3">
<h3>Fit Lee - Carter model</h3>
<p>I use the highest age as 90</p>
<div class="sourceCode" id="cb56"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb56-1" data-line-number="1"><span class="co">#males</span></a>
<a class="sourceLine" id="cb56-2" data-line-number="2">lca1&lt;-<span class="kw">lca</span>(usdatm, <span class="dt">max.age=</span><span class="dv">90</span>)</a>
<a class="sourceLine" id="cb56-3" data-line-number="3"><span class="kw">summary</span>(lca1)</a></code></pre></div>
<pre><code>## Lee-Carter analysis
## 
## Call: lca(data = usdatm, max.age = 90) 
## 
## Adjustment method: dt
## Region: USA
## Years in fit: 1940 - 2017
## Ages in fit: 0 - 90 
## 
## Percentage variation explained: 94.8%
## 
## ERROR MEASURES BASED ON MORTALITY RATES
## 
## Averages across ages:
##      ME     MSE     MPE    MAPE 
## 0.00008 0.00001 0.00889 0.07006 
## 
## Averages across years:
##      IE     ISE     IPE    IAPE 
## 0.00735 0.00044 0.79566 6.25470 
## 
## 
## ERROR MEASURES BASED ON LOG MORTALITY RATES
## 
## Averages across ages:
##       ME      MSE      MPE     MAPE 
##  0.00435  0.00918 -0.00074  0.01394 
## 
## Averages across years:
##       IE      ISE      IPE     IAPE 
##  0.39110  0.81147 -0.06745  1.23020</code></pre>
<div class="sourceCode" id="cb58"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb58-1" data-line-number="1"><span class="kw">plot</span>(lca1)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/Projection_files/figure-html/unnamed-chunk-15-1.png" /><!-- --></p>
<div class="sourceCode" id="cb59"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb59-1" data-line-number="1"><span class="kw">plot</span>(<span class="dt">x=</span>lca1<span class="op">$</span>age, <span class="dt">y=</span><span class="kw">log</span>(lca1<span class="op">$</span>male[,<span class="dv">1</span>]), <span class="dt">type=</span><span class="st">&quot;l&quot;</span>, <span class="dt">lty=</span><span class="dv">1</span>,</a>
<a class="sourceLine" id="cb59-2" data-line-number="2">     <span class="dt">main=</span><span class="st">&quot;Observed vs fitted Lee Carter Model - 1940 and 2017 Male Mortality&quot;</span>,</a>
<a class="sourceLine" id="cb59-3" data-line-number="3">     <span class="dt">ylim=</span><span class="kw">c</span>(<span class="op">-</span><span class="dv">10</span>, <span class="dv">0</span>))</a>
<a class="sourceLine" id="cb59-4" data-line-number="4"><span class="kw">lines</span>(lca1<span class="op">$</span>age, <span class="dt">y=</span>lca1<span class="op">$</span>fitted<span class="op">$</span>y[,<span class="dv">1</span>],</a>
<a class="sourceLine" id="cb59-5" data-line-number="5">      <span class="dt">col=</span><span class="dv">1</span>, <span class="dt">lty=</span><span class="dv">2</span>)</a>
<a class="sourceLine" id="cb59-6" data-line-number="6"></a>
<a class="sourceLine" id="cb59-7" data-line-number="7"><span class="kw">lines</span>(<span class="dt">x=</span>lca1<span class="op">$</span>age, <span class="dt">y=</span><span class="kw">log</span>(lca1<span class="op">$</span>male[,<span class="dv">78</span>]), <span class="dt">col=</span><span class="dv">3</span>)</a>
<a class="sourceLine" id="cb59-8" data-line-number="8"><span class="kw">lines</span>(lca1<span class="op">$</span>age, <span class="dt">y=</span>lca1<span class="op">$</span>fitted<span class="op">$</span>y[,<span class="dv">78</span>],</a>
<a class="sourceLine" id="cb59-9" data-line-number="9">      <span class="dt">col=</span><span class="dv">3</span>, <span class="dt">lty=</span><span class="dv">2</span>)</a>
<a class="sourceLine" id="cb59-10" data-line-number="10"><span class="kw">legend</span>(<span class="st">&quot;top&quot;</span>, <span class="dt">legend=</span><span class="kw">c</span>(<span class="st">&quot;Obs 1940&quot;</span>, <span class="st">&quot;Pred 1940&quot;</span>, <span class="st">&quot;Obs 2017&quot;</span>, <span class="st">&quot;Pred 2017&quot;</span>), <span class="dt">col=</span><span class="kw">c</span>(<span class="dv">1</span>,<span class="dv">1</span>,<span class="dv">3</span>,<span class="dv">3</span>), <span class="dt">lty=</span><span class="kw">c</span>(<span class="dv">1</span>,<span class="dv">2</span>,<span class="dv">1</span>,<span class="dv">2</span>))</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/Projection_files/figure-html/unnamed-chunk-15-2.png" /><!-- --></p>
<div class="sourceCode" id="cb60"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb60-1" data-line-number="1"><span class="co">#females</span></a>
<a class="sourceLine" id="cb60-2" data-line-number="2">lca2&lt;-<span class="kw">lca</span>(usdatf, <span class="dt">max.age=</span><span class="dv">90</span>)</a>
<a class="sourceLine" id="cb60-3" data-line-number="3"><span class="kw">summary</span>(lca2)</a></code></pre></div>
<pre><code>## Lee-Carter analysis
## 
## Call: lca(data = usdatf, max.age = 90) 
## 
## Adjustment method: dt
## Region: USA
## Years in fit: 1940 - 2017
## Ages in fit: 0 - 90 
## 
## Percentage variation explained: 96.1%
## 
## ERROR MEASURES BASED ON MORTALITY RATES
## 
## Averages across ages:
##      ME     MSE     MPE    MAPE 
## 0.00001 0.00000 0.00485 0.06322 
## 
## Averages across years:
##      IE     ISE     IPE    IAPE 
## 0.00076 0.00025 0.43738 5.65090 
## 
## 
## ERROR MEASURES BASED ON LOG MORTALITY RATES
## 
## Averages across ages:
##       ME      MSE      MPE     MAPE 
##  0.00080  0.00820 -0.00002  0.01114 
## 
## Averages across years:
##       IE      ISE      IPE     IAPE 
##  0.07162  0.73168 -0.00215  0.98433</code></pre>
<div class="sourceCode" id="cb62"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb62-1" data-line-number="1"><span class="kw">plot</span>(lca2)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/Projection_files/figure-html/unnamed-chunk-16-1.png" /><!-- --></p>
<div class="sourceCode" id="cb63"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb63-1" data-line-number="1"><span class="kw">plot</span>(<span class="dt">x=</span>lca2<span class="op">$</span>age, <span class="dt">y=</span><span class="kw">log</span>(lca2<span class="op">$</span>female[,<span class="dv">1</span>]), <span class="dt">type=</span><span class="st">&quot;l&quot;</span>, <span class="dt">lty=</span><span class="dv">1</span>,</a>
<a class="sourceLine" id="cb63-2" data-line-number="2">     <span class="dt">main=</span><span class="st">&quot;Observed vs fitted Lee Carter Model - 1940 and 2017 Female Mortality&quot;</span>,</a>
<a class="sourceLine" id="cb63-3" data-line-number="3">     <span class="dt">ylim=</span><span class="kw">c</span>(<span class="op">-</span><span class="dv">10</span>, <span class="dv">0</span>))</a>
<a class="sourceLine" id="cb63-4" data-line-number="4"><span class="kw">lines</span>(lca2<span class="op">$</span>age, <span class="dt">y=</span>lca2<span class="op">$</span>fitted<span class="op">$</span>y[,<span class="dv">1</span>],</a>
<a class="sourceLine" id="cb63-5" data-line-number="5">      <span class="dt">col=</span><span class="dv">1</span>, <span class="dt">lty=</span><span class="dv">2</span>)</a>
<a class="sourceLine" id="cb63-6" data-line-number="6"><span class="kw">lines</span>(<span class="dt">x=</span>lca2<span class="op">$</span>age, <span class="dt">y=</span><span class="kw">log</span>(lca2<span class="op">$</span>female[,<span class="dv">78</span>]), <span class="dt">col=</span><span class="dv">3</span>)</a>
<a class="sourceLine" id="cb63-7" data-line-number="7"><span class="kw">lines</span>(lca2<span class="op">$</span>age, <span class="dt">y=</span>lca2<span class="op">$</span>fitted<span class="op">$</span>y[,<span class="dv">78</span>],</a>
<a class="sourceLine" id="cb63-8" data-line-number="8">      <span class="dt">col=</span><span class="dv">3</span>, <span class="dt">lty=</span><span class="dv">2</span>)</a>
<a class="sourceLine" id="cb63-9" data-line-number="9"><span class="kw">legend</span>(<span class="st">&quot;top&quot;</span>, <span class="dt">legend=</span><span class="kw">c</span>(<span class="st">&quot;Obs 1940&quot;</span>, <span class="st">&quot;Pred 1940&quot;</span>, <span class="st">&quot;Obs 2017&quot;</span>, <span class="st">&quot;Pred 2017&quot;</span>), <span class="dt">col=</span><span class="kw">c</span>(<span class="dv">1</span>,<span class="dv">1</span>,<span class="dv">3</span>,<span class="dv">3</span>), <span class="dt">lty=</span><span class="kw">c</span>(<span class="dv">1</span>,<span class="dv">2</span>,<span class="dv">1</span>,<span class="dv">2</span>))</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/Projection_files/figure-html/unnamed-chunk-16-2.png" /><!-- --></p>
<div class="sourceCode" id="cb64"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb64-1" data-line-number="1"><span class="co">#lca1&lt;-fdm(usdatm, max.age=90, order = 3)</span></a>
<a class="sourceLine" id="cb64-2" data-line-number="2"><span class="co">#lca2&lt;-fdm(usdatf, max.age=90)</span></a></code></pre></div>
<div class="sourceCode" id="cb65"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb65-1" data-line-number="1"><span class="co">#par(mfrow=c(1,2))</span></a>
<a class="sourceLine" id="cb65-2" data-line-number="2"><span class="kw">plot</span>(usdatm,<span class="dt">years=</span><span class="dv">1940</span><span class="op">:</span><span class="dv">2017</span>,<span class="dt">ylim=</span><span class="kw">c</span>(<span class="op">-</span><span class="dv">11</span>,<span class="dv">1</span>), <span class="dt">ages =</span> <span class="dv">60</span><span class="op">:</span><span class="dv">90</span>, <span class="dt">main=</span><span class="st">&quot;Males - Ages 60 - 90&quot;</span>)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/Projection_files/figure-html/unnamed-chunk-17-1.png" /><!-- --></p>
<div class="sourceCode" id="cb66"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb66-1" data-line-number="1">out1&lt;-<span class="kw">forecast</span>(lca1,<span class="dt">h=</span><span class="dv">20</span>)</a>
<a class="sourceLine" id="cb66-2" data-line-number="2"></a>
<a class="sourceLine" id="cb66-3" data-line-number="3"><span class="kw">plot</span>(usdatf,<span class="dt">years=</span><span class="dv">1940</span><span class="op">:</span><span class="dv">2017</span>,<span class="dt">ylim=</span><span class="kw">c</span>(<span class="op">-</span><span class="dv">11</span>,<span class="dv">1</span>), <span class="dt">ages =</span> <span class="dv">60</span><span class="op">:</span><span class="dv">90</span>, <span class="dt">main=</span><span class="st">&quot;Females - Ages 60 - 90&quot;</span>)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/Projection_files/figure-html/unnamed-chunk-17-2.png" /><!-- --></p>
<div class="sourceCode" id="cb67"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb67-1" data-line-number="1">out2&lt;-<span class="kw">forecast</span>(lca2,<span class="dt">h=</span><span class="dv">20</span>)</a>
<a class="sourceLine" id="cb67-2" data-line-number="2"></a>
<a class="sourceLine" id="cb67-3" data-line-number="3"><span class="kw">par</span>(<span class="dt">mfrow=</span><span class="kw">c</span>(<span class="dv">2</span>,<span class="dv">1</span>))</a>
<a class="sourceLine" id="cb67-4" data-line-number="4"><span class="kw">plot</span>(<span class="kw">forecast</span>(lca1,<span class="dt">h=</span><span class="dv">20</span>,<span class="dt">jumpchoice=</span><span class="st">&quot;fit&quot;</span>),<span class="dt">ylim=</span><span class="kw">c</span>(<span class="op">-</span><span class="dv">11</span>,<span class="dv">1</span>) )</a>
<a class="sourceLine" id="cb67-5" data-line-number="5"><span class="kw">lines</span>(<span class="kw">log</span>(lca1<span class="op">$</span>male[, <span class="dv">1</span>]))</a>
<a class="sourceLine" id="cb67-6" data-line-number="6"></a>
<a class="sourceLine" id="cb67-7" data-line-number="7"><span class="kw">plot</span>(<span class="kw">forecast</span>(lca2,<span class="dt">h=</span><span class="dv">20</span>,<span class="dt">jumpchoice=</span><span class="st">&quot;fit&quot;</span>),<span class="dt">ylim=</span><span class="kw">c</span>(<span class="op">-</span><span class="dv">11</span>,<span class="dv">1</span>))</a>
<a class="sourceLine" id="cb67-8" data-line-number="8"><span class="kw">lines</span>(lca2<span class="op">$</span>female[, <span class="dv">1</span>])</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/Projection_files/figure-html/unnamed-chunk-17-3.png" /><!-- --></p>
<div class="sourceCode" id="cb68"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb68-1" data-line-number="1"><span class="kw">par</span>(<span class="dt">mfrow=</span><span class="kw">c</span>(<span class="dv">1</span>,<span class="dv">1</span>))</a></code></pre></div>
</div>
</div>
<div id="mechanics-of-lee---carter" class="section level1">
<h1>Mechanics of Lee - Carter</h1>
<p>I got this example largely from <a href="http://rstudio-pubs-static.s3.amazonaws.com/466062_fda07a1b42af4793b1f75e4b87435e3a.html">here</a>. It uses the <code>StMoMo</code> <a href="https://cran.r-project.org/web/packages/StMoMo/vignettes/StMoMoVignette.pdf">library</a>. You should also totally check out <a href="https://www.youtube.com/watch?v=prk-0G689GU">this video by the author of that package</a>, as to how great it is, also as to why it’s called StMoMo.</p>
<div class="sourceCode" id="cb69"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb69-1" data-line-number="1">usdat2&lt;-<span class="kw">hmd.mx</span>(<span class="dt">country =</span> <span class="st">&quot;USA&quot;</span>, <span class="dt">username =</span> myusername, <span class="dt">password =</span> mypassword, <span class="dt">label=</span><span class="st">&quot;USA&quot;</span>)</a></code></pre></div>
<pre><code>## Warning in hmd.mx(country = &quot;USA&quot;, username = myusername, password =
## mypassword, : NAs introduced by coercion</code></pre>
<div class="sourceCode" id="cb71"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb71-1" data-line-number="1">usdat2_m &lt;-<span class="st"> </span><span class="kw">StMoMoData</span>(usdat2,<span class="dt">series =</span> <span class="st">&quot;male&quot;</span>)</a>
<a class="sourceLine" id="cb71-2" data-line-number="2">usdat2_f &lt;-<span class="st"> </span><span class="kw">StMoMoData</span>(usdat2,<span class="dt">series =</span> <span class="st">&quot;female&quot;</span>)</a>
<a class="sourceLine" id="cb71-3" data-line-number="3"></a>
<a class="sourceLine" id="cb71-4" data-line-number="4"></a>
<a class="sourceLine" id="cb71-5" data-line-number="5">Years &lt;-<span class="st"> </span>usdat2<span class="op">$</span>year</a>
<a class="sourceLine" id="cb71-6" data-line-number="6">Age &lt;-<span class="st"> </span>usdat2<span class="op">$</span>age</a>
<a class="sourceLine" id="cb71-7" data-line-number="7"></a>
<a class="sourceLine" id="cb71-8" data-line-number="8">m &lt;-<span class="st"> </span>usdat2<span class="op">$</span>rate<span class="op">$</span>male</a>
<a class="sourceLine" id="cb71-9" data-line-number="9">m &lt;-<span class="st"> </span><span class="kw">log</span>(m)</a>
<a class="sourceLine" id="cb71-10" data-line-number="10">n.x =<span class="st"> </span><span class="kw">nrow</span>(m) <span class="co"># 111</span></a>
<a class="sourceLine" id="cb71-11" data-line-number="11">n.y =<span class="st"> </span><span class="kw">ncol</span>(m) <span class="co">#78</span></a>
<a class="sourceLine" id="cb71-12" data-line-number="12"></a>
<a class="sourceLine" id="cb71-13" data-line-number="13">m_mat &lt;-<span class="st"> </span><span class="kw">matrix</span>(m, <span class="dt">nrow =</span> n.x, <span class="dt">ncol =</span> n.y) <span class="co"># 111 X 85</span></a>
<a class="sourceLine" id="cb71-14" data-line-number="14"><span class="kw">plot</span>(<span class="dt">x=</span>Age, <span class="dt">y=</span>m_mat[,<span class="dv">1</span>], <span class="dt">type =</span> <span class="st">&quot;l&quot;</span>, <span class="dt">ylim=</span><span class="kw">c</span>(<span class="op">-</span><span class="dv">10</span>, <span class="dv">0</span>), <span class="dt">ylab=</span><span class="st">&quot;log rate&quot;</span>) <span class="co">#mortality of all ages in year 1940</span></a>
<a class="sourceLine" id="cb71-15" data-line-number="15"><span class="co"># Mean log mortality for all age groups under consideration (Age)</span></a>
<a class="sourceLine" id="cb71-16" data-line-number="16">a_age &lt;-<span class="st"> </span><span class="kw">rowMeans</span>(m_mat)</a>
<a class="sourceLine" id="cb71-17" data-line-number="17"><span class="kw">lines</span>(<span class="dt">x =</span> Age, <span class="dt">y =</span> a_age, <span class="dt">col =</span> <span class="dv">2</span>)<span class="co">#average mortality for all age groups</span></a>
<a class="sourceLine" id="cb71-18" data-line-number="18"><span class="kw">legend</span>(<span class="st">&quot;topleft&quot;</span>, <span class="dt">legend=</span><span class="kw">c</span>(<span class="st">&quot;1940 Mortality - M - Obs&quot;</span>,<span class="st">&quot;Average Mortality - Male&quot;</span>), <span class="dt">col=</span><span class="kw">c</span>(<span class="dv">1</span>,<span class="dv">2</span>), <span class="dt">lty=</span><span class="kw">c</span>(<span class="dv">1</span>,<span class="dv">1</span>))</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/Projection_files/figure-html/unnamed-chunk-18-1.png" /><!-- --></p>
<div class="sourceCode" id="cb72"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb72-1" data-line-number="1"><span class="co"># plotting mortality for Age = 60 as a trial run to see if code is working</span></a>
<a class="sourceLine" id="cb72-2" data-line-number="2"><span class="co"># as exxpected</span></a>
<a class="sourceLine" id="cb72-3" data-line-number="3"><span class="kw">plot</span>(<span class="dt">x =</span> Years, <span class="dt">y =</span> m_mat[<span class="dv">60</span>,], <span class="dt">pch =</span> <span class="dv">18</span>, <span class="dt">xlab =</span> <span class="st">&quot;Years&quot;</span>, <span class="dt">ylab =</span> <span class="st">&quot;log m&quot;</span>, <span class="dt">col =</span> <span class="dv">2</span>,</a>
<a class="sourceLine" id="cb72-4" data-line-number="4">     <span class="dt">main=</span><span class="st">&quot;Age 60 mortality over time&quot;</span>) <span class="co">#working!!!</span></a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/Projection_files/figure-html/unnamed-chunk-19-1.png" /><!-- --></p>
<div class="sourceCode" id="cb73"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb73-1" data-line-number="1"><span class="co"># LC with StMoMo-----</span></a>
<a class="sourceLine" id="cb73-2" data-line-number="2"><span class="co"># Extracting male and female dat from HMD, after creating a StMoMo data object</span></a>
<a class="sourceLine" id="cb73-3" data-line-number="3"><span class="co"># &quot;Male&quot; and &quot;Female&quot; data are assigned to different variables for easier</span></a>
<a class="sourceLine" id="cb73-4" data-line-number="4"><span class="co"># data wrangling.</span></a>
<a class="sourceLine" id="cb73-5" data-line-number="5"><span class="kw">library</span>(colorspace)</a>
<a class="sourceLine" id="cb73-6" data-line-number="6"><span class="kw">library</span>(gridExtra)</a></code></pre></div>
<pre><code>## 
## Attaching package: &#39;gridExtra&#39;</code></pre>
<pre><code>## The following object is masked from &#39;package:dplyr&#39;:
## 
##     combine</code></pre>
<div class="sourceCode" id="cb76"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb76-1" data-line-number="1"><span class="kw">library</span>(cowplot)</a></code></pre></div>
<pre><code>## 
## ********************************************************</code></pre>
<pre><code>## Note: As of version 1.0.0, cowplot does not change the</code></pre>
<pre><code>##   default ggplot2 theme anymore. To recover the previous</code></pre>
<pre><code>##   behavior, execute:
##   theme_set(theme_cowplot())</code></pre>
<pre><code>## ********************************************************</code></pre>
<div class="sourceCode" id="cb82"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb82-1" data-line-number="1"><span class="kw">library</span>(RColorBrewer)</a>
<a class="sourceLine" id="cb82-2" data-line-number="2"></a>
<a class="sourceLine" id="cb82-3" data-line-number="3"><span class="co">#Under a Binomial setting</span></a>
<a class="sourceLine" id="cb82-4" data-line-number="4"><span class="co">#Becasue I&#39;m opting to use the data with the Lee-Carter model under a Binomial setting</span></a>
<a class="sourceLine" id="cb82-5" data-line-number="5"><span class="co">#the exposures have to be converted to the initial exposures.</span></a>
<a class="sourceLine" id="cb82-6" data-line-number="6">LC1 &lt;-<span class="st"> </span><span class="kw">lc</span>(<span class="dt">link =</span> <span class="st">&quot;logit&quot;</span>)</a>
<a class="sourceLine" id="cb82-7" data-line-number="7">data_m &lt;-<span class="st"> </span><span class="kw">central2initial</span>(usdat2_m)</a>
<a class="sourceLine" id="cb82-8" data-line-number="8">data_f &lt;-<span class="st"> </span><span class="kw">central2initial</span>(usdat2_f)</a>
<a class="sourceLine" id="cb82-9" data-line-number="9">ages_fit &lt;-<span class="st"> </span><span class="dv">20</span><span class="op">:</span><span class="dv">60</span></a>
<a class="sourceLine" id="cb82-10" data-line-number="10"></a>
<a class="sourceLine" id="cb82-11" data-line-number="11"></a>
<a class="sourceLine" id="cb82-12" data-line-number="12"><span class="co">#This can be ussed ot generate a weight matrix over the ages and years in the data.</span></a>
<a class="sourceLine" id="cb82-13" data-line-number="13"><span class="co"># clips = 1 assigns 0 weights to the first and last cohorts.</span></a>
<a class="sourceLine" id="cb82-14" data-line-number="14">wxt_m &lt;-<span class="st"> </span><span class="kw">genWeightMat</span>(<span class="dt">ages =</span> ages_fit, <span class="dt">years =</span> data_m<span class="op">$</span>years,</a>
<a class="sourceLine" id="cb82-15" data-line-number="15">                     <span class="dt">clip =</span> <span class="dv">1</span>)</a>
<a class="sourceLine" id="cb82-16" data-line-number="16">wxt_f &lt;-<span class="st"> </span><span class="kw">genWeightMat</span>(<span class="dt">ages =</span> ages_fit, <span class="dt">years =</span> data_f<span class="op">$</span>years,</a>
<a class="sourceLine" id="cb82-17" data-line-number="17">                      <span class="dt">clip =</span> <span class="dv">1</span>)</a>
<a class="sourceLine" id="cb82-18" data-line-number="18"></a>
<a class="sourceLine" id="cb82-19" data-line-number="19"><span class="co">#For males</span></a>
<a class="sourceLine" id="cb82-20" data-line-number="20">LCfit_m &lt;-<span class="st"> </span><span class="kw">fit</span>(LC1, <span class="dt">data =</span> data_m, <span class="dt">ages.fit =</span> ages_fit, <span class="dt">wxt =</span> wxt_m)</a></code></pre></div>
<pre><code>## StMoMo: The following cohorts have been zero weigthed: 1873 1997 
## StMoMo: Start fitting with gnm
## Initialising
## Running start-up iterations..
## Running main iterations.........
## Done
## StMoMo: Finish fitting with gnm</code></pre>
<div class="sourceCode" id="cb84"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb84-1" data-line-number="1"><span class="co">#For females</span></a>
<a class="sourceLine" id="cb84-2" data-line-number="2">LCfit_f &lt;-<span class="st"> </span><span class="kw">fit</span>(LC1, <span class="dt">data =</span> data_f, <span class="dt">ages.fit =</span> ages_fit, <span class="dt">wxt =</span> wxt_f)</a></code></pre></div>
<pre><code>## StMoMo: The following cohorts have been zero weigthed: 1873 1997 
## StMoMo: Start fitting with gnm
## Initialising
## Running start-up iterations..
## Running main iterations........
## Done
## StMoMo: Finish fitting with gnm</code></pre>
<div class="sourceCode" id="cb86"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb86-1" data-line-number="1"><span class="co">#plotting parameters</span></a>
<a class="sourceLine" id="cb86-2" data-line-number="2"><span class="kw">par</span>(<span class="dt">mfrow =</span> <span class="kw">c</span>(<span class="dv">1</span>,<span class="dv">3</span>))</a>
<a class="sourceLine" id="cb86-3" data-line-number="3"><span class="kw">plot</span>(<span class="dt">x =</span> ages_fit, <span class="dt">y =</span> LCfit_m<span class="op">$</span>ax, <span class="dt">col =</span> <span class="dv">2</span>, <span class="dt">type =</span> <span class="st">&quot;l&quot;</span>, <span class="dt">ylim=</span><span class="kw">c</span>(<span class="op">-</span><span class="dv">10</span>, <span class="dv">-3</span>), <span class="dt">main=</span><span class="st">&quot;ax&quot;</span>)     <span class="co">#males</span></a>
<a class="sourceLine" id="cb86-4" data-line-number="4"><span class="kw">lines</span>(<span class="dt">x =</span> ages_fit, <span class="dt">y =</span> LCfit_f<span class="op">$</span>ax, <span class="dt">col =</span> <span class="dv">4</span>)     <span class="co">#females</span></a>
<a class="sourceLine" id="cb86-5" data-line-number="5"></a>
<a class="sourceLine" id="cb86-6" data-line-number="6"><span class="kw">plot</span>(<span class="dt">x =</span> ages_fit, <span class="dt">y =</span> LCfit_m<span class="op">$</span>bx, <span class="dt">col =</span> <span class="dv">2</span>, <span class="dt">type =</span> <span class="st">&quot;l&quot;</span>, <span class="dt">ylim=</span><span class="kw">c</span>(<span class="dv">0</span>, <span class="fl">.04</span>), <span class="dt">main=</span><span class="st">&quot;bx&quot;</span>)</a>
<a class="sourceLine" id="cb86-7" data-line-number="7"><span class="kw">lines</span>(<span class="dt">x =</span> ages_fit, <span class="dt">y =</span> LCfit_f<span class="op">$</span>bx, <span class="dt">col =</span> <span class="dv">4</span>)</a>
<a class="sourceLine" id="cb86-8" data-line-number="8"></a>
<a class="sourceLine" id="cb86-9" data-line-number="9"><span class="kw">plot</span>(<span class="dt">x =</span> usdat2_m<span class="op">$</span>years, <span class="dt">y =</span> LCfit_m<span class="op">$</span>kt[<span class="dv">1</span>,], <span class="dt">col =</span> <span class="dv">2</span>, <span class="dt">type =</span> <span class="st">&quot;l&quot;</span>, <span class="dt">ylim=</span><span class="kw">c</span>(<span class="op">-</span><span class="dv">25</span>,<span class="dv">40</span>), <span class="dt">main=</span><span class="st">&quot;kt&quot;</span>)</a>
<a class="sourceLine" id="cb86-10" data-line-number="10"><span class="kw">lines</span>(<span class="dt">x =</span> usdat2_m<span class="op">$</span>years, <span class="dt">y =</span> LCfit_f<span class="op">$</span>kt[<span class="dv">1</span>,], <span class="dt">col =</span> <span class="dv">4</span>)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/Projection_files/figure-html/unnamed-chunk-20-1.png" /><!-- --></p>
<div class="sourceCode" id="cb87"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb87-1" data-line-number="1"><span class="kw">par</span>(<span class="dt">mfrow =</span> <span class="kw">c</span>(<span class="dv">1</span>,<span class="dv">1</span>))</a></code></pre></div>
<div class="sourceCode" id="cb88"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb88-1" data-line-number="1"><span class="co">#Goodness-of-fit analysis-----</span></a>
<a class="sourceLine" id="cb88-2" data-line-number="2"><span class="co"># For males</span></a>
<a class="sourceLine" id="cb88-3" data-line-number="3">res_m &lt;-<span class="st"> </span><span class="kw">residuals</span>(LCfit_m)</a>
<a class="sourceLine" id="cb88-4" data-line-number="4">aic_ &lt;-<span class="st"> </span><span class="kw">AIC</span>(LCfit_m)</a>
<a class="sourceLine" id="cb88-5" data-line-number="5">bic_ &lt;-<span class="st"> </span><span class="kw">BIC</span>(LCfit_m)</a>
<a class="sourceLine" id="cb88-6" data-line-number="6">aic_</a></code></pre></div>
<pre><code>## [1] 194351.5</code></pre>
<div class="sourceCode" id="cb90"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb90-1" data-line-number="1">bic_</a></code></pre></div>
<pre><code>## [1] 195367.2</code></pre>
<div class="sourceCode" id="cb92"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb92-1" data-line-number="1"><span class="co">#For females</span></a>
<a class="sourceLine" id="cb92-2" data-line-number="2">res_f &lt;-<span class="st"> </span><span class="kw">residuals</span>(LCfit_f)</a>
<a class="sourceLine" id="cb92-3" data-line-number="3"></a>
<a class="sourceLine" id="cb92-4" data-line-number="4"><span class="co">#Plotting colour maps of males and females</span></a>
<a class="sourceLine" id="cb92-5" data-line-number="5">p1 &lt;-<span class="st"> </span><span class="kw">plot</span>(res_m, <span class="dt">type =</span> <span class="st">&quot;colourmap&quot;</span>, <span class="dt">main =</span> <span class="st">&quot;Residuals of Male data&quot;</span>)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/Projection_files/figure-html/unnamed-chunk-21-1.png" /><!-- --></p>
<div class="sourceCode" id="cb93"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb93-1" data-line-number="1">p2 &lt;-<span class="st"> </span><span class="kw">plot</span>(res_f, <span class="dt">type =</span> <span class="st">&quot;colourmap&quot;</span>, <span class="dt">main =</span> <span class="st">&quot;Residuals of Female data&quot;</span>)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/Projection_files/figure-html/unnamed-chunk-21-2.png" /><!-- --></p>
<div id="ok-actual-forecast" class="section level3">
<h3>Ok, actual forecast</h3>
<div class="sourceCode" id="cb94"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb94-1" data-line-number="1"><span class="co">#Forecasting----</span></a>
<a class="sourceLine" id="cb94-2" data-line-number="2">LCfore_m &lt;-<span class="st"> </span><span class="kw">forecast</span>(LCfit_m, <span class="dt">h =</span> <span class="dv">50</span>)</a>
<a class="sourceLine" id="cb94-3" data-line-number="3">LCfore_f &lt;-<span class="st"> </span><span class="kw">forecast</span>(LCfit_f, <span class="dt">h =</span> <span class="dv">50</span>) </a>
<a class="sourceLine" id="cb94-4" data-line-number="4"></a>
<a class="sourceLine" id="cb94-5" data-line-number="5"><span class="co">## Comparison of forcasting done in three instances:</span></a>
<a class="sourceLine" id="cb94-6" data-line-number="6"><span class="co"># a.) Forecasting kt with random walk using the forecast funciton.</span></a>
<a class="sourceLine" id="cb94-7" data-line-number="7"><span class="co"># b.) Forecast of kt done with SVD and first principles.</span></a>
<a class="sourceLine" id="cb94-8" data-line-number="8"><span class="co"># c.) Forecast of kf done with forecast and iarima.</span></a>
<a class="sourceLine" id="cb94-9" data-line-number="9"><span class="kw">par</span>(<span class="dt">mfrow=</span><span class="kw">c</span>(<span class="dv">1</span>,<span class="dv">2</span>))</a>
<a class="sourceLine" id="cb94-10" data-line-number="10"><span class="kw">plot</span>(<span class="dt">x =</span> LCfit_m<span class="op">$</span>years, <span class="dt">y =</span> LCfit_m<span class="op">$</span>kt[<span class="dv">1</span>,], <span class="dt">type =</span> <span class="st">&quot;l&quot;</span>,</a>
<a class="sourceLine" id="cb94-11" data-line-number="11">     <span class="dt">ylim=</span><span class="kw">c</span>(<span class="op">-</span><span class="dv">60</span>, <span class="dv">30</span>), <span class="dt">xlim=</span><span class="kw">c</span>(<span class="kw">min</span>(usdat2_m<span class="op">$</span>years),<span class="kw">max</span>(LCfore_m<span class="op">$</span>years)), </a>
<a class="sourceLine" id="cb94-12" data-line-number="12">     <span class="dt">main=</span><span class="st">&quot;kt for males - forecast&quot;</span>,</a>
<a class="sourceLine" id="cb94-13" data-line-number="13">      <span class="dt">ylab =</span> <span class="st">&quot;kt&quot;</span>)</a>
<a class="sourceLine" id="cb94-14" data-line-number="14"><span class="kw">lines</span>(<span class="dt">x =</span> LCfore_m<span class="op">$</span>years, <span class="dt">y =</span> LCfore_m<span class="op">$</span>kt.f<span class="op">$</span>mean, <span class="dt">col =</span> <span class="dv">2</span>)</a>
<a class="sourceLine" id="cb94-15" data-line-number="15"><span class="kw">lines</span>(<span class="dt">x =</span> LCfore_m<span class="op">$</span>years, <span class="dt">y =</span> LCfore_m<span class="op">$</span>kt.f<span class="op">$</span>upper[<span class="dv">1</span>,,<span class="dv">1</span>], <span class="dt">col =</span> <span class="dv">4</span>)</a>
<a class="sourceLine" id="cb94-16" data-line-number="16"><span class="kw">lines</span>(<span class="dt">x =</span> LCfore_m<span class="op">$</span>years, <span class="dt">y =</span> LCfore_m<span class="op">$</span>kt.f<span class="op">$</span>lower[<span class="dv">1</span>,,<span class="dv">1</span>], <span class="dt">col =</span> <span class="dv">4</span>)</a>
<a class="sourceLine" id="cb94-17" data-line-number="17"></a>
<a class="sourceLine" id="cb94-18" data-line-number="18"><span class="kw">plot</span>(<span class="dt">x =</span> LCfit_m<span class="op">$</span>years, <span class="dt">y =</span> LCfit_f<span class="op">$</span>kt[<span class="dv">1</span>,], <span class="dt">xlab =</span> <span class="st">&quot;Years&quot;</span>,<span class="dt">type =</span> <span class="st">&quot;l&quot;</span>,</a>
<a class="sourceLine" id="cb94-19" data-line-number="19">     <span class="dt">ylim=</span><span class="kw">c</span>(<span class="op">-</span><span class="dv">60</span>, <span class="dv">30</span>), <span class="dt">xlim=</span><span class="kw">c</span>(<span class="kw">min</span>(usdat2_m<span class="op">$</span>years),<span class="kw">max</span>(LCfore_m<span class="op">$</span>years)),</a>
<a class="sourceLine" id="cb94-20" data-line-number="20">     <span class="dt">main=</span><span class="st">&quot;kt for females - forecast&quot;</span>,</a>
<a class="sourceLine" id="cb94-21" data-line-number="21">     <span class="dt">ylab =</span> <span class="st">&quot;kt&quot;</span>)</a>
<a class="sourceLine" id="cb94-22" data-line-number="22"><span class="kw">lines</span>(<span class="dt">x =</span> LCfore_m<span class="op">$</span>years, <span class="dt">y =</span> LCfore_f<span class="op">$</span>kt.f<span class="op">$</span>mean, <span class="dt">col =</span> <span class="dv">2</span>)</a>
<a class="sourceLine" id="cb94-23" data-line-number="23"><span class="kw">lines</span>(<span class="dt">x =</span> LCfore_m<span class="op">$</span>years, <span class="dt">y =</span> LCfore_f<span class="op">$</span>kt.f<span class="op">$</span>upper[<span class="dv">1</span>,,<span class="dv">1</span>], <span class="dt">col =</span> <span class="dv">4</span>)</a>
<a class="sourceLine" id="cb94-24" data-line-number="24"><span class="kw">lines</span>(<span class="dt">x =</span> LCfore_m<span class="op">$</span>years, <span class="dt">y =</span> LCfore_f<span class="op">$</span>kt.f<span class="op">$</span>lower[<span class="dv">1</span>,,<span class="dv">1</span>], <span class="dt">col =</span> <span class="dv">4</span>)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/Projection_files/figure-html/unnamed-chunk-22-1.png" /><!-- --></p>
<div class="sourceCode" id="cb95"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb95-1" data-line-number="1">LCfit &lt;-<span class="st"> </span><span class="kw">fit</span>(<span class="kw">lc</span>(<span class="dt">link =</span> <span class="st">&quot;logit&quot;</span>), <span class="dt">data =</span> usdat2_m, <span class="dt">ages.fit =</span> <span class="dv">30</span><span class="op">:</span><span class="dv">80</span>)</a></code></pre></div>
<pre><code>## Warning in fit.StMoMo(lc(link = &quot;logit&quot;), data = usdat2_m, ages.fit = 30:80): logit-Binomial model fitted to central exposure data</code></pre>
<pre><code>## StMoMo: Start fitting with gnm
## Initialising
## Running start-up iterations..
## Running main iterations.........
## Done
## StMoMo: Finish fitting with gnm</code></pre>
<div class="sourceCode" id="cb98"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb98-1" data-line-number="1">LCfor&lt;-<span class="kw">forecast</span>(LCfit, <span class="dt">h=</span><span class="dv">50</span>)</a>
<a class="sourceLine" id="cb98-2" data-line-number="2"><span class="kw">plot</span>(LCfor)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/Projection_files/figure-html/unnamed-chunk-23-1.png" /><!-- --></p>
<p>#More simulations</p>
<div class="sourceCode" id="cb99"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb99-1" data-line-number="1">LCsim.mrwd &lt;-<span class="st"> </span><span class="kw">simulate</span>(LCfit, <span class="dt">nsim =</span> <span class="dv">100</span>)</a>
<a class="sourceLine" id="cb99-2" data-line-number="2">LCsim.iarima &lt;-<span class="st"> </span><span class="kw">simulate</span>(LCfit, <span class="dt">nsim =</span> <span class="dv">100</span>, <span class="dt">kt.method =</span> <span class="st">&quot;iarima&quot;</span>)</a>
<a class="sourceLine" id="cb99-3" data-line-number="3"></a>
<a class="sourceLine" id="cb99-4" data-line-number="4"></a>
<a class="sourceLine" id="cb99-5" data-line-number="5"><span class="kw">par</span>(<span class="dt">mfrow=</span><span class="kw">c</span>(<span class="dv">2</span>, <span class="dv">2</span>))</a>
<a class="sourceLine" id="cb99-6" data-line-number="6"><span class="kw">plot</span>(LCfit<span class="op">$</span>years, LCfit<span class="op">$</span>kt[<span class="dv">1</span>, ], <span class="dt">xlim =</span> <span class="kw">range</span>(LCfit<span class="op">$</span>years, LCsim.mrwd<span class="op">$</span>kt.s<span class="op">$</span>years),</a>
<a class="sourceLine" id="cb99-7" data-line-number="7">     <span class="dt">ylim =</span> <span class="kw">range</span>(LCfit<span class="op">$</span>kt, LCsim.mrwd<span class="op">$</span>kt.s<span class="op">$</span>sim), <span class="dt">type =</span> <span class="st">&quot;l&quot;</span>, </a>
<a class="sourceLine" id="cb99-8" data-line-number="8">     <span class="dt">xlab =</span> <span class="st">&quot;year&quot;</span>, <span class="dt">ylab =</span> <span class="st">&quot;kt&quot;</span>, </a>
<a class="sourceLine" id="cb99-9" data-line-number="9">     <span class="dt">main =</span> <span class="st">&quot;Lee-Carter: Simulated paths of the period index kt (mrwd)&quot;</span>)</a>
<a class="sourceLine" id="cb99-10" data-line-number="10"><span class="kw">matlines</span>(LCsim.mrwd<span class="op">$</span>kt.s<span class="op">$</span>years, LCsim.mrwd<span class="op">$</span>kt.s<span class="op">$</span>sim[<span class="dv">1</span>, , ], <span class="dt">type =</span> <span class="st">&quot;l&quot;</span>, <span class="dt">lty =</span> <span class="dv">1</span>)</a>
<a class="sourceLine" id="cb99-11" data-line-number="11"></a>
<a class="sourceLine" id="cb99-12" data-line-number="12"><span class="kw">plot</span>(LCfit<span class="op">$</span>years, (LCfit<span class="op">$</span>Dxt <span class="op">/</span><span class="st"> </span>LCfit<span class="op">$</span>Ext)[<span class="st">&quot;65&quot;</span>, ], </a>
<a class="sourceLine" id="cb99-13" data-line-number="13">     <span class="dt">xlim =</span> <span class="kw">range</span>(LCfit<span class="op">$</span>years, LCsim.mrwd<span class="op">$</span>years),</a>
<a class="sourceLine" id="cb99-14" data-line-number="14">     <span class="dt">ylim =</span> <span class="kw">range</span>((LCfit<span class="op">$</span>Dxt <span class="op">/</span><span class="st"> </span>LCfit<span class="op">$</span>Ext)[<span class="st">&quot;65&quot;</span>, ], LCsim.mrwd<span class="op">$</span>rates[<span class="st">&quot;65&quot;</span>, , ]), </a>
<a class="sourceLine" id="cb99-15" data-line-number="15">     <span class="dt">type =</span> <span class="st">&quot;l&quot;</span>, <span class="dt">xlab =</span> <span class="st">&quot;year&quot;</span>, <span class="dt">ylab =</span> <span class="st">&quot;rate&quot;</span>, </a>
<a class="sourceLine" id="cb99-16" data-line-number="16">     <span class="dt">main =</span> <span class="st">&quot;Lee-Carter: Simulated mortality rates at age 65&quot;</span>)</a>
<a class="sourceLine" id="cb99-17" data-line-number="17"><span class="kw">matlines</span>(LCsim.mrwd<span class="op">$</span>years, LCsim.mrwd<span class="op">$</span>rates[<span class="st">&quot;65&quot;</span>, , ], <span class="dt">type =</span> <span class="st">&quot;l&quot;</span>, <span class="dt">lty =</span> <span class="dv">1</span>)</a>
<a class="sourceLine" id="cb99-18" data-line-number="18"></a>
<a class="sourceLine" id="cb99-19" data-line-number="19"><span class="kw">plot</span>(LCfit<span class="op">$</span>years, LCfit<span class="op">$</span>kt[<span class="dv">1</span>, ], <span class="dt">xlim =</span> <span class="kw">range</span>(LCfit<span class="op">$</span>years, LCsim.iarima<span class="op">$</span>kt.s<span class="op">$</span>years),</a>
<a class="sourceLine" id="cb99-20" data-line-number="20">     <span class="dt">ylim =</span> <span class="kw">range</span>(LCfit<span class="op">$</span>kt, LCsim.iarima<span class="op">$</span>kt.s<span class="op">$</span>sim), <span class="dt">type =</span> <span class="st">&quot;l&quot;</span>, </a>
<a class="sourceLine" id="cb99-21" data-line-number="21">     <span class="dt">xlab =</span> <span class="st">&quot;year&quot;</span>, <span class="dt">ylab =</span> <span class="st">&quot;kt&quot;</span>, </a>
<a class="sourceLine" id="cb99-22" data-line-number="22">     <span class="dt">main =</span> <span class="st">&quot;Lee-Carter: Simulated paths of the period index kt (ARIMA(0,1,0))&quot;</span>)</a>
<a class="sourceLine" id="cb99-23" data-line-number="23"><span class="kw">matlines</span>(LCsim.iarima<span class="op">$</span>kt.s<span class="op">$</span>years, LCsim.iarima<span class="op">$</span>kt.s<span class="op">$</span>sim[<span class="dv">1</span>, , ], <span class="dt">type =</span> <span class="st">&quot;l&quot;</span>, <span class="dt">lty =</span> <span class="dv">1</span>)</a>
<a class="sourceLine" id="cb99-24" data-line-number="24"></a>
<a class="sourceLine" id="cb99-25" data-line-number="25"><span class="kw">plot</span>(LCfit<span class="op">$</span>years, (LCfit<span class="op">$</span>Dxt <span class="op">/</span><span class="st"> </span>LCfit<span class="op">$</span>Ext)[<span class="st">&quot;65&quot;</span>, ], </a>
<a class="sourceLine" id="cb99-26" data-line-number="26">     <span class="dt">xlim =</span> <span class="kw">range</span>(LCfit<span class="op">$</span>years, LCsim.iarima<span class="op">$</span>years),</a>
<a class="sourceLine" id="cb99-27" data-line-number="27">     <span class="dt">ylim =</span> <span class="kw">range</span>((LCfit<span class="op">$</span>Dxt <span class="op">/</span><span class="st"> </span>LCfit<span class="op">$</span>Ext)[<span class="st">&quot;65&quot;</span>, ], LCsim.iarima<span class="op">$</span>rates[<span class="st">&quot;65&quot;</span>, , ]), </a>
<a class="sourceLine" id="cb99-28" data-line-number="28">     <span class="dt">type =</span> <span class="st">&quot;l&quot;</span>, <span class="dt">xlab =</span> <span class="st">&quot;year&quot;</span>, <span class="dt">ylab =</span> <span class="st">&quot;rate&quot;</span>, </a>
<a class="sourceLine" id="cb99-29" data-line-number="29">     <span class="dt">main =</span> <span class="st">&quot;Lee-Carter: Simulated mortality rates at age 65 (ARIMA(0,1,0))&quot;</span>)</a>
<a class="sourceLine" id="cb99-30" data-line-number="30"><span class="kw">matlines</span>(LCsim.iarima<span class="op">$</span>years, LCsim.iarima<span class="op">$</span>rates[<span class="st">&quot;65&quot;</span>, , ], <span class="dt">type =</span> <span class="st">&quot;l&quot;</span>, <span class="dt">lty =</span> <span class="dv">1</span>)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/Projection_files/figure-html/unnamed-chunk-24-1.png" /><!-- --></p>
</div>
</div>
</section>

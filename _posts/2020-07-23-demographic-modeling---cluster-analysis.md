---
title: "Demographic Modeling - Cluster analysis"

author: "Corey Sparks, Ph.D."
date: "July 23, 2020"
layout: post
---


<section class="main-content">
<p>In this topic, we will discuss <strong>Unsupervised Learning</strong>, or as we talked about <a href="http://rpubs.com/corey_sparks/536994">last time</a>, the situation where you are looking for groups in your data when your data don’t come with a group variable. I.e. sometimes you want to find groups of similar observations, and you need a statistical tool for doing this.</p>
<p>In statistics, this is called <strong>Cluster analysis</strong>, another case of the machine learning people inventing a new word for something and taking credit for a type of analysis that’s been around for fifty years.</p>
<div id="cluster-analysis" class="section level3">
<h3>Cluster analysis</h3>
<ul>
<li>Attempts to find sub-groups within a data set</li>
<li>Observations within a particular sub-gruop are statistically more similar to other members of their sub-group than to members of another sub-group</li>
<li>Many ways in which to do this:
<ul>
<li>K-means/K-medioids</li>
<li>Hierarchical clustering</li>
<li>Model based clustering</li>
<li>Latent class analysis</li>
</ul></li>
<li>All of these methods use observed data to measure the dissimilarity between observations, then create groups, or clusters (buckets) from these observations.</li>
</ul>
</div>
<div id="metrics-of-similiarity" class="section level3">
<h3>Metrics of similiarity</h3>
<ul>
<li>Distance based</li>
<li>Euclidean distances between two observations, <em>i</em> and <em>j</em> is</li>
</ul>
<p><span class="math display">\[d(x_i,x_j) = \sqrt{(x_i-x_j)&#39;(x_i-x_j)}\]</span></p>
<p>Where the <em>x’s</em> are the variables measured on the two observations, for instance, if we have 3 x variables for two observations, then the distance between them is:</p>
<div class="sourceCode" id="cb1"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb1-1" data-line-number="1">x1&lt;-<span class="kw">c</span>(<span class="dv">1</span>,<span class="dv">5</span>, <span class="dv">1</span>)</a>
<a class="sourceLine" id="cb1-2" data-line-number="2">x2&lt;-<span class="kw">c</span>(<span class="dv">5</span>, <span class="dv">1</span>, <span class="dv">2</span>)</a>
<a class="sourceLine" id="cb1-3" data-line-number="3"></a>
<a class="sourceLine" id="cb1-4" data-line-number="4"><span class="kw">dist</span>( <span class="kw">rbind</span>(x1, x2), <span class="dt">method =</span> <span class="st">&quot;euclidean&quot;</span>)</a></code></pre></div>
<pre><code>##          x1
## x2 5.744563</code></pre>
<p>If the two observations are more similar, the distance is smaller:</p>
<div class="sourceCode" id="cb3"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb3-1" data-line-number="1">x1&lt;-<span class="kw">c</span>(<span class="dv">1</span>,<span class="dv">5</span>, <span class="dv">1</span>)</a>
<a class="sourceLine" id="cb3-2" data-line-number="2">x2&lt;-<span class="kw">c</span>(<span class="dv">1</span>, <span class="dv">2</span>, <span class="dv">2</span>)</a>
<a class="sourceLine" id="cb3-3" data-line-number="3">x3&lt;-<span class="kw">c</span>(<span class="dv">8</span>,<span class="dv">7</span>,<span class="dv">10</span>)</a>
<a class="sourceLine" id="cb3-4" data-line-number="4"></a>
<a class="sourceLine" id="cb3-5" data-line-number="5"><span class="kw">dist</span>( <span class="kw">rbind</span>(x1, x2, x3), <span class="dt">method =</span> <span class="st">&quot;euclidean&quot;</span>)</a></code></pre></div>
<pre><code>##           x1        x2
## x2  3.162278          
## x3 11.575837 11.747340</code></pre>
<p>and vice versa.</p>
<div class="sourceCode" id="cb5"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb5-1" data-line-number="1"><span class="kw">library</span>(readr)</a>
<a class="sourceLine" id="cb5-2" data-line-number="2">prb&lt;-<span class="kw">read_csv</span>(<span class="dt">file =</span> <span class="st">&quot;https://raw.githubusercontent.com/coreysparks/data/master/PRB2008_All.csv&quot;</span>, <span class="dt">col_types =</span> <span class="kw">read_rds</span>(<span class="kw">url</span>(<span class="st">&quot;https://raw.githubusercontent.com/coreysparks/r_courses/master/prbspec.rds&quot;</span>)))</a>
<a class="sourceLine" id="cb5-3" data-line-number="3"><span class="kw">names</span>(prb)&lt;-<span class="kw">tolower</span>(<span class="kw">names</span>(prb))</a>
<a class="sourceLine" id="cb5-4" data-line-number="4"></a>
<a class="sourceLine" id="cb5-5" data-line-number="5"><span class="kw">library</span>(dplyr)</a></code></pre></div>
<pre><code>## 
## Attaching package: &#39;dplyr&#39;</code></pre>
<pre><code>## The following objects are masked from &#39;package:stats&#39;:
## 
##     filter, lag</code></pre>
<pre><code>## The following objects are masked from &#39;package:base&#39;:
## 
##     intersect, setdiff, setequal, union</code></pre>
<div class="sourceCode" id="cb9"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb9-1" data-line-number="1">prb&lt;-prb<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb9-2" data-line-number="2"><span class="st"> </span><span class="co"># mutate(africa=ifelse(continent==&quot;Africa&quot;, 1, 0))%&gt;%</span></a>
<a class="sourceLine" id="cb9-3" data-line-number="3"><span class="st">  </span><span class="kw">filter</span>(<span class="kw">complete.cases</span>(imr, tfr, percpoplt15, e0total, percurban, percmarwomcontramodern))<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb9-4" data-line-number="4"><span class="st">  </span><span class="kw">select</span>(imr, tfr, percpoplt15, e0total, percurban, percmarwomcontramodern)</a></code></pre></div>
<div class="sourceCode" id="cb10"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb10-1" data-line-number="1">knitr<span class="op">::</span><span class="kw">kable</span>(<span class="kw">head</span>(prb))</a></code></pre></div>
<table>
<thead>
<tr class="header">
<th align="right">imr</th>
<th align="right">tfr</th>
<th align="right">percpoplt15</th>
<th align="right">e0total</th>
<th align="right">percurban</th>
<th align="right">percmarwomcontramodern</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="right">163.0</td>
<td align="right">6.8</td>
<td align="right">45</td>
<td align="right">43</td>
<td align="right">20</td>
<td align="right">9</td>
</tr>
<tr class="even">
<td align="right">8.0</td>
<td align="right">1.6</td>
<td align="right">27</td>
<td align="right">75</td>
<td align="right">45</td>
<td align="right">8</td>
</tr>
<tr class="odd">
<td align="right">27.0</td>
<td align="right">2.3</td>
<td align="right">30</td>
<td align="right">72</td>
<td align="right">63</td>
<td align="right">52</td>
</tr>
<tr class="even">
<td align="right">132.0</td>
<td align="right">6.8</td>
<td align="right">46</td>
<td align="right">43</td>
<td align="right">57</td>
<td align="right">5</td>
</tr>
<tr class="odd">
<td align="right">26.0</td>
<td align="right">1.7</td>
<td align="right">21</td>
<td align="right">71</td>
<td align="right">64</td>
<td align="right">20</td>
</tr>
<tr class="even">
<td align="right">4.7</td>
<td align="right">1.9</td>
<td align="right">19</td>
<td align="right">81</td>
<td align="right">87</td>
<td align="right">75</td>
</tr>
</tbody>
</table>
</div>
<div id="create-data-partition" class="section level3">
<h3>Create data partition</h3>
<p>Here we use 80% of the data to train our simple model</p>
<div class="sourceCode" id="cb11"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb11-1" data-line-number="1"><span class="kw">library</span>(caret)</a></code></pre></div>
<pre><code>## Loading required package: lattice</code></pre>
<pre><code>## Loading required package: ggplot2</code></pre>
<div class="sourceCode" id="cb14"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb14-1" data-line-number="1"><span class="kw">set.seed</span>(<span class="dv">1115</span>)</a>
<a class="sourceLine" id="cb14-2" data-line-number="2">train&lt;-<span class="st"> </span><span class="kw">createDataPartition</span>(<span class="dt">y =</span> prb<span class="op">$</span>imr , <span class="dt">p =</span> <span class="fl">.80</span>, <span class="dt">list=</span>F)</a>
<a class="sourceLine" id="cb14-3" data-line-number="3"></a>
<a class="sourceLine" id="cb14-4" data-line-number="4">prbtrain&lt;-prb[train,]</a></code></pre></div>
<pre><code>## Warning: The `i` argument of ``[`()` can&#39;t be a matrix as of tibble 3.0.0.
## Convert to a vector.
## This warning is displayed once every 8 hours.
## Call `lifecycle::last_warnings()` to see where this warning was generated.</code></pre>
<div class="sourceCode" id="cb16"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb16-1" data-line-number="1">prbtest&lt;-prb[<span class="op">-</span>train,]</a></code></pre></div>
</div>
<div id="hierarchical-clustering" class="section level2">
<h2>Hierarchical clustering</h2>
<p>First we form our matrix of distances between all the countries on our observed variables:</p>
<div class="sourceCode" id="cb17"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb17-1" data-line-number="1">dmat&lt;-<span class="kw">dist</span>(prbtrain, <span class="dt">method=</span><span class="st">&quot;euclidean&quot;</span>)</a></code></pre></div>
<p>Then we run a hierarhical clustering algorithm on the matrix. There are lots of different ways to do this, we will just use the simplest method, the single-linkage, or nearest neighbor approach. This works by first sorting the distances from smallest to largest, then making clusters from the smallest distance pair.</p>
<p>Once this is done, this pair is merged into a cluster, their distance is then compared to the remaining observations, so on and so on, until you have a set of clusters for every observation.</p>
<p>The original way to plot these analyses is by a <strong>dendrogram</strong>, or tree plot.</p>
<div class="sourceCode" id="cb18"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb18-1" data-line-number="1">hc1&lt;-<span class="kw">hclust</span>(<span class="dt">d=</span> dmat, <span class="dt">method=</span><span class="st">&quot;single&quot;</span>)</a>
<a class="sourceLine" id="cb18-2" data-line-number="2"><span class="kw">plot</span>(hc1, <span class="dt">hang=</span><span class="op">-</span><span class="dv">1</span>, <span class="dt">main=</span><span class="st">&quot;Single linkage cluster analysis of PRB data&quot;</span>)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/Clustering_files/figure-html/unnamed-chunk-7-1.png" /><!-- --></p>
<div class="sourceCode" id="cb19"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb19-1" data-line-number="1"><span class="kw">library</span>(scorecard)</a>
<a class="sourceLine" id="cb19-2" data-line-number="2"><span class="kw">library</span>(factoextra)</a></code></pre></div>
<pre><code>## Welcome! Want to learn more? See two factoextra-related books at https://goo.gl/ve3WBa</code></pre>
<div class="sourceCode" id="cb21"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb21-1" data-line-number="1"><span class="kw">library</span>(class)</a>
<a class="sourceLine" id="cb21-2" data-line-number="2"><span class="kw">library</span>(RColorBrewer)</a>
<a class="sourceLine" id="cb21-3" data-line-number="3"></a>
<a class="sourceLine" id="cb21-4" data-line-number="4"><span class="kw">fviz_dend</span>(hc1, <span class="dt">k=</span><span class="dv">5</span>, <span class="dt">k_colors =</span><span class="kw">brewer.pal</span>(<span class="dt">n=</span><span class="dv">5</span>, <span class="dt">name=</span><span class="st">&quot;Accent&quot;</span>),</a>
<a class="sourceLine" id="cb21-5" data-line-number="5">          <span class="dt">color_labels_by_k =</span> <span class="ot">TRUE</span>, <span class="dt">ggtheme =</span> <span class="kw">theme_minimal</span>())</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/Clustering_files/figure-html/unnamed-chunk-7-2.png" /><!-- --></p>
<div class="sourceCode" id="cb22"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb22-1" data-line-number="1">groups&lt;-<span class="kw">cutree</span>(hc1, <span class="dt">k=</span><span class="dv">5</span>)</a>
<a class="sourceLine" id="cb22-2" data-line-number="2"><span class="kw">table</span>(groups)</a></code></pre></div>
<pre><code>## groups
##   1   2   3   4   5 
##   2 130   1   1   1</code></pre>
<p>So this is silly becuase the method round 3 cluster that only had one observation. This is a weakness of the single linkage method, instead we use another method. Ward’s method is typically seen as a better alternative because it tends to find clusters of similar size.</p>
<div class="sourceCode" id="cb24"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb24-1" data-line-number="1">hc2&lt;-<span class="kw">hclust</span>(<span class="dt">d=</span> dmat, <span class="dt">method=</span><span class="st">&quot;ward.D&quot;</span>)</a>
<a class="sourceLine" id="cb24-2" data-line-number="2"><span class="kw">plot</span>(hc2, <span class="dt">hang=</span><span class="op">-</span><span class="dv">1</span>, <span class="dt">main=</span><span class="st">&quot;Ward&#39;s cluster analysis of PRB data&quot;</span>)</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/Clustering_files/figure-html/unnamed-chunk-8-1.png" /><!-- --></p>
<div class="sourceCode" id="cb25"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb25-1" data-line-number="1"><span class="kw">fviz_dend</span>(hc2, <span class="dt">k=</span><span class="dv">5</span>, <span class="dt">k_colors =</span> <span class="kw">brewer.pal</span>(<span class="dt">n=</span><span class="dv">5</span>, <span class="dt">name=</span><span class="st">&quot;Accent&quot;</span>),</a>
<a class="sourceLine" id="cb25-2" data-line-number="2">          <span class="dt">color_labels_by_k =</span> <span class="ot">TRUE</span>, <span class="dt">ggtheme =</span> <span class="kw">theme_minimal</span>())</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/Clustering_files/figure-html/unnamed-chunk-8-2.png" /><!-- --></p>
<div class="sourceCode" id="cb26"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb26-1" data-line-number="1">groups&lt;-<span class="kw">cutree</span>(hc2, <span class="dt">k=</span><span class="dv">5</span>)</a>
<a class="sourceLine" id="cb26-2" data-line-number="2"><span class="kw">table</span>(groups)</a></code></pre></div>
<pre><code>## groups
##  1  2  3  4  5 
## 13 38 43 12 29</code></pre>
<div class="sourceCode" id="cb28"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb28-1" data-line-number="1">prbtrain<span class="op">$</span>group1&lt;-<span class="kw">factor</span>(<span class="kw">cutree</span>(hc2, <span class="dt">k=</span><span class="dv">5</span>))</a>
<a class="sourceLine" id="cb28-2" data-line-number="2"></a>
<a class="sourceLine" id="cb28-3" data-line-number="3">prbtrain<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb28-4" data-line-number="4"><span class="st">  </span><span class="kw">ggplot</span>(<span class="kw">aes</span>(<span class="dt">x=</span>imr, <span class="dt">y=</span>tfr, <span class="dt">pch=</span>group1,<span class="dt">color=</span>group1, <span class="dt">cex=</span><span class="dv">3</span>))<span class="op">+</span><span class="kw">geom_point</span>()</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/Clustering_files/figure-html/unnamed-chunk-9-1.png" /><!-- --></p>
</div>
<div id="k-means" class="section level2">
<h2>K-means</h2>
<ul>
<li>Another type of cluster finder</li>
<li>Will always find a given number of <em>k</em> clusters.</li>
<li>Ideally we can minimize a within cluster variance measure to find the optimal number</li>
</ul>
<div class="sourceCode" id="cb29"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb29-1" data-line-number="1">prbtrain&lt;-prb[train,]</a>
<a class="sourceLine" id="cb29-2" data-line-number="2"></a>
<a class="sourceLine" id="cb29-3" data-line-number="3">km&lt;-<span class="kw">kmeans</span>(<span class="dt">x =</span> prbtrain, <span class="dt">center =</span> <span class="dv">3</span>, <span class="dt">nstart =</span> <span class="dv">10</span>)</a>
<a class="sourceLine" id="cb29-4" data-line-number="4">km</a></code></pre></div>
<pre><code>## K-means clustering with 3 clusters of sizes 41, 56, 38
## 
## Cluster means:
##        imr      tfr percpoplt15  e0total percurban percmarwomcontramodern
## 1 88.31707 5.263415    42.70732 54.39024  35.39024               15.12195
## 2 11.75536 2.035714    22.50000 75.23214  75.03571               53.71429
## 3 31.74737 2.915789    32.34211 66.92105  38.28947               41.86842
## 
## Clustering vector:
##   [1] 1 3 1 2 2 3 3 2 2 3 1 3 3 3 2 1 1 1 2 1 2 2 1 1 2 2 2 1 2 3 2 1 2 1 3 3 2
##  [38] 1 3 1 3 1 1 3 3 3 3 2 1 2 2 2 3 1 3 2 2 2 3 2 2 1 2 2 1 3 1 2 1 3 2 3 3 1
##  [75] 3 2 3 2 2 2 1 1 2 1 2 1 3 2 2 2 2 2 2 2 1 3 1 2 1 2 1 2 2 2 3 1 2 3 1 2 1
## [112] 3 1 1 1 1 3 3 2 2 1 3 1 2 2 2 2 2 3 3 3 1 3 3 2
## 
## Within cluster sum of squares by cluster:
## [1] 44040.58 38082.11 30145.52
##  (between_SS / total_SS =  68.8 %)
## 
## Available components:
## 
## [1] &quot;cluster&quot;      &quot;centers&quot;      &quot;totss&quot;        &quot;withinss&quot;     &quot;tot.withinss&quot;
## [6] &quot;betweenss&quot;    &quot;size&quot;         &quot;iter&quot;         &quot;ifault&quot;</code></pre>
<div class="sourceCode" id="cb31"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb31-1" data-line-number="1"><span class="kw">library</span>(ClusterR)</a></code></pre></div>
<pre><code>## Loading required package: gtools</code></pre>
<div class="sourceCode" id="cb33"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb33-1" data-line-number="1">km2&lt;-<span class="kw">KMeans_rcpp</span>(<span class="dt">data=</span>prbtrain, <span class="dt">cluster=</span><span class="dv">3</span>, <span class="dt">num_init =</span> <span class="dv">10</span>)</a>
<a class="sourceLine" id="cb33-2" data-line-number="2"></a>
<a class="sourceLine" id="cb33-3" data-line-number="3">prbtrain<span class="op">$</span>cluster&lt;-<span class="kw">as.factor</span>(km2<span class="op">$</span>cluster)</a>
<a class="sourceLine" id="cb33-4" data-line-number="4"></a>
<a class="sourceLine" id="cb33-5" data-line-number="5"></a>
<a class="sourceLine" id="cb33-6" data-line-number="6">prbtrain<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb33-7" data-line-number="7"><span class="st">  </span><span class="kw">ggplot</span>(<span class="kw">aes</span>(<span class="dt">x=</span>imr, <span class="dt">y=</span>tfr, <span class="dt">group=</span>cluster, <span class="dt">color=</span>cluster, <span class="dt">cex=</span><span class="dv">3</span>))<span class="op">+</span><span class="kw">geom_point</span>()</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/Clustering_files/figure-html/unnamed-chunk-10-1.png" /><!-- --></p>
<div id="finding-optimal-number-of-clusters" class="section level3">
<h3>Finding optimal number of clusters</h3>
<p>Here I loop over 1 to 10 clusters and store the between group variances, then plot the relative differences. You are looking for the number of clusters where you see a <strong>shoulder</strong> in the plot.</p>
<div class="sourceCode" id="cb34"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb34-1" data-line-number="1">ss&lt;-<span class="ot">NULL</span></a>
<a class="sourceLine" id="cb34-2" data-line-number="2"><span class="cf">for</span>(i <span class="cf">in</span> <span class="dv">1</span><span class="op">:</span><span class="dv">10</span>){</a>
<a class="sourceLine" id="cb34-3" data-line-number="3">  km&lt;-<span class="kw">kmeans</span>(<span class="dt">x=</span>prbtrain, <span class="dt">nstart =</span> <span class="dv">10</span>, <span class="dt">centers =</span> i)</a>
<a class="sourceLine" id="cb34-4" data-line-number="4">  ss[i]&lt;-km<span class="op">$</span>betweenss <span class="op">/</span><span class="st"> </span>km<span class="op">$</span>totss</a>
<a class="sourceLine" id="cb34-5" data-line-number="5">  </a>
<a class="sourceLine" id="cb34-6" data-line-number="6">}</a>
<a class="sourceLine" id="cb34-7" data-line-number="7"></a>
<a class="sourceLine" id="cb34-8" data-line-number="8"><span class="kw">plot</span>(<span class="dt">x=</span><span class="dv">2</span><span class="op">:</span><span class="dv">10</span>, <span class="dt">y=</span><span class="kw">diff</span>(ss))</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/Clustering_files/figure-html/unnamed-chunk-11-1.png" /><!-- --></p>
<p>Looks like the difference in variances stops declining dramatically at k=3 clusters.</p>
<p>Here are the test cases plotted as X’s</p>
<div class="sourceCode" id="cb35"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb35-1" data-line-number="1">prbtest<span class="op">$</span>cluster&lt;-<span class="kw">as.factor</span>(<span class="kw">predict_KMeans</span>(<span class="dt">data=</span>prbtest, <span class="dt">CENTROIDS =</span> km2<span class="op">$</span>centroids))</a>
<a class="sourceLine" id="cb35-2" data-line-number="2"></a>
<a class="sourceLine" id="cb35-3" data-line-number="3">prbtest<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb35-4" data-line-number="4"><span class="st">  </span><span class="kw">ggplot</span>(<span class="kw">aes</span>(<span class="dt">x=</span>imr, <span class="dt">y=</span>tfr, <span class="dt">group=</span>cluster, <span class="dt">color=</span>cluster,<span class="dt">cex=</span><span class="fl">1.5</span>))<span class="op">+</span><span class="kw">geom_point</span>(<span class="kw">aes</span>(<span class="dt">cex=</span><span class="fl">2.5</span>) ,<span class="dt">pch=</span><span class="st">&quot;x&quot;</span>)<span class="op">+</span><span class="kw">geom_point</span>(<span class="dt">data=</span>prbtrain, <span class="kw">aes</span>(<span class="dt">x=</span>imr, <span class="dt">y=</span>tfr, <span class="dt">group=</span>cluster, <span class="dt">color=</span>cluster))</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/Clustering_files/figure-html/unnamed-chunk-12-1.png" /><!-- --></p>
</div>
</div>
<div id="gaussian-mixtures" class="section level2">
<h2>Gaussian mixtures</h2>
<ul>
<li>These are a type of clustering method that is based on finding a finite mixture of Gaussian distributions within data.</li>
<li>Only useful for continuous variables in data</li>
</ul>
<div class="sourceCode" id="cb36"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb36-1" data-line-number="1">prbtrain&lt;-prb[train,]</a>
<a class="sourceLine" id="cb36-2" data-line-number="2">prbtest&lt;-prb[<span class="op">-</span>train,]</a>
<a class="sourceLine" id="cb36-3" data-line-number="3"></a>
<a class="sourceLine" id="cb36-4" data-line-number="4">prbtrain&lt;-<span class="kw">center_scale</span>(prb[train,])</a>
<a class="sourceLine" id="cb36-5" data-line-number="5">prbtest&lt;-<span class="kw">center_scale</span>(prb[<span class="op">-</span>train,])</a>
<a class="sourceLine" id="cb36-6" data-line-number="6"></a>
<a class="sourceLine" id="cb36-7" data-line-number="7"><span class="kw">plot</span>(<span class="kw">Optimal_Clusters_GMM</span>(<span class="dt">data =</span> prbtrain, <span class="dt">max_clusters =</span> <span class="dv">10</span>,<span class="dt">seed_mode =</span> <span class="st">&quot;random_subset&quot;</span>, <span class="dt">km_iter =</span> <span class="dv">20</span>, <span class="dt">em_iter =</span> <span class="dv">20</span>))</a>
<a class="sourceLine" id="cb36-8" data-line-number="8"></a>
<a class="sourceLine" id="cb36-9" data-line-number="9">gmprb&lt;-<span class="kw">GMM</span>(<span class="dt">data =</span> prbtrain, <span class="dt">gaussian_comps =</span> <span class="dv">3</span>,<span class="dt">seed_mode =</span> <span class="st">&quot;random_subset&quot;</span>, <span class="dt">km_iter =</span> <span class="dv">10</span>, <span class="dt">em_iter =</span> <span class="dv">10</span>)</a>
<a class="sourceLine" id="cb36-10" data-line-number="10"></a>
<a class="sourceLine" id="cb36-11" data-line-number="11"><span class="co">#gmprb</span></a></code></pre></div>
<div class="sourceCode" id="cb37"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb37-1" data-line-number="1">prbtrain&lt;-<span class="kw">data.frame</span>(prbtrain)</a>
<a class="sourceLine" id="cb37-2" data-line-number="2"><span class="kw">names</span>(prbtrain)&lt;-<span class="kw">names</span>(prb)</a>
<a class="sourceLine" id="cb37-3" data-line-number="3">prbtrain<span class="op">$</span>cls&lt;-<span class="kw">as.factor</span>(<span class="kw">apply</span>(gmprb<span class="op">$</span>Log_likelihood, <span class="dv">1</span>, which.max))</a>
<a class="sourceLine" id="cb37-4" data-line-number="4"></a>
<a class="sourceLine" id="cb37-5" data-line-number="5"></a>
<a class="sourceLine" id="cb37-6" data-line-number="6">pred&lt;-<span class="kw">predict_GMM</span>(<span class="dt">data=</span>prbtest, <span class="dt">CENTROIDS =</span> gmprb<span class="op">$</span>centroids, <span class="dt">COVARIANCE =</span> gmprb<span class="op">$</span>covariance_matrices, <span class="dt">WEIGHTS =</span> gmprb<span class="op">$</span>weights)</a>
<a class="sourceLine" id="cb37-7" data-line-number="7"></a>
<a class="sourceLine" id="cb37-8" data-line-number="8">prbtest&lt;-<span class="kw">data.frame</span>(prbtest)</a>
<a class="sourceLine" id="cb37-9" data-line-number="9"><span class="kw">names</span>(prbtest)&lt;-<span class="kw">names</span>(prb)</a>
<a class="sourceLine" id="cb37-10" data-line-number="10"></a>
<a class="sourceLine" id="cb37-11" data-line-number="11">prbtest<span class="op">$</span>cls&lt;-<span class="kw">as.factor</span>(pred<span class="op">$</span>cluster_labels<span class="op">+</span><span class="dv">1</span>)</a>
<a class="sourceLine" id="cb37-12" data-line-number="12"></a>
<a class="sourceLine" id="cb37-13" data-line-number="13">prbtest<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb37-14" data-line-number="14"><span class="st">  </span><span class="kw">ggplot</span>(<span class="kw">aes</span>(<span class="dt">x=</span>imr, <span class="dt">y=</span>tfr, <span class="dt">group=</span>cls, <span class="dt">color=</span>cls))<span class="op">+</span><span class="kw">geom_point</span>(<span class="dt">pch=</span><span class="st">&quot;x&quot;</span>, <span class="kw">aes</span>(<span class="dt">cex=</span><span class="fl">2.5</span>) )<span class="op">+</span><span class="kw">geom_point</span>(<span class="dt">data=</span>prbtrain, <span class="kw">aes</span>(<span class="dt">x=</span>imr, <span class="dt">y=</span>tfr, <span class="dt">group=</span>cls, <span class="dt">color=</span>cls))</a></code></pre></div>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/Clustering_files/figure-html/unnamed-chunk-14-1.png" /><!-- --></p>
</div>
</section>

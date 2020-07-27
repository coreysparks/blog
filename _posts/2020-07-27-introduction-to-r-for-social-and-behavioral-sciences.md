---
title: "Introduction to R for Social and Behavioral Sciences"

author: "Corey Sparks, PhD - UTSA Department of Demography"
date: "July 27, 2020"
layout: post
---


<section class="main-content">
<div id="welcome-to-r." class="section level1">
<h1>Welcome to R.</h1>
<p>R is an interpreted languages, not a compiled one. This means, you type something into R and it does it.</p>
<p>If you’re coming to R from SAS, there is no data step. There are no procs. The <a href="https://www.amazon.com/gp/product/1466584491/ref=as_li_tl?ie=UTF8&amp;camp=1789&amp;creative=390957&amp;creativeASIN=1466584491&amp;linkCode=as2&amp;tag=sasandrblog-20">SAS and R book</a> is very useful for going between the two programs.</p>
<p>If you’re coming from SPSS and you’ve been using the button clicking method, be prepared for a steep learning curve. If you’ve been writing syntax in SPSS, you’re at least used to having to code. There’s a good book for SAS and SPSS users by Bob Meunchen at the Univ. of Tennessee <a href="https://www.amazon.com/SAS-SPSS-Users-Statistics-Computing/dp/1461406846">here</a>, which may be of some help.</p>
<div id="r-and-rstudio" class="section level2">
<h2>R and Rstudio</h2>
<p>The Rgui is the base version of R, but is not very good to program in. Rstudio is much better, as it gives you a true integrated development environment (IDE), where you can write code in on window, see results in others, see locations of files, see objects you’ve created</p>
<div id="r-file-types" class="section level3">
<h3>R file types</h3>
<p><em>.R files</em> R uses a basic script file with the .R extension. This type of file is useful if you’re going to write a function or do some analysis and don’t want to have formatted output or text.</p>
<p><em>.Rmd files</em> Rstudio uses a form of the markdown formatting language, called Rmarkdown, for creating formatted documents that include code chunks, tables, figures and statistical output. <strong>This entire example is written in Rmarkdown!</strong></p>
<p>Rmarkdown is nice for lots of reasons, such as the abiltity to insert latex equations into documents</p>
<p><span class="math display">\[y_i \sim Normal (x` \beta, \sigma_2)\]</span> or to include output tables directly into a document:</p>
<p>Quitting from lines 36-44 (r_social_behavioral_course.Rmd)</p>
<p>without having to make tables in Word or some other program. You can basically do your entire anlaysis and slideshow or paper writeup, including bibliography in Rstudio.</p>
<p><em>R Notebooks</em> In recent versions of Rstudio, another kind of document, called a R Notebook has been created. This is nice because, like a Rmarkdown document, R notebooks include code, text and formatted output, but you can also show and hide code chunks throughout the document.</p>
</div>
</div>
<div id="getting-help-in-r" class="section level2">
<h2>Getting help in R</h2>
<p>I wish I had a nickel for every time I ran into a problem trying to do something in R, that would be a lot of nickles. Here are some good tips for finding help:</p>
<ol style="list-style-type: decimal">
<li>If you know the name of a function you want to use, but just need help using it, try <code>?</code></li>
</ol>
<p><code>?lm</code></p>
<ol start="2" style="list-style-type: decimal">
<li>If you need to find a function to do something, try <code>??</code></li>
</ol>
<p><code>??&quot;linear model&quot;</code></p>
<ol start="3" style="list-style-type: decimal">
<li>If you want to search among other R users’ questions to the R list serve, try <code>RSiteSearch()</code></li>
</ol>
<p><code>RSiteSearch(&quot;heteroskedasticity&quot;)</code></p>
<ol start="4" style="list-style-type: decimal">
<li><p>Speaking of which, there are multiple <a href="https://www.r-project.org/mail.html">R user email list serves</a> that you can ask questions on, but they typically want an example of what you’re trying to do. I wish I also had nickles for each question i’ve asked and answered on these forums.</p></li>
<li><p>A good source for all things programming is the statistics branch of <a href="https://stats.stackexchange.com">Stack Exchange</a>, which has lots of contributed questions and answers, although many answers are either very snarky or wrong or for an old version of a library, so <em>caveat emptor</em>.</p></li>
<li><p>Your local R guru or R user group UTSA is</p></li>
</ol>
</div>
</div>
<div id="using-r" class="section level1">
<h1>Using R</h1>
<div id="getting-around-in-r" class="section level2">
<h2>Getting around in R</h2>
<p>When you begin an R session (open R) you will begin in your home directory. This is traditionally on Windows at ‘C:/Users/yourusername/Documents’ and on Mac, is ‘/Users/yourusername’.</p>
<p>If you’re not sure where you are you can type <code>getwd()</code>, for get working directory, and R will tell you:</p>
<div class="sourceCode" id="cb1"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb1-1" data-line-number="1"><span class="kw">getwd</span>()</a></code></pre></div>
<pre><code>## [1] &quot;{{ site.url }}{{ site.baseurl }}/_knitr&quot;</code></pre>
<p>If you don’t like where you start, you can change it, by using <code>setwd()</code>, to set your working directory to a new location.</p>
<div class="sourceCode" id="cb3"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb3-1" data-line-number="1"><span class="kw">setwd</span>(<span class="st">&quot;C:/Users/ozd504/&quot;</span>)</a>
<a class="sourceLine" id="cb3-2" data-line-number="2"><span class="kw">getwd</span>()</a></code></pre></div>
</div>
<div id="r-libraries" class="section level2">
<h2>R libraries</h2>
<p>R uses libraries to do different types of analysis, so we will need to install lots of different libraries to do different things. There are around 10,000 different packages currently for R. These are also organized into <em>Task Views</em>, where packages are organized into thematic areas.</p>
<p>Libraries/packages need to be downloaded from the internet, using the <code>install.packages()</code> command. You only need to install a package once. E.g.</p>
<p><code>install.packages(&quot;car&quot;)</code></p>
<p>will install the <code>car</code> library. To use the functions within it, type</p>
<p><code>library(car)</code></p>
<p>Now you have access to those functions. You don’t need to install the package again, unless you update your R software.</p>
<p>I strongly recommend you install several packages prior to us beginning. I’ve written a short script on Github you can use it by running:</p>
<div class="sourceCode" id="cb4"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb4-1" data-line-number="1"><span class="kw">source</span>(<span class="st">&quot;https://raw.githubusercontent.com/coreysparks/Rcode/master/install_first_short.R&quot;</span>)</a></code></pre></div>
<p>This will install a few dozen R packages that are commonly used for social science analysis.</p>
</div>
</div>
<div id="r-examples" class="section level1">
<h1>R examples</h1>
<p>Below we will go through a simple R session where we introduce some concepts that are important for R.</p>
<div id="r-is-a-calculator" class="section level3">
<h3>R is a calculator</h3>
<div class="sourceCode" id="cb5"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb5-1" data-line-number="1"><span class="co">#addition and subtraction</span></a>
<a class="sourceLine" id="cb5-2" data-line-number="2"><span class="dv">3</span><span class="op">+</span><span class="dv">7</span></a></code></pre></div>
<pre><code>## [1] 10</code></pre>
<div class="sourceCode" id="cb7"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb7-1" data-line-number="1"><span class="dv">3-7</span></a></code></pre></div>
<pre><code>## [1] -4</code></pre>
<div class="sourceCode" id="cb9"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb9-1" data-line-number="1"><span class="co">#multiplication and division</span></a>
<a class="sourceLine" id="cb9-2" data-line-number="2"><span class="dv">3</span><span class="op">*</span><span class="dv">7</span></a></code></pre></div>
<pre><code>## [1] 21</code></pre>
<div class="sourceCode" id="cb11"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb11-1" data-line-number="1"><span class="dv">3</span><span class="op">/</span><span class="dv">7</span></a></code></pre></div>
<pre><code>## [1] 0.4285714</code></pre>
<div class="sourceCode" id="cb13"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb13-1" data-line-number="1"><span class="co">#powers</span></a>
<a class="sourceLine" id="cb13-2" data-line-number="2"><span class="dv">3</span><span class="op">^</span><span class="dv">2</span></a></code></pre></div>
<pre><code>## [1] 9</code></pre>
<div class="sourceCode" id="cb15"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb15-1" data-line-number="1"><span class="dv">3</span><span class="op">^</span><span class="dv">3</span></a></code></pre></div>
<pre><code>## [1] 27</code></pre>
<div class="sourceCode" id="cb17"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb17-1" data-line-number="1"><span class="co">#sommon math functions</span></a>
<a class="sourceLine" id="cb17-2" data-line-number="2"><span class="kw">log</span>(<span class="dv">3</span><span class="op">/</span><span class="dv">7</span>)</a></code></pre></div>
<pre><code>## [1] -0.8472979</code></pre>
<div class="sourceCode" id="cb19"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb19-1" data-line-number="1"><span class="kw">exp</span>(<span class="dv">3</span><span class="op">/</span><span class="dv">7</span>)</a></code></pre></div>
<pre><code>## [1] 1.535063</code></pre>
<div class="sourceCode" id="cb21"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb21-1" data-line-number="1"><span class="kw">sin</span>(<span class="dv">3</span><span class="op">/</span><span class="dv">7</span>)</a></code></pre></div>
<pre><code>## [1] 0.4155719</code></pre>
<div class="sourceCode" id="cb23"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb23-1" data-line-number="1"><span class="co">#custom functions</span></a>
<a class="sourceLine" id="cb23-2" data-line-number="2">myfun&lt;-<span class="cf">function</span>(x){</a>
<a class="sourceLine" id="cb23-3" data-line-number="3">  <span class="kw">sqrt</span>(x)<span class="op">^</span>x</a>
<a class="sourceLine" id="cb23-4" data-line-number="4">}</a>
<a class="sourceLine" id="cb23-5" data-line-number="5"></a>
<a class="sourceLine" id="cb23-6" data-line-number="6"><span class="kw">myfun</span>(<span class="dv">5</span>)</a></code></pre></div>
<pre><code>## [1] 55.9017</code></pre>
</div>
<div id="variables-and-objects" class="section level3">
<h3>Variables and objects</h3>
<p>In R we assign values to objects (object-oriented programming). These can generally have any name, but some names are reserved for R. For instance you probably wouldn’t want to call something ‘mean’ because there’s a ‘mean()’ function already in R. For instance:</p>
<div class="sourceCode" id="cb25"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb25-1" data-line-number="1">x&lt;-<span class="dv">3</span></a>
<a class="sourceLine" id="cb25-2" data-line-number="2">y&lt;-<span class="dv">7</span></a>
<a class="sourceLine" id="cb25-3" data-line-number="3">x<span class="op">+</span>y</a></code></pre></div>
<pre><code>## [1] 10</code></pre>
<div class="sourceCode" id="cb27"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb27-1" data-line-number="1">x<span class="op">*</span>y</a></code></pre></div>
<pre><code>## [1] 21</code></pre>
<div class="sourceCode" id="cb29"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb29-1" data-line-number="1"><span class="kw">log</span>(x<span class="op">*</span>y)</a></code></pre></div>
<pre><code>## [1] 3.044522</code></pre>
</div>
<div id="vectors" class="section level2">
<h2>vectors</h2>
<p>R thinks everything is a matrix, or a vector, meaning a row or column of numbers, or characters. One of R’s big selling points is that much of it is completely vectorized. Meaning, I can apply an operation along all elements of a vector without having to write a loop. For example, if I want to multiply a vector of numbers by a constant, in SAS, I could do:</p>
<p><code>for (i in 1 to 5)</code> <code>x[i]&lt;-y[i]*5</code> <code>end</code></p>
<p>but in R, I can just do:</p>
<div class="sourceCode" id="cb31"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb31-1" data-line-number="1">x&lt;-<span class="kw">c</span>(<span class="dv">3</span>, <span class="dv">4</span>, <span class="dv">5</span>, <span class="dv">6</span>, <span class="dv">7</span>)</a>
<a class="sourceLine" id="cb31-2" data-line-number="2"><span class="co">#c() makes a vector</span></a>
<a class="sourceLine" id="cb31-3" data-line-number="3">y&lt;-<span class="dv">7</span></a>
<a class="sourceLine" id="cb31-4" data-line-number="4"></a>
<a class="sourceLine" id="cb31-5" data-line-number="5">x<span class="op">*</span>y</a></code></pre></div>
<pre><code>## [1] 21 28 35 42 49</code></pre>
<p>R is also very good about using vectors, let’s say I wanted to find the third element of x:</p>
<div class="sourceCode" id="cb33"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb33-1" data-line-number="1">x[<span class="dv">3</span>]</a></code></pre></div>
<pre><code>## [1] 5</code></pre>
<div class="sourceCode" id="cb35"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb35-1" data-line-number="1"><span class="co">#or if I want to test if this element is 10</span></a>
<a class="sourceLine" id="cb35-2" data-line-number="2">x[<span class="dv">3</span>]<span class="op">==</span><span class="dv">10</span></a></code></pre></div>
<pre><code>## [1] FALSE</code></pre>
<div class="sourceCode" id="cb37"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb37-1" data-line-number="1">x[<span class="dv">3</span>]<span class="op">!=</span><span class="dv">10</span></a></code></pre></div>
<pre><code>## [1] TRUE</code></pre>
<div class="sourceCode" id="cb39"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb39-1" data-line-number="1"><span class="co">#of is it larger than another number:</span></a>
<a class="sourceLine" id="cb39-2" data-line-number="2">x[<span class="dv">3</span>]<span class="op">&gt;</span><span class="dv">3</span></a></code></pre></div>
<pre><code>## [1] TRUE</code></pre>
<div class="sourceCode" id="cb41"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb41-1" data-line-number="1"><span class="co">#or is any element of the whole vector greater than 3</span></a>
<a class="sourceLine" id="cb41-2" data-line-number="2">x<span class="op">&gt;</span><span class="dv">3</span></a></code></pre></div>
<pre><code>## [1] FALSE  TRUE  TRUE  TRUE  TRUE</code></pre>
<p>If you want to see what’s in an object, use <code>str()</code>, for <code>str</code>ucture</p>
<div class="sourceCode" id="cb43"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb43-1" data-line-number="1"><span class="kw">str</span>(x)</a></code></pre></div>
<pre><code>##  num [1:5] 3 4 5 6 7</code></pre>
<p>and we see that x is numeric, and has those values.</p>
<p>We can also see different characteristics of x</p>
<div class="sourceCode" id="cb45"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb45-1" data-line-number="1"><span class="co">#how long is x?</span></a>
<a class="sourceLine" id="cb45-2" data-line-number="2"><span class="kw">length</span>(x)</a></code></pre></div>
<pre><code>## [1] 5</code></pre>
<div class="sourceCode" id="cb47"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb47-1" data-line-number="1"><span class="co">#is x numeric?</span></a>
<a class="sourceLine" id="cb47-2" data-line-number="2"><span class="kw">is.numeric</span>(x)</a></code></pre></div>
<pre><code>## [1] TRUE</code></pre>
<div class="sourceCode" id="cb49"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb49-1" data-line-number="1"><span class="co">#is x full of characters?</span></a>
<a class="sourceLine" id="cb49-2" data-line-number="2"><span class="kw">is.character</span>(x)</a></code></pre></div>
<pre><code>## [1] FALSE</code></pre>
<div class="sourceCode" id="cb51"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb51-1" data-line-number="1"><span class="co">#is any element of x missing?</span></a>
<a class="sourceLine" id="cb51-2" data-line-number="2"><span class="kw">is.na</span>(x)</a></code></pre></div>
<pre><code>## [1] FALSE FALSE FALSE FALSE FALSE</code></pre>
<div class="sourceCode" id="cb53"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb53-1" data-line-number="1">xc&lt;-<span class="kw">c</span>(<span class="st">&quot;1&quot;</span>,<span class="st">&quot;2&quot;</span>)</a>
<a class="sourceLine" id="cb53-2" data-line-number="2"></a>
<a class="sourceLine" id="cb53-3" data-line-number="3"><span class="co">#now i&#39;ll modify x</span></a>
<a class="sourceLine" id="cb53-4" data-line-number="4">x&lt;-<span class="kw">c</span>(x, <span class="ot">NA</span>) <span class="co">#combine x and a missing value ==NA</span></a>
<a class="sourceLine" id="cb53-5" data-line-number="5">x</a></code></pre></div>
<pre><code>## [1]  3  4  5  6  7 NA</code></pre>
<div class="sourceCode" id="cb55"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb55-1" data-line-number="1"><span class="co">#Now ask if any x&#39;s are missing</span></a>
<a class="sourceLine" id="cb55-2" data-line-number="2"><span class="kw">is.na</span>(x)</a></code></pre></div>
<pre><code>## [1] FALSE FALSE FALSE FALSE FALSE  TRUE</code></pre>
<p>##replacing elements of vectors Above, we had a missing value in X, let’s say we want to replace it with another value:</p>
<div class="sourceCode" id="cb57"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb57-1" data-line-number="1">x&lt;-<span class="kw">ifelse</span>(<span class="dt">test =</span> <span class="kw">is.na</span>(x)<span class="op">==</span>T, <span class="dt">yes =</span>  <span class="kw">sqrt</span>(<span class="fl">7.2</span>), <span class="dt">no =</span>  x)</a>
<a class="sourceLine" id="cb57-2" data-line-number="2">x</a></code></pre></div>
<pre><code>## [1] 3.000000 4.000000 5.000000 6.000000 7.000000 2.683282</code></pre>
<p>Done!</p>
</div>
<div id="dataframes" class="section level2">
<h2>Dataframes</h2>
<p>Traditionally, R organizes variables into data frames, these are like a spreadsheet. The columns can have names, and the dataframe itself can have data of different types. Here we make a short data frame with three columns, two numeric and one character:</p>
<div class="sourceCode" id="cb59"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb59-1" data-line-number="1">mydat&lt;-<span class="kw">data.frame</span>(</a>
<a class="sourceLine" id="cb59-2" data-line-number="2">  <span class="dt">x=</span><span class="kw">c</span>(<span class="dv">1</span>,<span class="dv">2</span>,<span class="dv">3</span>,<span class="dv">4</span>,<span class="dv">5</span>),</a>
<a class="sourceLine" id="cb59-3" data-line-number="3">  <span class="dt">y=</span><span class="kw">c</span>(<span class="dv">10</span>, <span class="dv">20</span>, <span class="dv">35</span>, <span class="dv">57</span>, <span class="dv">37</span>),</a>
<a class="sourceLine" id="cb59-4" data-line-number="4">  <span class="dt">group=</span><span class="kw">c</span>(<span class="st">&quot;A&quot;</span>, <span class="st">&quot;A&quot;</span> ,<span class="st">&quot;A&quot;</span>, <span class="st">&quot;B&quot;</span>, <span class="st">&quot;B&quot;</span>)</a>
<a class="sourceLine" id="cb59-5" data-line-number="5">)</a>
<a class="sourceLine" id="cb59-6" data-line-number="6"></a>
<a class="sourceLine" id="cb59-7" data-line-number="7"><span class="co">#See the size of the dataframe</span></a>
<a class="sourceLine" id="cb59-8" data-line-number="8"><span class="kw">dim</span>(mydat)</a></code></pre></div>
<pre><code>## [1] 5 3</code></pre>
<div class="sourceCode" id="cb61"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb61-1" data-line-number="1"><span class="kw">length</span>(mydat<span class="op">$</span>x)</a></code></pre></div>
<pre><code>## [1] 5</code></pre>
<div class="sourceCode" id="cb63"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb63-1" data-line-number="1"><span class="co">#Open the dataframe in a viewer and just print it</span></a>
<a class="sourceLine" id="cb63-2" data-line-number="2">knitr<span class="op">::</span><span class="kw">kable</span>(mydat)</a></code></pre></div>
<table>
<thead>
<tr class="header">
<th align="right">x</th>
<th align="right">y</th>
<th align="left">group</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="right">1</td>
<td align="right">10</td>
<td align="left">A</td>
</tr>
<tr class="even">
<td align="right">2</td>
<td align="right">20</td>
<td align="left">A</td>
</tr>
<tr class="odd">
<td align="right">3</td>
<td align="right">35</td>
<td align="left">A</td>
</tr>
<tr class="even">
<td align="right">4</td>
<td align="right">57</td>
<td align="left">B</td>
</tr>
<tr class="odd">
<td align="right">5</td>
<td align="right">37</td>
<td align="left">B</td>
</tr>
</tbody>
</table>
<div class="sourceCode" id="cb64"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb64-1" data-line-number="1"><span class="kw">print</span>(mydat)</a></code></pre></div>
<pre><code>##   x  y group
## 1 1 10     A
## 2 2 20     A
## 3 3 35     A
## 4 4 57     B
## 5 5 37     B</code></pre>
</div>
<div id="real-data" class="section level2">
<h2>Real data</h2>
<p>Now let’s open a ‘real’ data file. This is the <a href="http://www.prb.org/Publications/Datasheets/2008/2008wpds.aspx">2008 World population data sheet</a> from the <a href="http://www.prb.org">Population Reference Bureau</a>. It contains summary information on many demographic and population level characteristics of nations around the world in 2008.</p>
<p>I’ve had this entered into a <strong>Comma Separated Values</strong> file by some poor previous GRA of mine and it lives happily on Github now for all the world to see. CSV files are a good way to store data coming out of a spreadsheet. R can read Excel files, but it digests text files easier. Save something from Excel as CSV.</p>
<p>I can read it from github directly by using a function in the <code>readr</code> library:</p>
<p>That’s handy. If the file lived on our computer, I could read it in like so: <em>note, please make a folder on your computer so you can store things for this class in a single location!!!! Organization is Key to Success in Graduate School</em></p>
<div class="sourceCode" id="cb66"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb66-1" data-line-number="1">prb&lt;-<span class="kw">read_csv</span>(<span class="kw">url</span>(<span class="st">&quot;https://raw.githubusercontent.com/coreysparks/data/master/PRB2008_All.csv&quot;</span>))</a></code></pre></div>
<pre><code>## Parsed with column specification:
## cols(
##   .default = col_double(),
##   Country = col_character(),
##   Continent = col_character(),
##   Region = col_character()
## )</code></pre>
<pre><code>## See spec(...) for full column specifications.</code></pre>
<p>Same result.</p>
<p>The <code>haven</code> library can read files from other statistical packages easily, so if you have data in Stata, SAS or SPSS, you can read it into R using those functions, for example, the <code>read_dta()</code> function reads stata files, <code>read_sav()</code> to read spss data files.</p>
<p>I would not recommend you store data in Excel files for many reasons, but if you do, save the files as a CSV file and use the <code>read_csv()</code> function above to read it in.</p>
<div class="sourceCode" id="cb69"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb69-1" data-line-number="1"><span class="kw">library</span>(haven)</a>
<a class="sourceLine" id="cb69-2" data-line-number="2">prb_stata&lt;-<span class="kw">read_dta</span>(<span class="st">&quot;C:/Users/ozd504/Documents/GitHub/r_courses/prb2008.dta&quot;</span>)</a>
<a class="sourceLine" id="cb69-3" data-line-number="3"></a>
<a class="sourceLine" id="cb69-4" data-line-number="4">prb_spss&lt;-<span class="kw">read_sav</span>(<span class="st">&quot;C:/Users/ozd504/Documents/GitHub/r_courses/prb_2008.sav&quot;</span>)</a></code></pre></div>
<p>Don’t know what a function’s called use ??</p>
<p><code>??stata</code> <code>??csv</code></p>
<p>and Rstudio will show you a list of functions that have these strings in them.</p>
<p>What if you know the function name, like <code>read_csv()</code> but you want to see all the function arguments?</p>
<p><code>?read_csv</code></p>
<p>will open up the help file for that specific function</p>
<div id="save-a-file" class="section level3">
<h3>Save a file</h3>
<p>Want to save something as a R data file? Use <code>save()</code></p>
<div class="sourceCode" id="cb70"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb70-1" data-line-number="1"><span class="kw">save</span>(prb, <span class="dt">file=</span><span class="st">&quot;~/prb_2008_saved.Rdata&quot;</span>)</a></code></pre></div>
<p>If you have an R data file, use <code>load()</code> to open it:</p>
<div class="sourceCode" id="cb71"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb71-1" data-line-number="1"><span class="kw">load</span>(<span class="st">&quot;~/prb_2008.Rdata&quot;</span>)</a></code></pre></div>
</div>
<div id="descriptive-analysis" class="section level3">
<h3>Descriptive analysis</h3>
<p>Let’s have a look at some descriptive information about the data:</p>
<div class="sourceCode" id="cb72"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb72-1" data-line-number="1"><span class="co">#Frequency Table of # of Contries by Continent</span></a>
<a class="sourceLine" id="cb72-2" data-line-number="2"><span class="kw">table</span>(prb<span class="op">$</span>Continent)</a></code></pre></div>
<pre><code>## 
##        Africa          Asia        Europe North America       Oceania 
##            56            51            45            27            17 
## South America 
##            13</code></pre>
<div class="sourceCode" id="cb74"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb74-1" data-line-number="1"><span class="co">#basic summary statistics for the fertility rate</span></a>
<a class="sourceLine" id="cb74-2" data-line-number="2"><span class="kw">summary</span>(prb<span class="op">$</span>TFR)</a></code></pre></div>
<pre><code>##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max.    NA&#39;s 
##   1.000   1.775   2.500   3.032   4.000   7.100       1</code></pre>
<p>From this summary, we see that the mean is 3.023, there is one country missing the Total fertility rate variable. The minimum is 1 and the maximum is 7.1 children per woman.</p>
<p>Now, we will cover some basic descriptive statistical analysis. We will describe measures of central tendency and variability and how these are affected by outliers in our data.</p>
</div>
<div id="measures-of-central-tendency" class="section level3">
<h3>Measures of central tendency</h3>
<p>We can use graphical methods to describe what data ‘look like’ in a visual sense, but graphical methods are rarely useful for comparative purposes. In order to make comparisons, you need to rely on a numerical summary of data vs. a graphical one (I’m not saying statistical graphics aren’t useful, they are!)</p>
<p>Numerical measures tell us a lot about the form of a distribution without resorting to graphical methods. The first kind of summary statistics we will see are those related to the measure of <em>central tendency</em>. Measures of central tendency tell us about the central part of the distribution</p>
</div>
</div>
<div id="mean-and-median" class="section level2">
<h2>Mean and median</h2>
<p>Here is an example from the PRB data. R has a few different ways to get a variable from a dataset. One way is the <code>$</code> notation, used like <code>dataset$variable</code></p>
<div class="sourceCode" id="cb76"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb76-1" data-line-number="1"><span class="kw">mean</span>(prb<span class="op">$</span>TFR)</a></code></pre></div>
<pre><code>## [1] NA</code></pre>
<p>Whoops! What happened? This means that R can’t calculate the mean because there’s a missing value, which we saw before. We can tell R to automatically remove missing values by:</p>
<div class="sourceCode" id="cb78"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb78-1" data-line-number="1"><span class="kw">mean</span>(prb<span class="op">$</span>TFR, <span class="dt">na.rm =</span> T)</a></code></pre></div>
<pre><code>## [1] 3.032212</code></pre>
<p>Which is correct.</p>
<div id="measures-of-variation" class="section level3">
<h3>Measures of variation</h3>
<p>One typical set of descriptive statistics that is very frequently used is the so-called <strong>five number summary</strong> and it consists of : the Minimum, lower quartile, median, upper quartile and maximum values. This is often useful if the data are not symmetric or skewed. This is what you get when you use the <code>fivenum()</code> function, or we can include the mean if we use the <code>summary()</code> function.</p>
<div class="sourceCode" id="cb80"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb80-1" data-line-number="1">?fivenum</a>
<a class="sourceLine" id="cb80-2" data-line-number="2"><span class="kw">fivenum</span>(prb<span class="op">$</span>TFR) </a></code></pre></div>
<pre><code>## [1] 1.00 1.75 2.50 4.00 7.10</code></pre>
<div class="sourceCode" id="cb82"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb82-1" data-line-number="1"><span class="kw">summary</span>(prb<span class="op">$</span>TFR)</a></code></pre></div>
<pre><code>##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max.    NA&#39;s 
##   1.000   1.775   2.500   3.032   4.000   7.100       1</code></pre>
</div>
<div id="variance" class="section level3">
<h3>Variance</h3>
<p>To calculate the variance and standard deviation of a variable:</p>
<div class="sourceCode" id="cb84"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb84-1" data-line-number="1"><span class="kw">var</span>(x)</a></code></pre></div>
<pre><code>## [1] 2.894531</code></pre>
<div class="sourceCode" id="cb86"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb86-1" data-line-number="1"><span class="kw">sd</span>(x)</a></code></pre></div>
<pre><code>## [1] 1.701332</code></pre>
<div class="sourceCode" id="cb88"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb88-1" data-line-number="1"><span class="kw">sqrt</span>(<span class="kw">var</span>(x))<span class="co">#same as using sd()</span></a></code></pre></div>
<pre><code>## [1] 1.701332</code></pre>
</div>
</div>
<div id="really-real-data-example" class="section level2">
<h2>Really Real data example</h2>
<p>Now let’s open a ‘really real’ data file. This is a sample from the 2015 1-year <a href="https://www.census.gov/programs-surveys/acs/">American Community Survey</a> microdata, meaning that each row in these data is a person who responded to the survey in 2015. I get these, and you should too from the <a href="https://pop.umn.edu">Minnesota Population Center</a> IPUMS data. The <a href="https://usa.ipums.org/usa/">IPUMS</a> stands for “Integrated Public Use Microdata Series”, and consists of individual person responses to decennial census returns going back to 1850, and the American Community Survey data from 2001 to the present.</p>
<p>I’m using data from the US, but there is an <a href="https://international.ipums.org/international/">IPUMS International</a> data series too, which has data from 85 countries and over 300 censuses.</p>
<p>I’ve done an extract (do example in class) and stored the data in a R data (.Rdata) format on <a href="https://github.com/coreysparks/r_courses">my github data site</a>. The file we are using is called <a href="https://github.com/coreysparks/r_courses/blob/master/census_data.Rdata?raw=true">census.Rdata</a>. This extract is small by demographic data standards and is only about 300,000 people.</p>
<p>There is also a codebook that describes the data and all the response levels for each variable in the data. They are also on my github data page, and called <a href="https://github.com/coreysparks/r_courses/blob/master/Codebook_census_data.pdf">Codebook_census_data</a>.</p>
<p>I can read it from my github repository directly by using the <code>load()</code> function combined with the <code>url()</code> function, to tell R that the file is on the web. If the file were, say, in my documents folder, I could likewise load the data from disk.</p>
<div class="sourceCode" id="cb90"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb90-1" data-line-number="1"><span class="kw">load</span>(<span class="dt">file=</span><span class="kw">url</span>(<span class="st">&quot;https://github.com/coreysparks/r_courses/blob/master/census_data.Rdata?raw=true&quot;</span>))</a>
<a class="sourceLine" id="cb90-2" data-line-number="2"></a>
<a class="sourceLine" id="cb90-3" data-line-number="3"><span class="co">#from disk</span></a>
<a class="sourceLine" id="cb90-4" data-line-number="4"><span class="co">#load(&quot;C:/Users/ozd504/Documents/census_data.Rdata&quot;)</span></a>
<a class="sourceLine" id="cb90-5" data-line-number="5"></a>
<a class="sourceLine" id="cb90-6" data-line-number="6"><span class="co">#print the column names</span></a>
<a class="sourceLine" id="cb90-7" data-line-number="7"><span class="kw">names</span>(census) </a></code></pre></div>
<pre><code>##  [1] &quot;year&quot;      &quot;datanum&quot;   &quot;serial&quot;    &quot;hhwt&quot;      &quot;statefip&quot;  &quot;met2013&quot;  
##  [7] &quot;puma&quot;      &quot;gq&quot;        &quot;pernum&quot;    &quot;perwt&quot;     &quot;famsize&quot;   &quot;nchild&quot;   
## [13] &quot;nchlt5&quot;    &quot;eldch&quot;     &quot;nsibs&quot;     &quot;relate&quot;    &quot;related&quot;   &quot;sex&quot;      
## [19] &quot;age&quot;       &quot;marst&quot;     &quot;birthyr&quot;   &quot;fertyr&quot;    &quot;race&quot;      &quot;raced&quot;    
## [25] &quot;hispan&quot;    &quot;hispand&quot;   &quot;bpl&quot;       &quot;bpld&quot;      &quot;citizen&quot;   &quot;yrsusa1&quot;  
## [31] &quot;language&quot;  &quot;languaged&quot; &quot;speakeng&quot;  &quot;educ&quot;      &quot;educd&quot;     &quot;empstat&quot;  
## [37] &quot;empstatd&quot;  &quot;labforce&quot;  &quot;occ&quot;       &quot;ind&quot;       &quot;inctot&quot;    &quot;incwage&quot;  
## [43] &quot;poverty&quot;   &quot;hwsei&quot;     &quot;migrate1&quot;  &quot;migrate1d&quot; &quot;carpool&quot;   &quot;trantime&quot;</code></pre>
</div>
<div id="pipes" class="section level2">
<h2>Pipes</h2>
<p>The <code>dplyr</code> library is a portion of a suite of libraries known as the <a href="https://www.tidyverse.org"><em>tidyverse</em></a>, which are oriented towards reproducible, intelligible coding for data science. There are too many things within the tidyverse to cover them all here, but I will introduce you to two aspects: 1) dplyr verbs and pipes 2) the ggplot2 library for producing graphs</p>
<div id="basic-tidyverse-verbs" class="section level3">
<h3>Basic tidyverse verbs</h3>
<p>The dplyr library has many verbs (action words) that are used to do various things. The neat thing about dplyr is it allows you to tell R what data source you want to do something to at the top of a <em>pipe</em>, then you can execute as many verbs as you need within the pipe without referring to the dataset by name again.</p>
<p>For instance, in the census data, let’s say we want to calculate the median income for adult, men and women who are in the labor force, in Texas. Sounds easy, right? In base R we would have to do some subsetting of the data first ( to limit our analysis to adults, in the labor force, who live in Texas), then use another function to calculate the median income for men and women.</p>
<p>dplyr allows us to do this in one fell swoop of code. We will use a few verbs, notably <code>filter()</code> to subset our cases, based on the conditions we describe, <code>mutate()</code> to recode our income variable, <code>group_by()</code> to let R know that the summaries we want will be for specific groups and <code>summarise()</code> to calculate the numeric summaries we want.</p>
<p>Now, most variables in the IPUMS can’t be used out of the box. For example open the pdf codebook and find the variable “incwage”, which is person’s income from wages in the previous year.</p>
<p>We are specifically wanting to pay attention to the “Coder Instructions” <em>you’re the coder</em>. Notice two codes (values) that are of special note. <em>Specific Variable Codes 999999 = N/A and 999998=Missing</em>. So if we want to use this variable, we need to do a basic recode of these values to tell R that they represent missing values.</p>
</div>
<div id="pipes-1" class="section level3">
<h3>Pipes</h3>
<p>The second thing we need to know about are <em>pipes</em>. Pipes can be used to chain together verbs so that the code executes on the same dataset in sequence. They are identified by a <code>%&gt;%</code> at the end of each verb statement. Here’s our example in action:</p>
<div class="sourceCode" id="cb92"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb92-1" data-line-number="1"><span class="kw">library</span>(dplyr)</a></code></pre></div>
<pre><code>## 
## Attaching package: &#39;dplyr&#39;</code></pre>
<pre><code>## The following objects are masked from &#39;package:stats&#39;:
## 
##     filter, lag</code></pre>
<pre><code>## The following objects are masked from &#39;package:base&#39;:
## 
##     intersect, setdiff, setequal, union</code></pre>
<div class="sourceCode" id="cb96"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb96-1" data-line-number="1">census<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb96-2" data-line-number="2"><span class="st">  </span><span class="kw">filter</span>(age<span class="op">&gt;</span><span class="dv">18</span>, statefip<span class="op">==</span><span class="dv">48</span>, labforce<span class="op">==</span><span class="dv">2</span>)<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb96-3" data-line-number="3"><span class="st">  </span><span class="kw">mutate</span>(<span class="dt">newwage=</span> <span class="kw">ifelse</span>(incwage<span class="op">%in%</span><span class="kw">c</span>(<span class="dv">999998</span>,<span class="dv">999999</span>), <span class="ot">NA</span>, incwage),</a>
<a class="sourceLine" id="cb96-4" data-line-number="4">         <span class="dt">newsex=</span><span class="kw">ifelse</span>(sex<span class="op">==</span><span class="dv">1</span>, <span class="st">&quot;male&quot;</span>, <span class="st">&quot;female&quot;</span> ))<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb96-5" data-line-number="5"><span class="st">  </span><span class="kw">group_by</span>(newsex)<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb96-6" data-line-number="6"><span class="st">  </span><span class="kw">summarise</span>(<span class="dt">med_income=</span> <span class="kw">median</span>(newwage, <span class="dt">na.rm=</span>T))</a></code></pre></div>
<pre><code>## `summarise()` ungrouping output (override with `.groups` argument)</code></pre>
<pre><code>## # A tibble: 2 x 2
##   newsex med_income
##   &lt;chr&gt;       &lt;dbl&gt;
## 1 female      26100
## 2 male        38000</code></pre>
<p>and we see a difference of about <code>$12,000</code> between men and women in Texas.</p>
<p>Notice in the code above, I did two three different filters in a single <code>filter()</code> statement and two recodes in a single <code>mutate()</code> statement, this is totally legal, and in general you can do several operations within a single verb statement. Otherwise I would have to do:</p>
<div class="sourceCode" id="cb99"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb99-1" data-line-number="1"><span class="kw">library</span>(dplyr)</a>
<a class="sourceLine" id="cb99-2" data-line-number="2"></a>
<a class="sourceLine" id="cb99-3" data-line-number="3">census<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb99-4" data-line-number="4"><span class="st">  </span><span class="kw">filter</span>(age<span class="op">&gt;</span><span class="dv">18</span>)<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb99-5" data-line-number="5"><span class="st">  </span><span class="kw">filter</span>(statefip<span class="op">==</span><span class="dv">48</span>)<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb99-6" data-line-number="6"><span class="st">  </span><span class="kw">filter</span>(labforce<span class="op">==</span><span class="dv">2</span>)<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb99-7" data-line-number="7"><span class="st">  </span><span class="kw">mutate</span>(<span class="dt">newwage=</span> <span class="kw">ifelse</span>(incwage<span class="op">%in%</span><span class="kw">c</span>(<span class="dv">999998</span>,<span class="dv">999999</span>), <span class="ot">NA</span>, incwage))<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb99-8" data-line-number="8"><span class="st">  </span><span class="kw">mutate</span>(<span class="dt">newsex=</span><span class="kw">ifelse</span>(sex<span class="op">==</span><span class="dv">1</span>, <span class="st">&quot;male&quot;</span>, <span class="st">&quot;female&quot;</span> ))<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb99-9" data-line-number="9"><span class="st">  </span><span class="kw">group_by</span>(newsex)<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb99-10" data-line-number="10"><span class="st">  </span><span class="kw">summarise</span>(<span class="dt">med_income=</span> <span class="kw">median</span>(newwage, <span class="dt">na.rm=</span>T))</a></code></pre></div>
<pre><code>## `summarise()` ungrouping output (override with `.groups` argument)</code></pre>
<pre><code>## # A tibble: 2 x 2
##   newsex med_income
##   &lt;chr&gt;       &lt;dbl&gt;
## 1 female      26100
## 2 male        38000</code></pre>
<p>So we get to the same place. It’s up to you which way you do it, always go with the route that you understand better and that is more readable and explicable to someone else.</p>
<p>I always say that in R, there’s <strong>always</strong> more than one way to do anything!</p>
<p>We could also see how incomes are different in San Antonio (variable met2013==41700) compared to Dallas (variable met2013==19100).</p>
<div class="sourceCode" id="cb102"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb102-1" data-line-number="1">census<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb102-2" data-line-number="2"><span class="st">  </span><span class="kw">filter</span>(labforce<span class="op">==</span><span class="dv">2</span>, met2013<span class="op">%in%</span><span class="kw">c</span>(<span class="dv">41700</span>, <span class="dv">19100</span>), age<span class="op">&gt;</span><span class="dv">18</span>) <span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb102-3" data-line-number="3"><span class="st">  </span><span class="kw">mutate</span>(<span class="dt">newwage=</span> <span class="kw">ifelse</span>(incwage<span class="op">%in%</span><span class="kw">c</span>(<span class="dv">999998</span>,<span class="dv">999999</span>), <span class="ot">NA</span>, incwage),</a>
<a class="sourceLine" id="cb102-4" data-line-number="4">         <span class="dt">sexrecode=</span><span class="kw">ifelse</span>(sex<span class="op">==</span><span class="dv">1</span>, <span class="st">&quot;male&quot;</span>, <span class="st">&quot;female&quot;</span>),</a>
<a class="sourceLine" id="cb102-5" data-line-number="5">         <span class="dt">city=</span><span class="kw">ifelse</span>(met2013<span class="op">==</span><span class="dv">41700</span>, <span class="st">&quot;San Antonio&quot;</span>, <span class="st">&quot;Dallas&quot;</span>)) <span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb102-6" data-line-number="6"><span class="st">  </span><span class="kw">group_by</span>(sexrecode, city)<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb102-7" data-line-number="7"><span class="st">  </span><span class="kw">summarise</span>(<span class="dt">med_income=</span><span class="kw">median</span>(newwage, <span class="dt">na.rm=</span>T), <span class="dt">n=</span><span class="kw">n</span>())</a></code></pre></div>
<pre><code>## `summarise()` regrouping output by &#39;sexrecode&#39; (override with `.groups` argument)</code></pre>
<pre><code>## # A tibble: 4 x 4
## # Groups:   sexrecode [2]
##   sexrecode city        med_income     n
##   &lt;chr&gt;     &lt;chr&gt;            &lt;dbl&gt; &lt;int&gt;
## 1 female    Dallas           32000  1437
## 2 female    San Antonio      25000   424
## 3 male      Dallas           40000  1744
## 4 male      San Antonio      36000   466</code></pre>
<p>So, we see that men in Dallas make about <code>$4000</code> more than men in San Antonio, and women in Dallas make about <code>$7000</code> more than women in San Antonio</p>
</div>
<div id="basic-ggplot" class="section level3">
<h3>Basic ggplot()</h3>
<p>Let’s say that we want to compare the distributions of income from the above examples graphically. Since the <code>ggplot2</code> library is part of the tidyverse, it integrates directly with dplyr and we can do plots within pipes too.</p>
<p>In generally, <code>ggplot()</code> has a few core statements.</p>
<ol style="list-style-type: decimal">
<li>ggplot() statement - This tells R the data and the basic aesthetic that will be plotted, think x and y axis of a graph</li>
<li>Define the geometries you want to use to plot your data, there are many types of plots you can do, some are more appropriate for certain types of data</li>
<li>Plot annotations - Titles, labels etc.</li>
</ol>
<p>Now I will illustrate some basic ggplot examples, and I’m going to use the PRB data for now because it’s much prettier than the ACS data for plotting.</p>
<div class="sourceCode" id="cb105"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb105-1" data-line-number="1"><span class="kw">library</span>(ggplot2)</a>
<a class="sourceLine" id="cb105-2" data-line-number="2"></a>
<a class="sourceLine" id="cb105-3" data-line-number="3"><span class="kw">ggplot</span>(<span class="dt">data=</span>prb, <span class="dt">mapping=</span><span class="kw">aes</span>(TFR))<span class="op">+</span></a>
<a class="sourceLine" id="cb105-4" data-line-number="4"><span class="st">  </span><span class="kw">geom_histogram</span>( <span class="dt">bins=</span><span class="dv">10</span>)<span class="op">+</span></a>
<a class="sourceLine" id="cb105-5" data-line-number="5"><span class="st">  </span><span class="kw">ggtitle</span>(<span class="dt">label =</span> <span class="st">&quot;Distribution of the Total Fertility Rate &quot;</span>, <span class="dt">subtitle =</span> <span class="st">&quot;2008 Estimates&quot;</span>)<span class="op">+</span></a>
<a class="sourceLine" id="cb105-6" data-line-number="6"><span class="st">  </span><span class="kw">xlab</span>(<span class="dt">label =</span> <span class="st">&quot;TFR&quot;</span>)<span class="op">+</span></a>
<a class="sourceLine" id="cb105-7" data-line-number="7"><span class="st">  </span><span class="kw">ylab</span>(<span class="dt">label=</span><span class="st">&quot;Frequency&quot;</span>)</a></code></pre></div>
<pre><code>## Warning: Removed 1 rows containing non-finite values (stat_bin).</code></pre>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/r_social_behavioral_course_files/figure-html/prbhist-1.png" /><!-- --></p>
<p>There is also a nice geometry called <code>freqpoly</code> that will draw polygons instead of bars for a histogram. I will use this to produce histograms for each continent.</p>
<div class="sourceCode" id="cb107"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb107-1" data-line-number="1"><span class="kw">ggplot</span>(<span class="dt">data=</span>prb,<span class="dt">mapping =</span> <span class="kw">aes</span>(TFR, <span class="dt">colour=</span>Continent))<span class="op">+</span></a>
<a class="sourceLine" id="cb107-2" data-line-number="2"><span class="st">  </span><span class="kw">geom_freqpoly</span>( <span class="dt">bins=</span><span class="dv">10</span>)<span class="op">+</span></a>
<a class="sourceLine" id="cb107-3" data-line-number="3"><span class="st">  </span><span class="kw">ggtitle</span>(<span class="dt">label =</span> <span class="st">&quot;Distribution of the Total Fertility Rate by Continent&quot;</span>, <span class="dt">subtitle =</span> <span class="st">&quot;2008 Estimates&quot;</span>)<span class="op">+</span></a>
<a class="sourceLine" id="cb107-4" data-line-number="4"><span class="st">  </span><span class="kw">xlab</span>(<span class="dt">label =</span> <span class="st">&quot;TFR&quot;</span>)<span class="op">+</span></a>
<a class="sourceLine" id="cb107-5" data-line-number="5"><span class="st">  </span><span class="kw">ylab</span>(<span class="dt">label=</span><span class="st">&quot;Frequency&quot;</span>)</a></code></pre></div>
<pre><code>## Warning: Removed 1 rows containing non-finite values (stat_bin).</code></pre>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/r_social_behavioral_course_files/figure-html/unnamed-chunk-28-1.png" /><!-- --></p>
<p>Also, we can plot the relative frequency , or density, instead of the count by including the <code>..density..</code> argument in the aesthetic <code>aes()</code>.</p>
<div class="sourceCode" id="cb109"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb109-1" data-line-number="1"><span class="kw">ggplot</span>(<span class="dt">data=</span>prb,<span class="dt">mapping =</span> <span class="kw">aes</span>(TFR, <span class="dt">colour=</span>Continent, ..density..))<span class="op">+</span></a>
<a class="sourceLine" id="cb109-2" data-line-number="2"><span class="st">  </span><span class="kw">geom_freqpoly</span>( <span class="dt">bins=</span><span class="dv">10</span>)<span class="op">+</span></a>
<a class="sourceLine" id="cb109-3" data-line-number="3"><span class="st">  </span><span class="kw">ggtitle</span>(<span class="dt">label =</span> <span class="st">&quot;Distribution of the Total Fertility Rate by Continent&quot;</span>, <span class="dt">subtitle =</span> <span class="st">&quot;2008 Estimates&quot;</span>)<span class="op">+</span></a>
<a class="sourceLine" id="cb109-4" data-line-number="4"><span class="st">  </span><span class="kw">xlab</span>(<span class="dt">label =</span> <span class="st">&quot;TFR&quot;</span>)<span class="op">+</span></a>
<a class="sourceLine" id="cb109-5" data-line-number="5"><span class="st">  </span><span class="kw">ylab</span>(<span class="dt">label=</span><span class="st">&quot;Frequency&quot;</span>)</a></code></pre></div>
<pre><code>## Warning: Removed 1 rows containing non-finite values (stat_bin).</code></pre>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/r_social_behavioral_course_files/figure-html/unnamed-chunk-29-1.png" /><!-- --></p>
</div>
<div id="stem-and-leaf-plotsbox-and-whisker-plots" class="section level3">
<h3>Stem and leaf plots/Box and Whisker plots</h3>
<p>Another visualization method is the stem and leaf plot, or box and whisker plot. This is useful when you have a continuous variable you want to display the distribution of across levels of a categorical variable. This is basically a graphical display of Tukey’s 5 number summary of data.</p>
<div class="sourceCode" id="cb111"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb111-1" data-line-number="1"><span class="kw">ggplot</span>(prb, <span class="dt">mapping =</span> <span class="kw">aes</span>(<span class="dt">x=</span> Continent, <span class="dt">y =</span>TFR))<span class="op">+</span></a>
<a class="sourceLine" id="cb111-2" data-line-number="2"><span class="st">  </span><span class="kw">geom_boxplot</span>()<span class="op">+</span></a>
<a class="sourceLine" id="cb111-3" data-line-number="3"><span class="st">  </span><span class="kw">ggtitle</span>(<span class="dt">label =</span> <span class="st">&quot;Distribution of the Total Fertility Rate by Continent&quot;</span>, <span class="dt">subtitle =</span> <span class="st">&quot;2008 Estimates&quot;</span>)</a></code></pre></div>
<pre><code>## Warning: Removed 1 rows containing non-finite values (stat_boxplot).</code></pre>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/r_social_behavioral_course_files/figure-html/unnamed-chunk-30-1.png" /><!-- --> You can flip the axes, by adding <code>coord_flip()</code></p>
<div class="sourceCode" id="cb113"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb113-1" data-line-number="1"><span class="kw">ggplot</span>(prb, <span class="dt">mapping =</span> <span class="kw">aes</span>(<span class="dt">x=</span> Continent, <span class="dt">y =</span>TFR))<span class="op">+</span></a>
<a class="sourceLine" id="cb113-2" data-line-number="2"><span class="st">  </span><span class="kw">geom_boxplot</span>()<span class="op">+</span></a>
<a class="sourceLine" id="cb113-3" data-line-number="3"><span class="st">  </span><span class="kw">ggtitle</span>(<span class="dt">label =</span> <span class="st">&quot;Distribution of the Total Fertility Rate by Continent&quot;</span>, <span class="dt">subtitle =</span> <span class="st">&quot;2008 Estimates&quot;</span>)<span class="op">+</span><span class="kw">coord_flip</span>()</a></code></pre></div>
<pre><code>## Warning: Removed 1 rows containing non-finite values (stat_boxplot).</code></pre>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/r_social_behavioral_course_files/figure-html/unnamed-chunk-31-1.png" /><!-- --> You can also color the boxes by a variable, Here, I will make a new variable that is the combination of the continent variable with the region variable, using the <code>paste()</code> function. It’s useful for combining values of two strings.</p>
<div class="sourceCode" id="cb115"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb115-1" data-line-number="1">prb<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb115-2" data-line-number="2"><span class="st">  </span><span class="kw">mutate</span>(<span class="dt">newname =</span> <span class="kw">paste</span>(Continent, Region, <span class="dt">sep=</span><span class="st">&quot;-&quot;</span>))<span class="op">%&gt;%</span></a>
<a class="sourceLine" id="cb115-3" data-line-number="3"><span class="st">  </span><span class="kw">ggplot</span>(<span class="kw">aes</span>(<span class="dt">x=</span> newname, <span class="dt">y =</span>TFR,<span class="dt">fill=</span>Continent))<span class="op">+</span></a>
<a class="sourceLine" id="cb115-4" data-line-number="4"><span class="st">  </span><span class="kw">geom_boxplot</span>()<span class="op">+</span><span class="kw">coord_flip</span>()<span class="op">+</span></a>
<a class="sourceLine" id="cb115-5" data-line-number="5"><span class="st">  </span><span class="kw">ggtitle</span>(<span class="dt">label =</span> <span class="st">&quot;Distribution of the Total Fertility Rate by Continent&quot;</span>, <span class="dt">subtitle =</span> <span class="st">&quot;2008 Estimates&quot;</span>)</a></code></pre></div>
<pre><code>## Warning: Removed 1 rows containing non-finite values (stat_boxplot).</code></pre>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/r_social_behavioral_course_files/figure-html/unnamed-chunk-32-1.png" /><!-- --></p>
</div>
<div id="x-y-scatter-plots" class="section level3">
<h3>X-Y Scatter plots</h3>
<p>These are useful for finding relationships among two or more continuous variables. <code>ggplot()</code> can really make these pretty.</p>
<p>Here are a few riffs using the PRB data:</p>
<div class="sourceCode" id="cb117"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb117-1" data-line-number="1"><span class="kw">ggplot</span>(<span class="dt">data=</span>prb,<span class="dt">mapping=</span> <span class="kw">aes</span>(<span class="dt">x=</span>TFR, <span class="dt">y=</span>IMR))<span class="op">+</span></a>
<a class="sourceLine" id="cb117-2" data-line-number="2"><span class="st">  </span><span class="kw">geom_point</span>()<span class="op">+</span></a>
<a class="sourceLine" id="cb117-3" data-line-number="3"><span class="st">  </span><span class="kw">ggtitle</span>(<span class="dt">label =</span> <span class="st">&quot;Relationship between Total Fertility and Infant Mortality&quot;</span>, <span class="dt">subtitle =</span> <span class="st">&quot;2008 Estimates&quot;</span>)<span class="op">+</span></a>
<a class="sourceLine" id="cb117-4" data-line-number="4"><span class="st">  </span><span class="kw">xlab</span>(<span class="dt">label =</span> <span class="st">&quot;TFR&quot;</span>)<span class="op">+</span></a>
<a class="sourceLine" id="cb117-5" data-line-number="5"><span class="st">  </span><span class="kw">ylab</span>(<span class="dt">label=</span><span class="st">&quot;IMR&quot;</span>)</a></code></pre></div>
<pre><code>## Warning: Removed 2 rows containing missing values (geom_point).</code></pre>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/r_social_behavioral_course_files/figure-html/unnamed-chunk-33-1.png" /><!-- --></p>
<p>Now we color varies by continent</p>
<div class="sourceCode" id="cb119"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb119-1" data-line-number="1"><span class="kw">ggplot</span>(<span class="dt">data=</span>prb,<span class="dt">mapping=</span> <span class="kw">aes</span>(<span class="dt">x=</span>TFR, <span class="dt">y=</span>IMR, <span class="dt">color=</span>Continent))<span class="op">+</span></a>
<a class="sourceLine" id="cb119-2" data-line-number="2"><span class="st">  </span><span class="kw">geom_point</span>()<span class="op">+</span></a>
<a class="sourceLine" id="cb119-3" data-line-number="3"><span class="st">  </span><span class="kw">ggtitle</span>(<span class="dt">label =</span> <span class="st">&quot;Relationship between Total Fertility and Infant Mortality&quot;</span>, <span class="dt">subtitle =</span> <span class="st">&quot;2008 Estimates&quot;</span>)<span class="op">+</span></a>
<a class="sourceLine" id="cb119-4" data-line-number="4"><span class="st">  </span><span class="kw">xlab</span>(<span class="dt">label =</span> <span class="st">&quot;TFR&quot;</span>)<span class="op">+</span></a>
<a class="sourceLine" id="cb119-5" data-line-number="5"><span class="st">  </span><span class="kw">ylab</span>(<span class="dt">label=</span><span class="st">&quot;IMR&quot;</span>)</a></code></pre></div>
<pre><code>## Warning: Removed 2 rows containing missing values (geom_point).</code></pre>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/r_social_behavioral_course_files/figure-html/unnamed-chunk-34-1.png" /><!-- --></p>
<p>Now we vary the shape of the point by continent</p>
<div class="sourceCode" id="cb121"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb121-1" data-line-number="1"><span class="co">#shape varies by continent</span></a>
<a class="sourceLine" id="cb121-2" data-line-number="2"><span class="kw">ggplot</span>(<span class="dt">data=</span>prb,<span class="dt">mapping=</span> <span class="kw">aes</span>(<span class="dt">x=</span>TFR, <span class="dt">y=</span>IMR, <span class="dt">shape=</span>Continent))<span class="op">+</span></a>
<a class="sourceLine" id="cb121-3" data-line-number="3"><span class="st">  </span><span class="kw">geom_point</span>()<span class="op">+</span></a>
<a class="sourceLine" id="cb121-4" data-line-number="4"><span class="st">  </span><span class="kw">ggtitle</span>(<span class="dt">label =</span> <span class="st">&quot;Relationship between Total Fertility and Infant Mortality&quot;</span>, <span class="dt">subtitle =</span> <span class="st">&quot;2008 Estimates&quot;</span>)<span class="op">+</span></a>
<a class="sourceLine" id="cb121-5" data-line-number="5"><span class="st">  </span><span class="kw">xlab</span>(<span class="dt">label =</span> <span class="st">&quot;TFR&quot;</span>)<span class="op">+</span></a>
<a class="sourceLine" id="cb121-6" data-line-number="6"><span class="st">  </span><span class="kw">ylab</span>(<span class="dt">label=</span><span class="st">&quot;IMR&quot;</span>)</a></code></pre></div>
<pre><code>## Warning: Removed 2 rows containing missing values (geom_point).</code></pre>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/r_social_behavioral_course_files/figure-html/unnamed-chunk-35-1.png" /><!-- --></p>
<p>##Facet plots Facet plots are nice, if you want to create a plot separately for a series of groups. This allows you to visualize if the relationship is constant across those groups, well at least graphically.</p>
<div class="sourceCode" id="cb123"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb123-1" data-line-number="1"><span class="kw">ggplot</span>(<span class="dt">data=</span>prb,<span class="dt">mapping=</span> <span class="kw">aes</span>(<span class="dt">x=</span>TFR, <span class="dt">y=</span>IMR, <span class="dt">color=</span>Continent))<span class="op">+</span></a>
<a class="sourceLine" id="cb123-2" data-line-number="2"><span class="st">  </span><span class="kw">geom_point</span>()<span class="op">+</span></a>
<a class="sourceLine" id="cb123-3" data-line-number="3"><span class="st">  </span><span class="kw">facet_wrap</span>(<span class="op">~</span>Continent)<span class="op">+</span></a>
<a class="sourceLine" id="cb123-4" data-line-number="4"><span class="st">  </span><span class="kw">ggtitle</span>(<span class="dt">label =</span> <span class="st">&quot;Relationship between Total Fertility and Infant Mortality&quot;</span>, <span class="dt">subtitle =</span> <span class="st">&quot;2008 Estimates&quot;</span>)<span class="op">+</span></a>
<a class="sourceLine" id="cb123-5" data-line-number="5"><span class="st">  </span><span class="kw">xlab</span>(<span class="dt">label =</span> <span class="st">&quot;TFR&quot;</span>)<span class="op">+</span></a>
<a class="sourceLine" id="cb123-6" data-line-number="6"><span class="st">  </span><span class="kw">ylab</span>(<span class="dt">label=</span><span class="st">&quot;IMR&quot;</span>)</a></code></pre></div>
<pre><code>## Warning: Removed 2 rows containing missing values (geom_point).</code></pre>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/r_social_behavioral_course_files/figure-html/unnamed-chunk-36-1.png" /><!-- --></p>
</div>
</div>
<div id="plotting-relationships-with-some-line-fits" class="section level2">
<h2>Plotting relationships with some line fits</h2>
<p><code>ggplot</code> allows you to make some very nice line-fit plots for scatter plots. While the math behind these lines is not what we are talking about, they do produce a nice graphical summary of the relationships.</p>
<div class="sourceCode" id="cb125"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb125-1" data-line-number="1"><span class="kw">ggplot</span>(<span class="dt">data=</span>prb,<span class="dt">mapping=</span> <span class="kw">aes</span>(<span class="dt">x=</span>TFR, <span class="dt">y=</span>IMR))<span class="op">+</span></a>
<a class="sourceLine" id="cb125-2" data-line-number="2"><span class="st">  </span><span class="kw">geom_point</span>()<span class="op">+</span></a>
<a class="sourceLine" id="cb125-3" data-line-number="3"><span class="st">  </span><span class="kw">geom_smooth</span>( <span class="dt">method =</span> <span class="st">&quot;lm&quot;</span>)<span class="op">+</span></a>
<a class="sourceLine" id="cb125-4" data-line-number="4"><span class="st">  </span><span class="kw">ggtitle</span>(<span class="dt">label =</span> <span class="st">&quot;Relationship between Total Fertility and Infant Mortality&quot;</span>, <span class="dt">subtitle =</span> <span class="st">&quot;2008 Estimates-linear fit&quot;</span>)<span class="op">+</span></a>
<a class="sourceLine" id="cb125-5" data-line-number="5"><span class="st">  </span><span class="kw">xlab</span>(<span class="dt">label =</span> <span class="st">&quot;TFR&quot;</span>)<span class="op">+</span></a>
<a class="sourceLine" id="cb125-6" data-line-number="6"><span class="st">  </span><span class="kw">ylab</span>(<span class="dt">label=</span><span class="st">&quot;IMR&quot;</span>)</a></code></pre></div>
<pre><code>## `geom_smooth()` using formula &#39;y ~ x&#39;</code></pre>
<pre><code>## Warning: Removed 2 rows containing non-finite values (stat_smooth).</code></pre>
<pre><code>## Warning: Removed 2 rows containing missing values (geom_point).</code></pre>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/r_social_behavioral_course_files/figure-html/unnamed-chunk-37-1.png" /><!-- --></p>
<div class="sourceCode" id="cb129"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb129-1" data-line-number="1"><span class="kw">ggplot</span>(<span class="dt">data=</span>prb)<span class="op">+</span></a>
<a class="sourceLine" id="cb129-2" data-line-number="2"><span class="st">  </span><span class="kw">geom_point</span>(<span class="dt">mapping=</span> <span class="kw">aes</span>(<span class="dt">x=</span>TFR, <span class="dt">y=</span>IMR))<span class="op">+</span></a>
<a class="sourceLine" id="cb129-3" data-line-number="3"><span class="st">  </span><span class="kw">geom_smooth</span>(<span class="dt">mapping=</span> <span class="kw">aes</span>(<span class="dt">x=</span>TFR, <span class="dt">y=</span>IMR) , <span class="dt">method =</span> <span class="st">&quot;loess&quot;</span>)<span class="op">+</span></a>
<a class="sourceLine" id="cb129-4" data-line-number="4"><span class="st">  </span><span class="kw">ggtitle</span>(<span class="dt">label =</span> <span class="st">&quot;Relationship between Total Fertility and Infant Mortality&quot;</span>, <span class="dt">subtitle =</span> <span class="st">&quot;2008 Estimates&quot;</span>)<span class="op">+</span></a>
<a class="sourceLine" id="cb129-5" data-line-number="5"><span class="st">  </span><span class="kw">xlab</span>(<span class="dt">label =</span> <span class="st">&quot;TFR&quot;</span>)<span class="op">+</span></a>
<a class="sourceLine" id="cb129-6" data-line-number="6"><span class="st">  </span><span class="kw">ylab</span>(<span class="dt">label=</span><span class="st">&quot;IMR&quot;</span>)</a></code></pre></div>
<pre><code>## `geom_smooth()` using formula &#39;y ~ x&#39;</code></pre>
<pre><code>## Warning: Removed 2 rows containing non-finite values (stat_smooth).

## Warning: Removed 2 rows containing missing values (geom_point).</code></pre>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/r_social_behavioral_course_files/figure-html/unnamed-chunk-37-2.png" /><!-- --></p>
<p>Another example, this time of a bad linear plot!</p>
<div class="sourceCode" id="cb132"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb132-1" data-line-number="1"><span class="kw">ggplot</span>(<span class="dt">data=</span>prb,<span class="dt">mapping=</span> <span class="kw">aes</span>(<span class="dt">x=</span>TFR, <span class="dt">y=</span>PercPopLT15))<span class="op">+</span></a>
<a class="sourceLine" id="cb132-2" data-line-number="2"><span class="st">  </span><span class="kw">geom_point</span>()<span class="op">+</span></a>
<a class="sourceLine" id="cb132-3" data-line-number="3"><span class="st">  </span><span class="kw">geom_smooth</span>( <span class="dt">method =</span> <span class="st">&quot;lm&quot;</span>)<span class="op">+</span></a>
<a class="sourceLine" id="cb132-4" data-line-number="4"><span class="st">  </span><span class="kw">ggtitle</span>(<span class="dt">label =</span> <span class="st">&quot;Relationship between Total Fertility and Percent under age 15&quot;</span>, <span class="dt">subtitle =</span> <span class="st">&quot;2008 Estimates-linear fit&quot;</span>)<span class="op">+</span></a>
<a class="sourceLine" id="cb132-5" data-line-number="5"><span class="st">  </span><span class="kw">xlab</span>(<span class="dt">label =</span> <span class="st">&quot;Percent under age 15&quot;</span>)<span class="op">+</span></a>
<a class="sourceLine" id="cb132-6" data-line-number="6"><span class="st">  </span><span class="kw">ylab</span>(<span class="dt">label=</span><span class="st">&quot;IMR&quot;</span>)</a></code></pre></div>
<pre><code>## `geom_smooth()` using formula &#39;y ~ x&#39;</code></pre>
<pre><code>## Warning: Removed 1 rows containing non-finite values (stat_smooth).</code></pre>
<pre><code>## Warning: Removed 1 rows containing missing values (geom_point).</code></pre>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/r_social_behavioral_course_files/figure-html/unnamed-chunk-38-1.png" /><!-- --></p>
<p>So instead, us a nonlinear fit, a la a loess regression:</p>
<div class="sourceCode" id="cb136"><pre class="sourceCode r"><code class="sourceCode r"><a class="sourceLine" id="cb136-1" data-line-number="1"><span class="kw">ggplot</span>(<span class="dt">data=</span>prb, <span class="dt">mapping=</span> <span class="kw">aes</span>(<span class="dt">x=</span>TFR, <span class="dt">y=</span>PercPopLT15))<span class="op">+</span></a>
<a class="sourceLine" id="cb136-2" data-line-number="2"><span class="st">  </span><span class="kw">geom_point</span>()<span class="op">+</span></a>
<a class="sourceLine" id="cb136-3" data-line-number="3"><span class="st">  </span><span class="kw">geom_smooth</span>( <span class="dt">method =</span> <span class="st">&quot;loess&quot;</span>)<span class="op">+</span></a>
<a class="sourceLine" id="cb136-4" data-line-number="4"><span class="st">  </span><span class="kw">ggtitle</span>(<span class="dt">label =</span> <span class="st">&quot;Relationship between Total Fertility and Percent under age 15&quot;</span>, <span class="dt">subtitle =</span> <span class="st">&quot;2008 Estimates- loess fit&quot;</span>)<span class="op">+</span></a>
<a class="sourceLine" id="cb136-5" data-line-number="5"><span class="st">  </span><span class="kw">xlab</span>(<span class="dt">label =</span> <span class="st">&quot;Percent under age 15&quot;</span>)<span class="op">+</span></a>
<a class="sourceLine" id="cb136-6" data-line-number="6"><span class="st">  </span><span class="kw">ylab</span>(<span class="dt">label=</span><span class="st">&quot;IMR&quot;</span>)</a></code></pre></div>
<pre><code>## `geom_smooth()` using formula &#39;y ~ x&#39;</code></pre>
<pre><code>## Warning: Removed 1 rows containing non-finite values (stat_smooth).</code></pre>
<pre><code>## Warning: Removed 1 rows containing missing values (geom_point).</code></pre>
<p><img src="{{ site.url }}{{ site.baseurl }}/knitr_files/r_social_behavioral_course_files/figure-html/unnamed-chunk-39-1.png" /><!-- --></p>
</div>
</div>
<div id="other-resources" class="section level1">
<h1>Other Resources</h1>
<div id="miktek" class="section level3">
<h3>Miktek</h3>
<p>To build pdf’s you’ll need a version of Latex installed, <a href="https://miktex.org/download">Miktek</a> is a good option</p>
</div>
<div id="r-markdown-cheat-sheets" class="section level3">
<h3>R-markdown cheat sheets</h3>
<p>Rstudio keeps a variety of <a href="https://www.rstudio.com/resources/cheatsheets/">cheat sheets</a> for various topics, they can be helpful in a pinch</p>
</div>
<div id="ucla-statistical-computing-help" class="section level3">
<h3>UCLA Statistical computing help</h3>
<p><a href="http://www.ats.ucla.edu/stat/">This page</a> has lots of examples of using R for various types of analysis.</p>
</div>
<div id="other-examples" class="section level3">
<h3>Other examples</h3>
<p>On <a href="http://rpubs.com/corey_sparks">my Rpubs page</a> I have lots of examples of various types of analysis using R and you can get the data for these on <a href="https://github.com/coreysparks/data">my Github data page</a></p>
</div>
</div>
</section>

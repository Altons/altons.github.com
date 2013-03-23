---
layout: post
title: "Dominoes, Probability and extra cash in your pocket"
description: ""
category: probability
tags: [probability, python, games]
---
{% include JB/setup %}

When I was studying statistics at University I was always running low in cash for Friday's beers (I think this applies to almost any student) thus betting against my non-statistical friends was always a good source for extra cash. [Dominoes](http://en.wikipedia.org/wiki/Dominoes) or "Las Piedras" are considered a sport in Venezuela, friends and family can gather together for hours just to play dominoes. The dominoes played in Venezuela is the Double-Six variant which contains 28 tiles or "Piedras". Tiles that have the same value on both ends are called doubles (there are 7 doubles), and are typically referred to as double-zero, double-one etc. Tiles with two different values are called singles.

The following image will give you a better a idea of what I have just explained.

<div class="highlight"><pre><span class="kn">from</span> <span class="nn">IPython.core.display</span> <span class="kn">import</span> <span class="n">Image</span> 
<span class="n">Image</span><span class="p">(</span><span class="n">filename</span><span class="o">=</span><span class="s">&#39;Domino2D.png&#39;</span><span class="p">)</span> 
</pre></div>


![Alt text](figs/python/Domino2D.png)


Now, I did not make extra cash playing dominoes (I did sometimes, but that's not the reason of my post) but using dominoes tiles and a bit of probability was my way to make extra cash. Now imaging the following situation: You have gathered some friends and on a table you have all domino tiles facing down and proceed to shuffle them around. Then with the tiles still **facing down** you make an arrangement of 4 rows and 7 columns just like the above image and tell your friends *"I bet you $5 there is at least one double in one of the four corners"*

Friends with a big smile thinking on easy money will accept the challenge and I thinking ignorance is bliss will commence my game. Round after round they all were completely baffled seeing how often doubles show in corners and at some point I was called cheater! so I let them do the shuflling and arrangement of tiles for a few more rounds while counting my money.

I learnt this trick while I was studying Probability I and it is my duty to pass down this knowledge to future generations of students looking to get some cash for Friday's beers.

Explanation
-----------

Some of you may be thinking "what is the probability of at least a double in one of the four corners?" but the truth is the arrangement of tiles was to make the game more appealing to others. The fact that tiles are arranged in columns and rows is just a way of deviating attention.

The real problem is picking a double in 4 tries.

We know that we have 28 tiles of which 7 are doubles and 4 tries so the probability of **NOT** getting a double in 4 tries is:


<div class="highlight"><pre><span class="n">probNotDouble</span> <span class="o">=</span> <span class="p">(</span><span class="mi">21</span><span class="o">/</span><span class="mf">28.0</span><span class="p">)</span><span class="o">*</span><span class="p">(</span><span class="mi">20</span><span class="o">/</span><span class="mf">27.0</span><span class="p">)</span><span class="o">*</span><span class="p">(</span><span class="mi">19</span><span class="o">/</span><span class="mf">26.0</span><span class="p">)</span><span class="o">*</span><span class="p">(</span><span class="mi">18</span><span class="o">/</span><span class="mf">25.0</span><span class="p">)</span>
<span class="n">probNotDouble</span>
</pre></div>


<pre>
    0.2923076923076923
</pre>


and the probability of getting a double in 4 tries then is:

<div class="highlight"><pre><span class="mi">1</span><span class="o">-</span><span class="n">probNotDouble</span>
</pre></div>


<pre>
    0.7076923076923076
</pre>


**70.77%** of time you will find a double in any of the four corners. If my math doesn't convince you then let's try experimentally.

I wrote a small class in python that simulates the domino set, shuffling and arrangement of tiles in a 4x7 array. 

<div class="highlight"><pre><span class="kn">from</span> <span class="nn">random</span> <span class="kn">import</span> <span class="n">shuffle</span>
<span class="kn">from</span> <span class="nn">copy</span> <span class="kn">import</span> <span class="n">deepcopy</span>
<span class="kn">from</span> <span class="nn">math</span> <span class="kn">import</span> <span class="n">sqrt</span>


<span class="k">class</span> <span class="nc">Domino</span><span class="p">():</span>
    <span class="s">&quot;Basic class for simulating domino tiles&quot;</span>
    <span class="k">def</span> <span class="nf">__init__</span><span class="p">(</span><span class="bp">self</span><span class="p">):</span>
        <span class="sd">&quot;&quot;&quot;Domino initialization &quot;&quot;&quot;</span>
        <span class="bp">self</span><span class="o">.</span><span class="n">dominoSet</span> <span class="o">=</span> <span class="p">[(</span><span class="mi">0</span><span class="p">,</span> <span class="mi">0</span><span class="p">),</span> <span class="p">(</span><span class="mi">0</span><span class="p">,</span> <span class="mi">1</span><span class="p">),</span> <span class="p">(</span><span class="mi">0</span><span class="p">,</span> <span class="mi">2</span><span class="p">),</span> <span class="p">(</span><span class="mi">0</span><span class="p">,</span> <span class="mi">3</span><span class="p">),</span> <span class="p">(</span><span class="mi">0</span><span class="p">,</span> <span class="mi">4</span><span class="p">),</span> <span class="p">(</span><span class="mi">0</span><span class="p">,</span> <span class="mi">5</span><span class="p">),</span> <span class="p">(</span><span class="mi">0</span><span class="p">,</span> <span class="mi">6</span><span class="p">),</span>
                          <span class="p">(</span><span class="mi">1</span><span class="p">,</span> <span class="mi">1</span><span class="p">),</span> <span class="p">(</span><span class="mi">1</span><span class="p">,</span> <span class="mi">2</span><span class="p">),</span> <span class="p">(</span><span class="mi">1</span><span class="p">,</span> <span class="mi">3</span><span class="p">),</span> <span class="p">(</span><span class="mi">1</span><span class="p">,</span> <span class="mi">4</span><span class="p">),</span> <span class="p">(</span><span class="mi">1</span><span class="p">,</span> <span class="mi">5</span><span class="p">),</span> <span class="p">(</span><span class="mi">1</span><span class="p">,</span> <span class="mi">6</span><span class="p">),</span>
                          <span class="p">(</span><span class="mi">2</span><span class="p">,</span> <span class="mi">2</span><span class="p">),</span> <span class="p">(</span><span class="mi">2</span><span class="p">,</span> <span class="mi">3</span><span class="p">),</span> <span class="p">(</span><span class="mi">2</span><span class="p">,</span> <span class="mi">4</span><span class="p">),</span> <span class="p">(</span><span class="mi">2</span><span class="p">,</span> <span class="mi">5</span><span class="p">),</span> <span class="p">(</span><span class="mi">2</span><span class="p">,</span> <span class="mi">6</span><span class="p">),</span>
                          <span class="p">(</span><span class="mi">3</span><span class="p">,</span> <span class="mi">3</span><span class="p">),</span> <span class="p">(</span><span class="mi">3</span><span class="p">,</span> <span class="mi">4</span><span class="p">),</span> <span class="p">(</span><span class="mi">3</span><span class="p">,</span> <span class="mi">5</span><span class="p">),</span> <span class="p">(</span><span class="mi">3</span><span class="p">,</span> <span class="mi">6</span><span class="p">),</span>
                          <span class="p">(</span><span class="mi">4</span><span class="p">,</span> <span class="mi">4</span><span class="p">),</span> <span class="p">(</span><span class="mi">4</span><span class="p">,</span> <span class="mi">5</span><span class="p">),</span> <span class="p">(</span><span class="mi">4</span><span class="p">,</span> <span class="mi">6</span><span class="p">),</span>
                          <span class="p">(</span><span class="mi">5</span><span class="p">,</span> <span class="mi">5</span><span class="p">),</span> <span class="p">(</span><span class="mi">5</span><span class="p">,</span> <span class="mi">6</span><span class="p">),</span>
                          <span class="p">(</span><span class="mi">6</span><span class="p">,</span> <span class="mi">6</span><span class="p">)]</span>

    <span class="k">def</span> <span class="nf">splitter</span><span class="p">(</span><span class="bp">self</span><span class="p">,</span> <span class="n">seq</span><span class="p">,</span> <span class="n">size</span><span class="p">):</span>
        <span class="k">return</span> <span class="p">(</span><span class="n">seq</span><span class="p">[</span><span class="n">pos</span><span class="p">:</span><span class="n">pos</span> <span class="o">+</span> <span class="n">size</span><span class="p">]</span> <span class="k">for</span> <span class="n">pos</span> <span class="ow">in</span> <span class="nb">xrange</span><span class="p">(</span><span class="mi">0</span><span class="p">,</span> <span class="nb">len</span><span class="p">(</span><span class="n">seq</span><span class="p">),</span> <span class="n">size</span><span class="p">))</span>

    <span class="k">def</span> <span class="nf">shuffleTiles</span><span class="p">(</span><span class="bp">self</span><span class="p">,</span> <span class="n">tiles</span><span class="p">):</span>
        <span class="n">new_tiles</span> <span class="o">=</span> <span class="n">deepcopy</span><span class="p">(</span><span class="n">tiles</span><span class="p">)</span>
        <span class="n">shuffle</span><span class="p">(</span><span class="n">new_tiles</span><span class="p">)</span>
        <span class="k">return</span> <span class="n">new_tiles</span>

    <span class="k">def</span> <span class="nf">arrangeTiles</span><span class="p">(</span><span class="bp">self</span><span class="p">,</span> <span class="n">tiles</span><span class="p">,</span> <span class="n">size</span><span class="p">):</span>
        <span class="n">shuffling</span> <span class="o">=</span> <span class="bp">self</span><span class="o">.</span><span class="n">shuffleTiles</span><span class="p">(</span><span class="n">tiles</span><span class="p">)</span>
        <span class="n">rows</span> <span class="o">=</span> <span class="p">[</span><span class="n">group</span> <span class="k">for</span> <span class="n">group</span> <span class="ow">in</span> <span class="bp">self</span><span class="o">.</span><span class="n">splitter</span><span class="p">(</span><span class="n">shuffling</span><span class="p">,</span> <span class="n">size</span><span class="p">)]</span>
        <span class="k">return</span> <span class="n">rows</span>

    <span class="k">def</span> <span class="nf">is_double_any_corner</span><span class="p">(</span><span class="bp">self</span><span class="p">,</span> <span class="n">tiles</span><span class="p">):</span>
        <span class="n">r1</span> <span class="o">=</span> <span class="n">tiles</span><span class="p">[</span><span class="mi">0</span><span class="p">]</span>  <span class="c"># top row</span>
        <span class="n">r2</span> <span class="o">=</span> <span class="n">tiles</span><span class="p">[</span><span class="mi">3</span><span class="p">]</span>  <span class="c"># bottom row</span>
        <span class="n">corners</span> <span class="o">=</span> <span class="p">[</span><span class="n">r1</span><span class="p">[</span><span class="mi">0</span><span class="p">],</span> <span class="n">r1</span><span class="p">[</span><span class="o">-</span><span class="mi">1</span><span class="p">],</span> <span class="n">r2</span><span class="p">[</span><span class="mi">0</span><span class="p">],</span> <span class="n">r2</span><span class="p">[</span><span class="o">-</span><span class="mi">1</span><span class="p">]]</span>
        <span class="k">for</span> <span class="n">c</span> <span class="ow">in</span> <span class="n">corners</span><span class="p">:</span>
            <span class="n">a</span><span class="p">,</span> <span class="n">b</span> <span class="o">=</span> <span class="n">c</span>
            <span class="k">if</span> <span class="n">a</span> <span class="o">==</span> <span class="n">b</span><span class="p">:</span>
                <span class="k">return</span> <span class="mi">1</span>
        <span class="k">return</span> <span class="mi">0</span>
</pre></div>



Let's simulate this game 10.000 times and save in a variable (cnt) whether or not we found a double in any corner.

<div class="highlight"><pre><span class="n">d</span> <span class="o">=</span> <span class="n">Domino</span><span class="p">()</span>
<span class="n">num_exp</span> <span class="o">=</span> <span class="mi">10000</span>

<span class="n">cnt</span> <span class="o">=</span> <span class="mi">0</span>  <span class="c"># counter whether there is a double in any corner</span>
<span class="k">for</span> <span class="n">n</span> <span class="ow">in</span> <span class="nb">xrange</span><span class="p">(</span><span class="mi">1</span><span class="p">,</span> <span class="n">num_exp</span><span class="p">):</span>
    <span class="n">arrangedTiles</span> <span class="o">=</span> <span class="n">d</span><span class="o">.</span><span class="n">arrangeTiles</span><span class="p">(</span><span class="n">d</span><span class="o">.</span><span class="n">dominoSet</span><span class="p">,</span> <span class="mi">7</span><span class="p">)</span>
    <span class="n">cnt</span> <span class="o">+=</span> <span class="n">d</span><span class="o">.</span><span class="n">is_double_any_corner</span><span class="p">(</span><span class="n">arrangedTiles</span><span class="p">)</span>
<span class="k">print</span> <span class="s">&quot;The probability of a double in any corner is: </span><span class="si">%.2f</span><span class="s">&quot;</span> <span class="o">%</span> <span class="p">(</span><span class="n">cnt</span> <span class="o">/</span> <span class="nb">float</span><span class="p">(</span><span class="n">num_exp</span><span class="p">))</span>
</pre></div>


    The probability of a double in any corner is: 0.70


Voila! again 70% of the time you will find at least one double in any of the 4 corners.

Now, the money - let's say you bet $5 each time so if you play 20 times a perfect run will a 100 dollars in your pocket

<div class="highlight"><pre><span class="n">money</span> <span class="o">=</span> <span class="mi">0</span>
<span class="k">for</span> <span class="n">p</span> <span class="ow">in</span> <span class="nb">xrange</span><span class="p">(</span><span class="mi">1</span><span class="p">,</span><span class="mi">21</span><span class="p">):</span>
    <span class="n">arrangedTiles</span> <span class="o">=</span> <span class="n">d</span><span class="o">.</span><span class="n">arrangeTiles</span><span class="p">(</span><span class="n">d</span><span class="o">.</span><span class="n">dominoSet</span><span class="p">,</span> <span class="mi">7</span><span class="p">)</span>
    <span class="n">is_double</span><span class="o">=</span> <span class="n">d</span><span class="o">.</span><span class="n">is_double_any_corner</span><span class="p">(</span><span class="n">arrangedTiles</span><span class="p">)</span>
    <span class="k">if</span> <span class="n">is_double</span><span class="o">==</span><span class="mi">1</span><span class="p">:</span>
        <span class="n">money</span><span class="o">+=</span><span class="mi">5</span>
        <span class="k">print</span> <span class="s">&quot;I win a fiver :-)&quot;</span>
    <span class="k">else</span><span class="p">:</span>
        <span class="n">money</span><span class="o">-=</span><span class="mi">5</span>
        <span class="k">print</span> <span class="s">&quot;I lose a fiver :-(&quot;</span>

<span class="k">print</span> <span class="s">&quot; My winnings are: $</span><span class="si">%s</span><span class="s">&quot;</span> <span class="o">%</span> <span class="p">(</span><span class="n">money</span><span class="p">)</span>
</pre></div>


    I win a fiver :-)
    I win a fiver :-)
    I lose a fiver :-(
    I lose a fiver :-(
    I win a fiver :-)
    I win a fiver :-)
    I win a fiver :-)
    I win a fiver :-)
    I win a fiver :-)
    I win a fiver :-)
    I win a fiver :-)
    I win a fiver :-)
    I win a fiver :-)
    I win a fiver :-)
    I lose a fiver :-(
    I win a fiver :-)
    I win a fiver :-)
    I win a fiver :-)
    I win a fiver :-)
    I win a fiver :-)
     My winnings are: $70


$70 is not bad if you started from nothing. Unfortunately this is  only one of many possible outcomes that why is always important to calculate your confidence interval

<div class="highlight"><pre><span class="c"># bootstrapped confidence interval</span>
<span class="n">plist</span> <span class="o">=</span> <span class="p">[]</span>
<span class="c"># 1000 samples of size 1000</span>
<span class="n">runs</span> <span class="o">=</span> <span class="mi">1000</span>
<span class="n">sample</span> <span class="o">=</span> <span class="mi">1000</span>
<span class="k">for</span> <span class="n">i</span> <span class="ow">in</span> <span class="nb">xrange</span><span class="p">(</span><span class="mi">1</span><span class="p">,</span> <span class="n">sample</span><span class="p">):</span>
    <span class="n">cnt2</span> <span class="o">=</span> <span class="mi">0</span>
    <span class="k">for</span> <span class="n">n</span> <span class="ow">in</span> <span class="nb">xrange</span><span class="p">(</span><span class="mi">1</span><span class="p">,</span> <span class="n">runs</span><span class="p">):</span>
        <span class="n">arrangedTiles</span> <span class="o">=</span> <span class="n">d</span><span class="o">.</span><span class="n">arrangeTiles</span><span class="p">(</span><span class="n">d</span><span class="o">.</span><span class="n">dominoSet</span><span class="p">,</span> <span class="mi">7</span><span class="p">)</span>
        <span class="n">cnt2</span> <span class="o">+=</span> <span class="n">d</span><span class="o">.</span><span class="n">is_double_any_corner</span><span class="p">(</span><span class="n">arrangedTiles</span><span class="p">)</span>
    <span class="n">plist</span><span class="o">.</span><span class="n">append</span><span class="p">(</span><span class="n">cnt2</span> <span class="o">/</span> <span class="nb">float</span><span class="p">(</span><span class="n">runs</span><span class="p">))</span>
<span class="n">p</span> <span class="o">=</span> <span class="nb">sum</span><span class="p">(</span><span class="n">plist</span><span class="p">)</span> <span class="o">/</span> <span class="n">sample</span>
<span class="n">i</span> <span class="o">=</span> <span class="mf">1.96</span> <span class="o">*</span> <span class="n">sqrt</span><span class="p">(</span><span class="n">p</span> <span class="o">*</span> <span class="p">(</span><span class="mi">1</span> <span class="o">-</span> <span class="n">p</span><span class="p">)</span> <span class="o">/</span> <span class="n">sample</span><span class="p">)</span>
<span class="k">print</span> <span class="s">&quot;95</span><span class="si">%%</span><span class="s"> Confindence Interval (</span><span class="si">%.3f</span><span class="s">, </span><span class="si">%.3f</span><span class="s">)&quot;</span> <span class="o">%</span> <span class="p">(</span><span class="n">p</span> <span class="o">-</span> <span class="n">i</span><span class="p">,</span> <span class="n">p</span> <span class="o">+</span> <span class="n">i</span><span class="p">)</span>
</pre></div>


    95% Confindence Interval (0.677, 0.734)


Ok so now we know that 95% of time the probability of picking a double in 4 tries is between 67% and 73%. Pray to not fall into the other 5% ;-)

Hope you find it useful!!.

---
layout: post
title: "Converting Northing and Easting to Latitude and Longitude"
description: ""
category: python
tags: [python, pyproj, GIS, openCode Point, Ordnance Survey, OS OpenData, pandas]
---
{% include JB/setup %}


I am not an expert on GIS but I like to think of myself as a geomapping  enthusiastic. I recently downloaded Code-Point® Open file from the [Ordnance Survey](https://www.ordnancesurvey.co.uk/opendatadownload/products.html), a CSV file that provides a precise geographic location for each postcode unit in Great Britain. The product is a CSV file containing postcodes, grid references, NHS® health and regional health authority codes, administrative ward, district, county and country area codes.

The geo coordinates of this file come as Easting & Northing. From Wikipedia "*the terms easting and northing are geographic Cartesian coordinates for a point. Easting refers to the eastward-measured distance (or the x-coordinate), while northing refers to the northward-measured distance (or the y-coordinate)*".

Unfortunately things like Google Maps, Google Earth & others use as internal coordinate system the geographic coordinates (latitude/longitude) on the World Geodetic System of 1984 (WGS84) datum so in other to use Code-Point® Open file on one the above tools I had to convert eastings & northings to WGS84.

Thus I scanned the internet for possible solutions to my dilemma but none of them where exactly what I needed it. Some options where to use well-known APIs that will do the conversion for you, but my problem was I had 1.7 million postcodes so this wasn't an option really.

I also looked for python modules and read about Fiona and Shapely but none of them had the solution (probably due to my lack of knowledge on the subject) I was looking for. 

Then I came across a python package called **Pyproj** which along with **Pandas** solved my problem easily (well, after couple of days of research).

Here it is my solution:

1. Download and unzip the Code-Point® Open file in your computer
2. Use the following this piece of code:

+ Import modules:

<div class="highlight"><pre><span class="kn">import</span> <span class="nn">os</span>
<span class="kn">import</span> <span class="nn">pandas</span> <span class="kn">as</span> <span class="nn">pd</span>
<span class="kn">import</span> <span class="nn">pyproj</span>
<span class="kn">import</span> <span class="nn">re</span>
</pre></div>


+ Read in all csv files into a Pandas Data Frame:

<div class="highlight"><pre><span class="n">listfiles</span> <span class="o">=</span> <span class="n">os</span><span class="o">.</span><span class="n">listdir</span><span class="p">(</span><span class="s">&quot;codepo_gb/Data&quot;</span><span class="p">)</span>
<span class="n">pieces</span> <span class="o">=</span> <span class="p">[]</span>
<span class="n">columns</span> <span class="o">=</span> <span class="p">[</span><span class="s">&#39;pstcode&#39;</span><span class="p">,</span><span class="s">&#39;positional_quality_indicator&#39;</span><span class="p">,</span><span class="s">&#39;eastings&#39;</span><span class="p">,</span><span class="s">&#39;northings&#39;</span><span class="p">,</span><span class="s">&#39;country_code&#39;</span><span class="p">,</span><span class="s">&#39;nhs_regional_ha_code&#39;</span><span class="p">,</span>
           <span class="s">&#39;nhs_ha_code&#39;</span><span class="p">,</span><span class="s">&#39;admin_county_code&#39;</span><span class="p">,</span><span class="s">&#39;admin_district_code&#39;</span><span class="p">,</span><span class="s">&#39;admin_ward_code&#39;</span><span class="p">]</span>
<span class="k">for</span> <span class="n">f</span> <span class="ow">in</span> <span class="n">listfiles</span><span class="p">:</span>
  <span class="n">path</span> <span class="o">=</span> <span class="s">&quot;codepo_gb/Data/</span><span class="si">%s</span><span class="s">&quot;</span> <span class="o">%</span> <span class="n">f</span>
  <span class="n">frame</span><span class="o">=</span><span class="n">pd</span><span class="o">.</span><span class="n">read_csv</span><span class="p">(</span><span class="n">path</span><span class="p">,</span> <span class="n">names</span> <span class="o">=</span> <span class="n">columns</span><span class="p">)</span>
  <span class="n">frame</span><span class="p">[</span><span class="s">&#39;filename&#39;</span><span class="p">]</span><span class="o">=</span><span class="n">f</span>
  <span class="n">pieces</span><span class="o">.</span><span class="n">append</span><span class="p">(</span><span class="n">frame</span><span class="p">)</span>
    
<span class="n">postcodes</span> <span class="o">=</span> <span class="n">pd</span><span class="o">.</span><span class="n">concat</span><span class="p">(</span><span class="n">pieces</span><span class="p">,</span> <span class="n">ignore_index</span><span class="o">=</span><span class="bp">True</span><span class="p">)</span>
</pre></div>



+ Check that Data frame postcodes has been created suscessfully:

<div class="highlight"><pre><span class="n">postcodes</span>
</pre></div>


<pre>
    <class pandas.core.frame.DataFrame>
    Int64Index: 1687605 entries, 0 to 1687604
    Data columns:
    pstcode                         1687605  non-null values
    positional_quality_indicator    1687605  non-null values
    eastings                        1687605  non-null values
    northings                       1687605  non-null values
    country_code                    1687605  non-null values
    nhs_regional_ha_code            1440838  non-null values
    nhs_ha_code                     1596706  non-null values
    admin_county_code               636420  non-null values
    admin_district_code             1687514  non-null values
    admin_ward_code                 1687514  non-null values
    filename                        1687605  non-null values
    dtypes: int64(3), object(8)
</pre>


Now here it is a quick example of converting eastings and northings to latitude and logitude and further down is the same piece of code wrapped into a function. I also extracting the area level from the post code.

<div class="highlight"><pre><span class="n">bng</span> <span class="o">=</span> <span class="n">pyproj</span><span class="o">.</span><span class="n">Proj</span><span class="p">(</span><span class="n">init</span><span class="o">=</span><span class="s">&#39;epsg:27700&#39;</span><span class="p">)</span>
<span class="n">wgs84</span> <span class="o">=</span> <span class="n">pyproj</span><span class="o">.</span><span class="n">Proj</span><span class="p">(</span><span class="n">init</span><span class="o">=</span><span class="s">&#39;epsg:4326&#39;</span><span class="p">)</span>
<span class="c"># AL1 1AB - pyproj.transform(from,to,easting,northing)</span>
<span class="n">lon</span><span class="p">,</span><span class="n">lat</span> <span class="o">=</span> <span class="n">pyproj</span><span class="o">.</span><span class="n">transform</span><span class="p">(</span><span class="n">bng</span><span class="p">,</span><span class="n">wgs84</span><span class="p">,</span> <span class="mi">394251</span><span class="p">,</span> <span class="mi">806376</span><span class="p">)</span>
<span class="k">print</span> <span class="n">lon</span><span class="p">,</span> <span class="n">lat</span>
</pre></div>


    -2.0966478976 57.1482316621

<div class="highlight"><pre><span class="n">sample</span> <span class="o">=</span> <span class="n">postcodes</span>
</pre></div>



<div class="highlight"><pre><span class="k">def</span> <span class="nf">proj_transform</span><span class="p">(</span><span class="n">df</span><span class="p">):</span>
    <span class="n">bng</span> <span class="o">=</span> <span class="n">pyproj</span><span class="o">.</span><span class="n">Proj</span><span class="p">(</span><span class="n">init</span><span class="o">=</span><span class="s">&#39;epsg:27700&#39;</span><span class="p">)</span>
    <span class="n">wgs84</span> <span class="o">=</span> <span class="n">pyproj</span><span class="o">.</span><span class="n">Proj</span><span class="p">(</span><span class="n">init</span><span class="o">=</span><span class="s">&#39;epsg:4326&#39;</span><span class="p">)</span>
    <span class="n">lon</span><span class="p">,</span><span class="n">lat</span> <span class="o">=</span> <span class="n">pyproj</span><span class="o">.</span><span class="n">transform</span><span class="p">(</span><span class="n">bng</span><span class="p">,</span><span class="n">wgs84</span><span class="p">,</span><span class="n">df</span><span class="p">[</span><span class="s">&#39;eastings&#39;</span><span class="p">],</span> <span class="n">df</span><span class="p">[</span><span class="s">&#39;northings&#39;</span><span class="p">])</span>
    <span class="n">area_pattern</span> <span class="o">=</span> <span class="s">r&#39;([a-zA-Z]+)(?=\d)&#39;</span>
    <span class="c">#area = re.search(r&#39;[a-zA-Z]+(?=\d)&#39;,df[&#39;pstcode&#39;].str[:-3]).group()</span>
    
    <span class="n">df</span><span class="p">[</span><span class="s">&#39;lat&#39;</span><span class="p">]</span> <span class="o">=</span> <span class="n">lat</span>
    <span class="n">df</span><span class="p">[</span><span class="s">&#39;lon&#39;</span><span class="p">]</span> <span class="o">=</span> <span class="n">lon</span>
    <span class="n">df</span><span class="p">[</span><span class="s">&#39;area&#39;</span><span class="p">]</span> <span class="o">=</span> <span class="n">df</span><span class="p">[</span><span class="s">&#39;pstcode&#39;</span><span class="p">]</span><span class="o">.</span><span class="n">str</span><span class="o">.</span><span class="n">findall</span><span class="p">(</span><span class="n">area_pattern</span><span class="p">,</span> <span class="n">flags</span><span class="o">=</span><span class="n">re</span><span class="o">.</span><span class="n">IGNORECASE</span><span class="p">)</span><span class="o">.</span><span class="n">str</span><span class="p">[</span><span class="mi">0</span><span class="p">]</span>
    <span class="k">return</span> <span class="n">df</span>
    
</pre></div>

I now create a new data frame called sample2 with 3 new columns: latitude, longitud and area postcode level.

<div class="highlight"><pre><span class="n">sample2</span> <span class="o">=</span> <span class="n">proj_transform</span><span class="p">(</span><span class="n">sample</span><span class="p">)</span>
</pre></div>


With my new dataframe I then export it to a csv file and job done.

<div class="highlight"><pre><span class="n">sample2</span><span class="o">.</span><span class="n">to_csv</span><span class="p">(</span><span class="s">&#39;uk-postcodes-latlng.csv&#39;</span><span class="p">,</span><span class="n">index</span><span class="o">=</span><span class="bp">False</span><span class="p">)</span>
</pre></div>


I have been playing with QuatumGIS but not enough to add something interesting yet so I'll be  posting about producing shapefiles than could be use on Google maps soon.

I am done for now.


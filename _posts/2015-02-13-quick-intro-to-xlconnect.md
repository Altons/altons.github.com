---
layout: post
title: "Quick Intro to XLConnect"
description: ""
category: r
tags: []
---
{% include JB/setup %}

This doc is a quick tutorial for the **XLConnect R** package. **XLConnect** allows for reading, writing and manipulating Microsoft Excel files from within R.

**XLConnect** allows you to produce formatted Excel reports, including graphics, straight from within R.This enables automation of manual formatting and reporting processes. Reading and writing named ranges enables you to process complex inputs and outputs in an efficient way.

###Main Features


- Reading & writing of Excel worksheets (via data.frames)
- Reading & writing of named ranges (via data.frames)
- Creating, removing, renaming and cloning worksheets
- Adding graphics
- Specifying cellstyles: data formats, borders, back- and foreground fill color, fill pattern, text wrapping
- Controlling sheet visibility
- Defining column width and row height
- Merging/unmerging cells
- Setting/getting cell formulas
- Defining formula recalculation behavior (when workbooks are opened)
- Setting auto-filters
- Style actions: controlling application of cell styles when writing (e.g. when using templates)
- Defining behavior when error cells are encountered


<a id="index"></a>

###Index

1. [Package Requirements](#req)
2. [Installing XLConnect](#install)
3. [Loading XLConnect](#basic)
4. [Loading/Creating and Excel workbook](#load)
5. [Creating Sheets](#sheet)
6. [Writing data to WorkSheets](#worksheet)
7. [Saving workbooks](#savebook)
8. [Basic Example](#example)
9. [Reading data from Worksheet](#readdata)
10. [Read Data straight from File](#readfile)
11. [Write Data straight to File](#writefile)
12. [Named Regions](#regions)
13. [Further readings](#further)
14. [Session Info](#session)

<a id="req"></a>

###Package Requirements

**XLConnect** is completely cross-platform and as such runs under Windows, Unix/Linux and Mac (32- and
64-bit). It does not require an installation of Microsoft Excel, or any special drivers.

All you need to use XLConnect are the following:

- R, version 2.10.0 or higher
- Java Runtime Environment (JRE), version 6.0 or higher

[back to index](#index)

<a id="install"></a>

###Installing XLConnect


{% highlight r %}
install.packages("XLConnect")
{% endhighlight %}
[back to index](#index)



<a id="basic"></a>

###Loading XLConnect


{% highlight r %}
require(XLConnect)
{% endhighlight %}



{% highlight text %}
## Loading required package: XLConnect
## Loading required package: XLConnectJars
## XLConnect 0.2-9 by Mirai Solutions GmbH [aut],
##   Martin Studer [cre],
##   The Apache Software Foundation [ctb, cph] (Apache POI, Apache Commons
##     Codec, XML Commons External Components XML APIs),
##   Stephen Colebourne [ctb, cph] (Joda-Time Java library),
##   Metastuff, Ltd. [ctb, cph] (dom4j)
## http://www.mirai-solutions.com ,
## http://miraisolutions.wordpress.com
{% endhighlight %}
[back to index](#index)

<a id="load"></a>

###Loading/Creating and Excel workbook

Whether you are creating or manipulating an existing is always a good practice to assign the workbook to a variable for further manipulation.


{% highlight r %}
# Load and Excel workbook (create if not existing)
wb = loadWorkbook("../data/xlconnect1.xlsx",create=T)
{% endhighlight %}
[back to index](#index)

<a id="sheet"></a>

###Creating Sheets

**createSheet()** creates a sheet of a chosen name in the workbook specified as the object argument.

Let's create 4 sheets for the following data: cars, iris, rivers and quakes

{% highlight r %}
createSheet(wb,"cars stats")
createSheet(wb,"Iris")
createSheet(wb,"Rivers")
createSheet(wb,"Quakes location Fiji")
{% endhighlight %}
[back to index](#index)

<a id="worksheet"></a>

###Writing data to WorkSheets
**writeWorksheet()** writes data into a worksheet (name or index specified as the sheet argument) of an Excel workbook (object). The startRow and startCol are both 1 by default, meaning that if they are not explicitly specified, the data will start being filled into the A1 cell of the worksheet.


{% highlight r %}
writeWorksheet(wb,cars,"cars stats")
writeWorksheet(wb,iris,"Iris")
writeWorksheet(wb,rivers,"Rivers")
writeWorksheet(wb,quakes,"Quakes location Fiji")
{% endhighlight %}

[back to index](#index)

<a id="savebook"></a>

###Saving Workbooks

Until now, everything you've done is in memory so you need to save your work to the disk. **saveWorkbook()** saves a workbook to the corresponding Excel file and writes the file to disk.


{% highlight r %}
saveWorkbook(wb)
# Alternatively you can pass a filename
# saveWorkbook(wb,'path_to_file')
{% endhighlight %}

[back to index](#index)

<a id="example"></a>

###Basic Example

A complete basic example

{% highlight r %}
wb1 = loadWorkbook("../data/example1.xlsx", create = TRUE)
createSheet(wb1, name = "chickSheet")
writeWorksheet(wb1, ChickWeight, sheet = "chickSheet", startRow = 3, startCol = 4)
saveWorkbook(wb1)
{% endhighlight %}

[back to index](#index)

<a id="readdata"></a>

###Read Data from Worksheet

**readWorksheet()** allows for reading data from a workbook that has been previously loaded and is passed
as the object argument. The name or index of the worksheet to read from should be passed as the sheet
argument. The startRow and startCol arguments specify the location of the top left corner of data to
be read, while endRow and endCol specify the bottom right corner. If ``header = TRUE``, the first row is
interpreted as column names of the data.frame object read in.


{% highlight r %}
wb2 = loadWorkbook("../data/example1.xlsx", create = F)
chick = readWorksheet (wb2,"chickSheet",3, 4, 581,7 ,header = TRUE)
summary(chick)
{% endhighlight %}



{% highlight text %}
##      weight         Time         Chick               Diet          
##  Min.   : 35   Min.   : 0.0   Length:578         Length:578        
##  1st Qu.: 63   1st Qu.: 4.0   Class :character   Class :character  
##  Median :103   Median :10.0   Mode  :character   Mode  :character  
##  Mean   :122   Mean   :10.7                                        
##  3rd Qu.:164   3rd Qu.:16.0                                        
##  Max.   :373   Max.   :21.0
{% endhighlight %}

[back to index](#index)

<a id="readfile"></a>

###Read Data straight from File

readWorksheetFromFile() allows for reading data from a workbook with one call, without loading the workbook first. The ``file`` argument is the path of the file to read from. All arguments of the ``readWorksheet()`` function can be passed within the ``’...’`` argument.


{% highlight r %}
iris = readWorksheetFromFile("../data/xlconnect1.xlsx",sheet="iris", header=T)
summary(iris)
{% endhighlight %}



{% highlight text %}
##   Sepal.Length   Sepal.Width    Petal.Length   Petal.Width 
##  Min.   :4.30   Min.   :2.00   Min.   :1.00   Min.   :0.1  
##  1st Qu.:5.10   1st Qu.:2.80   1st Qu.:1.60   1st Qu.:0.3  
##  Median :5.80   Median :3.00   Median :4.35   Median :1.3  
##  Mean   :5.84   Mean   :3.06   Mean   :3.76   Mean   :1.2  
##  3rd Qu.:6.40   3rd Qu.:3.30   3rd Qu.:5.10   3rd Qu.:1.8  
##  Max.   :7.90   Max.   :4.40   Max.   :6.90   Max.   :2.5  
##    Species         
##  Length:150        
##  Class :character  
##  Mode  :character  
##                    
##                    
## 
{% endhighlight %}

[back to index](#index)

<a id="writefile"></a>

###Write Data straight to File

``writeWorksheetToFile()`` is a wrapper function, calling ``loadWorkbook()``, ``createSheet()`` and ``saveWorkbook()``
functions subsequently. It therefore allows for writing data into worksheets of an Excel file in one call.
The file is the path name of the file to write to. The styleAction argument controls the application of cell
styles when writing to Excel (type ``?setStyleAction`` into the console to see the supported styles). Within
the ``’...’`` argument, arguments for the writeWorksheet function can be passed.


{% highlight r %}
# Copy twice the iris dataset in 2 different worksheets
writeWorksheetToFile("../data/example2.xlsx",data=iris,sheet="iris2")
writeWorksheetToFile("../data/example2.xlsx",data=iris,sheet="iris3")
{% endhighlight %}

[back to index](#index)

<a id="regions"></a>

###Named Regions

A Named Range or region is way to describe your formulas. So you don't have to have this in a cell:

``= SUM(B2:B4)``

You can replace the cell references between the round brackets. You replace them with a descriptive name, all of your own. So you could have this, instead:

``= SUM(Monthly_Totals)``

Behind the Monthly_Totals, though, Excel is hiding the cell references. We'll see how it works using XLConnect.

There 2 ways to do named regions, Step by Step and straight to File methods. When using a template that later on will be renamed to a new filename I suggest using the Step by Step option

To ilustrate how easy is to use named regions in XLconnect let's create a spreadsheet (`invoice_template.xlsx` for example) with some named regions to fill specific areas of the invoice with data.

The file comes with the following predefined named regions:

{% highlight r %}
  [Invoice Number]=rngInvoice
  [Date]=invoice_date
  [Contact]=bill_contact
  [Company]=bill_company
{% endhighlight %}



We also are going to create some named regions for description, units, Cost per Unit.


{% highlight r %}
#XLConnect assume that only data frames (or list of dfs) are passed to named regions
## Let's create some dummy data

### Invoice ID
inv_id = data.frame(id=527)
### Invoice date
inv_date = data.frame(id='17/11/2014')
### Bill Contact
inv_contact = data.frame(id='Chuck Norris')
### Company name
inv_company = data.frame(id='RoundHouse Kick')
### Items
items =data.frame(description=c("Nunchakus","Karate GI","Black Belt"),unit=c(3,5,1),price=c(3.95,45,7))

### Load xls template in memory
invoice_wb = loadWorkbook("../data/invoice_template.xlsx")
###Using XLConnect you normally overwrite the cell styles unless you set the style action to be "none" using
setStyleAction(invoice_wb,XLC$"STYLE_ACTION.NONE")

### Insert inv_id in the template
writeNamedRegion(invoice_wb, inv_id, name = "rngInvoice", header=F)
### Insert inv_date in the template
writeNamedRegion(invoice_wb, inv_date, name = "invoice_date", header=F)
### Insert Bill contact in the template
writeNamedRegion(invoice_wb, inv_contact, name = "bill_contact", header=F)
### Insert Company name in the template
writeNamedRegion(invoice_wb, inv_company, name = "bill_company", header=F)

##Let create some named regions manually
### Items
createName(invoice_wb, name = "items", formula = "invoice!$A$16", overwrite = TRUE)
writeNamedRegion(invoice_wb, items, name = "items", header=F)

### Since we have a lot of calculation inside the invoice template we need to force the 
### calculation after data has been inserted/updated
setForceFormulaRecalculation(invoice_wb, sheet = 1, TRUE)
###Save our template to a different file
saveWorkbook(invoice_wb, "../data/invoice_20141117.xlsx")
{% endhighlight %}

[back to index](#index)

<a id="further"></a>

###Further Readings

- [http://cran.r-project.org/web/packages/XLConnect/vignettes/XLConnect.pdf](http://cran.r-project.org/web/packages/XLConnect/vignettes/XLConnect.pdf)

[back to index](#index)

<a id="session"></a>

###R Session


{% highlight r %}
sessionInfo()
{% endhighlight %}



{% highlight text %}
## R version 3.1.1 (2014-07-10)
## Platform: x86_64-apple-darwin13.1.0 (64-bit)
## 
## locale:
## [1] en_GB.UTF-8/en_GB.UTF-8/en_GB.UTF-8/C/en_GB.UTF-8/en_GB.UTF-8
## 
## attached base packages:
## [1] stats     graphics  grDevices utils     datasets  methods   base     
## 
## other attached packages:
## [1] XLConnect_0.2-9     XLConnectJars_0.2-9 knitr_1.6          
## 
## loaded via a namespace (and not attached):
## [1] evaluate_0.5.5 formatR_0.10   rJava_0.9-6    stringr_0.6.2 
## [5] tools_3.1.1
{% endhighlight %}

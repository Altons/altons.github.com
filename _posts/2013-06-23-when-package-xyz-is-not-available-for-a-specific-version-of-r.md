---
layout: post
title: "When package XYZ is not available for a specific version of R"
description: ""
category: r
tags: [rstats,R,tutorial]
---
{% include JB/setup %}



When you try to install a package in R and you get the following message:

```r
Warning in install.packages :
  package XYZ is not available (for R version x.xx.x)
```

is normally down to:

* CRAN mirror is out-of-date in which case you can change the repo by using:

```r
install.packages("XYZ",repos = "http://cran.us.r-project.org/")
```

* Package author have not created a binary package for the specific version of R.

If the latter, here they are a set of simple steps to compile an R package from source.

1. Go to package's site, e.g. [ggplot2](http://cran.r-project.org/web/packages/ggplot2/index.html)
2. Download the package source for your OS. For MacOS would be `ggplot2_0.9.3.1.tgz`
3. Open an R session and type & run the following command:

```r
install.packages("/path/to/file/packageName.tgz",repos = NULL, type="source")
```
and Voila! your package is now available for your version of R.

> Note: If package has dependencies you may try to install them using the standard install.packages() and if they are not available then do the above steps.



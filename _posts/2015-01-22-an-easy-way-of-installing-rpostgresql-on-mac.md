---
layout: post
title: "An easy way of installing RpostgreSQL on Mac"
description: ""
category: r
tags: []
---
{% include JB/setup %}


In the last few months I have drastically changed the way I use R. One of the biggest changes was switching to [dplyr]() for the  data management process. The philosophy of **dplyr** reminds me a lot the way the *SAS Data Step* works, although they are syntactically different   they are quite similar in terms of data manipulation. I do all my work ona Mac BookPro with Mavericks.

**dplyr** comes with a battery of connectors for multiple databases such as: MySQL, MariaDB, SQLite, Postgresql, etc. I recently needed to connect to a *postgres* DB in Heroku and decided to give it a go to `src_postgres` to make my connection.


So I did

```r
library(dplyr)
db = src_postgres(dbname = 'xxxxxxxx', 
             host = 'ec2-54-83-201-96.compute-1.amazonaws.com',
              port = 5432,
              user = 'xxxxxxx',
              password = 'xxxxxxxx')
```

and got the following error:

```r
Loading required package: RPostgreSQL
Error: RPostgreSQL package required to connect to postgres db
In addition: Warning message:
In library(package, lib.loc = lib.loc, character.only = TRUE, logical.return = TRUE,  :
  there is no package called ‘RPostgreSQL’
> install.packages('RPostgreSQL')
Installing package into ‘/Users/altons/.rpackages’
(as ‘lib’ is unspecified)

   package ‘RPostgreSQL’ is available as a source package but not as a binary

Warning in install.packages :
  package ‘RPostgreSQL’ is not available (for R version 3.1.1)
```

Ok, I was missing the **RPostgreSQL** package  and it was not available for my version of R - not a biggie since I can download the source package and install it as usual.

But reading at [CRAN](http://cran.r-project.org/web/packages/RPostgreSQL/index.html ) I realised that in order to build and install this package from source, PostgreSQL itself must be present in my system (RPostgreSQL requires some of the PG libraries to compile the package).

I do have *postgres* but it is running on a vagrant machine and the solutions I've seen use **ports** in order to install *postgres* on Mac which was not an option since I only use homebrew.

So after a couple of hours of google research I came up with this solution which worked well for me.

Here are the steps:

###postgresapp.com

Go to [http://postgresapp.com/](http://postgresapp.com/) and download the postgresapp. Just download, drag to the applications folder, and double-click and you have PostgreSQL 9.4 up and running with no fuss.

Definitely easier than playing with Mac *ports*!

###Download RPostgreSQL source package

Run the following command from terminal:

```bash
$ svn checkout http://rpostgresql.googlecode.com/svn/trunk/ rpostgresql-read-only

```

###pg_config

In order to compile the source package we need `pg_config` which is locate in the `bin/` folder of Postgres.

Just do `which psql` in terminal to find out where postgresapp is installed. In my case is here:

`/Applications/Postgres.app/Contents/MacOS/bin/psql`

Keep that path at hand as we are going to need it pretty soon.

###RStudio

Here we need 2 packages: `DBI` and `devtools` - `DBI` is a dependency for RPostgreSQL in order to work properly and `devtools` is the package we are going to use to compile the source code.

So if you don't have them go ahead and install:

```r
install.packages('DBI')
install.packages('devtools')
```

> Before installing devtools make sure you have Xcode installed in your system (you can get it free from the Mac App Store.)

Now just open an RStudio and run the following commands:


```r
devtools::add_path('/Applications/Postgres.app/Contents/MacOS/bin/')
devtools::install('/path/to/rpostgresql-read-only/RPostgreSQL')

```
 The first command add the postgres `bin/` folder to devtools and the second command installs the RPostgreSQL package.

 After that you should be able to load the package as per usual:

 ```r
 library(RPostgreSQL)
Warning message:
package ‘DBI’ was built under R version 3.1.2 
```


My `sessionInfo()`

```r
R version 3.1.1 (2014-07-10)
Platform: x86_64-apple-darwin13.1.0 (64-bit)

locale:
[1] en_GB.UTF-8/en_GB.UTF-8/en_GB.UTF-8/C/en_GB.UTF-8/en_GB.UTF-8

attached base packages:
[1] stats     graphics  grDevices utils     datasets  methods   base     

other attached packages:
[1] RPostgreSQL_0.5-1 DBI_0.3.1         dplyr_0.4.1      

loaded via a namespace (and not attached):
[1] assertthat_0.1 magrittr_1.0.1 parallel_3.1.1 R6_2.0.1       Rcpp_0.11.2   
[6] tools_3.1.1   
```


Enjoy!!









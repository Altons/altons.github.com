---
layout: post
title: "Conda Commands"
description: ""
category: Python
tags: [Conda, ipython, science]
---
{% include JB/setup %}


CREATE CONDA ENV
--

```
conda create --quiet --yes -n envName python
```


DELETE CONDA ENV
--

```
conda env --remove -n envName
```

INSTALL PACKAGE FROM ANACONDA REPO
--

```
conda install pkgName=ver
```

INSTALL PACKAGE FROM PYTHON REPO
--
```
conda pip install pkgName
```

ACTIVATE CONDA ENVIRONMENT
--
```
source activate <env name>
```
DEACTIVATE CONDA ENVIRONMENT
-
```
source deactivate
```

LIST CONDA ENVIRONMENT
-
```
conda info -e
```

LIST AVAILABLE PACKAGE IN ENV
-

```
conda list -c
```

UPDATE ANACONDA
-


You can update your entire Anaconda distribution with:

```
conda update conda
conda update anaconda
```

This will update openssl to the latest version (1.0.1g) in your current active environment.

If you'd like to only update a single environment, use:

```
conda update -n ENVIRONMENT_NAME openssl
```


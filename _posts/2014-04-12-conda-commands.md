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

```bash
conda create --quiet --yes -n envName python
```


DELETE CONDA ENV
--

```bash
conda env --remove -n envName
```

INSTALL PACKAGE FROM ANACONDA REPO
--

```bash
conda install pkgName=ver
```

INSTALL PACKAGE FROM PYTHON REPO
--
```bash
conda pip install pkgName
```

ACTIVATE CONDA ENVIRONMENT
--
```bash
source activate <env name>
```

DEACTIVATE CONDA ENVIRONMENT
-

```bash
source deactivate
```

LIST CONDA ENVIRONMENT
-
```bash
conda info -e
```

LIST AVAILABLE PACKAGE IN ENV
-

```bash
conda list -c
```
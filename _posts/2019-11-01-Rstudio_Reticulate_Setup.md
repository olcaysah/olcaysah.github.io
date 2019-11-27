---
layout: archive
title: "RStudio Reticulate Setup"
modified: 2019-10-22
comments: true
share: true
gallery:
- url: /assets/images/unnamed-chunk-2-1.png
  image_path: /assets/images/unnamed-chunk-2-1.png
  alt: "Plot"
---

This code written in the Rstudio. Reticulate library enables to run Python codes in Rstudio.
I copied example from Reticulate manual. I will post my own examples soon.

R Code:
```r
    library(reticulate)
    use_condaenv("r-reticulate",required = T)
    py_run_string("import os as os")
    py_run_string("os.environ['QT_QPA_PLATFORM_PLUGIN_PATH'] = '../Local/Continuum/anaconda3/Library/plugins/platforms/'")
```
Python Code:
```python
    import pandas
    import matplotlib.pyplot as plt
    import numpy as np
    t = np.arange(0.0,2.0,0.01)
    s = 1 + np.sin(2*np.pi*t)
    plt.plot(t,s)
    plt.grid(True)
    plt.show()
```
{% include gallery id="gallery" %}

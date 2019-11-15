This code written in the Rstudio. Reticulate library enables to run Python codes in Rstudio.

R Code:

    library(reticulate)
    use_condaenv("r-reticulate",required = T)
    py_run_string("import os as os")
    py_run_string("os.environ['QT_QPA_PLATFORM_PLUGIN_PATH'] = '../Local/Continuum/anaconda3/Library/plugins/platforms/'")

Python Code:

    import pandas
    import matplotlib.pyplot as plt
    import numpy as np
    t = np.arange(0.0,2.0,0.01)
    s = 1 + np.sin(2*np.pi*t)
    plt.plot(t,s)
    plt.grid(True)
    plt.show()

<img src="unnamed-chunk-2-1.png" width="672" />

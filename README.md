# identifiability - Parameter identifiability analysis in Python

This module contains Custom functions for performing parameter identifiability
analysis to calculate and plot confidence intervals based on a profile-likelihood 
analysis. Adapted from [LMFIT](https://lmfit.github.io/lmfit-py/), with custom
functions to select the range for parameter scanning and for plotting the profile 
likelihood.

## Installation

`identifiability` is a pure-Python module. The latest development version can be 
installed with
```bash
$ pip install git+https://github.com/jmrohwer/identifiability
```

The latest stable release is available with
```bash
$ pip install https://github.com/jmrohwer/identifiability/archive/refs/tags/v0.1.zip
```

## Basic usage

For background, the reader is referred to the section on *Calculation of confidence 
intervals* in the [LMFIT documentation](https://lmfit.github.io/lmfit-py/confidence.html).

To start the indentifiability analysis, the user first needs to have performed a 
parameter estimation with LMFIT. The method for estimating confidence intervals 
takes an instantiated 
[LMFIT Mimimizer](https://lmfit.github.io/lmfit-py/fitting.html#lmfit.minimizer.Minimizer)
object and an 
[LMFIT mimimizer result](https://lmfit.github.io/lmfit-py/fitting.html#lmfit.minimizer.MinimizerResult)
object as input.

A typical workflow would entail:
```python
>>> from identifiability import conf_interval
>>> c = conf_interval(
        mini, result, prob=0.95, limits=0.5, log=False, points=11, return_CIclass=True
    )
>>> print c[0]  # OrderedDict of parameter names and corresponding confidence intervals
>>> c[1].plot_ci('a')  # plot confidence interval for parameter 'a'
```

### Docstring of the `conf_interval` method

```python
def conf_interval(
    minimizer,
    result,
    p_names=None,
    prob=0.95,
    limits=0.5,
    log=False,
    points=11,
    return_CIclass=False,
):
    """
    Calculate the confidence interval (CI) for parameters.
    
    The parameter for which the CI is calculated will be varied, while the
    remaining parameters are re-optimized to minimize the chi-square. The
    resulting chi-square is used to calculate the probability with a given
    statistic, i.e. chi-squared test.
    
    Parameters
    ----------
    minimizer : Minimizer
        The minimizer to use, holding objective function.
    result : MinimizerResult
        The result of running minimize().
    p_names : list, optional
        Names of the parameters for which the CI is calculated. If None
        (default), the CI is calculated for every parameter.
    prob : float, optional
        The probability for the confidence interval (<1). If None,
        the default is 0.95 (95 % confidence interval).
    limits : float, optional
        The limits (as a fraction of the original parameter value) within which 
        to vary the parameters for identifiability analysis (default is 0.5). 
        If ``log=False``, the parameter is varied from (1 - limits)*p to
        (1 + limits)*p, where p is the original value.
        If ``log=True``, the parameter is varied from p*limits to p/limits.
    log : bool, optional
        Whether to vary the parameter in a log (True) or a linear (False, 
        default) scale.
    points : int, optional
        The number of points for which to calculate the profile likelihood over
        the given parameter range.
    return_CIclass : bool, optional
        When true, return the instantiated ``ConfidenceInterval`` class to 
        access its methods directly (default=False).
    
    Returns
    -------
    output : dict
        A dictionary containing a list of ``(lower, upper)``-tuples containing 
        the confidence bounds for each parameter.
    ci : ``ConfidenceInterval`` instance, optional
        Instantiated ``ConfidenceInterval`` class to access the attached methods.
    """
```

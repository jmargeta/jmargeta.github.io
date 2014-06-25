---
layout: post
title:  "Cython vs Numba on Cython's own numpy example!"
date:   2014-01-21 00:13:10
categories: python
---

Looping with Python (with CPython interpreter) is slow.

[Cython][cython-page] is a wonderful tool that makes [working with numpy][cython-numpy] pretty simple and code execution much faster. A few days ago (2014-01-18) a new version 0.20 was released with [plenty of enhancements][cython-changes].

[Numba][numba-page] is a just in time python compiler from [Continuum Analytics][continuum-page] using LLVM designed to work well with numpy.

I was curious to see how Numba stands compared to the latest and shiniest Cython.
As a benchmark I chose Cython's own convolution example code and the naive python code that I autojited with Numba.


{% highlight python %}
import numpy as np
import numba
import convolve
import convolve_py

N=100
f = np.arange(N*N, dtype=np.int).reshape((N,N))
g = np.arange(81, dtype=np.int).reshape((9, 9))


python_convolve = convolve_py.naive_convolve
cython_convolve = convolve.naive_convolve
numba_convolve = numba.autojit(convolve_py.naive_convolve)


%timeit -n2 -r3 python_convolve(f, g)
%timeit -n2 -r3 cython_convolve(f, g)
%timeit -n2 -r3 numba_convolve(f, g)

# 2 loops, best of 3: 3.65 s per loop
# 2 loops, best of 3: 14.3 ms per loop
# 2 loops, best of 3: 3.51 ms per loop

# for N = 1000
# 2 loops, best of 3: 1.72 s per loop
# 2 loops, best of 3: 305 ms per loop
{% endhighlight %}

#Conclusion?
Numba takes directly the standard Python code and the JIT compiler handles the rest. It takes, however, quite some time to import numba itself compared to the swift Cython and Python modules.

The 1000x speedup of the Numba autojited naive python code code compared to pure Python and with 4x faster compared to the optimised Cython version is just wonderful.

Great job guys.


You can play with the notebook on [Wakari][wakari-notebook]

Check out also [Jake Vanderplas' blog][jake-article] on another cython - numba benchmarks.

[cython-page]: http://cython.org/
[cython-numpy]: http://docs.cython.org/src/tutorial/numpy.html
[cython-changes]: https://github.com/cython/cython/blob/master/CHANGES.rst
[numba-page]: http://numba.pydata.org/
[jake-article]: http://jakevdp.github.io/blog/2013/06/15/numba-vs-cython-take-2/
[continuum-page]: http://continuum.io/
[wakari-notebook]: https://www.wakari.io/sharing/bundle/jmargeta/Cython%20vs%20Numba
# 数学库

## Intel MKL

Intel Math Kernel Library（MKL）是一套针对Intel CPU高度优化的数学库，提供了一系列线性代数等计算密集型数学函数。很多高级语言如Anaconda版Python的numpy底层就使用了MKL进行加速，有近10倍左右性能提升。直接使用MKL，需要使用C/C++或Fortran。

加载MKL：

```bash
source /public/software/intel/etc/env.intel.2015
```

链接参数请使用 [Intel® Math Kernel Library Link Line Advisor][1]。如编译和链接过程中遇到任何问题，请联系我们获取帮助。

Fortran 90链接样例：

```bash
ifort example.f90 -lmkl_intel_lp64 -lmkl_sequential -lmkl_core -lpthread
```

## GSL C/C++数学库

GNU Scientific Library（GSL）是一款开源的C/C++语言数学库。

```bash
source /public/software/intel/etc/env.gsl.2.5
```

## FFTW 快速傅里叶变换

Fastest Fourier Transform in the West(FFTW)

```bash
source /public/software/intel/etc/env.fftw.3.3.8
```

[1]: https://software.intel.com/en-us/articles/intel-mkl-link-line-advisor

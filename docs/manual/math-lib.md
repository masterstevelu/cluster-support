# 数学库

## Intel MKL 

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

```bash
source /public/software/intel/etc/env.gsl.2.5
```

## FFTW 快速傅里叶变换

```bash
source /public/software/intel/etc/env.fftw.3.3.8
```

[1]: https://software.intel.com/en-us/articles/intel-mkl-link-line-advisor
## WARNING
This repo offers several version of the patch needed to enable CPU-specific compile option in the Linux kernel.  These are versioned based on which version of gcc you are using and also which version of the kernel you wish to build.

#Kernel_gcc_patch

This kernel patch adds additional CPU options to the Linux kernel accessible under:
 Processor type and features  --->
 Processor family --->

Why a specific patch?
The kernel uses its own set of CFLAGS, KCFLAGS. For exmaple, see:
* [arch/x86/Makefile](https://github.com/torvalds/linux/blob/master/arch/x86/Makefile)
* [arch/x86/Makefile_32.cpu](https://github.com/torvalds/linux/blob/master/arch/x86/Makefile_32.cpu)
* [arch/x86/Kconfig.cpu](https://github.com/torvalds/linux/blob/master/arch/x86/Kconfig.cpu)

<table>
  <tr>
    <th>CPU Family</th>
    <th>GCC Optimization</th>
  </tr>
  <tr>
    <td>Native optimizations autodetected by GCC</td>
    <td>-march=native</td>
  </tr>
  <tr>
    <td>AMD Improved K8-family</td>
    <td>-march=k8-sse3</td>
  </tr>
  <tr>
    <td>AMD K10-family</td>
    <td>-march=amdfam10</td>
  </tr>
  <tr>
    <td>AMD Family 10h (Barcelona)</td>
    <td>-march=barcelona</td>
  </tr>
  <tr>
    <td>AMD Family 14h (Bobcat)</td>
    <td>-march=btver1</td>
  </tr>
  <tr>
    <td>AMD Family 15h (Bulldozer)</td>
    <td>-march=bdver1</td>
  </tr>
  <tr>
    <td>AMD Piledriver Family 15h (Piledriver)</td>
    <td>-march=bdver2</td>
  </tr>
  <tr>
    <td>AMD Family 16h (Jaguar)</td>
    <td>-march=btver2</td>
  </tr>
  <tr>
    <td>Intel 1st Gen Core i3/i5/i7-family (Nehalem)</td>
    <td>-march=nehalem</td>
  </tr>
  <tr>
    <td>Intel 1.5 Gen Core i3/i5/i7-family (Westmere)</td>
    <td>-march=westmere</td>
  </tr>
  <tr>
    <td>Intel 2nd Gen Core i3/i5/i7-family (Sandybridge)</td>
    <td>-march=sandybridge</td>
  </tr>
  <tr>
    <td>Intel 3rd Gen Core i3/i5/i7-family (Ivybridge)</td>
    <td>-march=ivybridge</td>
  </tr>
  <tr>
    <td>Intel 4th Gen Core i3/i5/i7-family (Haswell)</td>
    <td>-march=haswell</td>
  </tr>
  <tr>
    <td>Intel 5th Gen Core i3/i5/i7-family (Broadwell)</td>
    <td>-march=broadwell</td>
  </tr>
</table>

#Benchmarks
##Intro
Three different machines running a generic x86-64 kernel and an otherwise identical kernel running with the optimized gcc options were tested using a make based endpoint.

##Conclusion
There are small but real speed increases to running with this patch as judged by a make endpoint. The increases are on par with the speed increase that the upstream sanctioned core2 option gives users, so not including additional options seems somewhat arbitrary to me.

##Details
1. Three test machines: Intel Xeon X3360, Intel i7-2620M, Intel Core i7-3660K.
2. All ran the make benchmark (linked below) 35 times while booted into a 'generic' kernel. Then all ran the same make benchmark 35 times after booting into an optimized kernel. Below are the optimizations chosen for each machine.
	* X3360 = core2
	* i7-2620M = sandybridge
	* i7-3660K = ivybridge
3. Results were analyzed for statistical significance via ANOVA plots that clearly show statistically significant albeit small differences.

##Discussion
1. All the assumptions for ANOVA are met:
	* Data are normally distributed as show in the normal quantile plots.
	* The population variances are fairly equal (Levene and Barlett tests).
2. The ANOVA plots clearly show significance.
	* Pair-wise analysis by Tukey-Kramer shows significance at the 0.05 level for all CPUs compared.

Below are the differences in median values:

<table>
  <tr>
    <th>CPU</th>
    <th>Difference in median value</th>
  </tr>
  <tr>
    <td>core2</td>
    <td>+87.5 ms</td>
  </tr>
  <tr>
    <td>sandybridge</td>
    <td>+79.7 ms</td>
  </tr>
  <tr>
    <td>ivybridge</td>
    <td>+257.2 ms</td>
  </tr>
</table>

##References
* Bash script that controls the benchmark: https://github.com/graysky2/bin/blob/master/bench
* Log file generated by script: http://repo-ck.com/bench/compile_time_optimization.txt.gz

##Credit
* Original author: jeroen AT linuxforge DOT net
* Link to original version: http://www.linuxforge.net/docs/linux/linux-gcc.php

##Data
###Sandybridge vs. Generic
![corei7avx](http://s19.postimage.org/68urcofzn/corei7_avx.png)

###Ivybridge vs. Generic
![coreavxi](http://s19.postimage.org/ozwomuak3/core_avx_i.png)

###Core2 vs. Generic
![core2](http://s19.postimage.org/d0l6fj4z7/core2.png)

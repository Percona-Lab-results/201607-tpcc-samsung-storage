Percona Server - TPCC-MySQL
===========================

Setup
-----

-   Client (tpcc) and server are on the same server.
-   CPU: 56 logical CPU threads servers Intel(R) Xeon(R) CPU E5-2683 v3 @ 2.00GHz
-   tpcc 1000 warehouses, 1 schema (about 100GB datasize)
-   OS: Ubuntu 16.04 (Xenial Xerus)
-   Kernel 4.4.0-28-generic
-   Storage devices
-   Samsung SM863 SATA SSD, single device, with ext4 filesystem
-   Samsung 850 PRO SATA SSD, single device, with ext4 filesystem
-   Samsung PM 1725 NVMe SSD, single device

Results
=======

=============

cachesize vary
--------------

We are varying buffer pool size from 5GB to 115GB. With 5GB buffer pool size a very small partion of data fits into memory, this results in intesive foreground IO reads and intensive background IO writes.

With 115GB almost all data fits into memory, this results in a very small (or almost zero) IO reads, and moderate background IO writes.

All buffer pool sizes in the middle of interval results to corresponding IO reads and writes.

The measurements are taken every 1 sec, so we can see variance in throughput and trends

### Pagesize 16k

The results for innodb\_page\_size=16k (default), 1 sec resolution ![](summary-tpcc-samsung_files/figure-markdown_github/unnamed-chunk-1-1.png)

The average results in NOTPM ![](summary-tpcc-samsung_files/figure-markdown_github/unnamed-chunk-2-1.png)

|   bp|     pm1725|    sam850|     sam863|  pm1725\_to\_sam863|  pm1725\_to\_sam850|
|----:|----------:|---------:|----------:|-------------------:|-------------------:|
|    5|   42427.57|   1931.54|   14709.69|                2.88|               21.97|
|   15|   78991.67|   2750.85|   31655.18|                2.50|               28.72|
|   25|  108077.56|   5156.72|   56777.82|                1.90|               20.96|
|   35|  122582.17|   8986.15|   93828.48|                1.31|               13.64|
|   45|  127828.82|  12136.51|  123979.99|                1.03|               10.53|
|   55|  130724.59|  19547.81|  127971.30|                1.02|                6.69|
|   65|  131901.38|  27653.94|  131020.07|                1.01|                4.77|
|   75|  133184.70|  38210.94|  131410.40|                1.01|                3.49|
|   85|  133058.50|  39669.90|  131657.16|                1.01|                3.35|
|   95|  133553.49|  39519.18|  132882.29|                1.01|                3.38|
|  105|  134021.26|  39631.03|  132126.29|                1.01|                3.38|
|  115|  134037.09|  39469.34|  132683.55|                1.01|                3.40|

#### Conclusion

Samsung 850 is obviously is not able to keep with with more advanced SM863 and PM1725

PM1725 shows a great benefit with small buffer pool sizes, while in case with big amount of memory, there is practically not difference with SM863. The reason is that with big buffer pool size MySQL does not push IO subsystem much to use all performance of PM1725

### Pagesize 4k

I also tested how innodb\_page\_size=4k affects the throughput

![](summary-tpcc-samsung_files/figure-markdown_github/unnamed-chunk-3-1.png)

The average results in NOTPM ![](summary-tpcc-samsung_files/figure-markdown_github/unnamed-chunk-4-1.png)

### Summary

There I show average throughput (in Transactions per Minute) ![](summary-tpcc-samsung_files/figure-markdown_github/unnamed-chunk-5-1.png)

|   bp|  pm1725\_16k|  pm1725\_4k|  sam850\_16k|  sam850\_4k|  sam863\_16k|  sam863\_4k|  pm1725\_4k\_to\_16k|
|----:|------------:|-----------:|------------:|-----------:|------------:|-----------:|--------------------:|
|    5|     42427.57|    73287.07|      1931.54|     2682.29|     14709.69|    48841.04|                 1.73|
|   15|     78991.67|   134466.86|      2750.85|     6587.72|     31655.18|    93880.36|                 1.70|
|   25|    108077.56|   173988.05|      5156.72|    10817.23|     56777.82|   133215.30|                 1.61|
|   35|    122582.17|   195116.80|      8986.15|    11922.59|     93828.48|   164281.55|                 1.59|
|   45|    127828.82|   209513.65|     12136.51|    20316.91|    123979.99|   192215.27|                 1.64|
|   55|    130724.59|   216793.99|     19547.81|    24476.74|    127971.30|   212647.97|                 1.66|
|   65|    131901.38|   224729.32|     27653.94|    23989.01|    131020.07|   220569.86|                 1.70|
|   75|    133184.70|   229089.61|     38210.94|    23457.18|    131410.40|   223103.07|                 1.72|
|   85|    133058.50|   227588.18|     39669.90|    24400.27|    131657.16|   227295.54|                 1.71|
|   95|    133553.49|   226241.41|     39519.18|    24327.22|    132882.29|   223963.99|                 1.69|
|  105|    134021.26|   224831.81|     39631.03|    24273.07|    132126.29|   222796.25|                 1.68|
|  115|    134037.09|   225632.80|     39469.34|    24073.36|    132683.55|   221446.90|                 1.68|

# PyPGME

PyPGME is a tiny Prometheus exporter exposing NVIDIA GPU resource usage.

Simply speaking, PyPGME was started as a Python port of pgme
(https://github.com/chhibber/pgme) written in Golang.

The initial motivation of PyPGME was intended for enabling GPU resource
monitoring on very restricted environment where only nvidia-smi and Python are
available and no additional software packages are not allowed.
For example, please imagine the ESXi shell environment.

PyPGME was originally written in July 2020.
Since then, it turned out (at least for me) that PyPGME can be a convenient
start point for writing quick hack/prototype Prometheus exporters
or more generally REST API servers.

## History

* 2020/07/24 Initial version
* 2021/11/01 Enable power consumption information
* 2021/11/06 Add -e option
* 2021/11/07 Add TYPE/HELP lines in response
* 2022/06/04 Make PyPGME HTTP/1.1 response
* 2024/02/25 Use argparse
* 2024/05/30 Split repository from junkbox


## Installation

PyPGME relies on the following standard Python modules only.

    sys, os, getopt, errno, subprocess, argparse, http

I believe there is no need to install additional modules.

Just place PyPGME somewhere on your GPU server. For example, /usr/local/bin.

## Description

By default PyPGME listens on tcp 19101. You can cange listen port and address
using -p and -b options.

If you like power usage information too that is not included in metrics of
the original pgme, specify -e option.


```
$ python3  PyPGME.py  -h
usage: PyPGME.py [-h] [-b BIND_ADDRESS] [-p PORT] [-e]

PyPGME.py

options:
  -h, --help            show this help message and exit
  -b BIND_ADDRESS, --bind_address BIND_ADDRESS
  -p PORT, --port PORT
  -e, --extended_attribute
$
```

* -h
    * Show the above help message
* -b BIND_ADDRESS
    * bind address to listen. Default is '0.0.0.0'
* -p PORT
    * bind port to listen. Default is 19101
* -e
    * Enable extended attributes. Note that below are not included in pygme.
        * pgme_power_draw
        * pgme_power_limit


## Output

The below example is from a PyPGME with -e (extended attribute) option.

```
$ curl -s http://localhost:19101/metrics
# TYPE temperature_gpu gauge
# HELP temperature.gpu of nvidia-smi
temperature_gpu{gpu="Tesla P100-PCIE-16GB[0]"} 32
# TYPE utilization_gpu gauge
# HELP utilization.gpu of nvidia-smi
utilization_gpu{gpu="Tesla P100-PCIE-16GB[0]"} 0
# TYPE utilization_memory gauge
# HELP utilization.memory of nvidia-smi
utilization_memory{gpu="Tesla P100-PCIE-16GB[0]"} 0
# TYPE memory_total gauge
# HELP memory.total of nvidia-smi
memory_total{gpu="Tesla P100-PCIE-16GB[0]"} 16384
# TYPE memory_free gauge
# HELP memory.free of nvidia-smi
memory_free{gpu="Tesla P100-PCIE-16GB[0]"} 16276
# TYPE memory_used gauge
# HELP memory.used of nvidia-smi
memory_used{gpu="Tesla P100-PCIE-16GB[0]"} 0
# TYPE pgme_power_draw gauge
# HELP power.draw of nvidia-smi
pgme_power_draw{gpu="Tesla P100-PCIE-16GB[0]"}  25.39
# TYPE pgme_power_limit gauge
# HELP power.limit of nvidia-smi
pgme_power_limit{gpu="Tesla P100-PCIE-16GB[0]"}  250.00
$
```

## License

Apache License, Version 2.0


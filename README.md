# conf-hpoll

The goal of this project is to make hybrid polling configurable so that its sleep duration decision logic can be customized. Currently, hybrid polling uses a fixed configuration of using 50% of the mean response time of recent I/Os for sleep duration, with 100 ms update period. conf-hpoll enables easy switching between various configurations, supporting research on improving the performance of hybrid polling.

## Information

##### main branch

Branch for configurable hybrid polling.

Configurable parameters:

- mode: mean, min
- sleep ratio: 0% ~ 100%
- update period: 1 ms ~ 1,000 ms

For more information, refer to Usage - Hybrid Polling Configuration(main branch).



##### simulator-log branch

Branch for getting log

[Hybrid Polling Simulator](https://github.com/oslab-swrc/hybrid_polling_sim) uses the log output from this branch as its input trace. A trace log contains the completion time and the timestamp of each I/O.

For more information, refer to [Hybrid Polling Simulator](https://github.com/oslab-swrc/hybrid_polling_sim).

- Requirement List
  - Multi device logging
    - Currently, the kernel can logging only one device. (Otherwise, logs are mixed in one file.)
    - If there are needs for multi device logging, logs are have to split logs by devices.



## License

GPL-2.0



## Usage

#### Build

##### Setup

You need to make config.

```bash
make menuconfig
```

SAVE & EXIT



##### Build Command

```bash
make -j$((`nproc`+1)) && make modules -j$((`nproc`+1)) && make modules_install -j$((`nproc`+1)) INSTALL_MOD_STRIP=1 && make install -j$((`nproc`+1))
```

This command automatically detects the number of cores, and builds with {core count} + 1. Or you can just specify core count to use like make -j8.



#### Hybrid Polling Configuration(main branch)

After building the kernel, you can get new sysfs values in /sys/block/nvmexn1/queue.

You can set three values:

##### io_poll_delay_multiply

- Default: 1
- It multiplies base sleep duration.

##### io_poll_delay_divide

- Default: 2
- It divides base sleep duration.

You can set various sleep ratios with the combination of above values.

Example: io_poll_delay_multiply = 8 & io_poll_delay_divide = 10 -> sleep ratio = 80%

##### hybrid_poll_sleep_min_flag

- Default: 0
- If the flag is set to 1, hybrid polling logic uses min value of poll stats.
- Else, hybrid polling uses mean value as default.



And you can also configure

```c
blk_stat_activate_msecs(q->poll_cb, 100);
```

in block/blk-mq.c(line 3774). If you change 100 to 10, the update period is set to 10 ms.



**These values only work when hybrid polling enabled.**


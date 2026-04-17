# 1. throughput
```shell
=====H2C throughput benchmark start=====
Try to init FPGA with PCI Bus: 0x1a
Init pci dev: 0x1a
Huge Pages Base VAddr: 0x7feb5c200000
Total Size: 1073741824
Number of errors: 0
Cycles: 4975274
Total length: 268435456
Speed: 12.56
Expected words per q: 100000
Real words per q: 100000 100000 100000 100000 0 0 0 0 0 0 0 0 0 0 0 0 
tlb.io.tlb_miss_count                                       : 0
io.c2h_cmd.[fire]                                           : 0
io.h2c_cmd.[fire]                                           : 262144
io.c2h_data.[fire]                                          : 0
io.h2c_data.[fire]                                          : 4194304
fifo_c2h_cmd.io.out.[fire]                                  : 0
fifo_h2c_cmd.io.out.[fire]                                  : 262144
fifo_c2h_data.io.out.[fire]                                 : 0
fifo_h2c_data.io.in.[fire]                                  : 4194304
fifo_c2h_cmd.io.out.valid                                   : 1
fifo_c2h_cmd.io.out.ready                                   : 0
fifo_h2c_cmd.io.out.valid                                   : 0
fifo_h2c_cmd.io.out.ready                                   : 1
fifo_c2h_data.io.out.valid                                  : 0
fifo_c2h_data.io.out.ready                                  : 1
fifo_h2c_data.io.in.valid                                   : 0
fifo_h2c_data.io.in.ready                                   : 1
=====C2H throughput benchmark start=====
This QDMA library now only support one CPU Memory Pool
Request pool size will be ignored
The previous CPU Memory Pool with size 1073741824 will be returned
0
1
2
3
count cmd: 138,right: 32768
count word: 68608,right: 16777216
count time: 500023174
Speed: 0.50 GB/s
right data count: 0, wrong data count: 16777216

tlb.io.tlb_miss_count                                       : 0
io.c2h_cmd.[fire]                                           : 0
io.h2c_cmd.[fire]                                           : 0
io.c2h_data.[fire]                                          : 0
io.h2c_data.[fire]                                          : 0
fifo_c2h_cmd.io.out.[fire]                                  : 0
fifo_h2c_cmd.io.out.[fire]                                  : 0
fifo_c2h_data.io.out.[fire]                                 : 0
fifo_h2c_data.io.in.[fire]                                  : 0
fifo_c2h_cmd.io.out.valid                                   : 1
fifo_c2h_cmd.io.out.ready                                   : 0
fifo_h2c_cmd.io.out.valid                                   : 0
fifo_h2c_cmd.io.out.ready                                   : 1
fifo_c2h_data.io.out.valid                                  : 0
fifo_c2h_data.io.out.ready                                  : 1
fifo_h2c_data.io.in.valid                                   : 0
fifo_h2c_data.io.in.ready                                   : 1
```
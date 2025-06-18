# gem5-final-project

(Q1) GEM5 + NVMAIN BUILD-UP 
---
(Q2) Enable L3 last level cache in GEM5 + NVMAIN
( 看到 log 裡面有 L3 cache 的資訊 )
---
按照ppt修改了
- Options.py
- Caches.py
- Xbar.py
- BaseCPU.py
- CacheConfig.py
改完要
```
scons build/X86/gem5.opt -j4
```

(Q3) Config last level cache to 2-way and full-way associative cache and test performance
---
<ARM架構下需要下載交叉編譯才能跑x86>
- 安裝 x86-64 交叉編譯器
```
sudo apt update
sudo apt install gcc-x86-64-linux-gnu
```
- 編譯quicksort.c(需靜態連結)
```
x86_64-linux-gnu-gcc -static quicksort.c -o quicksort_x86_static
file quicksort_x86_static
```
- 用這個 binary 跑 gem5
```
這是2way:
./build/X86/gem5.opt configs/example/se.py -c ./benchmark/quicksort_x86_static \
  --cpu-type=TimingSimpleCPU \
  --caches --l2cache --l3cache --l3_assoc=2 \
  --l1i_size=32kB --l1d_size=32kB \
  --l2_size=128kB --l3_size=1MB \
  --mem-type=NVMainMemory \
  --nvmain-config=../NVmain/Config/PCM_ISSCC_2012_4GB.config
```
```
這是fullway:
./build/X86/gem5.opt configs/example/se.py -c ./benchmark/quicksort_x86_static \
  --cpu-type=TimingSimpleCPU \
  --caches --l2cache --l3cache --l3_assoc=1 \
  --l1i_size=32kB --l1d_size=32kB \
  --l2_size=128kB --l3_size=1MB \
  --mem-type=NVMainMemory \
  --nvmain-config=../NVmain/Config/PCM_ISSCC_2012_4GB.config
```
- check L2 L3 資訊
```
grep -e 'system.l2.demand_accesses::total' -e 'system.l2.demand_misses::total' -e 'system.l2.demand_miss_rate::total' m5out/stats.txt
```
```
grep -e 'system.l3.demand_accesses::total' -e 'system.l3.demand_misses::total' -e 'system.l3.demand_miss_rate::total' m5out/stats.txt
```
- 複製output
```
cp m5out/stats.txt m5out/stat_2way.txt
cp m5out/stats.txt m5out/stat_fullway.txt
```


## PowerVC
Can add compute templates which can be used to deploy images.

## Tuning

### Home Node
- home node is core that a VP first gets dispatched to
- will try to use home node where possible

### Processor Folding

#### p5/6 Stack Then Spread
- p5/6 loads 1st & 2nd SMT to ~80% before unfolding next VP
#### p7 Spread Then Stack (Raw Throughput Mode)
- p7 loads first thread to 50% then unfolds next VP
- when all VPs unfolded, starts using 2nd SMT thread
- when all 2nd threads loaded, starts using tertiaries
- provides highest throughput at expense of more cores

#### Notes
- both report same physical usage
- more cores using in p7
- idle time higher
- reduce VPs accordingly
### Scaled Throughput
- behaves more like P6 - not recommended; try to reduce VPs instead
- `schedo -p -o vpm_throughput_mode=blah`
### SMT
- SMT threads go with VPs
- p5/6 SMT thread can go to 100%
- p7 SMT thread can only go up to 63%
- if only primary SMT threads are being used, you have too many VPs
### Entitlement
- If ent not long enough, VPs spend a lot of time warming cache
- put this somewhere between peak and average
### VPs
- don't set num VPs higher than cores in processor pool
### Commands
- `lparstat`, pool size, available in pool, virt context switches, phantom interrupts (vm dispatched to core, then ceeded on IO, but after IO interrupt comes back when target VM not running)
- `sar -mu -PALL` vertically, all threads with, physc & %entc
- `mpstat -s` shows procs horizontally
- `vmstat -IW 60 2` columns:
  - `p` threads waiting for IO to raw
  - `w` column is how many threads waiting for CIO/DIO.
### Memory
- %comp > 97% means you page
- comp is also called *dynamic* or *working storage*
- `lssrad -av` shows local/near memory for cpus
#### Paging
- try to avoid paging in first place
- use multiple, all same size on different disks
- `lru_file_repage=0` (restricted in AIX7) means care only about minperm
- on AIX 5.3TL4, should set maxperm & maxperm to 90, only change them with PMR
- `page_steal_method` default is `0`. `1` means scan only filesystem pages,
reduces scanning time. When combined with CIO can make a big difference.

`vmstat -v` don't want to see high IO blocked
- `numperm` = numclient + jfs and other stuff
- `numclient` jfs2 & network
- if `numperm` == `numclient` then it is all being used by network or jfs2
- pbuf is where IO initially gets built
- `vmstat -v | grep memory` find number of memory pools
- minfree & maxfree apply to memory pools
- vmstat fre column needs to be divided amongst memory pools


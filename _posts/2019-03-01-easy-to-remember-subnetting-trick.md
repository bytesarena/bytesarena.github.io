---
layout: post
title: "Easy to remember subnetting trick"
categories: Uncategorised
---

If you are related to the world of networking, you would ofcourse have had to subnet at some point of your career. Though it is rare that you may have to do it without the help of the internet, I am willing to bet that at least most you would have had to do this at least once ( maybe in an interview? ). 

Here is a neat trick that I like to use as a little cheat for subnetting without using a calculator.

What I do is, I consider the IP addresses as memory units, specifically megabytes. You dont even have to be from the world of computers to be familiar with these memory units. 

Consider:  

```
1 IP Address  = 1 MiB
```
Generally in storage devices like flash drives, memory cards and so on, we see the storage capacity incrementing as exponents of 2. The same can be applied in subnetting.

With this I came up with the below table.


| CIDR | Memory Unit   | Number of IP addresses | Comments |
| ---- | ---- | ---------------------- | -------- |
| /32  | 1 MiB | 1                      |          |
| /31  | 2 MiB | 2                      |          |
| /30  | 4 MiB | 4                      |          |
| /29  | 8 MiB | 8                      |          |
| /28  | 16 MiB | 16                      |          |
| /27  | 32 MiB | 32                      |          |
| /26  | 64 MiB | 64                      |          |
| /25  | 128 MiB | 128                      |          |
| /24  | 256 MiB | 256                      |          |
| /23  | 512 MiB | 512                      |          |
| /22  | 1 GiB | 1024                      | Convert GiB to MiB          |
| /21  | 2 GiB | 2048                      |          |
| /20  | 4 GiB | 4096                      |          |
| /19  | 8 GiB | 8192                      |          |
| /18  | 16 GiB | 16384                       |          |
| /17  | 32 GiB | 32768                       |          |
| /16  | 64 GiB | 65536                       |          |
| /15  | 128 GiB | 131072                       |          |
| /14  | 512 GiB | 262144                  |          |
| /13  | 1 TiB | 524288                       | Convert TiB to Mib         |
| /12  | 2 TiB | 1048576                  |          |
| /11  | 4 TiB | 2097152                 |          |
| /10  | 8 TiB | 4194304                 |          |
| /9   | 16 TiB | 8388608                  |          |
| /8   | 32 TiB | 16777216                 |          |
| /7   | 64 TiB | 33554432                |          |
| /6   | 128 TiB | 67108864                 |          |
| /5   | 256 TiB | 134217728                |          |
| /4  | 512 TiB | 268435456              | Convert PiB to Mib        |
| /3  | 1 PiB | 536870912             |          |
| /2  | 2 PiB | 1073741824                       |          |
| /1  | 4 PiB | 2147483648              |          |
| /0  | 8 PiB | 4294967296              |          |


In any given subnet (except /32 which is one IP address), subtract 2 from the number of IP addresses and you get the number of usable IP addresses ( 1 network address and 1 broadcast address).

I agree that this method is not particularly useful with the larger subnets like /8, but this makes it easy to calculate the smaller ones. Anything below /18 is a piece of cake. 

On a side note, contrary to popular belief, 1024 MB (Megabyte ) is not equal to 1 GB (Gigabyte). 1000 MB = 1 GB. 

The memory units used are Mebibyte (MiB) , Gibibyte (GiB) , Tebibyte (TiB)  and Pebibyte (PiB). 

```
1024 MiB = 1 GiB
1024 GiB = 1 TiB
1024 GiB = 1 PiB
```
Hope this was useful. 

 

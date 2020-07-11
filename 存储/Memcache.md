memcache相关

## slab allocator内存分配、管理机制

1. 概念
    1. slab：同样大小的chunk组成一类slab
    2. Page：分配给Slab的内存空间，默认是1M。分配给Slab之后根据Slab的大小切成chunk
    3. Chunk：固定大小的内存空间，用于缓存记录，默认为88Byte
2. 原理：memcached 根据收到的数据大小，选择最适合数据大小的Slab，memcached中保存着Slab内空闲chunk的列表，根据该列表选择chunk，然后将数据缓存其中。Slab allocator分配的内存不会释放，而是重复利用。
3. 缺点：由于分配的是特定长度的内存，因此无法有效的利用分配的内存。对与该问题没有完美的解决方案，但可以调节slab class的大小差别来减少空间浪费。
4. 使用Growth factor进行调优

## 删除机制

1. 懒删除：获取key时查看时间戳，检查是否过期。因此不会在过期监视上耗费cpu时间
2. LRU：内存空间不足时，删除最近最少使用的记录

## 分布式

1. 一致性哈希
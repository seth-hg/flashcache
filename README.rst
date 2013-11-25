===============================================
Flashcache with Lazy Adaptive Replacement Cache
===============================================

This is a clone of Facebook's Flashcache, with a new algorithm named Lazy Adaptive Replacement Cache.

LARC is based on LRU. Like the 2Q algorithm used in the Flashcache v3 [1]_, it divides recently accessed blocks into "hot" and "warm" groups. Specifically, "warm" here means the block has been accessed only once recently. The difference is that in LARC, only "hot" blocks are cached on SSD. For "warm" blocks, LARC keeps their block numbers in a candidate list. On cache misses, only those blocks in the candidate list will be loaded into SSD. LARC not only improves overall performance, but also prolongs SSD lifetime by reducing writes to SSD. For more details, check out my paper on MSST 2013 [2]_.

To use LARC, set reclaim_policy to 1(LRU) and then

.. sourcecode:: bash

                sysctl dev.flashcache.cache_dev+disk_dev.larc_enable=1

Two new sysctl parameters are added for LARC.

1. larc_read_only

If set to 1, LARC will always try to allocate cache block for writes, no matter whether the block is in the candidate list. According to my experiments, read-only mode works better for some applications.

2. larc_candidate_pct

The maximum length of candidate list, in percentage of cache size. The default value 0 is recommended since LARC can dynamically adjust this value.

Note that LARC was initially implemented in an older version of Flashcache and ported to Flashcache v3 recently. I haven't tested this version yet.

.. [1] `Flashcache at Facebook: From 2010 to 2013 and beyond`_
.. _`Flashcache at Facebook: From 2010 to 2013 and beyond`: https://www.facebook.com/notes/facebook-engineering/flashcache-at-facebook-from-2010-to-2013-and-beyond/10151725297413920
.. [2] `Improving Flash-based Disk Cache with Lazy Adaptive Replacement Cache`_
.. _`Improving Flash-based Disk Cache with Lazy Adaptive Replacement Cache`: http://ieeexplore.ieee.org/xpl/articleDetails.jsp?arnumber=6558447&punumber%3D6554307%26sortType%3Dasc_p_Sequence%26filter%3DAND%28p_IS_Number%3A6558419%29%26pageNumber%3D2

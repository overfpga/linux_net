# linux_net



# skb 初始函数
    skb 初始函数   skb_init函数创建skb描述符 ， 有两个原来分配SKB描述的高速缓存,一般情况skb描述符都是从skbuff_head_cache高速缓存创建,skbuff_fclone_cache以两倍SKB描述符创建
    void __init skb_init(void)
    {
        skbuff_head_cache = kmem_cache_create_usercopy("skbuff_head_cache",
                            sizeof(struct sk_buff),
                            0,
                            SLAB_HWCACHE_ALIGN|SLAB_PANIC,
                            offsetof(struct sk_buff, cb),  
                            sizeof_field(struct sk_buff, cb),
                            NULL);
        skbuff_fclone_cache = kmem_cache_create("skbuff_fclone_cache",
                            sizeof(struct sk_buff_fclones),
                            0,
                            SLAB_HWCACHE_ALIGN|SLAB_PANIC,
                            NULL);
        skb_extensions_init();
    }

    alloc_skb 分配SKB, 数据缓存区和SKB描述符是两个不同的实体，意味着，在分配一个SKB时，需要分配两块内存，一块时数据缓存区，一块是数据描述符,_alloc_skb调用kmem_cache_alloc_node从告诉 缓存中获取一个sk_buff结构空间,然后调用kmalloc_reserve分配数据缓存区

    dev_alloc_skb ： 也是一个缓存区分配函数，通常被设备驱动用在中断上下文，这是一个alloc_skb的封装函数，（原子操作）

    释放SKB
    dev_kfree_skb 和 kfree_skb
    dev_kfree_skb是kfree_skb的宏，dev_kfree_skb一般为设备驱动使用 ，只有在SKB引用为1时，调用释放函数才会释放缓存区。

    skb_reserve ： 在数据缓存区头部预留一定的空间，通常会在分配SKB之后就调用该函数

    skb_push　：　在数据缓存区前部加入一块数据，移动缓存区的头指针data和尾指针tail，数据由其他函数复制到数据缓存区

    skb_pull ：将data指针往下移动，在数据去首部忽略len字节长度的数据，通常用于接收到数据包后在各层间由下往上传播，上层忽略下层的首部

    skb_clone : 克隆SKB描述符，不克隆缓存区

    skb_share_check ： 检查skb引用计数users

    skb_copy : 修改聚合分散I/O存储区中的数据



# skb链表操作函数
    skb_queue_head_init ： 初始化SKB链表头节点，并创建一个空的SKB链表

    skb_queue_head和skb_queue_tail ： 将一个SKB加入到队列的头部和尾部

    skb_dequeue和skb_degueue_tail ：从队列的首部和尾部取下一个SKB

    skb_queue_purge ： 清空一个SKB链表

    skb_queue_walk 宏 ： 定义一个for语句，用来按顺序遍历SKB链表中的每一个元素

# 添加或删除尾部数据
    skb_add_data ：将指定用户空间的数据添加到SKB的数据缓存区的尾部

    skb_trim ： 根据指定长度删除SKB的数据缓存区尾部的数据

    skb_split　：　拆分指定长度的SKB

    pskb_expand_head : 扩展指定长度的数据缓存区

    pskb_may_pull : 检测SKB中的数据是否有指定的长度

    skb_queue_empty : 检测指定的SKB队列是否为空

    skb_realloc_headroom : 根据一个指定的SKB得到一个新的SKB,并确保新的SKB存在指定的headroom

    skb_get : 引用并返回一个SKB

    skb_shared : 检测指定的skb是否被多次引用

    skb_share_check : 检查并返回SKB,当被检查的SKB被引用多次,则克隆此SKB,并返回克隆得到的SKB

    skb_unshare : 检查并返回SKB,当被检查的SKB被克隆时,则复制此SKB,并返回复制得到的SKB

    skb_orphan : 使SKB不属于任何传输控制块

    skb_cow : 确保SKB存在指定的headroom空间,如果不足,会重新分配

    skb_pagelen : 获取SKB中线性数据区和SG类型的聚合分散I/O分片中数据的长度
​​​


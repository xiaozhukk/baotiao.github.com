---
layout: post
title: "Memcache initialization"
description: "Memcache slabs"
category: tech
tags: [memcache]
---

    1.signal(SIGINT, sig_handler);

    2.seetings_init(); //初始化默认的设置,初始化全局变量struct setting settings
    static void settings_init(void) {
        settings.use_cas = true;
        settings.access = 0700;
        settings.port = 11211;
        settings.udpport = 11211;
        /* By default this string should be NULL for getaddrinfo() */
        settings.inter = NULL;
        settings.maxbytes = 64 * 1024 * 1024; /* default is 64MB */
        settings.maxconns = 1024;         /* to limit connections-related memory to about 5MB */
        settings.verbose = 0;
        settings.oldest_live = 0;
        settings.evict_to_free = 1;       /* push old items out of cache when memory runs out */
        settings.socketpath = NULL;       /* by default, not using a unix socket */
        settings.factor = 1.25;
        settings.chunk_size = 48;         /* space for a modest key and value */
        settings.num_threads = 4;         /* N workers */
        settings.num_threads_per_udp = 0;
        settings.prefix_delimiter = ':';
        settings.detail_enabled = 0;
        settings.reqs_per_event = 20;
        settings.backlog = 1024;
        settings.binding_protocol = negotiating_prot;
        settings.item_size_max = 1024 * 1024; /* The famous 1MB upper limit. */
        settings.maxconns_fast = false;
        settings.hashpower_init = 0;
    }

    3. while (-1 != (c = getopt(argc, argv, //对输入的参数进行相应的设置,这种demon,item_max_size 等等

    4.getrlimit,setrlimit 是系统调用,用来修改最大连接数 struct rlimit .

    5.去掉root权限. 如果是root 通过 -u 参数来启动

    6.init_sasl() 是否开启 sasl

    7.daemonize(maxcore, setting.verbose) 开启 daemonize 模式

    8.main_base = event_init(); 初始化主线程

    9.stats_init() 初始化全局变量stats.

    10.assoc_init() 初始化hashtable, 默认calloc(2^16, sizeof(void *))个指针

    11.conn_init() 初始化连接池conn, 默认的空闲连接池的个数是200 声明calloc(200, sizeof(conn *))

    12.slabs_init(settings.maxbytes, setting.factor, preallocate) 初始化memcached. setting.maxbytes 是最大内存的大小,setting.factor 成长因子, preallocate是否预先声明空间 . 默认的最小的chunk_size 是48,成长因子是1.25.

    13.sigignore(SIGPIPE) 忽略SIGPIPE信号。 在Linux下写socket的程序的时候，如果尝试send到一个disconnected socket上，底层将抛出一个SIGPIPE信号。如果程序没有处理或没有忽略，这个信号将导致程序退出。这不是我们需要的.

    14.thread_init(settings.num_threads, main_base); //初始化线程池模型

    15.start_assoc_maintenance_thread() //在assoc里面,有一个独立的线程来维护从old_hashtable中的数据到相应的primary_hashtable中.开启这个线程

    16.clock_handler(0, 0, 0); //初始化libevent的时间处理

    17.server_socket_unix(settings.socketpath,settings.access) //建立tcp 或者 udp的socket连接sfd,dispatch_thread开始监听这个sfd连接.
    server_sockets(settings.port, tcp_transport,portnumber_file) //可以让dispatch同时监听多个连接的端口. 到此 服务器初始化结束,等到client的连接

    18.if (event_base_loop(main_base, 0) != 0) //循环main_base事件

    19.stop_assoc_maintenance_thread(); //退出在assoc里面的维护hashtable线程

---
title: Python进阶-Python并发
---
## [](#python-并发与java-并发的区别 "python 并发与java 并发的区别")python 并发与java 并发的区别

Python的并发分为多进程和多线程，进程在multiprocessing模块，线程在threading模块（线程虽然还有_thread模块，但是threading是对_thread的高级封装，使用起来更顺手所以这里只介绍threading）
对于Java语言，并发只能用多线程。但是对于Python语言当我们有的选的时候，先看规模，规模都很大的情况下如果不需要考虑数据共享，尽量用多进程，因为在分布式、微服务流行的年代，通过添加PC的方式进程数的限制不再成为瓶颈，为何不用更稳定的方式呢；如果要考虑数据共享先分析通过数据同步方式能否低消耗的解决，能解决还是用多进程，代价很大就用多线程。当然了运算规模很小，需要快餐式消费，还是多线程开销更小。

## [](#线程和Python "线程和Python")线程和Python

Python代码的执行由Python虚拟机进行控制的。
Python解释器可以运行多个线程，但是任意时刻只有1个程序会被解释器执行

## [](#python的threading模块 "python的threading模块")python的threading模块

Python提供了多个模块来支持多线程，包括thread,threading和Queue
推荐使用threading而非thread.
避免使用thread的原因：

*   threading模块更加先进，有更好的线程支持
*   低级别的thread模块拥有的同步原语很少
*   thread 对于进行何时退出没有控制，当主线程结束时，所有其他线程也都强制结束

    ### [](#thread-模块 "thread  模块")thread  模块
thread模块的核心函数是start_new_thread() 主要是用来派生一个新线程，使用给定的args和kwargs来执行function<pre class="highlight"><span class="line">#!usr/bin/env python</span>
<span class="line">import thread</span>
<span class="line">def loop()</span>
<span class="line">    print &quot;start loop 0 at:&quot;,ctime()</span>
<span class="line">    sleep(4)</span>
<span class="line">    print &quot;loop 0 done at:&quot;,ctime()</span>
<span class="line"></span>
<span class="line">def loop()</span>
<span class="line">    print &quot;start loop 1 at:&quot;,ctime()</span>
<span class="line">    sleep(4)</span>
<span class="line">    print &quot;loop 1 done at:&quot;,ctime()</span>
<span class="line"></span>
<span class="line">def main()</span>
<span class="line">    print &#x27;starting at:&#x27;,ctime()</span>
<span class="line">    thread.start_new_thread(loop0,())</span>
<span class="line">    thread.start_new_thread(loop1,())</span>
<span class="line">if __name__=&#x27;_main_&#x27;：</span>
<span class="line">    main()</span>
</pre>
使用锁的版本<pre class="highlight"><span class="line">import thread</span>
<span class="line">from time import sleep,ctime</span>
<span class="line">loops=[4,2]</span>
<span class="line"></span>
<span class="line">def loop(nloop,nsec,lock):</span>
<span class="line">    print(&#x27;start loo;%s,at,%s&#x27;%(nloop,ctime()))</span>
<span class="line">    sleep(nsec)</span>
<span class="line">    print(&#x27;loop%s,done at:,%s&#x27;%(nloop,ctime()))</span>
<span class="line">    lock.release()</span>
<span class="line"></span>
<span class="line">def main():</span>
<span class="line">        print(&quot;starting at:%s&quot;%ctime())</span>
<span class="line">        locks=[]</span>
<span class="line">        nloops=range(len(loops))</span>
<span class="line"></span>
<span class="line">        for i in nloops:</span>
<span class="line">            lock = thread.allocate_lock()</span>
<span class="line">            lock.acquire()</span>
<span class="line">            locks.append(lock)</span>
<span class="line"></span>
<span class="line">        for i in nloops:</span>
<span class="line">            thread.start_new_thread(loop,(i,loops[i],locks[i]))</span>
<span class="line"></span>
<span class="line">        for i in nloops:</span>
<span class="line">            while locks[i].locked(): pass</span>
<span class="line"></span>
<span class="line">        print(&#x27;all done at:%s&#x27;%ctime())</span>
<span class="line"></span>
<span class="line">if __name__ == &#x27;__main__&#x27;:</span>
<span class="line">    main()</span>
</pre>

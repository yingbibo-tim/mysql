<p>
##### AbstractQueuedSynchronizer#PROPAGATE 的作用
PROPAGATE用于共享锁的情况，表示传播属性,在共享锁的情况下,主要是通过前继节点来唤醒后继节点
<pre>
<code>
AbstractQueuedSynchronizer#setHeadAndPropagate
 private void setHeadAndPropagate(Node node, int propagate) {
         Node h = head; // Record old head for check below
        setHead(node);
        /**
          propagate 资源量
          h 原始headNode 的节点状态
        **/
        if (propagate > 0 || h == null || h.waitStatus < 0 ||
            (h = head) == null || h.waitStatus < 0) {
            Node s = node.next;
            if (s == null || s.isShared())
                doReleaseShared();
        }
    }
</code>
<code>
AbstractQueuedSynchronizer#AbstractQueuedSynchronizer()
   for (;;) {
            Node h = head;
            if (h != null && h != tail) {
                int ws = h.waitStatus;
                if (ws == Node.SIGNAL) {
                    if (!compareAndSetWaitStatus(h, Node.SIGNAL, 0))
                        continue;            // loop to recheck cases
                    unparkSuccessor(h);
                }
                else if (ws == 0 &&
                         !compareAndSetWaitStatus(h, 0, Node.PROPAGATE))
                    continue;                // loop on failed CAS
            }
            if (h == head)                   // loop if head changed
                break;
        }
</code>
</pre>
PROPAGATE的作用主要是为了防止某些极端情况下，前继节点无法做到唤醒后继节点
从setHeadAndPropagate这个方法可以看到,唤醒后继节点要满足的条件主要是 资源量>0 或者是 headNode.waitStatus < 0 
最开始看这个共享锁的时候，是从ReentrantReadWriteLock开始看起的，发现在获取资源量的时候（propagate）不是1就是-1，一度怀疑人生，认为Node.PROPAGATE纯属一个摆设,只是为了标记一下节点的状态,然而胖次眉头一皱,发现事情并不简单，从Semaphore（信号量）这个共享锁中发现了一丝问题，信号量在获取propagate（资源量）的时候,会存在得到资源量为0的情况,这时候如果node.waiteStatus 正好是0的时候,会存在我明明还有后继节点，竟然存在不能唤醒的情况
**场景描述 head->t1（node）-t2（node）,t3，t4执行unlock操作，t1，t2执行lock操作 Semaphore = new Semaphore(0)，同时假设t1，t2在t3，t4之前先执行
<br>0.刚开始状态 t1，t2阻塞
<br>1.t3线程执行了unlock操作，这时候head的状态 从 -1 变成了 0，同时唤醒了t1线程
<br>2.t1线程开始从阻塞状态恢复到活跃状态，获取到propagate（资源量）=0 ，然后进入setHeadAndPropagate方法，但并未进入setHead操作
<br>3.t4线程执行unlock操作,这时候读取到head的状态还是0，将 0 改成 -3
<br>4.t1线程，setHeader 然后进入时候要doReleaseShared的判断，如果这时候没有Node.PROPAGATE的判断，你会发现单纯靠propagage（资源量）来判断是否唤醒后记节点，你会发现t2线程死了...**
</p>

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
PROPAGATE的作用主要是为了防止某些极端情况下，前继节点无法做到唤醒后继节点（其实就是瞎逼写,老老实实一个lock 一个unloc不会出现..）
从setHeadAndPropagate这个方法可以看到,唤醒后继节点要满足的条件主要是 资源量>0 或者是 headNode.waitStatus < 0 


</p>

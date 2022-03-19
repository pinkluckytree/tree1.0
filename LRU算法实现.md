# LRU算法实现
操作系统内存管理使用的一种页面置换算法，最近最少使用

要点： capacity+哈希表（保证查找时间复杂度 O（1））+双向链表（记录元素的顺序）

```java
public class LRUCache {
    //定义一个双向结点类，里面需要保存key，value，前节点，后节点
    class DNode{//保存key，value的原因可以记录key的顺序  ，单单使用一个value不可记录顺序
        int key;
        int value;
        DNode next;
        DNode pre;
        DNode(int k,int v){
            this.value=v;
            this.key=k;
        }
        DNode(){}
    }

    public Map<Integer,DNode>map=new HashMap<>();

    private int capacity;//最大容量
    private int size;//当前存放总数

    //哨兵元素头节点尾节点
    DNode head;
    DNode tail;

    LRUCache(){ }
    LRUCache(int capacity){
        this.capacity=capacity;
        this.size=0;
        head=new DNode();
        tail=new DNode();
        head.next=tail;
        tail.pre=head;
    }
    public int  get(int key){
        DNode node=map.get(key);
        //未找到：node为空，返回-1
        if(node==null){
            return -1;
        }
        //能找到就更新移到表头
        moveTohead(node);

        return node.value;
    }

    public void put(int key,int value){
        DNode node = new DNode(key, value);
        //key已经存在，重新添加， 移到表头
        if(map.containsKey(key)) {
            //删除旧节点
            DNode node1=map.get(key);
            removeDnode(node1);
            //添加新节点到表头
            addTohead(node);
            map.put(key, node);//添加到缓存

        }
        //key不存在,分两种，第一种还有容量，第二种没有容量
        else {
            size++;
            if(size<=capacity){//容量还够
                map.put(key,node);
                addTohead(node);
            }
            else {//容量不够，就需要删除表尾元素
                map.remove(tail.pre.key);
                removeDnode(tail.pre);
                size--;
                map.put(key,node);
                addTohead(node);
            }

        }
    }

    //移到表头
    public void moveTohead(DNode node){
        removeDnode(node);
        addTohead(node);
    }

    //删除node
    public void removeDnode(DNode node){
        node.pre.next=node.next;
        node.next.pre=node.pre;
    }

    //加在表头:需要修改四个指向，当前节点的前后，头节点的next， 头节点原下一节点的pre
    public void addTohead(DNode node){
        node.pre=head;
        node.next=head.next;
        head.next.pre=node;
        head.next=node;
    }
}


```

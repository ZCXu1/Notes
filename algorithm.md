# 二分

> 二分查找是最经典的算法之一，以其极低的时间复杂度(**O(logn)**)闻名于世。主要有三种，在有序数组中查找是否存在目标，并返回下标，第一个下标，最后一个下标。注意边界处理。以及`mid = l + ((r - l) >> 1)`优化。

```java
public class Binary {
    /**
     * 基础的二分
     */
    public int binarySearch(int[] nums, int target) {
        int n = nums.length;
        int l = 0, r = n - 1;
        while (l <= r) {
            int mid = l + ((r - l) >> 1);
            if (nums[mid] == target) {
                return mid;
            } else if (nums[mid] < target) {
                l = mid + 1;
            } else {
                r = mid - 1;
            }
        }
        return -1;
    }
    /**
     * 在排序数组中查找元素的第一个位置
     */
    private int findFirst(int[] nums, int target) {
        if (nums.length == 0) {
            return -1;
        }
        int left = 0, right = nums.length - 1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] == target) {
                right = mid - 1;
            } else if (nums[mid] < target) {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        if (left >= nums.length || nums[left] != target) {
            return -1;
        }
        return left;
    }

    /**
     * 在排序数组中查找元素的最后一个位置
     */
    private int findLast(int[] nums, int target) {
        if (nums.length == 0) {
            return -1;
        }
        int left = 0, right = nums.length - 1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] == target) {
                left = mid + 1;
            } else if (nums[mid] < target) {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        if (right < 0 || nums[right] != target) {
            return -1;
        }
        return right;
    }
    /**
     * 在排序数组中查找元素的第一个和最后一个位置
     * P34
     */
    public int[] searchRange(int[] nums, int target) {
        return new int[]{findFirst(nums,target),findLast(nums,target)};
    }
}

```

# 排序

> 排序算法是基础，常用的算法根据时间复杂度分类有冒泡排序、插入排序、选择排序、归并排序、快速排序。

## 冒泡排序

> 冒泡排序主要是需要进行n轮比较，每轮比较后，都把本轮最大的数往后放。所以需要进行交换操作，每次比较如果前面比后面小，就交换。一共n轮，外层是n的循环，内层因为最后面有序，所以随i变化，当i = 0时，也就是第一轮，需要进行n - 1次比较，以此类推得到边界为`n-i-1`，此外，可以设置一个flag，若此轮没有交换，说明数组已经有序，则直接break退出。

```java

    public void bubbleSort(int[] nums) {
        int n = nums.length;
        if (n <= 1) return;
        // 最多n次交换
        for (int i = 0; i < n; i++) {
            boolean flag = false;
            // 每次都把大的数字往后放
            for (int j = 0; j < n - i - 1; j++) {
                if (nums[j + 1] < nums[j]) {
                    int temp = nums[j + 1];
                    nums[j + 1] = nums[j];
                    nums[j] = temp;
                    flag = true;
                }
            }
            // 若没有交换就是有序数组了
            if (!flag) {
                break;
            }
        }
    }
```

## 插入排序

> 插入排序就是把数组左边当成有序数组，然后依次往左边插入。初始时左边数组长度是1，从1开始循环。遍历有序数组时逆序遍历，方便移动元素，找到插入位置后插入。

```java
public void insertSort(int[] nums) {
        int n = nums.length;
        if (n <= 1) return;
        // 当前要往前插的元素的下标 也就是左边已经排好的数组长度
        for (int i = 1; i < n; i++) {
            int val = nums[i];
            int j = i - 1;
            // 寻找插入位置并右移
            for (; j >= 0; j--) {
                if (nums[j] > val) {
                    nums[j + 1] = nums[j];
                } else {
                    // 如果nums[j] <= val 则j+1就是插入位置
                    break;
                }
            }
            nums[j + 1] = val;
        }
    }
```

## 归并排序

> 主要是分治递归思想，把数组从中间拆分为两个数组，分别排序，注意递归终止条件，还有merge函数将两个有序的子数组合并成另一个有序数组，这里的合并我选择双指针法，注意数组必须复制一份，不然会在赋值的时候打乱原数组，那么归并排序也就不是原地排序算法。整个归并排序的时间复杂度是**O(nlogn)**。

```java
// 归并排序
    public static void mergeSort(int[] nums) {
        mergeSortC(nums, 0, nums.length - 1);
    }

    // 归并排序的递归调用函数
    private static void mergeSortC(int[] nums, int i, int j) {
        // 终止条件
        if (i >= j) return;
        // 取中间位置
        int k = i + ((j - i) >> 1);
        // 分治
        mergeSortC(nums, i, k);
        mergeSortC(nums, k + 1, j);
        // 此时就是把两个有序的子数组i--k和k+1--j合并成另一个子数组 这里用双指针的方式
        merge(nums, i, k, j);
    }
    // nums的i到k和k+1到j已经有序了 需要在i到j范围内合并出有序数组
    private static void merge(int[] nums, int i, int k, int j) {
        int l1 = k - i + 1, l2 = j - k;// j-(k+1)+1
        // 把i到k 复制到数组a
        int[] a = new int[l1];
        // 把k+1到j 复制到数组b
        int[] b = new int[l2];
        System.arraycopy(nums, i, a, 0, l1);
        System.arraycopy(nums, k + 1, b, 0, l2);
        int p = 0, q = 0;// p是a的指针 q是b的指针
        int r = i; // r是nums的指针 用于更新nums 注意起始位置是i不是0
        while (p < l1 && q < l2) {
            if (a[p] < b[q]) {
                nums[r++] = a[p++];
            } else {
                nums[r++] = b[q++];
            }
        }
        while (p < l1) {
            nums[r++] = a[p++];
        }
        while (q < l2) {
            nums[r++] = b[q++];
        }
    }
```

## 快速排序（重要）

> 快速排序也用了分治的思想，但思路和归并排序完全不一样。若排序下标p到r的数据，需要找一个数据作为分区点q，把p到r的小于q的数据放到q左边，大于q的数据放到q右边。这个选择q的分区函数就非常重要。必须是原地的，才有意义。因为真正的软件开发中，原地排序算法非常重要。归并排序的合并函数无法原地进行，快速排序通过巧妙的分区函数解决了归并排序太耗内存的问题。这也是快速排序作为时间复杂度为O(nlogn)突出的地方。但是快速排序很明显不是稳定排序。
>
> p到r的分区函数写法：令q为nums[r]，也就是最后一个数，通过游标i把p到r-1分为两部分，p到i-1都小于q，成为已处理区间（初始i为p，那么p到p-1就是空），对应的i到r-1为未处理区间。每次从未处理区间取一个数nums[j]，与q做对比，如果小于q，那么就插入已处理区间的尾部，也就是下标为i的位置，这里的插入只需要让i和j位置对换即可。其实可以这样理解i，i前面都是比pivot小的数。
>
> 如果数组本身有序，就会退化到二次的时间复杂度。快速排序有个常用的优化方法，随机法，在[l,r]中随机一个数，和r交换位置，在代码中也有所体现。有没有优化在算法题中还是差别挺大的。

```java
// 快速排序
    public static void quickSort(int[] nums) {
        quickSortC(nums, 0, nums.length - 1);
    }

    // 快速排序递归函数
    private static void quickSortC(int[] nums, int i, int j) {
        if (i >= j) return;
        int k = randomPartition(nums, i, j);
        quickSortC(nums, i, k - 1);
        quickSortC(nums, k + 1, j);
    }

    private static void swap(int[] nums, int i, int j) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }

    // 快速排序分区函数 返回一个数k
    // 使得数组中i到k-1都是小于k的 k+1到j都是大于k的
    private static int partition(int[] nums, int l, int r) {
        // 选取最后一个数作为pivot 处理前面的 最后交换
        int pivot = nums[r];
        // i的含义：i前面都是比pivot小的数
        int i = l;
        // j从l循环到r-1
        for (int j = l; j <= r - 1; j++) {
            // 若j指向的数比pivot小 就交换i和j指向的数
            // 同时i自增 这样保证了i前面都是比pivot小的数
            if (nums[j] < pivot) {
                swap(nums, i, j);
                i++;
            }
        }
        // 最后i就是pivot应该放的位置
        swap(nums, i, r);
        return i;
    }

    // 快速排序常用优化 随机法
    // 随机选择一个[l,r]中的数 和r交换
    private static int randomPartition(int[] nums, int l, int r) {
        // new Random().nextInt(r - l + 1)是[0,r-l] 再+l即可
        int x = new Random().nextInt(r - l + 1) + l;
        swap(nums,x,r);
        return partition(nums,l,r);
    }
```

### 子问题：数组中的第K个最大元素

> LeetCode 215
>
> 给定整数数组 `nums` 和整数 `k`，请返回数组中第 `k` 个最大的元素。
>
> 请注意，你需要找的是数组排序后的第 `k` 个最大的元素，而不是第 `k` 个不同的元素。

这里我们对数组0到n-1原地分区，注意是第k大，所以数组要逆向排序，只需在原地分区函数partition中用大于替代小于，让大于pivot的往前交换即可，然后选择随机法优化后的最后一个元素作为pivot进行原地分区，若k=pivot，则正好pivot指的就是我们第k大元素，否则若k>pivot，注意是第k大，k越大，数越小，也就在pivot后面，所以应该从[pivot+1，r]中用相同思路找，k<pivot同理，所以我们需要一个辅助递归函数help来完成任务。

```java
class Solution {
    public int findKthLargest(int[] nums, int k) {
        // 注意第k大元素实际上是逆序数组下标k-1，这里为了方便直接传k-1
        return nums[help(nums, 0, nums.length - 1, k - 1)];
    }

    private int help(int[] nums, int l, int r, int k) {
        int x = randomPartition(nums, l, r);
        if (k == x) {
            return x;
        } else if (k > x) {
            return help(nums, x + 1, r, k);
        } else {
            return help(nums, l, x - 1, k);
        }
    }
	// 依然保持随机数优化
    private int randomPartition(int[] nums, int l, int r) {
        int x = new Random().nextInt(r - l + 1) + l;
        swap(nums, x, r);
        return partition(nums, l, r);
    }

    private int partition(int[] nums, int l, int r) {
        int pivot = nums[r];
        int i = l;
        for (int j = l; j <= r - 1; j++) {
            // 这里换成大于就是让pivot前面是比pivot大的数 后面是比pivot小的数
            if (nums[j] > pivot) {
                swap(nums, i, j);
                i++;
            }
        }
        swap(nums, i, r);
        return i;
    }

    private void swap(int[] nums, int i, int j) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
}
```

# Hash

> 哈希表利用的是数组按下标访问元素的时间复杂度是O(1)这一特性。我们通过哈希函数把元素的键值映射为下标，然后将对应的数据存储在数组中对应下标的位置。在实际情况中，想找一个没有冲突得到哈希函数，几乎是不可能的。

## 工业级Hash表举例分析

> 以Java HashMap为例。

### 初始大小

HashMap的默认初始大小是16，这个初始大小可以设置。

### 装载因子和动态扩容

HashMap的最大装载因子默认是0.75。当HashMap中元素个数超过`0.75*capacity`时，就会触发扩容，扩大到原来的两倍。

### 哈希冲突的解决方法

HashMap采用链表法解决hash冲突。在jdk1.8中，做了进一步优化，引入了红黑树。在链表长度>=8时，链表就转换为红黑树，可以利用红黑树支持快速增加、删除、修改和查询的特点，提高HashMap的性能。当红黑树的节点<=6时，HashMap又会将红黑树转化为链表。

### Hash函数

```java
int hash(Object key){
    int h = key.hashCode();
    return (h ^ ( h>>>16 )) & (capacity - 1);
}
```

这里` & (capacity - 1)`，在capacity是2的幂的时候也就是`%capacity`，但效率要高得多，所以hash表的大小一般都是2的幂。

## 利用Hash表和双向链表写LRU缓存淘汰

> LeetCode 146
>
> 请你设计并实现一个满足  LRU (最近最少使用) 缓存 约束的数据结构。
> 实现 LRUCache 类：
> LRUCache(int capacity) 以 正整数 作为容量 capacity 初始化 LRU 缓存
> int get(int key) 如果关键字 key 存在于缓存中，则返回关键字的值，否则返回 -1 。
> void put(int key, int value) 如果关键字 key 已经存在，则变更其数据值 value ；如果不存在，则向缓存中插入该组 key-value 。如果插入操作导致关键字数量超过 capacity ，则应该 逐出 最久未使用的关键字。
> 函数 get 和 put 必须以 O(1) 的平均时间复杂度运行。

我们维护一个双链表，头节点存放最新访问的数据，尾节点存放最早访问的数据，并记录头指针和尾指针，需要淘汰数据的时候，世界把尾部节点删除，当要缓存某个数据的时候，先在链表中查找这个数据，如果没有找到，直接将数据放到链表头部，如果找到了，就移动到链表头部。这里查找这个数据的操作，用Hash表实现，直接存放某个数据对应的位置即可。

同时我们的链表为了好处理边界，在头尾增加哨兵节点。

有几点需要注意：

- 删除尾元素意味着淘汰key，所以hash表中也要把对应的键值对删除，这里也需要我们链表中的节点储存key，而不只是储存value，因为删除尾节点只有链表节点参数，hash表中淘汰需要key，所以链表节点需要储存一个key。
- 当put方法调用时，要判断容量满没满，然后还要判断key是否存在，如果不存在，就直接插入头部，并注意还要往hash表中插入key和链表节点的键值对，如果存在，就删除尾部，然后挪到头部，并修改value，由于java的引用特点，hash表中的值一直是节点的引用，所以不需要修改。

```java
class LRUCache {
    class DLinkedNode {
        // 链表里的data是key和value
        // value是必存的 key在删除尾节点时有用
        int key;
        int value;
        DLinkedNode prev;
        DLinkedNode next;

        public DLinkedNode() {
        }

        public DLinkedNode(int _key, int _value) {
            key = _key;
            value = _value;
        }
    }

    // 储存数据在双向链表中的位置 便于查找数据存放的位置
    // <data,position>键值对 这里data我们取数据中的key 位置直接用链表节点指针
    private Map<Integer, DLinkedNode> cache = new HashMap<>();
    private int count;
    private int capacity;
    private DLinkedNode head;
    private DLinkedNode tail;

    LRUCache(int capacity) {
        this.count = 0;
        this.capacity = capacity;
        // 头哨兵
        head = new DLinkedNode(-1, -1);
        // 尾哨兵
        tail = new DLinkedNode(-1, -1);
        // 初始化双向链表
        head.prev = null;
        head.next = tail;
        tail.prev = head;
        tail.next = null;
    }

    public int get(int key) {
        DLinkedNode node = cache.get(key);
        // 不存在返回-1
        if (node == null){
            return -1;
        }
        // 存在就删除这个节点 然后移到链表头
        deleteNode(node);
        addToHead(node);
        return node.value;
    }
    public void put(int key,int value){
        // 根据hash表找到key在链表中的位置
        DLinkedNode node = cache.get(key);
        // 如果key不在链表里 就直接插在头部 注意链表容量
        if (node == null){
            if (count < capacity){
                DLinkedNode d = new DLinkedNode(key,value);
                addToHead(d);
                cache.put(key,d);
                count++;
            }else{
                // 容量满了 删除尾元素
                removeTail();
                DLinkedNode d = new DLinkedNode(key,value);
                addToHead(d);
                cache.put(key,d);
                // 删1加1 count不动
            }
        }else{
            // 如果key在链表里 要删除这个key对应位置的node 然后再插入到头部
            deleteNode(node);
            addToHead(node);
            node.value = value;
        }
    }
    private void addToHead(DLinkedNode node){
        head.next.prev = node;
        node.next = head.next;
        node.prev = head;
        head.next = node;
    }
    // 删除尾元素 注意不删哨兵
    private void removeTail(){
        if (head.next == tail){
            return;
        }
        // 要删的节点是p
        DLinkedNode p = tail.prev;
        p.prev.next = tail;
        tail.prev = p.prev;
        // 注意删除尾节点之后也要在hash中删除映射
        // 这里就看出我们节点中存key就有用了
        cache.remove(p.key);
    }
    private void deleteNode(DLinkedNode node){
        // 这里哨兵的作用就出来了 如果链表只有一个元素（不含head和tail）不会造成空指针异常
        DLinkedNode pre = node.prev;
        DLinkedNode next = node.next;
        pre.next = next;
        next.prev = pre;
    }
}

```

## 位图

> 假设有1000w个范围在1-1亿的整数，如何快速查找某个整数是否出现在这1000w个整数当中？用hash表当然可以，但这里用位图Bitmap更合适。我们申请一个大小1亿，数据为布尔类型的数组a，将这1000w个整数作为数组下标对应的数组元素值设为true。当查询某个数K是否在这1000w个整数中时，就看a[K]是否为true。但是事实上，虽然布尔类型理论上用一位就可以表示，但是**Java中boolean占用1个字节**。所以这样并不合适。
>
> 事实上，我们可以用一个char数组来表示位图。Java中char占两个字节。在存取位图数据时，我们用数据除以16，得到在数据哪个数组元素中，用数据模16，得到存储在这个数组元素的哪一个二进制位上。

```java
public class BitMap {
    // java char 占 16bit
    private char[] bytes;
    private int n;

    BitMap(int n) {
        // 数据范围 也就是位图大小
        this.n = n;
        // 一个char16位 需要n/16+1个char空间
        // 这里可以理解为一个二维数组 第一维n / 16 + 1 第二维16
        // 需要用位运算计算第二维
        // 对于一个数a a/16表示在数组第几个元素上 a%16表示在哪个二进制位上
        this.bytes = new char[n / 16 + 1];
    }

    public void set(int k) {
        if (k > n) return;
        int byteIndex = k / 16;
        int bitIndex = k % 16;
        // 这个数组元素一共16位 把第(1 << bitIndex)位置为1
        // 表示byteIndex*16+bitIndex这个数在n个数中
        bytes[byteIndex] |= (1 << bitIndex);
    }

    public boolean get(int k) {
        if (k > n) return false;
        int byteIndex = k / 16;
        int bitIndex = k % 16;
        return (bytes[byteIndex] & (1 << bitIndex)) != 0;
    }
}

```

位图有一定局限性，就是数据范围不能太大，如果是10亿而不是1亿，就需要10亿bit就是120MB大小，相比哈希表40MB，内存不降反增。

# 树

## 二叉查找树(BST)

> 对于BST中的任意一个节点，其左子树中每个节点的值都小于这个节点的值，其右子树中每个节点的值都大于这个节点的值。
>
> BST的查找和插入比较好写，就是从根节点开始遍历，大了往右走，小了往左走，重点在删除操作。
>
> 针对要删除节点的子节点的个数不同，可以分为三种情况：
>
> - 要删除的节点没有子节点，只需要将父节点指向要删除节点的指针置为null。
> - 要删除的节点只有一个子节点，只需要更新父节点中指向要删除节点的指针，让它重新指向要删除节点的子节点。
> - 要删除的节点A有两个子节点。找到这个节点A的右子树的最小节点B，在树上删除这个最小节点B，然后和这个节点A替换。其中删除节点B，就可以用上面的方法，因为B一定是最多只有一个子节点，否则不可能是A的右子树中的最小节点。



注意删除操作也是让两个子节点的情况变为一个子节点的情况。

```java
public class BinarySearchTree {
    class Node {
        private int data;
        private Node left;
        private Node right;

        Node() {
        }

        Node(int data) {
            this.data = data;
        }

        Node(int data, Node left, Node right) {
            this.data = data;
            this.left = left;
            this.right = right;
        }
    }

    private Node root;

    public Node find(int data) {
        Node p = root;
        while (p != null) {
            if (data < p.data) p = p.left;
            else if (data > p.data) p = p.right;
            else return p;
        }
        return null;
    }
    public void insert(int data){
        if (root == null){
            root = new Node(data);
            return;
        }
        Node p = root;
        while (true){
            if (data > p.data){
                if (p.right == null){
                    p.right = new Node(data);
                    break;
                }else{
                    p = p.right;
                }
            }else{
                if (p.left == null){
                    p.left = new Node(data);
                    break;
                }else{
                    p = p.left;
                }
            }
        }
    }

    public void delete(int data){
        Node p = root; //要删除的节点
        Node pp = null; //要删除的节点的父节点
        while (p != null && p.data != data){
            pp = p;
            if (data > p.data) p = p.right;
            else p = p.left;
        }
        // data在树里不存在
        if (p == null) return;
        // 首先处理有两个子节点
        // 因为处理两个子节点后续需要用到处理<=1个子节点
        if (p.left != null && p.right != null){
            // 寻找节点p的右子树中的最小的节点
            Node minP = p.right;
            // minPP是minP的父节点
            Node minPP = p;
            // 最小节点一定没有左子节点
            // 没有左子节点的也是最小节点
            while (minP.left != null){
                minPP = minP;
                minP = minP.left;
            }
            // 此时minP指向了最小节点
            // 只需将最小节点的数据给到节点p
            p.data = minP.data;
            // 要删的节点变成了minP
            p = minP;
            pp = minPP;
        }
        // 要删除的节点最多只有一个子节点
        Node child; // P的子节点
        if (p.left != null) child = p.left;
        else if (p.right != null) child = p.right;
        else child = null;
        // 删除根节点(注意走到这一步 根节点也最多只有一个孩子)
        if (pp == null) root = child;
        else if (pp.left == p) pp.left = child;
        else pp.right = child;
        
    }
}

```

### 性能分析

> 在最极端的情况，BST会退化为链表，查找时间复杂度就变为了O(n)，因此平衡二叉查找树十分重要。

## 红黑树(R-B Tree)

> 平衡二叉树的严格定义：二叉树中任意一个节点的左右子树高度相差不能大于1。红黑树是一种相对平衡的二叉查找树，不符合严格定义。要满足如下要求：
>
> - 根节点是黑色的
> - 每个叶子节点都是黑色的空节点，也就是说，叶子节点不存储数据。
> - 任何上下相邻的节点不能同时为红色，也就是说红色节点被黑色节点隔开。
> - 对于每个节点，从该节点到其叶子节点的所有路径，都包含相同数目的黑色节点。

红黑树是一种近似平衡的二叉树，它是为了解决二叉查找树动态数据更新导致的性能退化问题而创造的。红黑树的高度近似于logn，插入删除和查找的时间复杂度都是O(logn)。

## B+树

> 在开发中，为了加快数据库中数据的查找速度，一般会对表种得数据创建索引，那么M有SQL数据库索引是如何实现的？

# 堆

> 堆的应用场景非常多，如堆排序、优先级队列、求TOP K和求中位数等。
>
> 因为堆是完全二叉树，所以用数组存放，对于一个下标为i的点，左孩子为2i，右孩子为2i+1，父亲是i/2
>
> 向堆中插入元素，删除堆顶元素的时间复杂度都是**O(logn)**

## 建堆

```java
public class Heap {

    private int[] a; //数组，从下标1开始存放数据
    private int n; // 堆的容量
    private int count;// 已存放的数据 也就是末尾元素的下标

    // 大顶堆
    public Heap(int capacity) {
        a = new int[capacity + 1];
        n = capacity;
        count = 0;
    }

    private void swap(int[] a, int i, int j) {
        int temp = a[i];
        a[i] = a[j];
        a[j] = temp;
    }

    // 插入元素
    // 插入末尾 然后自下而上堆化
    public void insert(int data) {
        if (count >= n) return;
        a[++count] = data;
        int i = count;
        // 有父节点而且子大于父
        while (i / 2 > 0 && a[i] > a[i / 2]) {
            swap(a, i, i / 2);
            i /= 2;
        }
    }

    // 删除元素
    // 用末尾元素置顶 然后自上而下堆化
    public void removeMax() {
        if (count == 0) return;
        // 末尾元素置顶
        a[1] = a[count--];
        heapify(a, count, 1);
    }

    // 对一共有n个元素的堆数组a的下标为i的元素自上而下堆化
    private void heapify(int[] a, int n, int i) {
        while (true) {
            // 找出左右孩子哪个最大，然后互换位置
            int maxPos = i;
            // 有左孩子并且父小于左孩子 并取最大值的下标存入maxPos
            if (i * 2 <= n && a[i] < a[i * 2]) maxPos = i * 2;
            // 有右孩子并且父和左孩子的最大值小于右孩子
            if (i * 2 + 1 <= n && a[maxPos] < a[i * 2 + 1]) maxPos = i * 2 + 1;
            // 父亲比左右节点都大 说明此时已经是堆了
            if (maxPos == i) break;
            swap(a,i,maxPos);
            // 进入下一次循环
            i = maxPos;
        }
    }
}
```

## 堆排序

> 借助堆这种数据结构进行排序称为堆排序，可大致分解为建堆和排序两个过程。

### 建堆

> **从后往前**处理数组，并且堆每个数据执行**自上而下的堆化**。因为叶子节点不需要堆化，对于完全二叉树，下标n/2+1到n的节点都是叶子节点，所以只需要对下标n/2到1的数据进行依次进行自上而下的堆化

```java
public static void buildHeap(int[] a, int n) {
        for (int i = n / 2; i >= 1; i--) {
            heapify(a, n, i);
        }
    }

    private static void swap(int[] a, int i, int j) {
        int temp = a[i];
        a[i] = a[j];
        a[j] = temp;
    }

    public static void heapify(int[] a, int n, int i) {
        while (true) {
            // 找出左右孩子哪个最大，然后互换位置
            int maxPos = i;
            // 有左孩子并且父小于左孩子 并取最大值的下标存入maxPos
            if (i * 2 <= n && a[i] < a[i * 2]) maxPos = i * 2;
            // 有右孩子并且父和左孩子的最大值小于右孩子
            if (i * 2 + 1 <= n && a[maxPos] < a[i * 2 + 1]) maxPos = i * 2 + 1;
            // 父亲比左右节点都大 说明此时已经是堆了
            if (maxPos == i) break;
            swap(a, i, maxPos);
            // 进入下一次循环
            i = maxPos;
        }
    }
```

### 排序

> 数组已经变成了大顶堆，数组中第一个元素也就是最大元素，把它与末尾元素互换，那么最大元素也就放到了下标为n的元素，交换之后的堆顶元素需要进行自上而下的堆化，注意此时count比之前少1，因为数组后面排好的元素不能再参与堆化了，这是显然的。

```java
public static void sort(int[] a,int n){
        buildHeap(a,n);
        int k = n;
        while(k > 1){
            // 堆顶和末尾元素互换
            swap(a,1,k);
            // 大的元素已经排到最后面了 数组末尾下标记得-1
            --k;
            // 对堆顶执行自上而下的堆化
            heapify(a,k,1);
        }
    }
```

### 性能分析

> 堆排序为什么不如快速排序？

- 堆排序的数据访问方式没有快速排序高效，快排按下标连续访问数组元素，堆排序依次访问下标是1、2、4、8的元素，无法利用CPU缓存预读数据。
- 对于同样的数据，堆排序的数据交换次数要多于快速排序，尤其是对于一组有序的数据，经过建堆之后反而变得无序了。

## 堆的应用

### 优先级队列

> Java中自带PriorityQueue，注意创建小顶堆lambda表达式是(a,b)->(a-b)
>
> 在k个有序数组合并到一个有序数组中的问题，可以用优先级队列。

假设有100个长度为n的有序数组，排列成一个有序的大数组。建立一个大小为100的小顶堆数组a，从这100个数组中各取第一个数放入a中，然后取出堆顶元素x放入结果数组ans中，假设x来自数组X，那么就从数组X中取出x的下一个数y放入堆a中，然后再取出堆顶元素放入结果数组ans中，以此类推，直到所有的数组元素都放入大数组ans中即可。

### TOP K

> 求数组中最大的k个数，用小顶堆，因为是淘汰小的（堆顶元素）。最后堆里就是最大的k个数。

这里还给TOP K数组排了逆序，从大到小，所以用了Integer数组，方便用Arrays.sort+排序器。

```java
public static Integer[] topK(Integer[] nums, int k) {
        if (k <= 0) {
            return new Integer[]{};
        }
        // 小顶堆
        PriorityQueue<Integer> pq = new PriorityQueue<>((a, b) -> (a - b));
        int n = nums.length;
        if (n <= k) {
            Arrays.sort(nums, (a, b) -> (b - a));
            return nums;
        }
        for (int i = 0; i < k; i++) {
            pq.add(nums[i]);
        }
        for (int i = k; i < n; i++) {
            if (nums[i] > pq.peek()) {
                pq.poll();
                pq.add(nums[i]);
            }
        }
        Integer[] a = new Integer[k];
        for (int i = 0; i < k; i++) {
            a[k - i - 1] = pq.poll();
        }
        return a;
    }
```

### 中位数

> LeetCode 295
>
> 中位数是有序列表中间的数。如果列表长度是偶数，中位数则是中间两个数的平均值。
>
> 例如，
>
> [2,3,4] 的中位数是 3
>
> [2,3] 的中位数是 (2 + 3) / 2 = 2.5
>
> 设计一个支持以下两种操作的数据结构：
>
> - void addNum(int num) - 从数据流中添加一个整数到数据结构中。
> - double findMedian() - 返回目前所有元素的中位数。

主要思路就是用大顶堆 存数组前半部分元素 （小的数），用小顶堆 存数组后半部分元素 （大的数），同时动态维持大顶堆数据最多比小顶堆多一个。

```java
class MedianFinder {
    // 小顶堆 存数组后半部分元素 （大）
    private PriorityQueue<Integer> smallest;
    // 大顶堆 存数组前半部分元素 （小）
    private PriorityQueue<Integer> biggest;

    public MedianFinder() {
        smallest = new PriorityQueue<>((a, b) -> (a - b));
        biggest = new PriorityQueue<>((a, b) -> (b - a));
    }

    public void addNum(int num) {
        if (biggest.isEmpty()) {
            biggest.add(num);
        } else {
            int m = biggest.size();
            int n = smallest.size();
            int peek = biggest.peek();
            // 如果插入数据后总数居个数是奇数 就让大顶堆多存一个元素
            // 如果是偶数 就平分

            // 目前总数据个数是奇数 大顶堆多一个
            if ((m + n) % 2 == 1) {
                // 要插入的数据比大顶堆堆顶（中位数）大
                // 直接往小顶堆插入
                if (num >= peek) {
                    // 这里合并等于是因为大小顶堆平分 左边一个num 右边一个num
                    smallest.add(num);
                } else {
                    // 要插入的数据比大顶堆堆顶（中位数）小 往大顶堆插入
                    // 为了调整两个堆的大小平衡 需要把peek移至小顶堆
                    biggest.add(num);
                    smallest.add(biggest.poll());
                }
            } else {
                // 目前总数据个数为偶数
                if (num > peek) {
                    // 注意需要调整大顶堆比小顶堆多一个
                    // 所以往小顶堆添加后需要把小顶堆堆顶元素移到大顶堆
                    smallest.add(num);
                    biggest.add(smallest.poll());
                } else {
                    biggest.add(num);
                }
            }
        }
    }

    public double findMedian() {
        int m = biggest.size();
        int n = smallest.size();
        if ((m + n) % 2 == 1) {
            // 奇数返回大顶堆堆顶
            return biggest.peek();
        } else {
            return (biggest.peek() + smallest.peek()) / 2.0;
        }
    }
}
```

# 回溯

> 为了有规律地枚举所有可能的解，避免遗漏和重复，我们把问题求解的过程分为多个阶段，每个阶段都会面对一个岔路口，我们随意挑一条路走，当发现这条路走不通的时候，就回退到上一个岔路口，另选一种方法继续走。
>
> 递归回溯的dfs，就是做选择（可选择的元素就是for循环下没有被use的元素），破坏现场，递归，恢复现场，撤销选择，可以看出递归的前后是对称的，那么基于这个特点，我们dfs方法的参数必须有path，used。

## 八皇后问题

> 8x8的期盼，放置8个皇后，要求不能互相吃，问有几种方法。回溯算法适合用递归。

最后可以验证有92个解。

```java
// 下标代表行 值代表列
    private int[] result = new int[8];
    private int count = 0;

    public void bt(int row) {
        // 结束回溯条件
        if (row == 8) {
            print();
            count++;
            return;
        }
        // 一行有8种放法
        for (int c = 0; c < 8; c++) {
            if (isOk(row, c)) {
                result[row] = c;
                bt(row + 1);
            }
        }
    }

    // 考察这个点能否放置皇后
    // 因为是自上而下回溯 所以在本行 只需考虑上面皇后的影响 下面没皇后
    private boolean isOk(int row, int c) {
        for (int i = 0; i < row; i++) {
            // 在同一列
            if (result[i] == c) {
                return false;
            }
            // 在左上角
            if (row - i == c - result[i]) {
                return false;
            }
            // 在右上角
            if (row - i == result[i] - c) {
                return false;
            }
        }
        return true;
    }

    private void print() {
        for (int row = 0; row < 8; row++) {
            for (int c = 0; c < 8; c++) {
                if (result[row] == c) {
                    System.out.print("Q ");
                } else {
                    System.out.print("* ");
                }
            }
            System.out.println();
        }
        System.out.println("================");
    }
```

## 全排列I

> LeetCode 46
>
> 给定一个不含重复数字的数组 `nums` ，返回其 *所有可能的全排列* 。你可以 **按任意顺序** 返回答案。

全排列问题是经典的回溯问题。注意本题不含重复数字，不需要去重。

```java
class Solution {
    public List<List<Integer>> permute(int[] nums) {
        int len = nums.length;
        List<List<Integer>> res = new ArrayList<>();
        if (len == 0) {
            return res;
        }

        boolean[] used = new boolean[len];
        Deque<Integer> path = new ArrayDeque<>();

        dfs(nums, len, path, used, res);
        return res;
    }

    private void dfs(int[] nums, int len, 
                     Deque<Integer>path, boolean[] used,
                     List<List<Integer>> res) {
        //递归出口
        if (path.size() == len) {
            res.add(new ArrayList<>(path));
            return;
        }

        // 在非叶子结点处，产生不同的分支，这一操作的语义是：在还未选择的数中依次选择一个元素作为下一个位置的元素，这显然得通过一个循环实现。
        for (int i = 0; i < len; i++) {
            if (!used[i]) {
                //做选择
                path.addLast(nums[i]);
                //破坏现场
                used[i] = true;
                //递归
                dfs(nums, len, path, used, res);
                // 注意：下面这两行代码发生 「回溯」，回溯发生在从 深层结点 回到 浅层结点 的过程，代码在形式上和递归之前是对称的
                //恢复现场
                used[i] = false;
                //撤销选择
                path.removeLast();
                //下一次循环
            }
        }
    }
}
```

这里需要注意：

```JAVA
if (path.size() == len) {
	res.add(new ArrayList<>(path));
	return;
}
	//错误写法
if (path.size() == len) {
	res.add(path);
	return;
}
```

这个错误写法是因为java是值传递，如果你add的是path，传进去是引用变量的地址值，那么下面的for循环，有一个` path.remove(path.size() - 1);`会将path中的元素删除，造成添加的path最后变成空list，也就是说整个递归-回溯过程中，path指向一个地方，解决这个问题的方法就是如上再拷贝一份。

## 全排列II

> LeetCode 47
>
> 给定一个可包含重复数字的序列 `nums` ，***按任意顺序*** 返回所有不重复的全排列。

注意本题需要去重。这里我们没有用used，而是采用了下标标记idx，因为方便去重。

```java
class Solution {
    boolean[] vis;
    public List<List<Integer>> permuteUnique(int[] nums) {
        List<List<Integer>> ans = new ArrayList<List<Integer>>();
        List<Integer> perm = new ArrayList<Integer>();
        vis = new boolean[nums.length];
        Arrays.sort(nums);
        backtrack(nums, ans, 0, perm);
        return ans;
    }

    public void backtrack(int[] nums, List<List<Integer>> ans, int idx, List<Integer> perm) {
        if (idx == nums.length) {
            ans.add(new ArrayList<Integer>(perm));
            return;
        }
        for (int i = 0; i < nums.length; ++i) {
            if (vis[i] || (i > 0 && nums[i] == nums[i - 1] && !vis[i - 1])) {
                continue;
            }
            perm.add(nums[i]);
            vis[i] = true;
            backtrack(nums, ans, idx + 1, perm);
            vis[i] = false;
            perm.remove(idx);
        }
    }
}
```

注意这里的去重思路，需要先对数组**排序**，乱序是不可以的。然后最关键的部分就是

```java
   if (vis[i] || (i > 0 && nums[i] == nums[i - 1] && !vis[i - 1])) {
		continue;
	}
```



就比如1,1,2，第一次回溯就会填入1,1,2，那么接下来到第二个1先放入了，那么在这个if下，第一个1是没有被访问的，所以满足条件，直接continue，跳过以第二个1为首的排列情况，去重成功。也就是所有相同的元素，只选择在某一个特定位置，只选择第一个。这里的去重挺通用的。

## 子集I

> LeetCode 78
>
> 给你一个整数数组 `nums` ，数组中的元素 **互不相同** 。返回该数组所有可能的子集（幂集）。
>
> 解集 **不能** 包含重复的子集。你可以按 **任意顺序** 返回解集。

因为这里子集可以use可以不use，所以不能用used进行标记，直接采用下标cur的方式，然后就是对于子集问题不是必须要选，不做选择也是一种选择，所以在回溯之后也要来一个不做选择的dfs，而且做选择的时候也没有循环体。其实就是从头到尾模拟一个元素选与不选，所以不用循环。

```java
class Solution {
    public List<List<Integer>> subsets(int[] nums) {
		List<Integer> l = new ArrayList<>();
		List<List<Integer>> ans = new ArrayList<>();
		dfs(0,nums,l,ans);
		return ans;
    }
	public void dfs(int cur,int[] nums,List<Integer> l,List<List<Integer>> ans){
		int n = nums.length;
		if(n == cur){
			ans.add(new ArrayList<>(l));
            return;
		}
        //做出选择
		l.add(nums[cur]);
        //进入下一层
		dfs(cur+1,nums,l,ans);
        //撤销选择
		l.remove(l.size()-1);
        //这里也是做选择 只不过做出的选择是不选
        //因为对于nums的每个元素 我们有选择和不选两种状态
        //处于这两种状态之一时 进入下一层dfs
        //由cur变量确定是否处于边界
		dfs(cur+1,nums,l,ans);
	}
}
```

## 子集II

> LeetCode 90
>
> 给你一个整数数组 nums ，其中可能包含重复元素，请你返回该数组所有可能的子集（幂集）。
>
> 解集 不能 包含重复的子集。返回的解集中，子集可以按 任意顺序 排列。

同样这里我们选择变量index表示遍历的下标。

```java
class Solution {
    public List<List<Integer>> subsetsWithDup(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();
        int len = nums.length;
        Arrays.sort(nums);      // 排序后便于去重
        dfs(res, new ArrayList<>(), 0, nums.length, nums);
        return res;
    }
    // dfs - 递归函数
    public void dfs(List<List<Integer>> res, List<Integer> list, int index, int len, int[] nums){
        if(index == len){   // [基本情况] 如果游标到达尾部，将当前list拷贝放入res中
            res.add(new ArrayList<>(list));
            return;
        }
        list.add(nums[index]); 
        dfs(res, list, index + 1, len, nums); // 选择当前的数
        list.remove(list.size() - 1);   // 回溯 - 将当前添加的数删除
        while (index < len - 1 && nums[index] == nums[index + 1]){ // 去重，不选当前数递归时直接跳过重复数
            index++;    // 所有重复数的可能性在前面递归中均会出现
        }
        dfs(res, list, index + 1, len, nums); // 不选择当前的数
    }
}
```

注意这里的去重为什么在不选当前数的时候去，举个例子说明，比如1，2，2在子集选了1，2，2时，开始回溯，不选择最后的2，则得一个1，2，然后再回溯，回溯到1，此时我们要不选中间的2，然后进入下一层，选择最后一个2，又一个1，2，这样子集就重复了，所以我们的去重方法是，在不选中间的2前做一个判断，若后面还有2，直接index往后移，移到最后一个2，然后不选，进入下一层。

## 组合

> LeetCode 77
>
> 给定两个整数 `n` 和 `k`，返回范围 `[1, n]` 中所有可能的 `k` 个数的组合。

```java
class Solution {
    public List<List<Integer>> combine(int n, int k) {
        List<List<Integer>> res = new ArrayList<>();
        Deque<Integer> stack = new ArrayDeque<>();
        dfs(n,k,1,stack,res);
        return res;

    }
    private void dfs(int n,int k,int begin,Deque<Integer> stack,List<List<Integer>> res){
        if (stack.size() == k){
            res.add(new ArrayList<>(stack));
            return;
        }
        //因为i从begin开始 从1到begin-1是不会被选择的 所以我们没有必要创建used数组来判断谁要不要选
        for (int i = begin; i <= n; i++) {
            stack.addLast(i);
            dfs(n,k,i+1,stack,res);
            stack.removeLast();
        }

    }
}
```


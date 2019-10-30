# Java版算法与数据结构

1.稀疏数组

​    当一个数组中大部分元素为０，或者为同一个值的数组时，可以使用稀疏数组来保存该数组。

稀疏数组的处理方法是:

记录数组一共有几行几列，有多少个不同的值

把具有不同值的元素的行列及值记录在一个小规模的数组中，从而缩小程序的规模

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/6be8bd0b80e242e28078b6e6ac9b23a4/8bdebdca60014ffb8e50e1c56f01ec7d.jpg)

二维数组转稀疏数组的思路

1. 遍历原始的二维数组，得到有效数据的个数 sum
2. 根据sum就可以创建稀疏数组sparseArr int[ sum + 1][3]
3. 将二维数组有效数据存入到稀疏数组

稀疏数组转原始的二位数组的思路

1. 先读取稀疏数组的第一行，根据第一行的数据，创建原始的二维数组
2. 在读取稀疏数组后几行的数据，并赋值给原始的二维数组即可

2.队列

  队列是一个有序列表，可以用数组或是链表来实现。

  遵循先入先出的原则。即：先存入队列的数据，要先取出。后存入的要后取出

  队列是一个有序列表，可以用数组或是链表来实现。

  遵循先入先出的原则。即：先存入队列的数据，要先取出。后存入的要后取出

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/31bb26fe38d64d3785c7db09ae85b860/fdca4a26e9954a58a8621fde39e04d05.jpg)

3.使用数组模拟环形队列

思路调整

- front：指向队列的第一个元素位置，也就是说arr[front]是队列的第一个元素
- front  = 0
- rear:  指向队列的最后一个元素的下一个位置，因为希望空出一个空间做为约定
- rear = 0
- 当队列满时，条件是：(rear + 1) % maxSize == front 表示队列已满
- 当队列为空时，条件是：rear == front 为空
- 当我们这样分析，队列中有效的数据个数是：(rear + maxSize - front) % maxSize

4.

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/aac62201ff89479faa8596d8fd837262/c284af296a644cb18fd2bf7bec186035.jpg)

5.双向链表

管理单向链表的缺点分析: 

单向链表，查找的方向只能是一个方向，而双向链表可以向前或者向后查找。

单向链表不能自我删除，需要靠辅助节点 ，而双向链表，则可以自我删除，所以前面我们单链表删除时节点，总是找到temp,temp是待删除节点的前一个节点(认真体会).

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/19eaba4421db4537b8561aa92f970a81/b60696617e164d6092615eca70670c8c.jpg)

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/8825093b36e540d18598bf29f2bdb228/001d01df78954f1e8d7ac4ff2b7db4cb.jpg)

6.用栈来实现计算器原理

7.前缀、中缀、后缀表达式(逆波兰表达式)

前缀表达式(波兰表达式)

前缀表达式又称波兰式，前缀表达式的运算符位于操作数之前

举例说明： (3+4)×5-6 对应的前缀表达式就是 - × + 3 4 5 6

8.中缀表达式转换为后缀表达式

具体步骤如下:

\1. 初始化两个栈：运算符栈s1和储存中间结果的栈s2；

\2. 从左至右扫描中缀表达式；

\3. 遇到操作数时，将其压s2；

\4. 遇到运算符时，比较其与s1栈顶运算符的优先级：

​    4.1 如果s1为空，或栈顶运算符为左括号“(”，则直接将此运算符入栈；

​    4.2 否则，若优先级比栈顶运算符的高，也将运算符压入s1；

​    4.3 否则，将s1栈顶的运算符弹出并压入到s2中，再次转到(4.1)与s1中新的栈顶运算符相比较；

\5. 遇到括号时：

​    (1) 如果是左括号“(”，则直接压入s1

​    (2) 如果是右括号“)”，则依次弹出s1栈顶的运算符，并压入s2，直到遇到左括号为止， 此时将这一对括号丢弃

\6. 重复步骤2至5，直到表达式的最右边

\7. 将s1中剩余的运算符依次弹出并压入s2

\8. 依次弹出s2中的元素并输出，结果的逆序即为中缀表达式对应的后缀表达式

9.递归

八皇后问题：在8*8格的国际象棋上摆放八个皇后，使其不能互相攻击，即： 任意两个皇后都不能处于同一行，同一列或同一斜线上，问有多少种摆法？ 解法思路： 1):第一个皇后先放在第一行第一列 2):第二个皇后放在第二行的第一列，然后判断是否ok，如果不ok，继续放在第 二列，第三列，依次把所有列都放完，找到一个合适的位置 3):继续放第三个皇后，还是从第一列，第二列....直到第八个皇后也能放在一个 不冲突的位置，算是找到了一个正确解 4):当得到一个正确蛸时，在栈回退到上一个栈时，就会开始回溯，即将第一个皇后 ，放到第一列的所有正确，全部得到 5):然后回头继续第一个皇后放在第二列，后面继续循环执行1,2,3,4步骤  说明： 理论上应该创建一个二维数组来表示棋盘，但是实际上可以通过算法，用一个一维 数组即可解决问题，如arr[8] = {0,4,7,5,2,6,1,3} 对应arr的下标表示第几行，即第几个皇后,arr[i]=value,value表示第i+1个皇后 放在i+1行的第value+1列 

10.排序

时间复杂度：常见的算法时间复杂度由小到大依次为：Ο(1)＜Ο(log2n)＜Ο(n)＜Ο(nlog2n)＜Ο(n2)＜Ο(n3)＜ Ο(nk) ＜Ο(2n) ，随着问题规模n的不断增大，上述时间复杂度不断增大，算法的执行效率越低

排序的分类：

  1) ：内部排序:指将需要处理的所有数据都加载到内部存储器中进行排序。

  2) ：外部排序法：数据量过大，无法全部加载到内存中，需要借助外部存储进行

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/0245ab89e9074ca2aa731a54d68d8270/clipboard.png)

11.希尔排序：

1. 希尔排序时， 对有序序列在插入时采用交换法, 并测试排序速度.

​       2.希尔排序时， 对有序序列在插入时采用移动法, 并测试排序速度

图解：

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/22306fb0c79c44c08cc0559473e224a2/clipboard.png)

12.快速排序

  是对冒泡排序的一种改进。基本思想是：通过一趟排序将要排序的数据分割成独立的两部分，其中一部分的所有数据都比另外一部分的所有数据都要小，然后再按此方法对这两部分数据分别进行快速排序，整个排序过程可以递归进行，以此达到整个数据变成有序序列

图解：

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/cf88e66e5d5d4fdfb745285334c092f7/clipboard.png)

13.归并排序：是利用归并的思想实现的排序方法，该算法采用经典的分治（divide-and-conquer）策略（分治法将问题分(divide)成一些小的问题然后递归求解，而治(conquer)的阶段则将分的阶段得到的各答案"修补"在一起，即分而治之)。

  

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/1ab62408a2df4c74a0a5f5768082e7e0/clipboard.png)

  我们需要将两个已经有序的子序列合并成一个有序序列，比如上图中的最后一次合并，要将[4,5,7,8]和[1,2,3,6]两个已经有序的子序列，合并为最终序列[1,2,3,4,5,6,7,8]，来看下实现步骤

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/06e5fdf9f17a46f79f668afafda82b88/clipboard.png)

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/1ef61df4265144fb82b784ddf3db34ba/clipboard.png)

14.基数排序

  属于“分配式排序”（distribution sort），又称“桶子法”（bucket sort）或bin sort，顾名思义，它是通过键值的各个位的值，将要排序的元素分配至某些“桶”中，达到排序的作用

思想：

将所有待比较数值统一为同样的数位长度，数位较短的数前面补零。然后，从最低位开始，依次进行一次排序。这样从最低位排序一直到最高位排序完成以后, 数列就变成一个有序序列。

15.各种排序的对比

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/cf3f1e585f5246ea9bd5d051000aa3c4/clipboard.png)

16.查找算法介绍:

​	在java中，我们常用的查找有四种:

​	1) 顺序(线性)查找

​	2) 二分查找/折半查找

​        3) 插值查找

​        4) 斐波那契查找

17.插值查找算法：

  插值查找算法类似于二分查找，不同的是插值查找每次从自适应mid处开始查找。

将折半查找中的求mid 索引的公式 , low 表示左边索引left, high表示右边索引right.key 就是前面我们讲的  findVal，key为我们要查找的值

/**mid 是一个自适应的值*/ int mid = left+(right-left)*(value-num[left])/(num[right]-num[left]); 

/*插值索引*/对应前面的代码公式：int mid = left + (right – left) * (findVal – arr[left]) / (arr[right] – arr[left])

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/7536215f8e9a47db89a78f7235033cff/clipboard.png)

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/06106cee6b764f3096bba6198c75663a/clipboard.png)

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/cbdcd15ad65d4756a71394069cdab592/clipboard.png)

18.斐波那契(黄金分割法)查找算法

斐波那契(黄金分割法)原理:

斐波那契查找原理与前两种相似，仅仅改变了中间结点（mid）的位置，mid不再是中间或插值得到，而是位于黄金分割点附近，即mid=low+F(k-1)-1（F代表斐波那契数列）

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/d53d0d69e98541e0b32ddda373fe6a30/clipboard.png)

对F(k-1)-1的理解：

由斐波那契数列 F[k]=F[k-1]+F[k-2] 的性质，可以得到 （F[k]-1）=（F[k-1]-1）+（F[k-2]-1）+1 。该式说明：只要顺序表的长度为F[k]-1，则可以将该表分成长度为F[k-1]-1和F[k-2]-1的两段，即如上图所示。从而中间位置为mid=low+F(k-1)-1           

类似的，每一子段也可以用相同的方式分割

但顺序表长度n不一定刚好等于F[k]-1，所以需要将原来的顺序表长度n增加至F[k]-1。这里的k值只要能使得F[k]-1恰好大于或等于n即可，由以下代码得到,顺序表长度增加后，新增的位置（从n+1到F[k]-1位置），都赋为n位置的值即可。

19.哈希表(数组+链表，数组+二叉树)

  散列表（Hash table，也叫哈希表），是根据关键码值(Key value)而直接进行访问的数据结构。也就是说，它通过把关键码值映射到表中一个位置来访问记录，以加快查找的速度。这个映射函数叫做散列函数，存放记录的数组叫做散列表。

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/ec8d7a9a652d4292a3644998220e7dcb/clipboard.png)

20.二叉树

为什么需要树这种数据结构？

   数组存储方式的分析优点：通过下标方式访问元素，速度快。对于有序数组，还可使用二分查找提高检索速度。缺点：如果要检索具体某个值，或者插入值(按一定顺序)会整体移动，效率较低 [示意图]

   链式存储方式的分析优点：在一定程度上对数组存储方式有优化(比如：插入一个数值节点，只需要将插入节点，链接到链表中即可， 删除效率也很好)。缺点：在进行检索时，效率仍然较低，比如(检索某个值，需要从头节点开始遍历)

   树存储方式的分析能提高数据存储，读取的效率,  比如利用 二叉排序树(Binary Sort Tree)，既可以保证数据的检索速度，同时也可以保证数据的插入，删除，修改的速度。

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/1e42cc0999994c86b70bf337f0781aa1/clipboard.png)

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/b30f89a7fb7549d9b94156bafe5e945c/clipboard.png)

21.顺序存储二叉树

基本说明

从数据存储来看，数组存储方式和树

的存储方式可以相互转换，即数组可

以转换成树，树也可以转换成数组，

看右面的示意图。

要求:

右图的二叉树的结点，要求以数组的方式来存放 arr : [1, 2, 3, 4, 5, 6, 6]

要求在遍历数组 arr时，仍然可以以前序遍历，中序遍历和后序遍历的方式完成结点的遍历

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/4ebdbc37e833448195cd2840b9a78107/clipboard.png)

22.线索二叉树

   n个结点的二叉链表中含有n+1  【公式 2n-(n-1)=n+1】 个空指针域。利用二叉链表中的空指针域，存放指向该结点在某种遍历次序下的前驱和后继结点的指针（这种附加的指针称为"线索"）

这种加上了线索的二叉链表称为线索链表，相应的二叉树称为线索二叉树(Threaded BinaryTree)。根据线索性质的不同，线索二叉树可分为前序线索二叉树、中序线索二叉树和后序线索二叉树三种

一个结点的前一个结点，称为前驱结点

一个结点的后一个结点，称为后继结点

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/027d208532224f6d8f57787a033b8a21/13749e7a31c04e0fa71835d00dff41a5.jpg)

23.堆排序

堆排序是利用堆这种数据结构而设计的一种排序算法，堆排序是一种选择排序，它的最坏，最好，平均时间复杂度均为O(nlogn)，它也是不稳定排序。

堆是具有以下性质的完全二叉树：每个结点的值都大于或等于其左右孩子结点的值，称为大顶堆, 注意 : 没有要求结点的左孩子的值和右孩子的值的大小关系。

每个结点的值都小于或等于其左右孩子结点的值，称为小顶堆

我们对堆中的结点按层进行编号，映射到数组中就是下面这个样子: 

大顶堆特点：arr[i] >= arr[2*i+1] && arr[i] >= arr[2*i+2]  // i 对应第几个节点，i从0开始编号

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/32eeb10b7976439a9408a05dec431f30/clipboard.png)

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/8c16fc99c5ae4c2ba87bab10ad3d4040/clipboard.png)

小顶堆：arr[i] <= arr[2*i+1] && arr[i] <= arr[2*i+2] // i 对应第几个节点，i从0开始编号

一般升序采用大顶堆，降序采用小顶堆 

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/25134ff58dec4cb09ce9ab682b5b2937/clipboard.png)

堆排序的思想：

- 将待排序序列构造成一个大顶堆
- 此时，整个序列的最大值就是堆顶的根节点。
- 将其与末尾元素进行交换，此时末尾就为最大值。
- 然后将剩余n-1个元素重新构造成一个堆，这样会得到n个元素的次小值。如此反复执行，便能得到一个有序序列了。
- 可以看到在构建大顶堆的过程中，元素的个数逐渐减少，最后就得到一个有序序列了.

24.霍夫曼树

  给定n个权值作为n个叶子结点，构造一棵二叉树，若该树的带权路径长度(wpl)达到最小，称这样的二叉树为最优二叉树，也称为哈夫曼树(Huffman Tree), 还有的书翻译为霍夫曼树。

赫夫曼树是带权路径长度最短的树，权值较大的结点离根较近

构成赫夫曼树的步骤：

1. 从小到大进行排序, 将每一个数据，每个数据都是一个节点 ， 每个节点可以看成是一颗最简单的二叉树
2. 取出根节点权值最小的两颗二叉树 
3. 组成一颗新的二叉树, 该新的二叉树的根节点的权值是前面两颗二叉树根节点权值的和  
4. 再将这颗新的二叉树，以根节点的权值大小 再次排序， 不断重复  1-2-3-4 的步骤，直到数列中，所有的数据都被处理，就得到一颗赫夫曼树

25.霍夫曼编码

  

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/d9aa43fc4010425c9262941367f768bb/clipboard.png)

对应的霍夫曼编码

  /根据赫夫曼树，给各个字符

//规定编码 ， 向左的路径为0

//向右的路径为1 ， 编码如下:

o: 1000   u: 10010  d: 100110  y: 100111  i: 101

a : 110     k: 1110    e: 1111       j: 0000       v: 0001

l: 001          : 01

按照上面的赫夫曼编码，我们的"i like like like java do you like a java"   字符串对应的编码为 (注意这里我们使用的无损压缩)

1010100110111101111010011011110111101001101111011110100001100001110011001111000011001111000100100100110111101111011100100001100001110

长度为 ： 133 

说明:

- 原来长度是  359 , 压缩了  (359-133) / 359 = 62.9%
- 此编码满足前缀编码, 即字符的编码都不能是其他字符编码的前缀。不会造成匹配的多义性

25.二叉排序树

  二叉排序树：BST: (Binary Sort(Search) Tree), 对于二叉排序树的任何一个非叶子节点，要求左子节点的值比当前节点的值小，右子节点的值比当前节点的值大。

特别说明：如果有相同的值，可以将该节点放在左子节点或右子节点

比如针对前面的数据 (7, 3, 10, 12, 5, 1, 9) ，对应的二叉排序树为：

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/a0a571e75d26451c80d3962e05064f13/clipboard.png)

插入2

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/77ee537b5d894a27a54aec7745cb2f82/clipboard.png)

27.二叉排序树的节点删除

思想：

二叉排序树删除节点的三种情况: * 一：删除的是叶子节点 *   1):先找到要删除的节点 targetNode *   2):找到要删除节点的父节点 parentNode (还需要考虑是否存在父节点) *   3):确定 targetNode 是 parentNode 的左子节点还是右子节点，再确定删除 *   4):根据以上情况来对应删除 parentNode.left=null,parentNode.right=null * * 二：删除只有一棵子树的节点 *   1):先找到要删除的节点 targetNode *   2):找到要删除节点的父节点 parentNode (还需要考虑是否存在父节点) *   3):确定 targetNode的子节点是 左子节点还是右子节点 *   4):确定 targetNode是parentNode 的左子节点还是右子节点 *   5):如果 targetNode是parentNode的左子节点，并且targetNode的子节点是左子节点 *    5.1):parentNode.left = targetNode.left *    5.2):如果 targetNode是parentNode的左子节点，并且targetNode的子节点是右子节点 *    5.3):parentNode.left = targetNode.right *   6):如果 targetNode是parentNode的右子节点，并且targetNode的子节点是左子节点 *     6.1):parentNode.right = targetNode.left *     6.2):如果 targetNode是parentNode的右子节点，并且targetNode的子节点是右子节点 *     6.3):parentNode.right = targetNode.right * * 三：删除有左右两边都有子树的节点 *   1):先找到要删除的节点 targetNode *   2):找到要删除节点的父节点 parentNode (还需要考虑是否存在父节点) *   3):从targetNode的右子树找到最小的节点 (如果从targetNode的左子树找，就应该找最大的节点) *   4):用一个临时变量，将最小节点的值保存 temp = minValue *   5):删除该最小的节点 *   6):targetNode.value = temp; 

28.平衡二叉树（AVL树，前提是一个二叉排序树）

基本介绍

  平衡二叉树也叫平衡二叉搜索树（Self-balancing binary search tree）又被称为AVL树， 可以保证查询效率较高。

  具有以下特点：它是一 棵空树或它的左右两个子树的高度差的绝对值不超过1，并且左右两个子树都是一棵平衡二叉树。平衡二叉树的常用实现方法有红黑树、AVL、替罪羊树、Treap、伸展树等。

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/87166e9d18a44ab68cb34955b86d7792/clipboard.png)

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/42a57e968c054e00a0423e07515a5f57/clipboard.png)

问题分析：

1. 当满足右旋转的条件时
2. 如果它的左子树的右子树高度大于它的左子树的高度
3. 先对当前这个节点进行左旋转
4. 再对当前节点进行右旋转的操作

/**当添加完一个节点后，如果右子树的高度与左子树的高度差值大于1，则进行左旋转*/ if(this.getRightHeight() - this.getLeftHeight() > 1){    /**如果它的右子树的左子树高度大于它的右子树的高度，先对它的右子树进行右旋转*/    if(null != this.right && this.right.getRightHeight() < this.right.getLeftHeight()){        this.right.rightRotate();    }    this.leftRotate();    /**这个return 是必须要的,因为已经平衡了，下面没必要再去执行*/    return; } /**当添加完一个节点后，如果左子树的高度与右子树的高度差值大于1，则进行右旋转*/ if(this.getLeftHeight() - this.getRightHeight() > 1){    /**     * 当满足右旋转的条件时     * 如果它的左子树的右子树高度大于它的左子树的高度     * 先对当前这个节点进行左旋转     * 再对当前节点进行右旋转的操作     */    if(null != this.left && this.left.getRightHeight() > this.left.getLeftHeight()){        this.left.leftRotate();    }    this.rightRotate(); } 

29.多叉树（依然要满足二叉排序树的特点）

  二叉树存在的缺点：

- 二叉树需要加载到内存的，如果二叉树的节点少，没有什么问题，但是如果二叉树的节点很多(比如1亿)， 就存在如下问题:
- 问题1：在构建二叉树时，需要多次进行i/o操作(海量数据存在数据库或文件中)，节点海量，构建二叉树时，速度有影响
- 问题2：节点海量，也会造成二叉树的高度很大，会降低操作速度.

多叉树的定义：

  在二叉树中，每个节点有一个数据项，最多有两个子节点。如果允许每个节点可以有更多的数据项和更多的子节点，就是多叉树（multiway tree）

后面我们讲解的2-3树，2-3-4树就是多叉树，多叉树通过重新组织节点，减少树的高度，能对二叉树进行优化。

举例说明(下面2-3树就是一颗多叉树)

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/193d8567354d4967b5ed33b92a01ee02/clipboard.png)

2-3树基本介绍

  2-3树是最简单的B树结构, 具有如下特点:

- 2-3树的所有叶子节点都在同一层.(只要是B树都满足这个条件)
- 有两个子节点的节点叫二节点，二节点要么没有子节点，要么有两个子节点.
- 有三个子节点的节点叫三节点，三节点要么没有子节点，要么有三个子节点.
- 2-3树是由二节点和三节点构成的树。

插入规则:

- 2-3树的所有叶子节点都在同一层.(只要是B树都满足这个条件)
- 有两个子节点的节点叫二节点，二节点要么没有子节点，要么有两个子节点.
- 有三个子节点的节点叫三节点，三节点要么没有子节点，要么有三个子节点
- 当按照规则插入一个数到某个节点时，不能满足上面三个要求，就需要拆，先向上拆，如果上层满，则拆本层，拆后仍然需要满足上面3个条件。 
- 对于三节点的子树的值大小仍然遵守(BST 二叉排序树)的规则

30.B树、B+树和B*树

  B-tree树即B树，B即Balanced，平衡的意思。有人把B-tree翻译成B-树，容易让人产生误解。会以为B-树是一种树，而B树又是另一种树。实际上，B-tree就是指的B树

B树的说明:

- B树的阶：节点的最多子节点个数。比如2-3树的阶是3，2-3-4树的阶是4
- B-树的搜索，从根结点开始，对结点内的关键字（有序）序列进行二分查找，如果命中则结束，否则进入查询关键字所属范围的儿子结点；重复，直到所对应的儿子指针为空，或已经是叶子结点

- 关键字集合分布在整颗树中, 即叶子节点和非叶子节点都存放数据.
- 搜索有可能在非叶子结点结束
- 其搜索性能等价于在关键字全集内做一次二分查找

如图B树：

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/c00a2bb8e89741ccbae22ed750d2d047/clipboard.png)

B+树的说明（B+树是B树的变体，也是一种多路搜索树）:

- B+树的搜索与B树也基本相同，区别是B+树只有达到叶子结点才命中（B树可以在非叶子结点命中），其性能也等价于在关键字全集做一次二分查找
- 所有关键字都出现在叶子结点的链表中（即数据只能在叶子节点【也叫稠密索引】），且链表中的关键字(数据)恰好是有序的。
- 不可能在非叶子结点命中
- 非叶子结点相当于是叶子结点的索引（稀疏索引），叶子结点相当于是存储（关键字）数据的数据层

- 更适合文件索引系统
- B树和B+树各有自己的应用场景，不能说B+树完全比B树好，反之亦然.

如图B+树：

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/d3be7c745b554c75a7489deb248d96c8/clipboard.png)

B*树的说明（B*树是B+树的变体，在B+树的非根和非叶子结点再增加指向兄弟的指针）:

- B*树定义了非叶子结点关键字个数至少为(2/3)*M，即块的最低使用率为2/3，而B+树的块的最低使用率为B+树的1/2。
- 从第1个特点我们可以看出，B*树分配新结点的概率比B+树要低，空间使用率更高

如图B*树：

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/50eaf15c629b4569ba0e4ce86944aa66/clipboard.png)

30.图

  为什么要有图

- 前面我们学了线性表和树
- 线性表局限于一个直接前驱和一个直接后继的关系
- 树也只能有一个直接前驱也就是父节点
- 当我们需要表示多对多的关系时， 这里我们就用到了图

图是一种数据结构，其中结点可以具有零个或多个相邻元素。两个结点之间的连接称为边。 结点也可以称为顶点。如图：

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/0ca66bfe283142ebb36fe4f7bcaa9ea1/55645ce51a034876bcfc3418295097c5.jpg)

图的表示方式有两种：二维数组表示（邻接矩阵）；链表表示（邻接表）

邻接矩阵

邻接矩阵是表示图形中顶点之间相邻关系的矩阵，对于n个顶点的图而言，矩阵是的row和col表示的是1....n个点。

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/9701c9c68a004cf6a0c428fa76f2402f/d0bf1f9245c74ee18b3c3115567446d0.jpg)

邻接表

邻接矩阵需要为每个顶点都分配n个边的空间，其实有很多边都是不存在,会造成空间的一定损失.

邻接表的实现只关心存在的边，不关心不存在的边。因此没有空间浪费，邻接表由数组+链表组成

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/c3452659bc8b43ffa41dde09414728ae/414c65b331454548b3de11d5d89016a8.jpg)

31.图的遍历

- 深度优先遍历
-  广度优先遍历

图的深度优先搜索(Depth First Search) ：

1. 深度优先遍历，从初始访问结点出发，初始访问结点可能有多个邻接结点，深度优先遍历的策略就是首先访问第一个邻接结点，然后再以这个被访问的邻接结点作为初始结点，访问它的第一个邻接结点， 可以这样理解：每次都在访问完当前结点后首先访问当前结点的第一个邻接结点。
2. 我们可以看到，这样的访问策略是优先往纵向挖掘深入，而不是对一个结点的所有邻接结点进行横向访问。
3. 显然，深度优先搜索是一个递归的过程

深度优先遍历算法步骤:

1. 访问初始结点v，并标记结点v为已访问。
2. 查找结点v的第一个邻接结点w。
3. 若w存在，则继续执行4，如果w不存在，则回到第1步，将从v的下一个结点继续。
4. 若w未被访问，对w进行深度优先遍历递归（即把w当做另一个v，然后进行步骤123）。
5. 查找结点v的w邻接结点的下一个邻接结点，转到步骤3。

广度优先遍历基本思想：

  广度优先遍历需要使用一个队列以保持访问过的结点的顺序，以便按这个顺序来访问些结点的邻接结点.

​     广度优先遍历算法步骤:

1. 访问初始结点v并标记结点v为已访问。
2. 结点v入队列
3. 当队列非空时，继续执行，否则算法结束。
4. 出队列，取得队头结点index。
5. 查找结点index的第一个邻接结点w。
6. 若结点index的邻接结点w不存在，则转到步骤3；否则循环执行以下三个步骤：

​         6.1 若结点w尚未被访问，则访问结点w并标记为已访问。 

​         6.2 结点w入队列 

​         6.3 查找结点index的继w邻接结点后的下一个邻接结点w，转到步骤6。

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/49440a603216471f8b1891ea7ea20dd9/clipboard.png)

对上图进行图的两种遍历方式：

- 深度优先遍历顺序为 1->2->4->8->5->3->6->7
- 广度优先算法的遍历顺序为：1->2->3->4->5->6->7->8 

32.
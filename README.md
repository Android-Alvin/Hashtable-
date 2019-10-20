### HashTable**

- 底层数组+链表实现，无论key还是value都**不能为null**，线程**安全**，实现线程安全的方式是在修改数据时锁住整个HashTable，效率低，ConcurrentHashMap做了相关优化。
- 初始size为**11**，扩容：newsize = olesize*2+1
- 计算index的方法：index = (hash & 0x7FFFFFFF) % tab.length

# 1、哈希冲突

题目描述

设有哈希函数 H ( key ) = key mod 7 ，哈希表的地址空间为 0 ～ 6 ，对关键字序列（ 32 ， 13 ， 49 ， 55 ， 22 ， 38 ， 21 ）按线性探测再散列和二次探测再散列的方法分别构造哈希表。 
题目解析
（ 1 ）线性探测再散列： 
32 ％ 7 = 4 ； 13 ％ 7 = 6 ； 49 ％ 7 = 0 ； 
55 ％ 7 = 6 发生冲突，下一个存储地址（ 6 ＋ 1 ）％ 7 ＝ 0 ，仍然发生冲突，再下一个存储地址：（ 6 ＋ 2 ）％ 7 ＝ 1 未发生冲突，可以存入。 
22 ％ 7 ＝ 1 发生冲突，下一个存储地址是：（ 1 ＋ 1 ）％ 7 ＝ 2 未发生冲突； 
38 ％ 7 ＝ 3 ； 
21 ％ 7 ＝ 0 发生冲突，按照上面方法继续探测直至空间 5 ，不发生冲突，所得到的哈希表对应存储位置： 
下标： 0 1 2 3 4 5 6 
49 55 22 38 32 21 13 
（ 2 ）二次探测再散列： 
下标： 0 1 2 3 4 5 6 
49 22 21 38 32 55 13 
 注意：对于利用开放地址法处理冲突所产生的哈希表中删除一个元素时需要谨慎，不能直接地删除，因为这样将会截断其他具有相同哈希地址的元素的查找地址，所以，通常采用设定一个特殊的标志以示该元素已被删除。  

# 2、线程安全

　题目描述：

 Hashtable 提供的线程安全的方法

　题目解析

**Hashtable的Synchronized静态方法提供线程安全的实例**，如下：

　　Hashtable ht = Hashtable.Synchronized(new Hashtable());

　　**内部实现如下：**

```
public override void Add(object key, object value)
{
    lock (this._table.SyncRoot)
    {
    　　this._table.Add(key, value);
    }
}
```

　　**按输入方式输出**

　　因为hashtable内部是无序的，所以输出不一定，hashtable取数据的机制没搞明白。按照下面代码可以实现先进先出。

可以通过控制ArrayList里面keys的排序来控制hashtable的输出，当然也可以用SortedDictionary和SortedList实现排序集合。

```
public class NoSortHashtable : Hashtable`

{

private ArrayList keys = new ArrayList();

public NoSortHashtable()

{

}

public override void Add(object key, object value)

{

base.Add (key, value);            
keys.Add (key);

}

public override ICollection Keys         
{

    get

     {  

     return keys;  

       }

 }

  public override void Clear()

    {

  base.Clear ();            
  keys.Clear ();`

 }

public override void Remove(object key)

 {

base.Remove (key);            
keys.Remove    (key);

 }

public override IDictionaryEnumerator GetEnumerator()

 {

returnbase.GetEnumerator ();
 }


}
```



# 3、两个数组的交集

### 题目解析

容器类 set 的使用。

遍历 num1，通过 set 容器 record 存储 num1 的元素
遍历 num2，在 record 中查找是否有相同的元素，如果有，用 set 容器 resultSet 进行存储
将 resultSet 转换为 vector 类型

### 动画描述

![7a408c787b235e787e9f656af34302c4ff5ad00dd08a0e33e85883b6219a8143](D:\360MoveData\Users\Administrator\Desktop\7a408c787b235e787e9f656af34302c4ff5ad00dd08a0e33e85883b6219a8143.gif)



### 代码实现

`// 时间复杂度: O(nlogn)
// 空间复杂度: O(n)
class Solution {
public:
    vector<int> intersect(vector<int>& nums1, vector<int>& nums2) {
        map<int, int> record;
        for(int i = 0 ; i < nums1.size() ; i ++){
             record[nums1[i]] += 1;
        }
        vector<int> resultVector;
        for(int i = 0 ; i < nums2.size() ; i ++){
            if(record[nums2[i]] > 0){
                resultVector.push_back(nums2[i]);
                record[nums2[i]] --;
            }
        }
        return resultVector;
    }
};`

# 4、回旋镖的数量

### 题目描述

给定平面上 n 对不同的点，“回旋镖” 是由点表示的元组 (i, j, k) ，其中 i 和 j 之间的距离和 i 和 k 之间的距离相等（需要考虑元组的顺序）。

找到所有回旋镖的数量。你可以假设 n 最大为 500，所有点的坐标在闭区间 [-10000, 10000] 中。

### 题目解析

n 最大为 500，可以使用时间复杂度为 O(n^2)的算法。

遍历所有的点，让每个点作为一个锚点
然后再遍历其他的点，统计和锚点距离相等的点有多少个
然后分别带入 n(n-1) 计算结果并累加到 res 中
注意点：

如果有一个点a，还有两个点 b 和 c ，如果 ab 和 ac 之间的距离相等，那么就有两种排列方法 abc 和 acb ；

如果有三个点b，c，d 都分别和 a 之间的距离相等，那么有六种排列方法，abc, acb, acd, adc, abd, adb；

如果有 n 个点和点 a 距离相等，那么排列方式为 n(n-1)；

计算距离时不进行开根运算, 以保证精度；

只有当 n 大于等于 2 时，res 值才会真正增加，因为当n=1时，增加量为1 * ( 1 - 1 ) = 0 。

### 动画描述

![ca44e0fc2539df2bc9a11a9a71cc0161d295153c021d32f7f7fb3af715501969](D:\360MoveData\Users\Administrator\Desktop\ca44e0fc2539df2bc9a11a9a71cc0161d295153c021d32f7f7fb3af715501969.gif)

### 代码实现

`// 时间复杂度: O(n^2)`
`// 空间复杂度: O(n)`
`class Solution {`
`public:`
    `int numberOfBoomerangs(vector<pair<int, int>>& points) {`
        `int res = 0;`
        `for( int i = 0 ; i < points.size() ; i ++ ){`
            `// record中存储 点i 到所有其他点的距离出现的频次`
            `unordered_map<int, int> record;`
            `for(int j = 0 ; j < points.size() ; j ++){`
                `if(j != i){`
                    `// 计算距离时不进行开根运算, 以保证精度`
                    `record[dis(points[i], points[j])] += 1;`
                `}`
            `}`
            `for(unordered_map<int, int>::iterator iter = record.begin() ; iter != record.end() ; iter ++){`
                `res += (iter->second) * (iter->second - 1);`
            `}`
        `}`
        `return res;`
    `}`

`private:`
    `int dis(const pair<int,int> &pa, const pair<int,int> &pb){`
        `return (pa.first - pb.first) * (pa.first - pb.first) +`
               `(pa.second - pb.second) * (pa.second - pb.second);`
    `}`
`};`

**最近面试被怼了？缺面试题刷提升自己吗？**

**点击:**

[Android 学习，面试文档，视频收集大整理](https://shimo.im/docs/vrvxvW8DY3RTDGGg)

**来获取学习资料+面试题视频解析提升自己去挑战一下BAT面试难关吧**

![15233854-8eacad8141424cef](D:\湖南享学信息科技有限公司\Android 相关\Android 发文\图片文件\15233854-8eacad8141424cef.png)

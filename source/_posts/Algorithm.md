---
title: JavaScript实现经典排序算法
date: 2020-01-04 07:18:22
categories: 
  - 前端技术
tags: 
  - 排序算法
  - 面试
---
> 作者：李旭光
> 引用请标明出处


# 基本概念
## 时间复杂度
一个算法的时间复杂度反映了程序运行从开始到结束所需要的时间。把算法中基本操作重复执行的次数（频度）作为算法的时间复杂度。
没有循环语句，记作O(1)，也称为常数阶。只有一重循环，则算法的基本操作的执行频度与问题规模n呈线性增大关系，记作O（n），也叫线性阶。
常见的时间复杂度有：

- O(1): Constant Complexity: Constant 常数复杂度
- O(log n): Logarithmic Complexity: 对数复杂度
- O(n): Linear Complexity: 线性时间复杂度
- O(n^2): N square Complexity 平⽅方
- O(n^3): N square Complexity ⽴立⽅方
- O(2^n): Exponential Growth 指数
- O(n!): Factorial 阶乘

## 空间复杂度
一个程序的空间复杂度是指运行完一个程序所需内存的大小。利用程序的空间复杂度，可以对程序的运行所需要的内存多少有个预先估计。

一个程序执行时除了需要存储空间和存储本身所使用的指令、常数、变量和输入数据外，还需要一些对数据进行操作的工作单元和存储一些为现实计算所需信息的辅助空间。

- 稳定
- 不稳定
# 算法汇总
![十大经典排序.jpg](https://i.loli.net/2020/01/04/TqaRjEJSnhtQ5vH.png)
## 关于时间复杂度：
平方阶 (O(n2)) 排序 各类简单排序：直接插入、直接选择和冒泡排序。
线性对数阶 (O(nlog2n)) 排序 快速排序、堆排序和归并排序；
O(n1+§)) 排序，§ 是介于 0 和 1 之间的常数。 希尔排序
线性阶 (O(n)) 排序 基数排序，此外还有桶、箱排序。

## 关于稳定性：
稳定的排序算法：冒泡排序、插入排序、归并排序和基数排序。
不是稳定的排序算法：选择排序、快速排序、希尔排序、堆排序。

## 名词解释：
- n：数据规模
- k：“桶”的个数
- In-place：占用常数内存，不占用额外内存
- Out-place：占用额外内存
- 稳定性：排序后 2 个相等键值的顺序和排序之前它们的顺序相同

# 1. 冒泡排序（Bubble Sort） --- 前后两两比较 --- 气泡
冒泡排序可谓是最经典的排序算法了，它是基于比较的排序算法，其优点是实现简单，排序数量较小时性能较好。

它重复地走访过要排序的数列，一次比较两个元素，如果它们的顺序错误就把它们交换过来。走访数列的工作是重复地进行直到没有再需要交换，也就是说该数列已经排序完成。这个算法的名字由来是因为越小的元素会经由交换慢慢“浮”到数列的顶端。

## 1. 1 算法原理
相邻的数据进行两两比较，小数放在前面，大数放在后面，如果前面的数据比后面的数据大，就交换这两个数的位置。也可以实现大数放在前面，小数放在后面，如果前面的数据比后面的小，就交换两个的位置。要实现上述规则需要用到两层for循环。

## 1. 2 算法描述
1. 比较相邻的元素。如果第一个比第二个大，就交换它们两个；
2. 对每一对相邻元素作同样的工作，从开始第一对到结尾的最后一对，这样在最后的元素应该会是最大的数；
3. 针对所有的元素重复以上的步骤，除了最后一个；
4. 重复步骤1~3，直到排序完成。

## 1. 3 动图演示
![ldB5VS.gif](https://s2.ax1x.com/2020/01/04/ldB5VS.gif)
## 1. 4 js代码实现
```
// js代码

function bubbleSort(arr) {
  var len = arr.length;
  for (var i = 0; i < len; i++) {
    for (var j = 0; j < len - 1 - i; j++) {
      if (arr[j] > arr[j + 1]) { // 相邻元素两两对比
      // 元素交换
      /** 1.使用中间变量 **/ 
        var temp = arr[j + 1]; 
        arr[j + 1] = arr[j]
        arr[j] = temp
        /** 2.适用纯数字的数组排序 **/
        arr[j] = arr[j] +  arr[j + 1]
        arr[j + 1] =  arr[j] - arr[j + 1]
        arr[j] -= arr[j + 1]
        /** 3.使用es6解构赋值 **/
        [arr[j], arr[j + 1]] = [arr[j + 1], arr[j]]
      }
    }
  }
  return arr;
}
```
冒泡排序算法优化
```
// js代码

function bubbleSort(arr) {
  var len = arr.length;
  for (var i = 0; i < len; i++) {
    var exchange=false; // 交换标志 
    for (var j = 0; j < len - 1 - i; j++) {
      if (arr[j] > arr[j + 1]) { // 相邻元素两两对比
        [arr[j], arr[j + 1]] = [arr[j + 1], arr[j]] // 元素交换
        exchange=true; //
      }
    }
    if(!exchange){ // 若本趟排序未发生交换，提前终止算法
        break;
    }
  }
  return arr;
}
```
# 2. 选择排序（Selection Sort） --- 面试挑简历，在剩下的里面挑最好的
表现最稳定的排序算法之一，因为无论什么数据进去都是O(n²)的时间复杂度。。。所以用到它的时候，数据规模越小越好。唯一的好处可能就是不占用额外的内存空间了吧。

## 2. 1 算法原理
先在未排序序列中找到最小（大）元素，存放到排序序列的起始位置，然后，再从剩余未排序元素中继续寻找最小（大）元素，然后放到已排序序列的末尾。以此类推，直到所有元素均排序完毕。

## 2. 2 算法描述
n个记录的直接选择排序可经过n-1趟直接选择排序得到有序结果。具体算法描述如下：

1. 初始状态：无序区为R[1..n]，有序区为空；
2. 第i趟排序(i=1,2,3…n-1)开始时，当前有序区和无序区分别为R[1..i-1]和R(i..n）。该趟排序从当前无序区中
3.  选出关键字最小的记录R[k]，将它与无序区的第1个记录R交换，使R[1..i]和R[i+..n)分别变为记录个数增加1个的新有序区和记录个数减少1个的新无序区；
4.  n-1趟结束，数组有序化了。

## 2. 3 动图演示
![ldDWW9.gif](https://s2.ax1x.com/2020/01/04/ldDWW9.gif)
## 2. 4 js代码实现
```
// js代码

function selectionSort(arr) {
    var len = arr.length;
    var minIndex, temp;
    for (var i = 0; i < len - 1; i++) {
        minIndex = i;
        for (var j = i + 1; j < len; j++) {
            if (arr[j] < arr[minIndex]) {     //寻找最小的数
                minIndex = j;                 //将最小数的索引保存
            }
        }
        temp = arr[i];
        arr[i] = arr[minIndex];
        arr[minIndex] = temp;
    }
    return arr;
}
```

# 3. 插入排序（Insertion Sort）----- 麻将/扑克
插入排序（Insertion-Sort）的算法描述是一种简单直观的排序算法。

## 3. 1 算法原理
它的工作原理是通过构建有序序列，对于未排序数据，在已排序序列中从后向前扫描，找到相应位置并插入。插入排序在实现上，通常采用in-place排序（即只需用到O(1)的额外空间的排序），因而在从后向前扫描过程中，需要反复把已排序元素逐步向后挪位，为最新元素提供插入空间。

## 3. 2 算法描述
一般来说，插入排序都采用in-place在数组上实现。具体算法描述如下：

1. 从第一个元素开始，该元素可以认为已经被排序；
2. 取出下一个元素，在已经排序的元素序列中从后向前扫描；
3. 如果该元素（已排序）大于新元素，将该元素移到下一位置；
4. 重复步骤3，直到找到已排序的元素小于或者等于新元素的位置；
5. 将新元素插入到该位置后；
6. 重复步骤2~5。

## 3. 3 动图演示
![ldDfzR.gif](https://s2.ax1x.com/2020/01/04/ldDfzR.gif)
## 3. 4 js代码实现
```
// js代码

function insertSort(arr) {
  // 从1位置开始遍历arr中每元素，同时声明空变量temp
  for (let i = 1; i < arr.length; i++) {
    if (arr[i] < arr[i - 1]) { // 如果当前元素<前一个元素
      let temp = arr[i] // 将当前元素值临时保存在temp中
      let p = i - 1 // 定义变量 p = i- 1
      // 循环 条件：
      // 1. p>=0且temp小于p位置的元素
      while (p >= 0 && temp < arr[p]) {
        // 循环体： 将P位置的值赋值给p的后一个元素
        arr[p + 1] = arr[p]
        p-- // p向前移动一个
      }
      arr[p + 1] = temp // 将temp的值赋值给p+1位置的元素
    }
  }
}
```

# 4. 快速排序（Selection Sort）
快速排序是由东尼·霍尔所发展的一种排序算法。在平均状况下，排序 n 个项目要 Ο(nlogn) 次比较。在最坏状况下则需要 Ο(n2) 次比较，但这种状况并不常见。事实上，快速排序通常明显比其他 Ο(nlogn) 算法更快，因为它的内部循环（inner loop）可以在大部分的架构上很有效率地被实现出来。
## 4. 1 算法原理
快速排序又是一种分而治之思想在排序算法上的典型应用。本质上来看，快速排序应该算是在冒泡排序基础上的递归分治法。

## 4. 2 算法描述
选基准：在数据结构中选择一个元素作为基准(pivot
划分区：参照基准元素值的大小，划分无序区，所有小于基准元素的数据放入一个区间，所有大于基准元素的数据放入另一区间，分区操作结束后，基准元素所处的位置就是最终排序后它应该所处的位置
递归：对初次划分出来的两个无序区间，递归调用第 1步和第 2步的算法，直到所有无序区间都只剩下一个元素为止。

简单理解就是，选择一个目标值，比目标值小的放左边，比目标值大的放右边，目标值的位置已排好，将左右两侧再进行快排。
## 4. 3 动图演示
![快速排序.gif](https://i.loli.net/2020/01/04/AtNahbZF9J1dTnS.gif)
## 4. 4 js代码实现
```
// js代码

function quickSort(arr){
    //如果arr.length<=1,则直接返回arr
    if(arr.length<=1){return arr}
    // arr的元素个数/2，再下去整，将值保存在pivotIndex中
    var pivotIndex=Math.floor(arr.length/2);
    // 将arr中pivotIndex位置的元素，保存在变量pivot中
    var pivot=arr[pivotIndex];
    //声明空数组left和right
    var left=[];
    var right=[];
    for(var i=0;i<arr.length;i++){  // 遍历arr中每个元素
        if(i !== pivotIndex){ // 如果i !== pivotIndex
            if(arr[i]<=pivot){ // 如果当前元素值<pivot
                left.push(arr[i]); // 就将当前值压入left
            }else{
                right.push(arr[i]); // 就将当前值压入right
            }
        }
    }
    //递归
    return quickSort(left).concat(pivot, quickSort(right)); // 链接多个数组到 left 从小到大
}
```
# 5. 希尔排序
## 5. 1 算法原理
希尔排序，也称递减增量排序算法，是插入排序的一种更高效的改进版本。但希尔排序是非稳定排序算法。
希尔排序是基于插入排序的以下两点性质而提出改进方法的：
- 插入排序在对几乎已经排好序的数据操作时，效率高，即可以达到线性排序的效率；
- 但插入排序一般来说是低效的，因为插入排序每次只能将数据移动一位；
希尔排序的基本思想是：先将整个待排序的记录序列分割成为若干子序列分别进行直接插入排序，待整个序列中的记录“基本有序”时，再对全体记录进行依次直接插入排序。

## 5. 2 算法描述
- 选择一个增量序列 t1，t2，……，tk，其中 ti > tj, tk = 1；
- 按增量序列个数 k，对序列进行 k 趟排序；
- 每趟排序，根据对应的增量 ti，将待排序列分割成若干长度为 m 的子序列，分别对各子表进行直接插入排序。仅增量因子为 1 时，整个序列作为一个表来处理，表长度即为整个序列的长度。

## 5.3 js代码实现
```
// js代码

function shellSort(arr) {
    var len = arr.length,
        temp,
        gap = 1;
    while(gap < len/3) {          //动态定义间隔序列
        gap =gap*3+1;
    }
    for (gap; gap > 0; gap = Math.floor(gap/3)) {
        for (var i = gap; i < len; i++) {
            temp = arr[i];
            for (var j = i-gap; j >= 0 && arr[j] > temp; j-=gap) {
                arr[j+gap] = arr[j];
            }
            arr[j+gap] = temp;
        }
    }
    return arr;
}
```

# 6. 归并排序
## 6. 1 算法原理
归并排序（Merge sort）是建立在归并操作上的一种有效的排序算法。该算法是采用分治法（Divide and Conquer）的一个非常典型的应用。
作为一种典型的分而治之思想的算法应用，归并排序的实现由两种方法：
- 自上而下的递归（所有递归的方法都可以用迭代重写，所以就有了第 2 种方法）；
- 自下而上的迭代；
> 在《数据结构与算法 JavaScript 描述》中，作者给出了自下而上的迭代方法。但是对于递归法，作者却认为：
However, it is not possible to do so in JavaScript, as the recursion goes too deep for the language to handle.
然而，在 JavaScript 中这种方式不太可行，因为这个算法的递归深度对它来讲太深了。

说实话，我不太理解这句话。意思是 JavaScript 编译器内存太小，递归太深容易造成内存溢出吗？还望有大神能够指教。

和选择排序一样，归并排序的性能不受输入数据的影响，但表现比选择排序好的多，因为始终都是 O(nlogn) 的时间复杂度。代价是需要额外的内存空间。

## 6. 2 算法描述
1. 申请空间，使其大小为两个已经排序序列之和，该空间用来存放合并后的序列；
2. 设定两个指针，最初位置分别为两个已经排序序列的起始位置；
3. 比较两个指针所指向的元素，选择相对小的元素放入到合并空间，并移动指针到下一位置；
4. 重复步骤 3 直到某一指针达到序列尾；
5. 将另一序列剩下的所有元素直接复制到合并序列尾。

## 6. 3 动图演示
![归并排序.gif](https://i.loli.net/2020/01/04/WMFo8Au4CGah6Kv.gif)
## 6. 4 js代码实现
```
// js代码

function mergeSort(arr) {  // 采用自上而下的递归方法
    var len = arr.length;
    if(len < 2) {
        return arr;
    }
    var middle = Math.floor(len / 2),
        left = arr.slice(0, middle),
        right = arr.slice(middle);
    return merge(mergeSort(left), mergeSort(right));
}

function merge(left, right){
    var result = [];
    while (left.length && right.length) {
        if (left[0] <= right[0]) {
            result.push(left.shift());
        } else {
            result.push(right.shift());
        }
    }

    while (left.length)
        result.push(left.shift());

    while (right.length)
        result.push(right.shift());

    return result;
}
```

# 7. 堆排序
## 7. 1 算法原理
堆排序（Heapsort）是指利用堆这种数据结构所设计的一种排序算法。堆积是一个近似完全二叉树的结构，并同时满足堆积的性质：即子结点的键值或索引总是小于（或者大于）它的父节点。堆排序可以说是一种利用堆的概念来排序的选择排序。分为两种方法：
- 大顶堆：每个节点的值都大于或等于其子节点的值，在堆排序算法中用于升序排列；
- 小顶堆：每个节点的值都小于或等于其子节点的值，在堆排序算法中用于降序排列；
堆排序的平均时间复杂度为 Ο(nlogn)。

# 7. 2 算法描述
1. 将待排序序列构建成一个堆 H[0……n-1]，根据（升序降序需求）选择大顶堆或小顶堆；
2. 把堆首（最大值）和堆尾互换；
3. 把堆的尺寸缩小 1，并调用 shift_down(0)，目的是把新的数组顶端数据调整到相应位置；
4. 重复步骤 2，直到堆的尺寸为 1。

## 7. 3 动图演示
![堆排序.gif](https://s2.ax1x.com/2020/01/05/lBK9QH.gif)

## 7. 4 js代码实现
```
// js代码

var len;    // 因为声明的多个函数都需要数据长度，所以把len设置成为全局变量

function buildMaxHeap(arr) {   // 建立大顶堆
    len = arr.length;
    for (var i = Math.floor(len/2); i >= 0; i--) {
        heapify(arr, i);
    }
}

function heapify(arr, i) {     // 堆调整
    var left = 2 * i + 1,
        right = 2 * i + 2,
        largest = i;

    if (left < len && arr[left] > arr[largest]) {
        largest = left;
    }

    if (right < len && arr[right] > arr[largest]) {
        largest = right;
    }

    if (largest != i) {
        swap(arr, i, largest);
        heapify(arr, largest);
    }
}

function swap(arr, i, j) {
    var temp = arr[i];
    arr[i] = arr[j];
    arr[j] = temp;
}

function heapSort(arr) {
    buildMaxHeap(arr);

    for (var i = arr.length-1; i > 0; i--) {
        swap(arr, 0, i);
        len--;
        heapify(arr, 0);
    }
    return arr;
}
```

# 8. 计数排序
## 8. 1 算法原理
计数排序的核心在于将输入的数据值转化为键存储在额外开辟的数组空间中。作为一种线性时间复杂度的排序，计数排序要求输入的数据必须是有确定范围的整数。

## 8. 2 算法描述
## 8. 3 动图演示
![计数排序.gif](https://s2.ax1x.com/2020/01/05/lBKq1g.gif)
## 8. 4 js代码实现
```
// js代码

function countingSort(arr, maxValue) {
    var bucket = new Array(maxValue+1),
        sortedIndex = 0;
        arrLen = arr.length,
        bucketLen = maxValue + 1;

    for (var i = 0; i < arrLen; i++) {
        if (!bucket[arr[i]]) {
            bucket[arr[i]] = 0;
        }
        bucket[arr[i]]++;
    }

    for (var j = 0; j < bucketLen; j++) {
        while(bucket[j] > 0) {
            arr[sortedIndex++] = j;
            bucket[j]--;
        }
    }

    return arr;
}
```

# 9. 桶排序
## 9. 1 算法原理
桶排序是计数排序的升级版。它利用了函数的映射关系，高效与否的关键就在于这个映射函数的确定。为了使桶排序更加高效，我们需要做到这两点：
- 在额外空间充足的情况下，尽量增大桶的数量
- 使用的映射函数能够将输入的 N 个数据均匀的分配到 K 个桶中
同时，对于桶中元素的排序，选择何种比较排序算法对于性能的影响至关重要。
## 9. 2 算法描述
### 1. 什么时候最快
当输入的数据可以均匀的分配到每一个桶中。
### 2. 什么时候最慢
当输入的数据被分配到了同一个桶中。
## 9. 3 动图演示

## 9. 4 js代码实现
```
// js代码

function bucketSort(arr, bucketSize) {
    if (arr.length === 0) {
      return arr;
    }

    var i;
    var minValue = arr[0];
    var maxValue = arr[0];
    for (i = 1; i < arr.length; i++) {
      if (arr[i] < minValue) {
          minValue = arr[i];                // 输入数据的最小值
      } else if (arr[i] > maxValue) {
          maxValue = arr[i];                // 输入数据的最大值
      }
    }

    //桶的初始化
    var DEFAULT_BUCKET_SIZE = 5;            // 设置桶的默认数量为5
    bucketSize = bucketSize || DEFAULT_BUCKET_SIZE;
    var bucketCount = Math.floor((maxValue - minValue) / bucketSize) + 1;   
    var buckets = new Array(bucketCount);
    for (i = 0; i < buckets.length; i++) {
        buckets[i] = [];
    }

    //利用映射函数将数据分配到各个桶中
    for (i = 0; i < arr.length; i++) {
        buckets[Math.floor((arr[i] - minValue) / bucketSize)].push(arr[i]);
    }

    arr.length = 0;
    for (i = 0; i < buckets.length; i++) {
        insertionSort(buckets[i]);                      // 对每个桶进行排序，这里使用了插入排序
        for (var j = 0; j < buckets[i].length; j++) {
            arr.push(buckets[i][j]);                      
        }
    }

    return arr;
}
```

# 10. 基数排序
## 10. 1 算法原理
基数排序是一种非比较型整数排序算法，其原理是将整数按位数切割成不同的数字，然后按每个位数分别比较。由于整数也可以表达字符串（比如名字或日期）和特定格式的浮点数，所以基数排序也不是只能使用于整数。

## 10. 2 算法描述
### 1. 基数排序 vs 计数排序 vs 桶排序
基数排序有三种方法：
这三种排序算法都利用了桶的概念，但对桶的使用方法上有明显差异案例看大家发的：
- 基数排序：根据键值的每位数字来分配桶；
- 计数排序：每个桶只存储单一键值；
- 桶排序：每个桶存储一定范围的数值；
## 10. 3 动图演示
2. LSD 基数排序动图演示
![基数排序.gif](https://s2.ax1x.com/2020/01/05/lBQkPf.gif)
## 10. 4 js代码实现
```
// js代码

// LSD Radix Sort
var counter = [];
function radixSort(arr, maxDigit) {
    var mod = 10;
    var dev = 1;
    for (var i = 0; i < maxDigit; i++, dev *= 10, mod *= 10) {
        for(var j = 0; j < arr.length; j++) {
            var bucket = parseInt((arr[j] % mod) / dev);
            if(counter[bucket]==null) {
                counter[bucket] = [];
            }
            counter[bucket].push(arr[j]);
        }
        var pos = 0;
        for(var j = 0; j < counter.length; j++) {
            var value = null;
            if(counter[j]!=null) {
                while ((value = counter[j].shift()) != null) {
                      arr[pos++] = value;
                }
          }
        }
    }
    return arr;
}
```

# 总结
![排序算法.png](https://s2.ax1x.com/2020/01/05/lBQTSS.png)
以上就是十大经典算法，算法对于前端来说并不是一个十分熟悉的领域，但是排序算法算是算法里比较入门的，还是需要掌握的，毕竟即使是为了面试也是要准备的。

## 参考资料
https://github.com/hustcc/JS-Sorting-Algorithm
一本关于排序算法的 GitBook 在线书籍 《十大经典排序算法》，多语言实现。

http://www.sohu.com/a/136157205_671058
技术面试宝典： 很全面的算法和数据结构知识（含代码实现）下篇

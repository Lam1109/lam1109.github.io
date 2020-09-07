---
date: 2020-09-07 14:23:40
layout: post
title: Sorting Algorithm
subtitle: Sorting Algorithm
description: Sorting Algorithm
image: /assets/img/post_img/SortingAlgorithm.png
optimized_image: /assets/img/post_img/SortingAlgorithm.png
category: java
tags:
  - sort
  - algorithm
author: Lam
---

# 1 排序算法

## 1.1 算法概述

### 1.1.1 算法分类
- 常见排序算法可以分为两大类：

> 1. **比较类排序**：通过比较来决定元素间的相对次序，由于其时间复杂度不能突破O(nlogn)，因此也称为非线性时间比较类排序。
> 2. **非比较类排序**：不通过比较来决定元素间的相对次序，它可以突破基于比较排序的时间下界，以线性时间运行，因此也称为线性时间非比较类排序。 

![image](/assets/img/post_img/AlgorithmTypes.png)

### 1.1.2 算法复杂度

![image](/assets/img/post_img/AlgorithmComplexity.png)

> 稳定：如果a原本在b前面，而a=b，排序之后a仍然在b的前面。  
不稳定：如果a原本在b的前面，而a=b，排序之后 a 可能会出现在 b 的后面。  
时间复杂度：对排序数据的总的操作次数。反映当n变化时，操作次数呈现什么规律。  
空间复杂度：是指算法在计算机内执行时所需存储空间的度量，它也是数据规模n的函数。   

## 1.2 具体算法
### 1.2.1 冒泡排序（Bubble Sort）

> 冒泡排序是一种简单的排序算法。它重复地走访过要排序的数列，一次比较两个元素，如果它们的顺序错误就把它们交换过来。走访数列的工作是重复地进行直到没有再需要交换，也就是说该数列已经排序完成。这个算法的名字由来是因为越小的元素会经由交换慢慢“浮”到数列的顶端。

- **算法描述**

> 1. 比较相邻的元素。如果第一个比第二个大，就交换它们两个；
> 2. 对每一对相邻元素作同样的工作，从开始第一对到结尾的最后一对，这样在最后的元素应该会是最大的数；
> 3. 针对所有的元素重复以上的步骤，除了最后一个；
> 4. 重复步骤1~3，直到排序完成。

- **动图演示**

![image](/assets/img/post_img/BubbleSort.gif)

- **代码实现**

```java
    public void bubbleSort(int[] source) {
        int n = source.length;
        for(int i = 0; i < n - 1; i++) {
            for(int j = 0; j < n - 1 - i; j++) {
                if(source[j] > source[j + 1]) {
                    int hold = source[j];
                    source[j] = source[j + 1];
                    source[j + 1] = hold;
                }
            }
        }
    }
```

### 1.2.2 选择排序（Selection Sort）

> 选择排序是一种简单直观的排序算法。它的工作原理：首先在未排序序列中找到最小（大）元素，存放到排序序列的起始位置，然后，再从剩余未排序元素中继续寻找最小（大）元素，然后放到已排序序列的末尾。以此类推，直到所有元素均排序完毕。 

- **算法描述**

> 1. 初始状态：无序区为R[1..n]，有序区为空；
> 2. 第i趟排序(i=1,2,3…n-1)开始时，当前有序区和无序区分别为R[1..i-1]和R(i..n）。该趟排序从当前无序区中-选出关键字最小的记录 R[k]，将它与无序区的第1个记录R交换，使R[1..i]和R[i+1..n)分别变为记录个数增加1个的新有序区和记录个数减少1个的新无序区；
> 3. n-1趟结束，数组有序化了。

- **动图演示**

![image](/assets/img/post_img/SelectionSort.gif)

- **代码实现**

```java
    public void selectionSort(int[] source) {
        int n = source.length;
        for(int i = 0; i < n; i++) {
            int minIndex = i;
            for(int j = i + 1; j < n; j++) {
                if(source[j] < source[minIndex])
                    minIndex = j;
            }
            int hold = source[i];
            source[i] = source[minIndex];
            source[minIndex] = hold;
        }
    }
```

### 1.2.3 插入排序（Insertion Sort）

> 插入排序（Insertion-Sort）的算法描述是一种简单直观的排序算法。它的工作原理是通过构建有序序列，对于未排序数据，在已排序序列中从后向前扫描，找到相应位置并插入。

- **算法描述**

> 1. 从第一个元素开始，该元素可以认为已经被排序；
> 2. 取出下一个元素，在已经排序的元素序列中从后向前扫描；
> 3. 如果该元素（已排序）大于新元素，将该元素移到下一位置；
> 4. 重复步骤3，直到找到已排序的元素小于或者等于新元素的位置；
> 5. 将新元素插入到该位置后；
> 6. 重复步骤2~5。

- **动图演示**

![image](/assets/img/post_img/InsertionSort.gif)

- **代码实现**

```java
    public void insertionSort(int[] source) {
        int n = source.length;
        for(int i = 1; i < n; i++) {
            int index = i;
            int hold = source[i];
            while(index - 1 >= 0 && hold < source[index - 1]) {
                source[index] = source[index - 1];
                index--;
            }
            source[index] = hold;
        }
    }
```

### 1.2.4 希尔排序（Shell Sort）

> 1959年Shell发明，第一个突破O(n2)的排序算法，是简单插入排序的改进版。它与插入排序的不同之处在于，它会优先比较距离较远的元素。希尔排序又叫缩小增量排序。

- **算法描述**

> 1. 选择一个增量序列t1，t2，…，tk，其中ti>tj，tk=1；
> 2. 按增量序列个数k，对序列进行k 趟排序；
> 3. 每趟排序，根据对应的增量ti，将待排序列分割成若干长度为m 的子序列，分别对各子表进行直接插入排序。仅增量因子为1 时，整个序列作为一个表来处理，表长度即为整个序列的长度。

- **动图演示**

![image](/assets/img/post_img/ShellSort.gif)

- **代码实现**

```java
    public void shellSort(int[] source) {
        int n = source.length;
        for(int gap = n / 2; gap > 0; gap = gap / 2) {
            for(int i = gap; i < n; i++) {
                int index = i;
                int hold = source[i];
                while(index - gap >= 0 && hold < source[index - gap]) {
                    source[index] = source[index - gap];
                    index -= gap;
                }
                source[index] = hold;
            }
        }
    }
```

### 1.2.5 归并排序（Merge Sort）

> 归并排序是建立在归并操作上的一种有效的排序算法。该算法是采用分治法（Divide and Conquer）的一个非常典型的应用。将已有序的子序列合并，得到完全有序的序列；即先使每个子序列有序，再使子序列段间有序。若将两个有序表合并成一个有序表，称为2-路归并。 

- **算法描述**

> 1. 把长度为n的输入序列分成两个长度为n/2的子序列；
> 2. 对这两个子序列分别采用归并排序；
> 3. 将两个排序好的子序列合并成一个最终的排序序列。

- **动图演示**

![image](/assets/img/post_img/MergeSort.gif)

- **代码实现**

```java
    public void mergeSort(int[] source, int start, int end) {
        if(start < end) {
            int middle = start + (end - start) / 2;
            mergeSort(source, start, middle);
            mergeSort(source, middle + 1, end);
            merge(source, start, middle, end);
        }
    }

    public void merge(int[] source, int start, int middle, int end) {
        int n1 = middle - start + 1;
        int n2 = end - middle;
        int[] left = new int[n1 + 1];
        int[] right = new int[n2 + 1];

        for(int i = 0; i < n1; i++) {
            left[i] = source[start + i];
        }
        for(int i = 0; i < n2; i++) {
            right[i] = source[middle + i + 1];
        }

        left[n1] = Integer.MAX_VALUE;
        right[n2] = Integer.MAX_VALUE;

        int i = 0, j =0;
        for(int k = start; k <= end; k++) {
            if(left[i] <= right[j]) {
                source[k] = left[i];
                i++;
            }
            else {
                source[k] = right[j];
                j++;
            }
        }
    }
```

### 1.2.6 快速排序（Quick Sort）

> 快速排序的基本思想：通过一趟排序将待排记录分隔成独立的两部分，其中一部分记录的关键字均比另一部分的关键字小，则可分别对这两部分记录继续进行排序，以达到整个序列有序。

- **算法描述**

> 1. 从数列中挑出一个元素，称为 “基准”（pivot）；
> 2. 重新排序数列，所有元素比基准值小的摆放在基准前面，所有元素比基准值大的摆在基准的后面（相同的数可以到任一边）。在这个分区退出之后，该基准就处于数列的中间位置。这个称为分区（partition）操作；
> 3. 递归地（recursive）把小于基准值元素的子数列和大于基准值元素的子数列排序。

- **动图演示**

![image](/assets/img/post_img/QuickSort.gif)

- **代码实现**

```java
    public void quickSort(int[] source, int low, int high) {
        int pivot = source[low];
        int i = low;
        int j = high;
        while(i < j) {
            while(source[j] >= pivot && i < j) j--;
            while(source[i] <= pivot && i < j) i++;

            if(i < j) {
                int hold = source[i];
                source[i] = source[j];
                source[j] = hold;
            }
        }

        source[low] = source[i];
        source[i] = pivot;

        if(low < j)
            quickSort(source, 0, j - 1);
        if(high > i)
            quickSort(source, j + 1, source.length - 1);
    }
```

### 1.2.7 堆排序（Heap Sort）

> 堆排序是指利用堆这种数据结构所设计的一种排序算法。堆积是一个近似完全二叉树的结构，并同时满足堆积的性质：即子结点的键值或索引总是小于（或者大于）它的父节点。

- **算法描述**

> 1. 将初始待排序关键字序列(R1,R2….Rn)构建成大顶堆，此堆为初始的无序区；
> 2. 将堆顶元素R[1]与最后一个元素R[n]交换，此时得到新的无序区(R1,R2,……Rn-1)和新的有序区(Rn),且满足R[1,2…n-1]<=R[n]；
> 3. 由于交换后新的堆顶R[1]可能违反堆的性质，因此需要对当前无序区(R1,R2,……Rn-1)调整为新堆，然后再次将R[1]与无序区最后一个元素交换，得到新的无序区(R1,R2….Rn-2)和新的有序区(Rn-1,Rn)。不断重复此过程直到有序区的元素个数为n-1，则整个排序过程完成。

- **动图演示**

![image](/assets/img/post_img/HeapSort.gif)

- **代码实现**

```java
    public void heapSort(int[] source) {
        int n = source.length;
        // 1. 构建大顶堆 （降序排序构建小顶堆）
        for(int i = n / 2 - 1; i >= 0; i--){
            //从第一个非叶子结点从下至上，从右至左调整结构
            adjustHeap(source, i, n);
        }

        //2.调整堆结构+交换堆顶元素与末尾元素
        for(int i = n - 1; i > 0; i--){
            int hold = source[0];
            source[0] = source[i];
            source[i] = hold;
            adjustHeap(source,0, i); //重新对堆进行调整
        }
    }
    
    public void adjustHeap(int []arr, int i, int length){
        int hold = arr[i]; //先取出当前元素i
        for(int k = i * 2 + 1; k < length; k = k * 2 + 1){ //从i结点的左子结点开始，也就是2i+1处开始
            System.out.println(k);
            if(k + 1 < length && arr[k] < arr[k + 1]){ //如果左子结点小于右子结点，k指向右子结点
                k++;
            }
            if(arr[k] > hold){ //如果子节点大于父节点，将子节点值赋给父节点
                arr[i] = arr[k];
                arr[k] = hold; //将temp值放到最终的位置
                i = k;
            }else{
                break;
            }
        }
    }
```

### 1.2.8 计数排序（Counting Sort）

> 计数排序不是基于比较的排序算法，其核心在于将输入的数据值转化为键存储在额外开辟的数组空间中。 作为一种线性时间复杂度的排序，计数排序要求输入的数据必须是有确定范围的整数。

- **算法描述**

> 1. 找出待排序的数组中最大和最小的元素；
> 2. 统计数组中每个值为i的元素出现的次数，存入数组C的第i项；
> 3. 对所有的计数累加（从C中的第一个元素开始，每一项和前一项相加）；
> 4. 反向填充目标数组：将每个元素i放在新数组的第C(i)项，每放一个元素就将C(i)减去1。

- **动图演示**

![image](/assets/img/post_img/CountingSort.gif)

- **代码实现**

```java
    public void countingSort(int[] source) {
        int n = source.length;
        int minValue = source[0];
        int maxValue = source[0];

        for(int i = 1; i < n; i++) {
            if(source[i] < minValue) minValue = source[i];
            if(source[i] > maxValue) maxValue = source[i];
        }
        int length = maxValue - minValue + 1;
        int[] count = new int[length];
        for(int i = 0; i < n; i++) {
            count[source[i]-minValue]++;
        }
        int index = 0;
        for(int i = 0; i < length; i++){
            for(int j = 0; j < count[i]; j++) {
                source[index++] = i + minValue;
            }
        }
    }
```

### 1.2.9 桶排序（Bucket Sort）

> 桶排序是计数排序的升级版。它利用了函数的映射关系，高效与否的关键就在于这个映射函数的确定。桶排序 (Bucket sort)的工作的原理：假设输入数据服从均匀分布，将数据分到有限数量的桶里，每个桶再分别排序（有可能再使用别的排序算法或是以递归方式继续使用桶排序进行排）。

- **算法描述**

> 1. 设置一个定量的数组当作空桶；
> 2. 遍历输入数据，并且把数据一个一个放到对应的桶里去；
> 3. 对每个不是空的桶进行排序；
> 4. 从不是空的桶里把排好序的数据拼接起来。 

- **图片演示**

![image](/assets/img/post_img/BucketSort.png)

- **代码实现**

```java
    public void bucketSort() {
        /**
         * 最简单的桶排序演示
         * 实际一个桶之中可能不止一个数，各个桶之中再利用其他排序算法排序
         */
        int[] arr = {5, 7, 3, 5, 4, 8, 6, 4, 1, 2};
        int[] buckets = new int[10];
        for(int i = 0; i < arr.length; i++) {
            buckets[arr[i]]++;
        }
        int index = 0;
        for(int i = 0; i < 10; i++) {
            for(int j = 0; j < buckets[i]; j++) {
                arr[index++] = i;
            }
        }
        System.out.println(Arrays.toString(arr));
    }
```

### 1.2.10 基数排序（Radix Sort）

> 基数排序是按照低位先排序，然后收集；再按照高位排序，然后再收集；依次类推，直到最高位。有时候有些属性是有优先级顺序的，先按低优先级排序，再按高优先级排序。最后的次序就是高优先级高的在前，高优先级相同的低优先级高的在前。

- **算法描述**

> 1. 取得数组中的最大数，并取得位数；
> 2. arr为原始数组，从最低位开始取每个位组成radix数组；
> 3. 对radix进行计数排序（利用计数排序适用于小范围数的特点）；

- **动图演示**

![image](/assets/img/post_img/RadixSort.gif)

- **代码实现**

```java
    public void radixSort(int[] source) {
        int digit = getMaxDigit(source); // 获取最大的数是多少位
        for (int i = 0; i < digit; i++) {
            bucketSort(source, i); // 执行 digit 次 bucketSort 排序即可
        }
    }

    public int getMaxDigit(int[] source) {
        int digit = 1; // 默认只有一位
        int base = 10; // 十进制每多一位，代表其值大了10倍
        for (int i : source) {
            while (i > base) {
                digit++;
                base *= 10;
            }
        }
        return digit;
    }

    public  void bucketSort(int[] source, int digit) {
        int base = (int) Math.pow(10, digit);
        // init buckets
        ArrayList<ArrayList<Integer>> buckets = new ArrayList<ArrayList<Integer>>();
        for (int i = 0; i < 10; i++) { // 只有0~9这十个数，所以准备十个桶
            buckets.add(new ArrayList<Integer>());
        }
        // sort
        for (int i : source) {
            int index = i / base % 10;
            buckets.get(index).add(i);
        }
        // output: copy back to arr
        int index = 0;
        for (ArrayList<Integer> bucket : buckets) {
            for (int i : bucket) {
                source[index++] = i;
            }
        }
    }
```

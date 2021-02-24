# 排序算法分析及实现

## 冒泡排序 （Bubble Sort）

**思想**

* 遍历数据，每次比较两个相邻数据
* 两个相邻数据不满足大小关系时交换两个数据的位置
* 一次遍历只能保证完成一个数据项的排序工作，所以重复n-1次后即可保证所有数据项排序完成    
    >在确定第n-1个数时，比较的是第n-1个数和第n个数，所以不需要再进行第

**实现**    
```js
const bubbleSort = (arr) => {
    const n = arr.length;
    for (let i = 0; i < n - 1; i++) {
        for (let j = 0; j < n - i - 1; j++) {
            if (arr[j] > arr[j + 1]) {
                const temp = arr[j];
				arr[j] = arr[j + 1];
				arr[j + 1] = temp;
            }
        }
    }
}
```
**优化**
* 数据/剩余数据已经是排序好的   
```js
const bubbleSort = (arr) => {
    const n = arr.length;
    for (let i = 0; i < n - 1; i++) {
        // 标记数据是否已排序完成
        let flag = true;
        for (let j = 0; j < n - i - 1; j++) {
            if (arr[j] > arr[j + 1]) {
                const temp = arr[j];
				arr[j] = arr[j + 1];
				arr[j + 1] = temp;
                flag = false;
            }
        }
        if (flag) break;
    }
}
```

**时间复杂度**  
最佳情况：数据已经是排序好的，只会遍历第一次，T(n)=O(n)。  
最差情况：n - 1 + n - 2 + ... + n - (n - 1)，T(n)=O(n^2)。

**空间复杂度**  
只需要极少临时变量空间，所以空间复杂度为O(1)，该算法为原地排序算法。   

**是否稳定排序算法**   
比较相邻数据时，数据判断相等时不交换两个数据的位置，则该算法为稳定排序算法，否则该算法为不稳定排序算法。

**应用场景**    
* 数据量小
* 数据几乎接近排序好的
* 要求空间复杂度为O(1)
* 要求排序必须是稳定的
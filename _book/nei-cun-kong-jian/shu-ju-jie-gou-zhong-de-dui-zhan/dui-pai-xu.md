# 堆排序

JavaScript 实现堆排序：

```javascript
const heapSort = target => {
    const arr = target.slice(0);
    // 交换的方法
    const swap = (i, j) => {
        let temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }
    // 确保父节点永远大于子节点
    const max_heap = (start, end) => {
        let dad = start;
        // 左边子节点
        let son = dad * 2 + 1;
        if(son >= end){
            return;
        }
        // 两个子节点中选择大的
        if(son+1<end && arr[son]<arr[son+1]){
            son++;
        }
        // 如果大于父节点，交换
        if(arr[dad] <= arr[son]){
            swap(dad, son);
            max_heap(son, end);
        }
    }
    let len = arr.length;
    // 最后一个父节点 Math.floor(len/2)-1
    for(let i = Math.floor(len/2)-1; i >= 0; i--){
         max_heap(i, len);
    }
    for(let i = len-1; i > 0; i--){
        swap(0, i);
        max_heap(0, i);
    }
    return arr;
}

heapSort([3,5,3,0,8,0,1,5,1,6,5,4,0,8]);
//[0, 0, 0, 1, 1, 3, 3, 4, 5, 5, 5, 6, 8, 8]
```


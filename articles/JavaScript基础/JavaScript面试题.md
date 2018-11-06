# JavaScript Interview

## 1

    ['1','2','3'].map(parseInt) // [1, NaN, NaN]
    
    ['1','2','3'].map(value => parseInt(value)) // [1, 2, 3]
    
map默认传递三个参数：value，index，array。parseInt接收两个参数：string，radix。

    parseInt('1', 0) // 1
    parseInt('2', 1) // NaN
    parseInt('3', 2) // NaN
    
    

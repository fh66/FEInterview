### 如何判断两个数组的内容是否相等

给定两个数组，判断两数组**内容**是否相等。

- 不使用排序
- 不考虑元素位置

```javascript
[1, 2, 3] 和 [1, 3, 2] // true
[1, 2, 3] 和 [1, 2, 4] // false
```

```javascript
function fn(arr1, arr2) {
    if (arr1.length !== arr2.length) {
        return false;
    }
    const m = new Map();
    for (let i = 0; i < arr1.length; i++) {
        m.set(arr1[i], m.has(arr1[i]) ? m.get(arr1[i]) + 1 : 1);
        if (m.has(arr2[i])) {
            m.set(arr2[i], m.get(arr2[i]) - 1);
        } else {
            return false;
        }
    }
    for (let value of m.values()) {
        if (value !== 0) {
            return false;
        }
    }
    return true;
}
```

解决方案的时间复杂度是O(n)，其中n是数组的长度。空间复杂度是O(n)，因为使用了一个Map来存储元素和其出现次数的映射关系。
https://github.com/Snailclimb/JavaGuide/blob/main/docs/java/collection/arraylist-source-code.md



## ArrayList扩容机制原理



在创建ArrayList对象的时候，会分配一个初始容量，默认为10。但是如果使用的是无参构造的话，它不会初始化容器，只会初始化一个空数组，只有在向里面添加数据的时候，才会真正的初始化容器。数组容量扩为10。



在向ArrayList添加元素的时候，会检查当前容器是否足够，如果不足，会进行扩容操作，一般默认情况下，扩容大小为1.5倍。

扩容起售就是创建一个新的数组，然后将原有的数组元素复制到新数组中。然后更新ArrayList内部的引用，使其指向新数组。



扩容的核心方法是grow()方法。

​	 	

```
/**
 * 要分配的最大数组大小
 */
private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;

/**
 * ArrayList扩容的核心方法。
 */
private void grow(int minCapacity) {
    // oldCapacity为旧容量，newCapacity为新容量
    int oldCapacity = elementData.length;
    // 将oldCapacity 右移一位，其效果相当于oldCapacity /2，
    // 我们知道位运算的速度远远快于整除运算，整句运算式的结果就是将新容量更新为旧容量的1.5倍，
    int newCapacity = oldCapacity + (oldCapacity >> 1);

    // 然后检查新容量是否大于最小需要容量，若还是小于最小需要容量，那么就把最小需要容量当作数组的新容量，
    if (newCapacity - minCapacity < 0)
        newCapacity = minCapacity;

    // 如果新容量大于 MAX_ARRAY_SIZE,进入(执行) `hugeCapacity()` 方法来比较 minCapacity 和 MAX_ARRAY_SIZE，
    // 如果minCapacity大于最大容量，则新容量则为`Integer.MAX_VALUE`，否则，新容量大小则为 MAX_ARRAY_SIZE 即为 `Integer.MAX_VALUE - 8`。
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        newCapacity = hugeCapacity(minCapacity);

    // minCapacity is usually close to size, so this is a win:
    elementData = Arrays.copyOf(elementData, newCapacity);
}
```




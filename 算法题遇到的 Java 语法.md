# Java 中字符串与字符数组间的转换语法

字符串转为字符数组：使用 `toCharArray()` 函数。  
字符数组转为字符串：使用 `String` 类构造方法 `public String(char[] value)`。  
例:  

```java
public class Test {
    public static void main(String[] args) {
        String str = "Hello, World";
        // 字符串转为字符数组
        char[] data = str.toCharArray();
        for (int i = 0; i < data.length; i++) {
            System.out.println(i + " = \"" + data[i] + '"');
        }
        // 字符数组转为字符串
        System.out.println(new String(data));
    }
}
```

# 从 int 到 String 的转化

使用 `java.lang.Integer` 类中的 `public static String toString(int i)` 方法。  
使用 `java.lang.String` 类中的 `public static String valueOf(int i)` 方法。  

# `java.util.Arrays` 类常用方法

## 输出数组元素

`Arrays` 类的 `public static String toString(int[] a)` 方法  

```java
import java.util.Arrays;

public class Test {
    public static void main(String[] args) {
        int[] ints = new int[3];
        for (int i = 0; i < ints.length; i++) {
            ints[i] = i + 1;
        }
        System.out.println(Arrays.toString(ints));
    }
}
```

# 数组排序算法-选择排序

```java
/*
选择排序相关代码: https://www.runoob.com/w3cnote/selection-sort.html
 */

import java.util.Arrays;

public class Test2 {
    public static void main(String[] args) {
        int[] arr = new int[10];
        for (int i = 0; i < arr.length; i++) {
            arr[i] = (int) (Math.random() * 1000);
        }
        System.out.println(Arrays.toString(arr));

        // 选择排序开始
        for (int i = 0; i < arr.length - 1; i++) {
            int min = i;

            // 每轮需要比较的次数 N-i
            for (int j = i + 1; j < arr.length; j++) {
                if (arr[j] < arr[min]) {
                    // 记录目前能找到的最小值元素的下标
                    min = j;
                }
            }

            // 将找到的最小值和i位置所在的值进行交换
            if (i != min) {
                int tmp = arr[i];
                arr[i] = arr[min];
                arr[min] = tmp;
            }

        }
        // 选择排序结束

        boolean ifSuccess = true;
        for (int i = 0; i < arr.length - 1; i++) {
            if (arr[i] > arr[i + 1]) {
                ifSuccess = false;
                System.out.println("失败");
            }
        }
        if (ifSuccess) {
            System.out.println("成功");
        }
        System.out.println(Arrays.toString(arr));
    }
}
```

# `System` 类的方法

## `System` 类的 `arraycopy` 方法

```java
public static native void arraycopy(Object src, int srcPos, Object dest, int destPos, int length);
```

| 参数        | 类型        | 意义                   |
|:----------|:----------|:---------------------|
| `src`     | `Object`  | 要被复制的数组              |
| `srcPos`  | `int`     | 被复制的数字开始复制的下标        |
| `dest`    | ` Object` | 目标数组，也就是要把数据放进来的数组   |
| `destPos` | `int`     | 从目标数据第几个下标开始放入数据     |
| `length`  | `int`     | 从被复制的数组中拿几个数值放到目标数组中 |

```java
import java.util.Arrays;

public class Test {
    public static void main(String[] args) {
        int[] arr = {1, 2, 3, 4, 5};
        int[] arr2 = {5, 6, 7, 8, 9};
        int[] arr3 = {5, 6, 7, 8, 9};
        int[] arr4 = {5, 6, 7, 8, 9};

        System.out.println(Arrays.toString(arr));
        System.out.println(Arrays.toString(arr2));

        System.arraycopy(arr, 1, arr2, 0, 3);
        System.out.println("2: " + Arrays.toString(arr2)); // [2, 3, 4, 8, 9]

        System.arraycopy(arr, 1, arr3, 0, 0); // 复制长度可以为 0
        System.out.println("3: " + Arrays.toString(arr3)); // 数组无变化

        System.arraycopy(arr, 5, arr4, 5, 0); // 数组下标可以越界
        System.out.println("4: " + Arrays.toString(arr4)); // 数组无变化
    }
}
```

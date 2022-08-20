---
title: 百分位数计算（percentile）
date: 2019-11-12 12:36:07
tags:
- percentile
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

1.计算公式 （参考：https://blog.csdn.net/u011489043/article/details/78056851）

（1）首先将输入的数组进行从小到大排序，然后计算: 

```
(n−1)∗p=i+j    （其中n为数组元素的个数，将计算结果的整数部分用i表示，小数部分用j来表示，p是百分位数，如90%的话就是0.9） 
```

（2）计算百分位数

```
res=(1−j)∗array[i]+j∗array[i+1]     （res就是我们所需要的百分位数）
```

2.python代码实现 （两种方式）

```python
def test_percentile():
    """
    方式一：直接写算法
    :return: 
    """
    array = sorted([2, 10, 5, 8, 2, 5, 9, 10])
    n = len(array)
    location_10 = (n - 1) * 0.1
    i = int(location_10)
    j = (round(location_10, 1) - i)
    percentage10 = (1 - j) * array[i] + j * array[i + 1]
 
    location_50 = (n - 1) * 0.5
    i = int(location_50)
    j = (round(location_50, 1) - i)
    percentage50 = (1 - j) * array[i] + j * array[i + 1]
 
    location_90 = (n - 1) * 0.9
    i = int(location_90)
    j = (round(location_90, 1) - i)
    percentage90 = (1 - j) * array[i] + j * array[i + 1]
    print(percentage10, percentage50, percentage90)  # 输出：(2.0, 6.5, 10.0)
    
    
def test_numpy_percentile():
    """
    方式二：使用numpy库
    :return: None
    """
    import numpy as np
    array = sorted([2, 10, 5, 8, 2, 5, 9, 10])
    percentage10 = np.percentile(array, 10)
    percentage50 = np.percentile(array, 50)
    percentage90 = np.percentile(array, 90)
    print(percentage10, percentage50, percentage90)  # 输出：(2.0, 6.5, 10.0)
 
 
if __name__ == '__main__':
    test_percentile()
    test_numpy_percentile()
```

3.使用java实现

```java
import java.util.Arrays;
 
/**
 * <p>
 * <code>Test</code>
 * </p>
 * Description:
 *
 * @author Jalen
 * @date 2019/11/12 12:23
 */
public class Test {
 
    public static void main(String[] args) {
        int[] array = {2, 10, 5, 8, 2, 5, 9, 10};
        double percentage10 = getPercentile(array, 0.1);
        double percentage50 = getPercentile(array, 0.5);
        double percentage90 = getPercentile(array, 0.9);
        System.out.println(percentage10);  //输出：2.0
        System.out.println(percentage50);  //输出：6.5
        System.out.println(percentage90);  //输出：10.0
    }
 
    private static double getPercentile(int[] array, double percentile) {
        Arrays.sort(array);
        double x = (array.length - 1) * percentile;
        int i = (int) x;
        double j = x - i;
        return (1 - j) * array[i] + j * array[i + 1];
    }
}
```

4.Oracle数据库函数 

```sql
SELECT testcolumn,
   PERCENTILE_CONT(0.1) WITHIN GROUP (ORDER BY TESTNUMBER asc) "percentile10",
   PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY TESTNUMBER asc) "percentile50",
   PERCENTILE_CONT(0.9) WITHIN GROUP (ORDER BY TESTNUMBER asc) "percentile90"
   FROM STAP_PROV_TEST where TESTNUMBER is not null GROUP BY testcolumn;
```

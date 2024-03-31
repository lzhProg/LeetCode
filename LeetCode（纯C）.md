# LeetCode（备战科软） #

## 一、数组 ##

### 35. 搜索插入位置 (easy) ###

给定一个**升序数组**和一个目标值，在数组中找到目标值，并返回其索引。如果目标值不存在于数组中，返回它将会被**按顺序插入的位置**。
请必须使用时间复杂度为  `O(log n)` 的算法。

**示例 1:**

```
输入: nums = [1,3,5,6], target = 5
输出: 2
```

**示例 2:**

```
输入: nums = [1,3,5,6], target = 2
输出: 1
```

**示例 3:**

```
输入: nums = [1,3,5,6], target = 7
输出: 4
```

**代码示例：**

```c
//二分查找时间复杂度为O(logn)符合要求
int searchInsert(int* nums, int numsSize, int target) {
    int left = 0, right = numsSize - 1;
    int mid = -1;
    while (left <= right) {
        mid = (left + right) / 2;
        if (nums[mid] == target)
            return mid;
        if (nums[mid] < target)
            left = mid + 1;
        else
            right = mid - 1;
    }
    //退出循环后，left就是待插入数据的下标
    return left;
}
```

### 34. 在排序数组中查找元素的第一个和最后一个位置（middle） ###

给你一个按照**非递减顺序**排列的整数数组 `nums`，和一个目标值 `target`。请你找出给定目标值在数组中的开始位置和结束位置。

如果数组中不存在目标值 `target`，返回 `[-1, -1]`。

你必须设计并实现时间复杂度为 `O(log n)` 的算法解决此问题。

**示例 1：**

```
输入：nums = [5,7,7,8,8,10], target = 8
输出：[3,4]
```

**示例 2：**

```
输入：nums = [5,7,7,8,8,10], target = 6
输出：[-1,-1]
```

**示例 3：**

```
输入：nums = [], target = 0
输出：[-1,-1]
```

**代码示例1：**

```c
//二分查找到target后顺序遍历该元素前后的数据
int* searchRange(int* nums, int numsSize, int target, int* returnSize) {
    int left = 0, right = numsSize - 1;
    int mid, start, end;
    //返回数组必须分配空间，且必须用returnSize记录大小
    int* ret = (int*)malloc(sizeof(int) * 2);
    while (left <= right) {
        mid = (left + right) / 2;
        if (nums[mid] == target) {
            start = mid;
            end = mid;
            while (nums[start] == target ){
                start--;
                if(start<0) break;
            }
            while (nums[end] == target ){
                end++;
                if(end>numsSize-1) break;
            }
            start++;
            end--;
            *returnSize = 2;
            ret[0] = start;
            ret[1] = end;
            return ret;
        }
        if (nums[mid] > target)
            right = mid - 1;
        else
            left = mid + 1;
    }
    *returnSize = 2;
    ret[0] = -1;
    ret[1] = -1;
    return ret;
}
```

**代码示例2：**

```c
//直接二分查找target数据的左边界和右边界
//找两个边界：关键在于找到target时要继续让左/右指针移动
int* searchRange(int* nums, int numsSize, int target, int* returnSize) {
    int left = 0, right = numsSize - 1;
    int mid, start, end;
    int* ret = (int*)malloc(sizeof(int) * 2);
    *returnSize = 2;
    ret[0] = -1;
    ret[1] = -1;
    bool if_find = 0;
    //找左边界
    while (left <= right) {
        mid = (left + right) / 2;
        // left会在循环结束时指向最左边的target
        if (nums[mid] == target)
            if_find = 1;
        if (nums[mid] >= target)
            right = mid - 1;
        else
            left = mid + 1;
    }
    start = left;
    //找右边界
    left = 0, right = numsSize - 1;
    while (left <= right && if_find) {
        mid = (right + left) / 2;
        if (nums[mid] <= target)
            left = mid + 1;
        else
            right = mid - 1;
    }
    end = right;
    if (if_find) {
        ret[0] = start;
        ret[1] = end;
    }
    return ret;
}
```

### LCR 187. 破冰游戏（easy） ###

社团共有 `num` 位成员参与破冰游戏，编号为 `0 ~ num-1`。成员们按照编号顺序围绕圆桌而坐。社长抽取一个数字 `target`，从 0 号成员起开始计数，排在第 `target` 位的成员离开圆桌，且成员离开后从下一个成员开始计数。请返回游戏结束时最后一位成员的编号。

**示例 1：**

```
输入：num = 7, target = 4
输出：1
```

**示例 2：**

```
输入：num = 12, target = 5
输出：0
```

 **代码示例：**

```c
int iceBreakingGame(int num, int target) {
    int save=0;//最后留在桌子上的人的下标一定是零
    for(int i=2;i<=num;i++){
        save=(save+target)%i;
    }
    return save;
}
```

**解析（num=7，target=4，最后存活的是1）：**![image-20240228111650084](D:/typoraphotos/image-20240228111650084.png)

约瑟夫环问题，从下往上看，最后的时候存活下来的人的坐标一定是0，因为只有他一个人了。现在推算剩两个人的时候他的位置是0还是1：设他在**倒数第二轮**的时候下标为x，则有**(x+target)%2=0，则x=(0+target)%2**。target=4时，解得x=0，所以倒数第二轮时他的坐标是0。**同理，倒数第三轮时他的位置为：**(0+target)%3=1

### 658.找到 K 个最接近的元素（middle） ###

给定一个 **排序好** 的数组 `arr` ，两个整数 `k` 和 `x` ，从数组中找到最靠近 `x`（两数之差最小）的 `k` 个数。返回的结果必须要是按升序排好的。

整数 `a` 比整数 `b` 更接近 `x` 需要满足：

- `|a - x| < |b - x|` 或者
- `|a - x| == |b - x|` 且 `a < b`

**示例 1：**

```
输入：arr = [1,2,3,4,5], k = 4, x = 3
输出：[1,2,3,4]
```

**示例 2：**

```
输入：arr = [1,2,3,4,5], k = 4, x = -1
输出：[1,2,3,4]
```

**代码示例：**

```c
//二分查找到目标位置
int* findClosestElements(int* arr, int arrSize, int k, int x, int* returnSize) {
    int* result = (int*)malloc(sizeof(int) * k);
    *returnSize = k;
    int left = 0, right = arrSize - 1, mid;
    while (left <= right) {
        mid = (right + left) / 2;
        if (arr[mid] == x)
            break;
        if (arr[mid] > x)
            right = mid - 1;
        else
            left = mid + 1;
    }
    //这里的left和right用作双指针使用，和上面二分查找不一样
    //这里双指针的位置是解题的关键，尤其是不存在x时的指针位置判断
    if (arr[mid] >= x) {
        left = mid - 1;
        right = mid;
    } else {
        left = mid;
        right = mid + 1;
    }
    int num = k;
    while (num--) {
        if (left < 0)
            right++;
        else if (right >= arrSize)
            left--;
        else if ((x - arr[left]) <= (arr[right] - x))
            left--;
        else
            right++;
    }
    for (int i = left + 1, j = 0; j < k; i++, j++) {
        result[j] = arr[i];
    }
    return result;
}
```

### 162.寻找峰值（middle） ###

峰值元素是指其值严格大于左右相邻值的元素。

给你一个整数数组 `nums`，找到峰值元素并返回其索引。数组可能包含多个峰值，在这种情况下，返回 **任何一个峰值** 所在位置即可。

你可以假设 `nums[-1] = nums[n] = -∞` 。

对于所有有效的 `i` 都有 `nums[i] != nums[i + 1]`

你必须实现时间复杂度为 `O(log n)` 的算法来解决此问题。

**示例 1：**

```
输入：nums = [1,2,3,1]
输出：2
解释：3 是峰值元素，你的函数应该返回其索引 2。
```

**示例 2：**

```
输入：nums = [1,2,1,3,5,6,4]
输出：1 或 5 
解释：你的函数可以返回索引 1，其峰值元素为 2；
     或者返回索引 5， 其峰值元素为 6。
```

**代码示例：**

```c
//尽管不是有序数组，但由于题目的特殊性仍旧可以使用二分查找
int findPeakElement(int* nums, int numsSize) {
    int left = 0;
    int right = numsSize - 1;
    while(left < right)
    {
        int mid = left+(right-left)/2;
        if(nums[mid]>nums[mid+1]){
            right = mid;
        }
        else {
        	left = mid+1;
        }
    }
    return left;
}
```

**解析：**

- 如果**nums[mid]>nums[mid+1]**，若0\~mid上递减，因为-1处是负无穷，则0处为峰值，若0~mid上非递减，则该区间内一定有峰值（包含边界），所以无论那种情况，mid左边一定有峰值
- 同理，如果**nums[mid]<nums[mid+1]**，mid有边一定有峰值
- 不断的缩小有峰值的那个区间，直到区间内只剩一个数的时候就是峰值

### 1760.袋子里最少数目的球(middle) ###

给你一个整数数组 `nums` ，其中 `nums[i]` 表示第 `i` 个袋子里球的数目。同时给你一个整数 `maxOperations` 。

你可以进行如下操作至多 `maxOperations` 次：

选择任意一个袋子，并将袋子里的球分到 2 个新的袋子中，每个袋子里都有

正整数个球。比方说，一个袋子里有 `5` 个球，你可以把它们分到两个新袋子里，分别有 `1` 个和 `4` 个球，或者分别有 `2` 个和 `3` 个球。

你的开销是单个袋子里球数目的 **最大值** ，你想要 **最小化** 开销。

请你返回进行上述操作后的最小开销。

**示例 1：**

```
输入：nums = [9], maxOperations = 2
输出：3
解释：
- 将装有 9 个球的袋子分成装有 6 个和 3 个球的袋子。[9] -> [6,3] 。
- 将装有 6 个球的袋子分成装有 3 个和 3 个球的袋子。[6,3] -> [3,3,3] 。
装有最多球的袋子里装有 3 个球，所以开销为 3 并返回 3 。
```

**示例 2：**

```
输入：nums = [2,4,8,2], maxOperations = 4
输出：2
解释：
- 将装有 8 个球的袋子分成装有 4 个和 4 个球的袋子。[2,4,8,2] -> [2,4,4,4,2] 。
- 将装有 4 个球的袋子分成装有 2 个和 2 个球的袋子。[2,4,4,4,2] -> [2,2,2,4,4,2] 。
- 将装有 4 个球的袋子分成装有 2 个和 2 个球的袋子。[2,2,2,4,4,2] -> [2,2,2,2,2,4,2] 。
- 将装有 4 个球的袋子分成装有 2 个和 2 个球的袋子。[2,2,2,2,2,4,2] -> [2,2,2,2,2,2,2,2] 。
装有最多球的袋子里装有 2 个球，所以开销为 2 并返回 2 。
```

**示例 3：**

```
输入：nums = [7,17], maxOperations = 2
输出：7
```

**示例代码：**

```c
int minimumSize(int* nums, int numsSize, int maxOperations) {
    int maxNum = -1;
    int res; // 记录返回结果,注意是开销，不是count！！
    // 找到nums中最大的数,用maxNum记录
    for (int i = 0; i < numsSize; i++) {
        // fmax找到并返回两个数中的最大值，include<math.h>
        maxNum = fmax(maxNum, nums[i]);
    }
    // 拆分完之后所有袋子中的最大值MAX在1~maxNum之间
    // 拆分0次：MAX=maxNum；拆分无限次：MAX=1
    // MAX就是开销，从1到maxNum列举MAX的值
    // 计算使得所有袋子中球的个数<=MAX需要的总次数count
    // 当count刚好等于maxOperations时符合题意
    // 为了提高效率，这里对于MAX的值不是简单列举，而使用二分查找
    int left = 1, right = maxNum, mid;
    while (left <= right) {
        int count = 0;            // 记录需要的次数
        mid = (left + right) / 2; // 该轮的开销值
        for (int i = 0; i < numsSize; i++) {
            // 需要简单推导count的计算公式
            count += (nums[i] - 1) / mid;
        }
        if (count <= maxOperations) {
            res = mid;
            right = mid - 1;
        } else
            left = mid + 1;
    }
    return res;
}
```

**解析（count计算公式推导）：**

对于**一个单独的袋子**来说，里面有**x**个球，现在将其分为多个袋子，要求每个袋子里面有**y**个球，最后剩下几个球(<y)单独放在一个袋子里，求一共要分几次。

**count=$\lceil x/y \rceil$-1**，例如x=10，y=5，则需要分1次就好；x=10，y=4，则需要分2次

向上取整不方便编码计算，转换为向下取整：**count=$\lfloor (x-1)/y \rfloor$**，

推导过程如下，**原理：**$\lceil x/y \rceil$=$\lfloor (x+y-1)/y \rfloor$

count=$\lceil x/y \rceil$-1=$\lceil x/y-1 \rceil$=$\lfloor (x+y-1)/y-1 \rfloor$=$\lfloor (x-1)/y \rfloor$

### 1552.两球之间的磁力（middle） ###

在代号为 C-137 的地球上，Rick 发现如果他将两个球放在他新发明的篮子里，它们之间会形成特殊形式的磁力。Rick 有 `n` 个空的篮子，第 `i` 个篮子的位置在 `position[i]` ，Morty 想把 `m` 个球放到这些篮子里，使得任意两球间 **最小磁力** 最大。

已知两个球如果分别位于 `x` 和 `y` ，那么它们之间的磁力为 `|x - y|` 。

给你一个整数数组 `position` 和一个整数 `m` ，请你返回最大化的最小磁力。

所有 `position` 中的整数 **互不相同** ，2 <= m <= position.length

**示例 1：**

![img](D:/typoraphotos/q3v1.jpg)

```
输入：position = [1,2,3,4,7], m = 3
输出：3
解释：将 3 个球分别放入位于 1，4 和 7 的三个篮子，两球间的磁力分别为 [3, 3, 6]。最小磁力为 3 。我们没办法让最小磁力大于 3 。
```

**示例 2：**

```
输入：position = [5,4,3,2,1,1000000000], m = 2
输出：999999999
解释：我们使用位于 1 和 1000000000 的篮子时最小磁力最大。
```

 **代码示例：**

```c
// qsort()中用到的比较函数
int cmp_int(const void* elem1, const void* elem2) {
    return *(int*)elem1 - *(int*)elem2;
}
// 检查函数,贪心思想
bool check(int* position, int positionSize, int m, int force) {
    int sum = 0;
    int index=0;//当前小球，默认第一个位置放一个小球
    for (int i = 1; i < positionSize ; i++) {
        if (position[i] - position[index] >= force){
            sum++;
            index=i;// 记录上一个放了小球的篮子的位置
        }
    }
    if (sum >= m - 1)
        return true;
    return false;
}

int maxDistance(int* position, int positionSize, int m) {
    int res; // 返回结果
    // 对数组进行排序,<stdlib.h>
    qsort(position, positionSize, sizeof(int), cmp_int);
    // 找到小球间的最小磁力和最大磁力
    int minForce = position[1] - position[0],
        maxForce = position[positionSize - 1] - position[0];
    for (int i = 0; i < positionSize - 1; i++) {
        int force = position[i + 1] - position[i];
        minForce = minForce <= force ? minForce : force;
    }
    // 二分查找磁力的可能值（区间为minForce~maxForce）
    int left = minForce, rignt = maxForce, mid;
    while (left <= rignt) {
        mid = (left + rignt) / 2;
        // 检查所有篮子的位置是否能放下m个小球，并且最小磁力为mid
        if (check(position, positionSize, m, mid)) { 
            // 满足要求，试着增大最小磁力
            left = mid + 1;
            res = mid;
        } else
            rignt = mid - 1; // 不满足要求，试着减小最小磁力
    }
    return res;
}
```

**解析（检查函数）：**

检查函数只负责检查每一轮找到的mid值（也就是最小磁力）能不能满足要求，即放置好m个小球后所有**相邻小球**的位置差是否都能**>=mid**。这里使用贪心算法去计算相邻小球的位置差，**要记录当前小球所在的位置**，**不能算成了相邻篮子的位置差**

### 1802.有界数组中指定下标处的最大值（middle） ###

给你三个正整数 `n`、`index` 和 `maxSum` 。你需要构造一个同时满足下述所有条件的数组 `nums`（下标 **从 0 开始** 计数）：

- `nums.length == n`
- `nums[i]` 是 **正整数** ，其中 `0 <= i < n`
- `abs(nums[i] - nums[i+1]) <= 1` ，其中 `0 <= i < n-1`
- `nums` 中所有元素之和不超过 `maxSum`
- `nums[index]` 的值被 **最大化**

返回你所构造的数组中的 `nums[index]` 。

注意：`abs(x)` 等于 `x` 的前提是 `x >= 0` ；否则，`abs(x)` 等于 `-x` 。

**示例 1：**

```
输入：n = 4, index = 2,  maxSum = 6
输出：2
解释：数组 [1,1,2,1] 和 [1,2,2,1] 满足所有条件。不存在其他在指定下标处具有更大值的有效数组。
```

**示例 2：**

```
输入：n = 6, index = 1,  maxSum = 10
输出：3
```

**代码示例：**

```c
//两个int相乘结果可能会越界，要用long保存结果
//中间过程(maxNum)也需要改为long，否则结果会先用int保存再转为long
long sum(long maxNum, int len) {
    if (len > maxNum)
        return (1 + maxNum) * maxNum / 2 + len - maxNum;
    else
        return (maxNum - len + 1 + maxNum) * len / 2;
}
int maxValue(int n, int index, int maxSum) {
    int res; // 记录返回结果
    // 二分查找可能的num[index]值
    int left = 1, right = maxSum, mid;
    while (left <= right) {
        mid = (left + right) / 2;
        // 检查是否存在满足当前最大值mid的数组
        if (sum(mid, index + 1) + sum(mid - 1, n - index - 1) <= maxSum) {
            res = mid;
            left = mid + 1;
        } else
            right = mid - 1;
    }
    return res;
}
```

**解析：**

此题关键在于如何判断二分查找到的mid值是否满足要求**sum<=maxSum**，这里注意：**当mid值确定时，整个数组的最小值就是固定的**，只要让index处的值等于mid，两边一次递减即可。同时注意：**递减到1之后，再往后都只能是1了**

### 1089.复写零（easy） ###

给你一个长度固定的整数数组 `arr` ，请你将该数组中出现的每个零都复写一遍，并将其余的元素向右平移。

注意：请不要在超过该数组长度的位置写入元素。请对输入的数组 **就地** 进行上述修改，不要从函数返回任何东西。

**示例 1：**

```
输入：arr = [1,0,2,3,0,4,5,0]
输出：[1,0,0,2,3,0,0,4]
解释：调用函数后，输入的数组将被修改为：[1,0,0,2,3,0,0,4]
```

**示例 2：**

```
输入：arr = [1,2,3]
输出：[1,2,3]
解释：调用函数后，输入的数组将被修改为：[1,2,3]
```

**代码示例：**

```c
void duplicateZeros(int* arr, int arrSize) {
    // 原地变换大多使用双指针
    int i = 0, j = 0;
    for (; j < arrSize; i++, j++) {
        if (arr[i] == 0)
            j++;
    }
    j--;
    i--;
    // 此时i指向复写后的数组的最后一个元素（比如，示例1中是4）
    // 一般情况下，此时j==arrSize-1
    // 但如果此时i指向的是零，j就会再进一步
    // 处理这种特殊情况
    if (j == arrSize) {
        j--; // 指向arrSize-1
        arr[j] = arr[i];
        i--;
        j--;
    }
    while (i >= 0) {
        if (arr[i] != 0)
            arr[j--] = arr[i--];
        else {
            arr[j--] = 0;
            arr[j--] = 0;
            i--;
        }
    }
}
```

### 26.删除有序数组中的重复项（easy） ###

给你一个 **非严格递增排列** 的数组 `nums` ，请你**[ 原地](http://baike.baidu.com/item/原地算法)** 删除重复出现的元素，使每个元素 **只出现一次** ，返回删除后数组的新长度。元素的 **相对顺序** 应该保持 **一致** 。然后返回 `nums` 中唯一元素的个数。

考虑 `nums` 的唯一元素的数量为 `k` ，你需要做以下事情确保你的题解可以被通过：

- 更改数组 `nums` ，使 `nums` 的前 `k` 个元素包含唯一元素，并按照它们最初在 `nums` 中出现的顺序排列。`nums` 的其余元素与 `nums` 的大小不重要。
- 返回 `k` 。

**示例 1：**

```
输入：nums = [1,1,2]
输出：2, nums = [1,2,_]
解释：函数应该返回新的长度 2 ，并且原数组 nums 的前两个元素被修改为 1, 2 。不需要考虑数组中超出新长度后面的元素。
```

**示例 2：**

```
输入：nums = [0,0,1,1,1,2,2,3,3,4]
输出：5, nums = [0,1,2,3,4]
解释：函数应该返回新的长度 5 ， 并且原数组 nums 的前五个元素被修改为 0, 1, 2, 3, 4 。不需要考虑数组中超出新长度后面的元素。
```

**代码示例1：**

```c
//双指针
int removeDuplicates(int* nums, int numsSize) {
    int p1=0,p2=1;
    for(;p2<numsSize;p2++){
        if(nums[p1]!=nums[p2]) nums[++p1]=nums[p2];
    }
    return p1+1;
}
```

**代码示例2：**

```c
//开辟新的空间
int removeDuplicates(int* nums, int numsSize) {
    int arr[numsSize];
    arr[0]=nums[0];
    int resNum=1;
    for(int i=1;i<numsSize;i++){
        if(nums[i]!=nums[i-1]){
            arr[resNum]=nums[i];
            resNum++;
        }  
    }
    for(int i=0;i<resNum;i++){
        nums[i]=arr[i];
    }
    return resNum;
}
```

### 845.数组中的最长山脉（middle） ###

把符合下列属性的数组 `arr` 称为 **山脉数组** ：

- `arr.length >= 3`
- - 存在下标`i`（`0 < i < arr.length - 1`），满足
    - `arr[0] < arr[1] < ... < arr[i - 1] < arr[i]`
    - `arr[i] > arr[i + 1] > ... > arr[arr.length - 1]`

给出一个整数数组 `arr`，返回最长山脉子数组的长度。如果不存在山脉子数组，返回 `0` 。

**示例 1：**

```
输入：arr = [2,1,4,7,3,2,5]
输出：5
解释：最长的山脉子数组是 [1,4,7,3,2]，长度为 5。
```

**示例 2：**

```
输入：arr = [2,2,2]
输出：0
解释：不存在山脉子数组。
```

**代码实例：**

```c
int longestMountain(int* arr, int arrSize) {
    int left = 0, right;
    int res = 0;
    while (left + 1 <= arrSize - 1) {
        right = left + 1;
        if (arr[right] > arr[left]) { // 找到一个上坡
            while (right + 1 < arrSize && arr[right] < arr[right + 1])
                right++; // 走完这个上坡，这里不改变res的值，因为还没找到下坡
            // 此时，right为山顶
            // 找完上坡才能考虑下坡，所以找下坡的if语句要在找上坡的if里面
            if (right + 1 < arrSize && arr[right + 1] < arr[right]) {
                while (right + 1 < arrSize && arr[right + 1] < arr[right])
                    right++;
                // 找到并且找完下坡才能计算res，所以该语句必须放这里
                res = fmax(res, right - left + 1);
            }
        }
        left = right ;
    }
    return res;
}
```

### LCR 121.寻找目标值 - 二维数组（middle） ###

`m`*`n` 的二维数组 `plants` 记录了园林景观的植物排布情况，具有以下特性：

- 每行中，每棵植物的右侧相邻植物不矮于该植物；
- 每列中，每棵植物的下侧相邻植物不矮于该植物。

请判断 `plants` 中是否存在目标高度值 `target`。

**示例 1：**

```
输入：plants = [[2,3,6,8],[4,5,8,9],[5,9,10,12]], target = 8
输出：true
```

**示例 2：**

```
输入：plants = [[1,3,5],[2,5,7]], target = 4
输出：false
```

**代码示例：**

```c
bool findTargetIn2DPlants(int** plants, int plantsSize, int* plantsColSize,int target) {
    // 从第一行最后一个元素开始，往下走变大，往左走变小
    if (plants == NULL || plantsSize == 0 || *plantsColSize == 0)
        return 0;
    int i = 0, j = *plantsColSize - 1;
    while (i < plantsSize && j >= 0) {
        if (plants[i][j] == target)
            return 1;
        else if (plants[i][j] > target)
            j--;
        else
            i++;
    }
    return 0;
}
```

### LCR 012.寻找数组的中心下标（easy） ###

给你一个整数数组 `nums` ，请计算数组的 **中心下标** 。

数组 **中心下标** 是数组的一个下标，其左侧所有元素相加的和等于右侧所有元素相加的和。

如果中心下标位于数组最左端，那么左侧数之和视为 `0` ，因为在下标的左侧不存在元素。这一点对于中心下标位于数组最右端同样适用。

如果数组有多个中心下标，应该返回 **最靠近左边** 的那一个。如果数组不存在中心下标，返回 `-1` 。

**示例 1：**

```
输入：nums = [1,7,3,6,5,6]
输出：3
解释：
中心下标是 3 。
左侧数之和 sum = nums[0] + nums[1] + nums[2] = 1 + 7 + 3 = 11 ，
右侧数之和 sum = nums[4] + nums[5] = 5 + 6 = 11 ，二者相等。
```

**示例 2：**

```
输入：nums = [1, 2, 3]
输出：-1
解释：
数组中不存在满足此条件的中心下标。
```

**示例 3：**

```
输入：nums = [2, 1, -1]
输出：0
解释：
中心下标是 0 。
左侧数之和 sum = 0 ，（下标 0 左侧不存在元素），
右侧数之和 sum = nums[1] + nums[2] = 1 + -1 = 0 。
```

**代码示例：**

```c
//显然，left+right+nums[i]==sum
int pivotIndex(int* nums, int numsSize) {
    int left = 0, right = 0, sum = 0;
    for (int i = 0; i < numsSize; i++)
        sum += nums[i];
    for (int i = 0; i < numsSize; i++) {
        if (left == sum - left - nums[i])
            return i;
        left += nums[i];
    }
    return -1;
}
```

### 169.多数元素（easy） ###

给定一个大小为 `n` 的数组 `nums` ，返回其中的多数元素。多数元素是指在数组中出现次数 **大于** `⌊ n/2 ⌋` 的元素。

你可以假设数组是非空的，并且给定的数组总是存在多数元素。

**示例 1：**

```
输入：nums = [3,2,3]
输出：3
```

**示例 2：**

```
输入：nums = [2,2,1,1,1,2,2]
输出：2
```

**代码示例：**

```c
int majorityElement(int* nums, int numsSize) {
    //多数元素的个数一定大于其他所有元素的个数总和
    //将不同的元素互相抵消，剩下的只能是多数元素
    int res=nums[0],num=1;
    for(int i=1;i<numsSize;i++){
        if(nums[i]==res) num++;
        else{
            num--;
            if(num<0){
                res=nums[i];
                num=1;
            }
        }
    }
    return res;
}
```

### 413.等差数列划分（middle） ###

如果一个数列 **至少有三个元素** ，并且任意两个相邻元素之差相同，则称该数列为等差数列。

例如，`[1,3,5,7,9]`、`[7,7,7,7]` 和 `[3,-1,-5,-9]` 都是等差数列。

给你一个整数数组 `nums` ，返回数组 `nums` 中所有为等差数组的 **子数组** 个数。

**子数组** 是数组中的一个连续序列。

**示例 1：**

```
输入：nums = [1,2,3,4]
输出：3
解释：nums 中有三个子等差数组：[1, 2, 3]、[2, 3, 4] 和 [1,2,3,4] 自身。
```

**示例 2：**

```
输入：nums = [1]
输出：0
```

 **代码示例1：**

```c
//双指针
int numberOfArithmeticSlices(int* nums, int numsSize) {
    if (numsSize < 3)
        return 0;
    int left = 0, right;
    int sum = 0; // 等差数列的数量
    while (right < numsSize - 2) {
        right = left + 1;
        int d = nums[right] - nums[left];
        while (right + 1 < numsSize && nums[right + 1] - nums[right] == d)
            right++;
        int len = right - left + 1; // 当前找到的等差数列长度
        if (len > 2) {
            for (int i = 3; i <= len; i++) {
                sum += len - i + 1;
            }
        }
        left = right;
    }
    return sum;
}
```

**代码示例2：**

```c
//动态规划
int numberOfArithmeticSlices(int* nums, int numsSize){
    if(numsSize<3) return 0;
    int dp[numsSize]; //dp[i]表示以i结尾的等差数列个数
    //确定边界值
    dp[0]=0;
    dp[1]=0;
    int sum=0;
    for(int i=2;i<numsSize;i++){
        if(nums[i]-nums[i-1] == nums[i-1]-nums[i-2]){
            dp[i]=dp[i-1]+1;
            sum+=dp[i];
        }else dp[i]=0;
    }
    return sum;
}
```

### 1588.所有奇数长度子数组的和（easy） ###

给你一个正整数数组 `arr` ，请你计算所有可能的奇数长度子数组的和。

**子数组** 定义为原数组中的一个连续子序列。

请你返回 `arr` 中 **所有奇数长度子数组的和** 。

**示例 1：**

```
输入：arr = [1,4,2,5,3]
输出：58
解释：所有奇数长度子数组和它们的和为：
[1] = 1
[4] = 4
[2] = 2
[5] = 5
[3] = 3
[1,4,2] = 7
[4,2,5] = 11
[2,5,3] = 10
[1,4,2,5,3] = 15
我们将所有值求和得到 1 + 4 + 2 + 5 + 3 + 7 + 11 + 10 + 15 = 58
```

**示例 2：**

```
输入：arr = [1,2]
输出：3
解释：总共只有 2 个长度为奇数的子数组，[1] 和 [2]。它们的和为 3 。
```

**示例 3：**

```
输入：arr = [10,11,12]
输出：66
```

 **代码示例：**

```c
//使用前缀和
int sumOddLengthSubarrays(int* arr, int arrSize){
    int presum[arrSize+1];
    presum[0]=0; //presum[i]表示下标为0~i-1的元素和
    //计算每个元素的前缀和（不包含该元素）
    for(int i=1;i<=arrSize;i++){ 
        presum[i] = presum[i-1]+arr[i-1];
    }
    int sum=0;
    for(int len=1;len<=arrSize;len+=2){ // 遍历子序列长度
        for(int start=0;start<=arrSize-len;start++){ // 遍历子序列开头元素
            // 解法一:暴力解
            // for(int j=start;j-start<len;j++){
            //     sum+=arr[j];
            // }
            // 解法二：使用前缀和优化解1的最内层循环,如下:
            int end = start + len - 1;
            sum+= presum[end+1]-presum[start];
        }
    }
    return sum;
}
```

### 324.摆动排序 II（middle） ###

给你一个整数数组 `nums`，将它重新排列成 `nums[0] < nums[1] > nums[2] < nums[3]...` 的顺序。

你可以假设所有输入数组都可以得到满足题目要求的结果。

**示例 1：**

```
输入：nums = [1,5,1,1,6,4]
输出：[1,6,1,5,1,4]
解释：[1,4,1,5,1,6] 同样是符合题目要求的结果，可以被判题程序接受。
```

**示例 2：**

```
输入：nums = [1,3,2,2,3,1]
输出：[2,3,1,3,1,2]
```

**代码示例：**

```c
int cmp(void* a, void* b) { return *(int*)a - *(int*)b; }
void wiggleSort(int* nums, int numsSize) {
    qsort(nums, numsSize, sizeof(int), cmp);
    int* arr = (int*)malloc(sizeof(int) * numsSize);
    // 无论奇偶位置，都是先插入未插入的数中最大的数！
    // 只有这要才能将中间分界处相等的数分开
    int r = numsSize - 1;
    // 插入奇数位置
    for (int i = 1; i < numsSize; i += 2) {
        arr[i] = nums[r--];
    }
    // 插入偶数位置
    for (int i = 0; i < numsSize; i += 2) {
        arr[i] = nums[r--];
    }
    for (int i = 0; i < numsSize; i++) {
        nums[i] = arr[i];
    }
}
```

### 15.三数之和（middle） ###

给你一个整数数组 `nums` ，判断是否存在三元组 `[nums[i], nums[j], nums[k]]` 满足 `i != j`、`i != k` 且 `j != k` ，同时还满足 `nums[i] + nums[j] + nums[k] == 0` 。请

你返回所有和为 `0` 且不重复的三元组。

**注意：**答案中不可以包含重复的三元组。

**示例 1：**

```
输入：nums = [-1,0,1,2,-1,-4]
输出：[[-1,-1,2],[-1,0,1]]
解释：
nums[0] + nums[1] + nums[2] = (-1) + 0 + 1 = 0 。
nums[1] + nums[2] + nums[4] = 0 + 1 + (-1) = 0 。
nums[0] + nums[3] + nums[4] = (-1) + 2 + (-1) = 0 。
不同的三元组是 [-1,0,1] 和 [-1,-1,2] 。
注意，输出的顺序和三元组的顺序并不重要。
```

**示例 2：**

```
输入：nums = [0,1,1]
输出：[]
解释：唯一可能的三元组和不为 0 。
```

**示例 3：**

```
输入：nums = [0,0,0]
输出：[[0,0,0]]
解释：唯一可能的三元组和为 0 。
```

**代码示例：**

```c
// 双指针，需要先排序
int** threeSum(int* nums, int numsSize, int* returnSize,
               int** returnColumnSizes) {
    int** res = (int**)malloc(sizeof(int*) * 100000);
    *returnColumnSizes = (int*)malloc(sizeof(int) * 100000);
    *returnSize = 0;
    // 冒泡排序
    for (int i = 0; i < numsSize; i++) {
        bool ifSorted = 1; // 记录后面是否已经有序
        for (int j = 1; j < numsSize; j++) {
            if (nums[j - 1] > nums[j]) {
                ifSorted = 0;
                int tmp = nums[j - 1];
                nums[j - 1] = nums[j];
                nums[j] = tmp;
            }
        }
        if (ifSorted)
            break; // 上一轮未进入循环，已经有序
    }
    if (nums[0] > 0)
        return res;
    // 双指针遍历
    int left, right;
    for (int i = 0; i < numsSize; i++) {
        if (i > 0 && nums[i] == nums[i - 1])
            continue;
        left = i + 1;
        right = numsSize - 1;
        while (left < right) {
            if (nums[i] + nums[left] + nums[right] == 0) {
                // 注意：将一维数组tmp加入二维数组res时必须每次对tmp重新分配空间
                // 这里如果将tmp的定义放在函数一开始的地方
                // 那么res中的每个指针都指向相同的内存空间
                // 后果是res中的每个一维数组都是一样的值（最后找到的三元组的值）
                int* tmp = (int*)malloc(sizeof(int) * 3);
                tmp[0] = nums[i];
                tmp[1] = nums[left];
                tmp[2] = nums[right];
                res[(*returnSize)] = tmp;
                (*returnColumnSizes)[(*returnSize)++] = 3;
                // 找到一个三元组就要去重
                while (right > left && nums[right] == nums[right - 1])
                    right--;
                while (left < right && nums[left] == nums[left + 1])
                    left++;
                right--;
                left++;
            } else if (nums[i] + nums[left] + nums[right] > 0) {
                while (right > left && nums[right] == nums[right - 1])
                    right--;
                right--;
            } else if (nums[i] + nums[left] + nums[right] < 0) {
                while (left < right && nums[left] == nums[left + 1])
                    left++;
                left++;
            }
        }
    }
    return res;
}
```

### 18.四数之和（middle） ###

给你一个由 `n` 个整数组成的数组 `nums` ，和一个目标值 `target` 。请你找出并返回满足下述全部条件且**不重复**的四元组 `[nums[a], nums[b], nums[c], nums[d]]` （若两个四元组元素一一对应，则认为两个四元组重复）：

- `0 <= a, b, c, d < n`
- `a`、`b`、`c` 和 `d` **互不相同**
- `nums[a] + nums[b] + nums[c] + nums[d] == target`

你可以按 **任意顺序** 返回答案 。

**示例 1：**

```
输入：nums = [1,0,-1,0,-2,2], target = 0
输出：[[-2,-1,1,2],[-2,0,0,2],[-1,0,0,1]]
```

**示例 2：**

```
输入：nums = [2,2,2,2,2], target = 8
输出：[[2,2,2,2]]
```

**代码示例：**

```c
// 注意，剪枝操作可以不要，但能提高效率
int cmp(const void* a, const void* b) { return *(int*)a - *(int*)b; }
int** fourSum(int* nums, int numsSize, int target, int* returnSize,
              int** returnColumnSizes) {
    int** res = (int**)malloc(sizeof(int*) * 1001);
    *returnSize = 0;
    *returnColumnSizes = malloc(sizeof(int) * 1001);
    // 排序
    qsort(nums, numsSize, sizeof(int), cmp);
    // 双指针遍历,每轮固定两个值i和j
    for (int i = 0; i < numsSize; i++) {
        // 剪枝处理：注意必须要nums[i]>=0，因为负数相加是会变小的
        if (nums[i] > target && nums[i] >= 0) {
            break; // 这里使用break，统一通过最后的return返回
        }
        if (i > 0 && nums[i] == nums[i - 1])
            continue; // 去重
        for (int j = i + 1; j < numsSize; j++) {
            // 2级剪枝处理
            if (nums[i] + nums[j] > target && nums[i] + nums[j] >= 0) {
                break;
            }
            if (j > (i + 1) && nums[j] == nums[j - 1])
                continue; // 去重
            int left = j + 1, right = numsSize - 1;
            while (right > left) {
                // 这里会溢出，改成long就好了
                if ((long)nums[i] + nums[j] + nums[left] + nums[right] == target) {
                    int* tmp = (int*)malloc(sizeof(int) * 4);
                    tmp[0] = nums[i];
                    tmp[1] = nums[j];
                    tmp[2] = nums[left];
                    tmp[3] = nums[right];
                    res[(*returnSize)] = tmp;
                    (*returnColumnSizes)[(*returnSize)++] = 4;
                    // 去重
                    while (left < right && nums[right] == nums[right - 1])
                        right--;
                    while (left < right && nums[left] == nums[left + 1])
                        left++;
                    right--;
                    left++;
                } else if ((long)nums[i] + nums[j] + nums[left] + nums[right] > target) {
                    while (left < right && nums[right] == nums[right - 1])
                        right--;
                    right--;
                } else if ((long)nums[i] + nums[j] + nums[left] + nums[right] < target) {
                    while (left < right && nums[left] == nums[left + 1])
                        left++;
                    left++;
                }
            }
        }
    }
    return res;
}
```

### 238.除自身以外数组的乘积（middle） ###

给你一个整数数组 `nums`，返回 *数组 `answer` ，其中 `answer[i]` 等于 `nums` 中除 `nums[i]` 之外其余各元素的乘积* 。

题目数据 **保证** 数组 `nums`之中任意元素的全部前缀元素和后缀的乘积都在 **32 位** 整数范围内。

请 **不要使用除法，**且在 `O(n)` 时间复杂度内完成此题。

**示例 1:**

```
输入: nums = [1,2,3,4]
输出: [24,12,8,6]
```

**示例 2:**

```
输入: nums = [-1,1,0,-3,3]
输出: [0,0,9,0,0]
```

**代码示例：**

```c
// 精妙的思路，自己体会
// 注意，一直到循环结束，每个位置的最终答案才确定
int* productExceptSelf(int* nums, int numsSize, int* returnSize) {
    int* res = (int*)malloc(sizeof(int) * numsSize);
    for (int i = 0; i < numsSize; i++) {
        res[i] = 1;
    }
    *returnSize = numsSize;
    int pre = 1, back = 1;
    for (int i = 1; i < numsSize; i++) {
        pre *= nums[i - 1];
        back *= nums[numsSize - i];
        res[i] *= pre;
        res[numsSize - 1 - i] *= back;
    }
    return res;
}
```

### 31.下一个排列（middle） ###

整数数组的一个 **排列** 就是将其所有成员以序列或线性顺序排列。

- 例如，`arr = [1,2,3]` ，以下这些都可以视作 `arr` 的排列：`[1,2,3]`、`[1,3,2]`、`[3,1,2]`、`[2,3,1]` 。

整数数组的 **下一个排列** 是指其整数的下一个字典序更大的排列。更正式地，如果数组的所有排列根据其字典顺序从小到大排列在一个容器中，那么数组的 **下一个排列** 就是在这个有序容器中排在它后面的那个排列。如果不存在下一个更大的排列，那么这个数组必须重排为字典序最小的排列（即，其元素按升序排列）。

- 例如，`arr = [1,2,3]` 的下一个排列是 `[1,3,2]` 。
- 类似地，`arr = [2,3,1]` 的下一个排列是 `[3,1,2]` 。
- 而 `arr = [3,2,1]` 的下一个排列是 `[1,2,3]` ，因为 `[3,2,1]` 不存在一个字典序更大的排列。

给你一个整数数组 `nums` ，找出 `nums` 的下一个排列。

必须**[ 原地 ](https://baike.baidu.com/item/原地算法)**修改，只允许使用额外常数空间。

**示例 1：**

```
输入：nums = [1,2,3]
输出：[1,3,2]
```

**示例 2：**

```
输入：nums = [3,2,1]
输出：[1,2,3]
```

**示例 3：**

```
输入：nums = [1,1,5]
输出：[1,5,1]
```

**代码示例：**

```c
void reverse(int* nums, int start, int end) {
    while (start < end) {
        int tmp = nums[start];
        nums[start++] = nums[end];
        nums[end--] = tmp;
    }
}
void nextPermutation(int* nums, int numsSize) {
    // l：从后往前第一个降序的数的下标
    // r：从后往前第一个比l大的数的下标
    int l = -1, r = numsSize - 1;
    for (int i = numsSize - 1; i > 0; i--) {
        if (nums[i - 1] < nums[i]) {
            l = i - 1;
            break;
        }
    }
    if (l >= 0) {
        while (r >= 0 && nums[r] <= nums[l])
            r--;
        // 交换l和r
        int tmp = nums[l];
        nums[l] = nums[r];
        nums[r] = tmp;
    }
    // 翻转l右边的数
    reverse(nums, l + 1, numsSize - 1);
}
```

## 二、位运算 ##

### 136.只出现一次的数字（easy） ###

给你一个 **非空** 整数数组 `nums` ，除了某个元素只出现一次以外，其余每个元素均出现两次。找出那个只出现了一次的元素。

你必须设计并实现线性时间复杂度的算法来解决此问题，且该算法只使用常量额外空间。

**示例 1 ：**

```
输入：nums = [2,2,1]
输出：1
```

**示例 2 ：**

```
输入：nums = [4,1,2,1,2]
输出：4
```

**示例 3 ：**

```
输入：nums = [1]
输出：1
```

**代码示例：**

```c
//二进制异或^：相同为0，不同为1
//所有数和零异或结果还是这个数
//异或运算满足结合律、交换律
int singleNumber(int* nums, int numsSize) {
    int res=nums[0];
    for(int i=1;i<numsSize;i++){
        res = res ^ nums[i];
    }
    return res;
}
```

**解析：**

由异或运算的性质(注释部分)可知 a\^b\^c=a\^c\^b，所以如果给定数组是[a,b,c,b,a]，将数组中所有数进行异或运算，**a\^b\^c\^b\^a = (a\^a)\^(b\^b)\^c = 0\^0\^c = c**

### 1310.子数组异或查询（middle） ###

有一个正整数数组 `arr`，现给你一个对应的查询数组 `queries`，其中 `queries[i] = [Li, Ri]`。

对于每个查询 `i`，请你计算从 `Li` 到 `Ri` 的 **XOR** 值（即 `arr[Li] xor arr[Li+1] xor ... xor arr[Ri]`）作为本次查询的结果。

并返回一个包含给定查询 `queries` 所有结果的数组。

**示例 1：**

```
输入：arr = [1,3,4,8], queries = [[0,1],[1,2],[0,3],[3,3]]
输出：[2,7,14,8] 
解释：
数组中元素的二进制表示形式是：
1 = 0001 
3 = 0011 
4 = 0100 
8 = 1000 
查询的 XOR 值为：
[0,1] = 1 xor 3 = 2 
[1,2] = 3 xor 4 = 7 
[0,3] = 1 xor 3 xor 4 xor 8 = 14 
[3,3] = 8
```

**示例 2：**

```
输入：arr = [4,8,2,10], queries = [[2,3],[1,3],[0,0],[0,3]]
输出：[8,0,4,4]
```

**代码示例：**

```c
// A ^ A = 0;
// B ^ 0 = B;
// 则有，Q[L, R] = Q[0, L-1] ^ Q[0, L-1] ^ Q[L, R] = Q[0, L-1] ^ Q[0, R]
int* xorQueries(int* arr, int arrSize, int** queries, int queriesSize,
                int* queriesColSize, int* returnSize) {
    int* ret = (int*)malloc(sizeof(int) * queriesSize);
    *returnSize = 0;
    int xors[arrSize + 1];
    xors[0] = 0;
    for (int i = 1; i <= arrSize; i++) {
        xors[i] = arr[i - 1] ^ xors[i - 1];
    }
    // xors[1]=arr[0],xors[1]=arrs[0]^arr[1],...
    for (int i = 0; i < queriesSize; i++) {
        int l = queries[i][0];
        int r = queries[i][1];
        // Q[L, R]= Q[0, L-1] ^ Q[0, R]
        ret[(*returnSize)++] = xors[l] ^ xors[r + 1];
    }
    return ret;
}
```

### 260.只出现一次的数字 III（middle） ###

给你一个整数数组 `nums`，其中恰好有两个元素只出现一次，其余所有元素均出现两次。 找出只出现一次的那两个元素。你可以按 **任意顺序** 返回答案。

你必须设计并实现线性时间复杂度的算法且仅使用常量额外空间来解决此问题。

**示例 1：**

```
输入：nums = [1,2,1,3,2,5]
输出：[3,5]
解释：[5, 3] 也是有效的答案。
```

**示例 2：**

```
输入：nums = [-1,0]
输出：[-1,0]
```

**示例 3：**

```
输入：nums = [0,1]
输出：[1,0]
```

**代码示例：**

```c
int* singleNumber(int* nums, int numsSize, int* returnSize) {
    *returnSize = 2;
    int* res = (int*)malloc(sizeof(int) * 2);
    int xorSum = 0;
    for (int i = 0; i < numsSize; i++)
        xorSum ^= nums[i];
    // 循环结束后 xorSum等于要求的这两个数的异或值x
    // 看二进制 此时的xorSum等于1的位就是这两个数所不同的位
    // 利用x&(-x)保留最右边的1（假设在p位），其余位置都是0
    // 此时nums分为两类数据——p位是1 or p位是0
    // 对两类各自内部进行异或，分别得到两个返回值
    // 防止溢出！！！！！
    int rigntOne = (xorSum == INT_MIN ? xorSum : xorSum & (-xorSum));
    int r = 0;
    for (int i = 0; i < numsSize; i++) {
        if (nums[i] & rigntOne) // 找出p位为1的那类数
            r ^= nums[i];
    }
    res[0] = r;
    res[1] = r ^ xorSum;
    return res;
}
```

### 461.汉明距离（easy） ###

两个整数之间的 [汉明距离](https://baike.baidu.com/item/汉明距离) 指的是这两个数字对应二进制位不同的位置的数目。

给你两个整数 `x` 和 `y`，计算并返回它们之间的汉明距离。

**示例 1：**

```
输入：x = 1, y = 4
输出：2
解释：
1   (0 0 0 1)
4   (0 1 0 0)
       ↑   ↑
上面的箭头指出了对应二进制位不同的位置。
```

**示例 2：**

```
输入：x = 3, y = 1
输出：1
```

**代码示例：**

```c
// 可以先异或再遍历，这里使用Brian Kernighan 算法
int hammingDistance(int x, int y) {
    int xor = x ^ y;
    int ret = 0;
    while (xor) {
        xor = xor&(xor-1);
        ret++;
    }
    return ret;
}
```

**解析：**

x&(x-1)的效果：将x最右边的1变为0

### 477.汉明距离总和（middle） ###

两个整数的 [汉明距离](https://baike.baidu.com/item/汉明距离/475174?fr=aladdin) 指的是这两个数字的二进制数对应位不同的数量。

给你一个整数数组 `nums`，请你计算并返回 `nums` 中任意两个数之间 **汉明距离的总和** .

注意：0 <= nums[i] <= 10^9^

**示例 1：**

```
输入：nums = [4,14,2]
输出：6
解释：在二进制表示中，4 表示为 0100 ，14 表示为 1110 ，2表示为 0010 。（这样表示是为了体现后四位之间关系）
所以答案为：
HammingDistance(4, 14) + HammingDistance(4, 2) + HammingDistance(14, 2) = 2 + 2 + 2 = 6
```

**示例 2：**

```
输入：nums = [4,14,4]
输出：4
```

**代码示例：**

```c
int totalHammingDistance(int* nums, int numsSize) {
    int sum = 0;
    // 按照位数逐个对比，nums[i]的范围决定了其最多三十位
    for (int i = 0; i < 30; i++) {
        int c = 0;
        // 遍历每个数
        for (int j = 0; j < numsSize; j++) {
            // 找到当前数的第i位是什么:(nums[i]>>i)&1
            c += (nums[j] >> i) & 1;
        }
        // 第i位上有c个是1，numsSize-c个是0，两两组合
        sum += c * (numsSize - c);
    }
    return sum;
}

```

### 338.比特位计数（easy） ###

给你一个整数 `n` ，对于 `0 <= i <= n` 中的每个 `i` ，计算其二进制表示中 **`1` 的个数** ，返回一个长度为 `n + 1` 的数组 `ans` 作为答案。

**示例 1：**

```
输入：n = 2
输出：[0,1,1]
解释：
0 --> 0
1 --> 1
2 --> 10
```

**示例 2：**

```
输入：n = 5
输出：[0,1,1,2,1,2]
解释：
0 --> 0
1 --> 1
2 --> 10
3 --> 11
4 --> 100
5 --> 101
```

**代码示例：**

```c
int* countBits(int n, int* returnSize) {
    *returnSize = n + 1;
    int* res = (int*)malloc(sizeof(int) * (n + 1));
    res[0] = 0;
    for (int i = 1; i <= n; i++) {
        // 注意这一步的思路
        // 将i最右边的1变成0得到的数的1的个数再加上1
        res[i] = res[i & (i - 1)] + 1;
    }
    return res;
}
```

### LCR 002. 二进制求和（easy） ###

给定两个 01 字符串 `a` 和 `b` ，请计算它们的和，并以二进制字符串的形式输出。

输入为 **非空** 字符串且只包含数字 `1` 和 `0`。

**示例 1:**

```
输入: a = "11", b = "10"
输出: "101"
```

**示例 2:**

```
输入: a = "1010", b = "1011"
输出: "10101"
```

**代码示例：**

```c
// 数组翻转
void reverse(char* s) {
    int len = strlen(s);
    for (int i = 0; i < strlen(s) / 2; i++) {
        char t = s[i];
        s[i] = s[len - i - 1];
        s[len - i - 1] = t;
    }
}
char* addBinary(char* a, char* b) {
    reverse(a);
    reverse(b);
    // strlen()需要头文件#include <string.h>
    // 可以换成sizeof(myArray) / sizeof(myArray[0])
    int len_a = strlen(a);
    int len_b = strlen(b);
    int n = fmax(len_a, len_b);
    // 返回数组的最大长度是n+2，多的部分：最高位进位和终止符
    char* res = (char*)malloc(sizeof(char) * (n + 2));
    int len = 0;   // 返回字符串的长度
    int carry = 0; // 进位
    for (int i = 0; i < n; i++) {
        carry += i < len_a ? (a[i] == '1') : 0;
        carry += i < len_b ? (b[i] == '1') : 0;
        res[len++] = carry % 2 + '0';
        carry /= 2; // 重置进位值
    }
    // 注意最高位如果还有进位需要单独写
    if (carry == 1)
        res[len++] = '1';
    res[len] = '\0';
    reverse(res);
    return res;
}
```

### 405.数字转换为十六进制数（easy） ###

给定一个整数，编写一个算法将这个数转换为十六进制数。对于负整数，我们通常使用 [补码运算](https://baike.baidu.com/item/补码/6854613?fr=aladdin) 方法。

**注意:**

1. 十六进制中所有字母(`a-f`)都必须是小写。
2. 十六进制字符串中不能包含多余的前导零。如果要转化的数为0，那么以单个字符`'0'`来表示；对于其他情况，十六进制字符串中的第一个字符将不会是0字符。 
3. 给定的数确保在32位有符号整数范围内。
4. **不能使用任何由库提供的将数字直接转换或格式化为十六进制的方法。**

**示例 1：**

```
输入:
26
输出:
"1a"
```

**示例 2：**

```
输入:
-1
输出:
"ffffffff"
```

**代码示例：**

```c
//直接利用无符号数
char* toHex(int num) {
    char* res = (char*)malloc(sizeof(char) * 9);
    int len = 0; // 返回数组的长度
    char flag[17] = "0123456789abcdef";
    unsigned num1 = (unsigned)num;
    if (num == 0) {
        return "0";
    }
    while (num1) {
        int n = num1 % 16;
        res[len++] = flag[n];
        num1 /= 16;
    }
    res[len] = '\0';
    for (int i = 0; i < len / 2; i++) {
        char t = res[i];
        res[i] = res[len - i - 1];
        res[len - i - 1] = t;
    }
    return res;
}
```

### 89.格雷编码（middle） ###

**n 位格雷码序列** 是一个由 `2n` 个整数组成的序列，其中：

- 每个整数都在范围 `[0, 2n - 1]` 内（含 `0` 和 `2n - 1`）
- 第一个整数是 `0`
- 一个整数在序列中出现 **不超过一次**
- 每对 **相邻** 整数的二进制表示 **恰好一位不同** ，且
- **第一个** 和 **最后一个** 整数的二进制表示 **恰好一位不同**

给你一个整数 `n` ，返回任一有效的 **n 位格雷码序列** 。

**示例 1：**

```
输入：n = 2
输出：[0,1,3,2]
解释：
[0,1,3,2] 的二进制表示是 [00,01,11,10] 。
- 00 和 01 有一位不同
- 01 和 11 有一位不同
- 11 和 10 有一位不同
- 10 和 00 有一位不同
[0,2,3,1] 也是一个有效的格雷码序列，其二进制表示是 [00,10,11,01] 。
- 00 和 10 有一位不同
- 10 和 11 有一位不同
- 11 和 01 有一位不同
- 01 和 00 有一位不同
```

**示例 2：**

```
输入：n = 1
输出：[0,1]
```

**代码示例：**

```c
// 回溯法也能写
// 格雷码公式：第i个格雷码即为i^(i/2)
int* grayCode(int n, int* returnSize) {
    int ret_size = 1 << n; // 2的n次方
    int *ret = (int *)malloc(ret_size * sizeof(int));
    for (int i = 0; i < ret_size; i++) {
        // i/2向下取整可以表示为i>>1
        ret[i] = (i>>1) ^ i;
    }
    *returnSize = ret_size;
    return ret;
}
```

### 190.颠倒二进制位（easy） ###

颠倒给定的 32 位无符号整数的二进制位。

**示例 1：**

```
输入：n = 00000010100101000001111010011100
输出：964176192 (00111001011110000010100101000000)
解释：输入的二进制串 00000010100101000001111010011100 表示无符号整数 43261596，
     因此返回 964176192，其二进制表示形式为 00111001011110000010100101000000。
```

**示例 2：**

```
输入：n = 11111111111111111111111111111101
输出：3221225471 (10111111111111111111111111111111)
解释：输入的二进制串 11111111111111111111111111111101 表示无符号整数 4294967293，
     因此返回 3221225471 其二进制表示形式为 10111111111111111111111111111111 。
```

 **代码示例：**

```c
uint32_t reverseBits(uint32_t n) {
    uint32_t res = 0;
    // 解法1
    for (int i = 31; i >= 0; i--) {
        // 注意int型的1左移31位是符号位，所以要加unsigned
        res += ((unsigned)1 << i) * (n % 2);
        n /= 2;
    }
    // 解法2
    // for (int i = 0; i < 32; i++) {
    //     res |= (n & 1) << (31 - i);
    //     n >>= 1;
    // }
    return res;
}
```

### 1356.根据数字二进制下 1 的数目排序（easy） ###

给你一个整数数组 `arr` 。请你将数组中的元素按照其二进制表示中数字 **1** 的数目升序排序。

如果存在多个数字二进制中 **1** 的数目相同，则必须将它们按照数值大小升序排列。

请你返回排序后的数组。

**示例 1：**

```
输入：arr = [0,1,2,3,4,5,6,7,8]
输出：[0,1,2,4,8,3,5,6,7]
解释：[0] 是唯一一个有 0 个 1 的数。
[1,2,4,8] 都有 1 个 1 。
[3,5,6] 有 2 个 1 。
[7] 有 3 个 1 。
按照 1 的个数排序得到的结果数组为 [0,1,2,4,8,3,5,6,7]
```

**示例 2：**

```
输入：arr = [1024,512,256,128,64,32,16,8,4,2,1]
输出：[1,2,4,8,16,32,64,128,256,512,1024]
解释：数组中所有整数二进制下都只有 1 个 1 ，所以你需要按照数值大小将它们排序。
```

**示例 3：**

```
输入：arr = [10000,10000]
输出：[10000,10000]
```

**示例 4：**

```
输入：arr = [2,3,5,7,11,13,17,19]
输出：[2,3,5,17,7,11,13,19]
```

**示例 5：**

```
输入：arr = [10,100,1000,10000]
输出：[10,100,10000,1000]
```

**代码示例：**

```c
// 计算int变量的1的个数
int calculate(int s) {
    int sum = 0;
    while (s) {
        if (s & 1)
            sum++;
        s >>= 1;
    }
    return sum;
}
int cmp(void* a, void* b) {
    int x = *(int*)a, y = *(int*)b;
    int c1 = calculate(x), c2 = calculate(y);
    // 如果c1==c2就按照x和y的大小排序，否则就按照c1和c2排序
    return c1 == c2 ? x - y : c1 - c2;
}
int* sortByBits(int* arr, int arrSize, int* returnSize) {
    *returnSize = arrSize;
    qsort(arr, arrSize, sizeof(int), cmp);
    return arr;
}
```

### 397.整数替换（middle） ###

给定一个正整数 `n` ，你可以做如下操作：

1. 如果 `n` 是偶数，则用 `n / 2`替换 `n` 。
2. 如果 `n` 是奇数，则可以用 `n + 1`或`n - 1`替换 `n` 。

返回 `n` 变为 `1` 所需的 *最小替换次数* 。

**示例 1：**

```
输入：n = 8
输出：3
解释：8 -> 4 -> 2 -> 1
```

**示例 2：**

```
输入：n = 7
输出：4
解释：7 -> 8 -> 4 -> 2 -> 1
或 7 -> 6 -> 3 -> 2 -> 1
```

**示例 3：**

```
输入：n = 4
输出：2
```

**代码示例：**

```c
// 当x为最大的int时，x+1会溢出
long long dfs(long long x) {
    if (x == 1)
        return 0;
    if (x % 2 == 0)
        return dfs(x / 2) + 1;
    else
        return fmin(dfs(x - 1), dfs(x + 1)) + 1;
}
int integerReplacement(int n) {
    return (int)dfs((long long)n);
}
//思路相同的另一种写法
int integerReplacement(int n) {
    if (n == 1)
        return 0;
    // 处理溢出的情况
    if (n == 2147483647)
        return fmin(integerReplacement(1073741824) + 1,
                    integerReplacement(n - 1)) + 1;
    if (n % 2 == 0)
        return integerReplacement(n / 2) + 1;
    else
        return fmin(integerReplacement(n + 1), integerReplacement(n - 1)) + 1;
}
```

### 172.阶乘后的零 ###

给定一个整数 `n` ，返回 `n!` 结果中尾随零的数量。

**示例 1：**

```
输入：n = 3
输出：0
解释：3! = 6 ，不含尾随 0
```

**示例 2：**

```
输入：n = 5
输出：1
解释：5! = 120 ，有一个尾随 0
```

**示例 3：**

```
输入：n = 0
输出：0
```

**代码示例：**

```c
// 数学题！！！！
int trailingZeroes(int n) {
    int sum = 0;
    while (n > 4) {
        sum += n / 5;
        n /= 5;
    }
    return sum;
}
```

**解析：**

对于一个数**x=n!**，x末尾有10，一定有x=**10***y=**2\*5**\*y，所以对于x只需要找**1~n**中一共有多少个2和5的组合，**因为2、4、6、8等都含有2，5、10、15都含有5，显然同一范围内2的个数肯定比5的多，所以2和5的组合的数量就等于5的数量**，从大到小找，n中含5的个数就是n/5，**而1~n中下一个含5的数就是n/5**，所以直接循环下去就可得到答案

### 779.第K个语法符号（middle） ###

我们构建了一个包含 `n` 行( **索引从 1 开始** )的表。首先在第一行我们写上一个 `0`。接下来的每一行，将前一行中的`0`替换为`01`，`1`替换为`10`。

- 例如，对于 `n = 3` ，第 `1` 行是 `0` ，第 `2` 行是 `01` ，第3行是 `0110` 。

给定行数 `n` 和序数 `k`，返回第 `n` 行中第 `k` 个字符。（ `k` **从索引 1 开始**）
**示例 1:**

```
输入: n = 1, k = 1
输出: 0
解释: 第一行：0
```

**示例 2:**

```
输入: n = 2, k = 1
输出: 0
解释: 
第一行: 0 
第二行: 01
```

**示例 3:**

```
输入: n = 2, k = 2
输出: 1
解释:
第一行: 0
第二行: 01
```

**代码示例：**

```c
// 找规律
int kthGrammar(int n, int k) {
    if (n == 1)
        return 0;
    if (n == 2 && k == 1)
        return 0;
    if (n == 2 && k == 2)
        return 1;

    if (k <= pow(2, n - 2))
        return kthGrammar(n - 1, k);
    else
        return !kthGrammar(n - 1, k - pow(2, n - 2));
}
```

### 面试题 05.07. 配对交换（easy） ###

配对交换。编写程序，交换某个整数的奇数位和偶数位，尽量使用较少的指令（也就是说，位0与位1交换，位2与位3交换，以此类推）。

**示例1:**

```
 输入：num = 2（或者0b10）
 输出 1 (或者 0b01)
```

**示例2:**

```
 输入：num = 3
 输出：3
```

**提示:**

1. `num`的范围在[0, 2^30 - 1]之间，不会发生整数溢出。

**代码示例：**

```c
int exchangeBits(int num){
    // 0b开头是二进制
    unsigned int t=0b01010101010101010101010101010101;
    int m=num;
    // 保留奇数位
    num&=t;
    // 奇数位移到偶数位
    num<<=1;
    // 保留偶数位
    m&=(t<<1);
    // 偶数位移到奇数位
    m>>=1;
    // 合并
    num += m;
    return num;
}
```

## 三、字符串 ##

### 28.找出字符串中第一个匹配项的下标（easy） ###

给你两个字符串 `haystack` 和 `needle` ，请你在 `haystack` 字符串中找出 `needle` 字符串的第一个匹配项的下标（下标从 0 开始）。如果 `needle` 不是 `haystack` 的一部分，则返回 `-1` 。

**示例 1：**

```
输入：haystack = "sadbutsad", needle = "sad"
输出：0
解释："sad" 在下标 0 和 6 处匹配。
第一个匹配项的下标是 0 ，所以返回 0 。
```

**示例 2：**

```
输入：haystack = "leetcode", needle = "leeto"
输出：-1
解释："leeto" 没有在 "leetcode" 中出现，所以返回 -1 。
```

**代码示例：**

```c
int strStr(char* haystack, char* needle) {
    int len1 = strlen(haystack), len2 = strlen(needle);
    if (len2 > len1)
        return -1;
    int p = 0, q = 0;
    for (int i = 0; i < len1 - len2 + 1; i++) {
        p = i;
        q = 0;
        while (haystack[p] == needle[q]) {
            p++;
            q++;
            if (q == len2)
                return p - len2;
        }
    }
    return -1;
}
```

### 459.重复的子字符串（easy） ###

给定一个非空的字符串 `s` ，检查是否可以通过由它的一个子串重复多次构成。

**示例 1:**

```
输入: s = "abab"
输出: true
解释: 可由子串 "ab" 重复两次构成。
```

**示例 2:**

```
输入: s = "aba"
输出: false
```

**示例 3:**

```
输入: s = "abcabcabcabc"
输出: true
解释: 可由子串 "abc" 重复四次构成。 (或子串 "abcabc" 重复两次构成。)
```

**代码示例：**

```c
bool repeatedSubstringPattern(char* s) {
    int len = strlen(s);
    if (len == 1)
        return 0;
    for (int i = 1; i <= len / 2; i++) {
        int front = 0, rear = i;
        while (s[front] == s[rear]) {
            front++;
            rear = (rear + 1) % len;
            if (front == len)
                return 1;
        }
    }
    return 0;
}
```

### 557.反转字符串中的单词 III（easy） ###

给定一个字符串 `s` ，你需要反转字符串中每个单词的字符顺序，同时仍保留空格和单词的初始顺序。

**示例 1：**

```
输入：s = "Let's take LeetCode contest"
输出："s'teL ekat edoCteeL tsetnoc"
```

**示例 2:**

```
输入： s = "Mr Ding"
输出："rM gniD"
```

**提示：**

- `1 <= s.length <= 5 * 104`
- `s` 包含可打印的 **ASCII** 字符。
- `s` 不包含任何开头或结尾空格。
- `s` 里 **至少** 有一个词。
- `s` 中的所有单词都用一个空格隔开。

**代码示例：**

```c
char* reverseWords(char* s) {
    int left = 0, right = 0;
    int len = strlen(s);
    char* res = (char*)malloc(sizeof(char) * (len + 1));
    int len_res = 0;
    while (left < len) {
        while (s[right] != ' ' && right < len - 1)
            right++;
        int temp = right - 1;
        if (right == len - 1)
            temp++;
        while (temp >= left)
            res[len_res++] = s[temp--];
        if (right != len - 1)
            res[len_res++] = ' ';
        right++;
        left = right;
    }
    res[len_res] = '\0';
    return res;
}
```

### LCR 167. 招式拆解 I（middle） ###

某套连招动作记作序列 `arr`，其中 `arr[i]` 为第 `i` 个招式的名字。请返回 `arr` 中最多可以出连续不重复的多少个招式。

**示例 1:**

```
输入: arr = "dbascDdad"
输出: 6
解释: 因为连续且最长的招式序列是 "dbascD" 或 "bascDd"，所以其长度为 6。
```

**示例 2:**

```
输入: arr = "KKK"
输出: 1
解释: 因为无重复字符的最长子串是 "K"，所以其长度为 1。
```

**示例 3:**

```
输入: arr = "pwwkew"
输出: 3
解释: 因为连续且最长的招式序列是 "wke"，所以其长度为 3。     
请注意区分 子串 与 子序列 的概念：你的答案必须是 连续招式 的长度，也就是 子串。而 "pwke" 是一个非连续的 子序列，不是 子串。
```

**代码示例：**

```c
int dismantlingAction(char* arr) {
    int len = strlen(arr);
    int max = 0, temp = 0;
    int appear[128]; // 记录所有asc2码最后出现的位置,共有128个
    for (int i = 0; i < 128; i++) // 初始化所有字符出现位置为-1
        appear[i] = -1;
    for (int i = 0; i < len; i++) {
        int last = appear[arr[i]]; // 记录上一个arr[i]出现的位置
        appear[arr[i]] = i;        // 更新arr[i]最后出现的位置
        // temp记录了以arr[i]结尾的最长不重复子串
        if ((i - last) > temp) {
            // 以arr[i-1]结尾的子串在两个arr[i]之间有重复
            temp++;
        } else
            temp = i - last;
        max = fmax(temp, max);
    }
    return max;
}
```

### LCR 032. 有效的字母异位词（easy） ###

给定两个字符串 `s` 和 `t` ，编写一个函数来判断它们是不是一组变位词（字母异位词）。

**注意：**若 `*s*` 和 `*t*` 中每个字符出现的次数都相同且**字符顺序不完全相同**，则称 `*s*` 和 `*t*` 互为变位词（字母异位词）。`s` and `t` 仅包含小写字母

**示例 1:**

```
输入: s = "anagram", t = "nagaram"
输出: true
```

**示例 2:**

```
输入: s = "rat", t = "car"
输出: false
```

**示例 3:**

```
输入: s = "a", t = "a"
输出: false
```

**代码示例1：**

```c
// 记录每个字符出现次数
// 哈希表
bool isAnagram(char* s, char* t) {
    if (strcmp(s, t) == 0 || strlen(s) != strlen(t))
        return 0;
    int a[26]; // 记录'a'~'z'出现的次数
    memset(a, 0, sizeof(a));
    int len_s = strlen(s);
    for (int i = 0; i < len_s; i++)
        a[s[i] - 'a']++;
    for (int j = 0; j < len_s; j++) {
        a[t[j] - 'a']--;
        if (a[t[j] - 'a'] < 0)
            return 0;
    }
    return 1;
}
```

**代码示例2：**

```c
// 符合条件：排序前不相等，排序后相等
int cmp(void* a, void* b) { return *(char*)a - *(char*)b; }
bool isAnagram(char* s, char* t) {
    // 相等返回0
    if (strcmp(s, t) == 0)
        return 0;
    qsort(s, strlen(s), sizeof(char), cmp);
    qsort(t, strlen(t), sizeof(char), cmp);
    if (strcmp(s, t) != 0)
        return 0;
    return 1;
}
```

### 面试题 01.09. 字符串轮转（easy） ###

字符串轮转。给定两个字符串`s1`和`s2`，请编写代码检查`s2`是否为`s1`旋转而成（比如，`waterbottle`是`erbottlewat`旋转后的字符串）。

**示例1:**

```
 输入：s1 = "waterbottle", s2 = "erbottlewat"
 输出：True
```

**示例2:**

```
 输入：s1 = "aa", s2 = "aba"
 输出：False
```

**代码示例：**

```c
bool isFlipedString(char* s1, char* s2) {
    int len_1 = strlen(s1), len_2 = strlen(s2);
    if (len_1 != len_2)
        return 0;
    if (len_1 == 0)
        return 1;
    // 循环判断s1[(i+j)%n]与s2[j]
    for (int i = 0; i < len_1; i++) {
        bool flag = 0;
        for (int j = 0; j < len_2; j++) {
            if (s1[(i + j) % len_1] != s2[j]) {
                flag = 1;
                break;
            }
        }
        if (!flag)
            return 1;
    }
    return 0;
}
// TODO 方法二：对s1扩充一倍，使用KMP
```

### 2042.检查句子中的数字是否递增（easy） ###

句子是由若干 **token** 组成的一个列表，**token** 间用 **单个** 空格分隔，句子没有前导或尾随空格。每个 token 要么是一个由数字 `0-9` 组成的不含前导零的 **正整数** ，要么是一个由小写英文字母组成的 **单词** 。

- 示例，`"a puppy has 2 eyes 4 legs"` 是一个由 7 个 token 组成的句子：`"2"` 和 `"4"` 是数字，其他像 `"puppy"` 这样的 tokens 属于单词。

给你一个表示句子的字符串 `s` ，你需要检查 `s` 中的 **全部** 数字是否从左到右严格递增（即，除了最后一个数字，`s` 中的 **每个** 数字都严格小于它 **右侧** 的数字）。

如果满足题目要求，返回 `true` ，否则，返回 `false` 。

**示例 1：**

![example-1](D:/typoraphotos/example1.png)

```
输入：s = "1 box has 3 blue 4 red 6 green and 12 yellow marbles"
输出：true
解释：句子中的数字是：1, 3, 4, 6, 12 。
这些数字是按从左到右严格递增的 1 < 3 < 4 < 6 < 12 。
```

**示例 2：**

```
输入：s = "hello world 5 x 5"
输出：false
解释：句子中的数字是：5, 5 。这些数字不是严格递增的。
```

**示例 3：**

![example-3](D:/typoraphotos/example3.png)

```
输入：s = "sunset is at 7 51 pm overnight lows will be in the low 50 and 60 s"
输出：false
解释：s 中的数字是：7, 51, 50, 60 。这些数字不是严格递增的。
```

**示例 4：**

```
输入：s = "4 5 11 26"
输出：true
解释：s 中的数字是：4, 5, 11, 26 。
这些数字是按从左到右严格递增的：4 < 5 < 11 < 26 。
```

**代码示例：**

```c
bool areNumbersAscending(char* s) {
    int num = 0;
    int i = 0;
    while (s[i] != '\0') {
        if (s[i] >= '0' && s[i] <= '9') {
            int r = i + 1;
            while (s[r] >= '0' && s[r] <= '9' && s[r] != '\0')
                r++;
            int x = 0;
            while (i < r) {
                // 字符串数字转int数据
                x = x * 10 + s[i++] - '0';
            }
            if (num >= x)
                return 0;
            num = x;
        } else
            i++;
    }
    return 1;
}
```

### 面试题 17.11. 单词距离（middle） ###

有个内含单词的超大文本文件，给定任意两个`不同的`单词，找出在这个文件中这两个单词的最短距离(相隔单词数)。如果寻找过程在这个文件中会重复多次，而每次寻找的单词不同，你能对此优化吗?

**示例：**

```
输入：words = ["I","am","a","student","from","a","university","in","a","city"], word1 = "a", word2 = "student"
输出：1
```

**代码示例：**

```c
// index1、index2分别记录最新遇到的word1和word2的值
// index1和index2都大于0时意味着已经出现了这两个单词，计算当前距离并与min比较即可
int findClosest(char** words, int wordsSize, char* word1, char* word2) {
    int index1 = -1, index2 = -1;
    int min = wordsSize + 1;
    for (int i = 0; i < wordsSize; i++) {
        if (strcmp(word1, words[i]) == 0)
            index1 = i;
        if (strcmp(word2, words[i]) == 0)
            index2 = i;
        if (index1 >= 0 && index2 >= 0)
            min = fmin(min,
                       index1 > index2 ? (index1 - index2) : (index2 - index1));
    }
    return min;
}
```

### 541.反转字符串 II（easy） ###

给定一个字符串 `s` 和一个整数 `k`，从字符串开头算起，每计数至 `2k` 个字符，就反转这 `2k` 字符中的前 `k` 个字符。

- 如果剩余字符少于 `k` 个，则将剩余字符全部反转。
- 如果剩余字符小于 `2k` 但大于或等于 `k` 个，则反转前 `k` 个字符，其余字符保持原样。

**示例 1：**

```
输入：s = "abcdefg", k = 2
输出："bacdfeg"
```

**示例 2：**

```
输入：s = "abcd", k = 2
输出："bacd"
```

**代码示例：**

```c
char* reverseStr(char* s, int k) {
    int len = strlen(s);
    for (int i = 0; i < len; i += 2 * k) {
        int left = i;
        int right = (i + k) > len ? (len - 1) : (i + k - 1);
        while (right > left) {
            char tmp = s[left];
            s[left++] = s[right];
            s[right--] = tmp;
        }
    }
    return s;
}
```

### 151.反转字符串中的单词（middle） ###

给你一个字符串 `s` ，请你反转字符串中 **单词** 的顺序。

**单词** 是由非空格字符组成的字符串。`s` 中使用至少一个空格将字符串中的 **单词** 分隔开。

返回 **单词** 顺序颠倒且 **单词** 之间用单个空格连接的结果字符串。

**注意：**输入字符串 `s`中可能会存在前导空格、尾随空格或者单词间的多个空格。返回的结果字符串中，单词间应当仅用单个空格分隔，且不包含任何额外的空格。

**示例 1：**

```
输入：s = "the sky is blue"
输出："blue is sky the"
```

**示例 2：**

```
输入：s = "  hello world  "
输出："world hello"
解释：反转后的字符串中不能存在前导空格和尾随空格。
```

**示例 3：**

```
输入：s = "a good   example"
输出："example good a"
解释：如果两个单词间有多余的空格，反转后的字符串需要将单词间的空格减少到仅有一个。
```

**代码示例：**

```c
// 1.去除多余空格
// 2.翻转整个字符串
// 3.翻转每个单词
char* reverseWords(char* s) {
    int len = strlen(s);
    // 1.去除多余空格——双指针
    int fast = 0, slow = 0;
    // 去除首部空格
    while (fast < len && s[fast] == ' ') {
        fast++;
    } // 此时fast指向第一个非空格字符
    // 把字符串前移以及去除中间的连续空格
    for (; fast < len; fast++) {
        // 遇到连续的空格
        if (s[fast] == ' ' && s[fast - 1] == ' ')
            continue;
        s[slow++] = s[fast];
    }
    // 去除末尾空格
    if (s[slow - 1] == ' ' && slow - 1 >= 0) {
        s[slow - 1] = '\0';
        len = slow - 1;
    } else {
        s[slow] = '\0';
        len = slow;
    }

    // 2.翻转整个字符串
    int front = 0, rear = len - 1;
    while (front < rear) {
        char tmp = s[front];
        s[front++] = s[rear];
        s[rear--] = tmp;
    }

    // 3.翻转每个单词
    for (int i = 0; i < len; i++) {
        front = i;
        while (s[i] != ' ' && i < len)
            i++;
        rear = i - 1;
        while (front < rear) {
            char tmp = s[front];
            s[front++] = s[rear];
            s[rear--] = tmp;
        }
    }
    return s;
}
```





## 四、贪心算法 ##

### 376.摆动序列（middle） ###

如果连续数字之间的差严格地在正数和负数之间交替，则数字序列称为 **摆动序列 。**第一个差（如果存在的话）可能是正数或负数。仅有一个元素或者含两个不等元素的序列也视作摆动序列。

- 例如， `[1, 7, 4, 9, 2, 5]` 是一个 **摆动序列** ，因为差值 `(6, -3, 5, -7, 3)` 是正负交替出现的。
- 相反，`[1, 4, 7, 2, 5]` 和 `[1, 7, 4, 5, 5]` 不是摆动序列，第一个序列是因为它的前两个差值都是正数，第二个序列是因为它的最后一个差值为零。

**子序列** 可以通过从原始序列中删除一些（也可以不删除）元素来获得，剩下的元素保持其原始顺序。

给你一个整数数组 `nums` ，返回 `nums` 中作为 **摆动序列** 的 **最长子序列的长度** 。

 

**示例 1：**

```
输入：nums = [1,7,4,9,2,5]
输出：6
解释：整个序列均为摆动序列，各元素之间的差值为 (6, -3, 5, -7, 3) 。
```

**示例 2：**

```
输入：nums = [1,17,5,10,13,15,10,5,16,8]
输出：7
解释：这个序列包含几个长度为 7 摆动序列。
其中一个是 [1, 17, 10, 13, 10, 16, 8] ，各元素之间的差值为 (16, -7, 3, -3, 6, -8) 。
```

**示例 3：**

```
输入：nums = [1,2,3,4,5,6,7,8,9]
输出：2
```

**代码示例1：**

```c
// 看代码随想录吧，真理解不了
// 贪心
int wiggleMaxLength(int* nums, int numsSize) {
    if (numsSize == 1)
        return 1;
    int res = 1;
    int pre = 0, back = 0;
    for (int i = 0; i < numsSize - 1; i++) {
        back = nums[i + 1] - nums[i];
        if ((back > 0 && pre <= 0) || (back < 0 && pre >= 0)) {
            res++;
            pre = back;
        }
    }
    return res;
}
```

**代码示例2：**

```c
// 动态规划
// dp[i][0]，表示考虑前 i 个数，第 i 个数作为山峰的摆动子序列的最长长度
// dp[i][1]，表示考虑前 i 个数，第 i 个数作为山谷的摆动子序列的最长长度
int wiggleMaxLength(int* nums, int numsSize){
    int dp[1001][2];
    memset(dp,0,sizeof(dp));
    dp[0][0]=dp[0][1]=1;
    for(int i=0;i<numsSize;i++){
        dp[i][0]=dp[i][1]=1;
        // 遍历它前面的数
        for(int j=0;j<i;j++){
            // i相对于j，只能当山峰
            if(nums[i]>nums[j]) dp[i][0]=fmax(dp[i][0],dp[j][1]+1);
            // i相对于j，只能当山谷
            if(nums[i]<nums[j]) dp[i][1]=fmax(dp[i][1],dp[j][0]+1);
        }
    }
    return fmax(dp[numsSize-1][0],dp[numsSize-1][1]);
}
```

### 53.最大子数组和（middle） ###

给你一个整数数组 `nums` ，请你找出一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。

**子数组**是数组中的一个连续部分。

**示例 1：**

```
输入：nums = [-2,1,-3,4,-1,2,1,-5,4]
输出：6
解释：连续子数组 [4,-1,2,1] 的和最大，为 6 。
```

**示例 2：**

```
输入：nums = [1]
输出：1
```

**示例 3：**

```
输入：nums = [5,4,-1,7,8]
输出：23
```

**代码示例1：**

```c
// 贪心
// 局部最优：当前“连续和”为负数的时候立刻放弃
// 从下一个元素重新计算“连续和”
// 因为负数加上下一个元素 “连续和”只会越来越小
int maxSubArray(int* nums, int numsSize) {
    int max = INT32_MIN;
    int curr = 0;
    for (int i = 0; i < numsSize; i++) {
        curr += nums[i];
        if (curr > max)
            max = curr;
        if (curr < 0)
            curr = 0;
    }
    return max;
}
```

**代码示例2：**

```c
// 动态规划
int maxSubArray(int* nums, int numsSize) {
    int dp[numsSize];
    int max = nums[0];
    dp[0] = nums[0];
    for (int i = 1; i < numsSize; i++) {
        dp[i] = fmax(dp[i - 1] + nums[i], nums[i]);
        max = fmax(max, dp[i]);
    }
    return max;
}
```

### 121.买卖股票的最佳时机（easy） ###

给定一个数组 `prices` ，它的第 `i` 个元素 `prices[i]` 表示一支给定股票第 `i` 天的价格。

你只能选择 **某一天** 买入这只股票，并选择在 **未来的某一个不同的日子** 卖出该股票。设计一个算法来计算你所能获取的最大利润。

返回你可以从这笔交易中获取的最大利润。如果你不能获取任何利润，返回 `0` 。

**示例 1：**

```
输入：[7,1,5,3,6,4]
输出：5
解释：在第 2 天（股票价格 = 1）的时候买入，在第 5 天（股票价格 = 6）的时候卖出，最大利润 = 6-1 = 5 。
     注意利润不能是 7-1 = 6, 因为卖出价格需要大于买入价格；同时，你不能在买入前卖出股票。
```

**示例 2：**

```
输入：prices = [7,6,4,3,1]
输出：0
解释：在这种情况下, 没有交易完成, 所以最大利润为 0。
```

**代码示例1：**

```c
// dp
int maxProfit(int* prices, int pricesSize) {
    if (pricesSize == 0)
        return 0;
    // 0:第i天不持股时拥有的最多现金
    // 1:第i天持股状态拥有的最多现金
    int dp[pricesSize][2];
    memset(dp, 0, sizeof(dp));
    dp[0][0] = 0;
    dp[0][1] = -prices[0];
    for (int i = 1; i < pricesSize; i++) {
        dp[i][0] = fmax(dp[i - 1][1] + prices[i], dp[i - 1][0]);
        // dp[i][1] = fmax(dp[i - 1][1], dp[i-1][0]-prices[i]);
        // 千万不能写成上面这样，只能买卖一次！！
        dp[i][1] = fmax(dp[i - 1][1], -prices[i]);
    }
    return dp[pricesSize - 1][0];
}
```

**代码示例2：**

```c
// 贪心
int maxProfit(int* prices, int pricesSize) {
    int res = 0;
    int price = INT_MAX; // 记录历史最低价
    for (int i = 0; i < pricesSize; i++) {
        price = fmin(price, prices[i]);
        // 取当前价格和历史最低价的差值中最大的
        res = fmax(res, prices[i] - price);
    }
    return res;
}
```



### 122.买卖股票的最佳时机 II（middle） ###

给你一个整数数组 `prices` ，其中 `prices[i]` 表示某支股票第 `i` 天的价格。

在每一天，你可以决定是否购买和/或出售股票。你在任何时候 **最多** 只能持有 **一股** 股票。你也可以先购买，然后在 **同一天** 出售。

返回 *你能获得的 **最大** 利润* 。

**示例 1：**

```
输入：prices = [7,1,5,3,6,4]
输出：7
解释：在第 2 天（股票价格 = 1）的时候买入，在第 3 天（股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5 - 1 = 4 。
     随后，在第 4 天（股票价格 = 3）的时候买入，在第 5 天（股票价格 = 6）的时候卖出, 这笔交易所能获得利润 = 6 - 3 = 3 。
     总利润为 4 + 3 = 7 。
```

**示例 2：**

```
输入：prices = [1,2,3,4,5]
输出：4
解释：在第 1 天（股票价格 = 1）的时候买入，在第 5 天 （股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5 - 1 = 4 。
     总利润为 4 。
```

**示例 3：**

```
输入：prices = [7,6,4,3,1]
输出：0
解释：在这种情况下, 交易无法获得正利润，所以不参与交易可以获得最大利润，最大利润为 0 。
```

**代码示例1：**

```c
// 贪心
int maxProfit(int* prices, int pricesSize) {
    bool ifhold = 0; // 是否持股
    int money = 0;   // 利润
    for (int i = 0; i < pricesSize - 1; i++) {
        // 没股票，明天涨价，那就买
        if (prices[i + 1] > prices[i] && !ifhold) {
            money -= prices[i];
            ifhold = 1;
        }
        // 有股票，明天降价 那就卖
        if (prices[i + 1] < prices[i] && ifhold) {
            money += prices[i];
            ifhold = 0;
        }
    }
    // 买了一直没卖，最后一天必须卖了（容易忽略！）
    if (ifhold)
        money += prices[pricesSize - 1];
    return money;
}
```

**代码示例2：**

```c
// 动态规划
int maxProfit(int* prices, int pricesSize) {
    // 0:第i天不持股时拥有的最多现金
    // 1:第i天持股状态拥有的最多现金
    int dp[pricesSize][2];
    dp[0][0] = 0;
    dp[0][1] = -prices[0];
    int money = 0;
    for (int i = 1; i < pricesSize; i++) {
        dp[i][0] = fmax(dp[i - 1][0], dp[i - 1][1] + prices[i]);
        dp[i][1] = fmax(dp[i - 1][0] - prices[i], dp[i - 1][1]);
        money = fmax(money, dp[i][0]);
    }
    return money;
}
```

### 55.跳跃游戏（middle） ###

给你一个非负整数数组 `nums` ，你最初位于数组的 **第一个下标** 。数组中的每个元素代表你在该位置可以跳跃的最大长度。

判断你是否能够到达最后一个下标，如果可以，返回 `true` ；否则，返回 `false` 。

**示例 1：**

```
输入：nums = [2,3,1,1,4]
输出：true
解释：可以先跳 1 步，从下标 0 到达下标 1, 然后再从下标 1 跳 3 步到达最后一个下标。
```

**示例 2：**

```
输入：nums = [3,2,1,0,4]
输出：false
解释：无论怎样，总会到达下标为 3 的位置。但该下标的最大跳跃长度是 0 ， 所以永远不可能到达最后一个下标。
```

**代码示例：**

```c
bool canJump(int* nums, int numsSize) {
    // 特殊情况
    if (nums[0] == 0 && numsSize != 1)
        return 0;
    int stepLeft = nums[0]; // 能走几步
    for (int i = 1; i < numsSize; i++) {
        stepLeft--;
        if (stepLeft < nums[i]) {
            stepLeft = nums[i];
        }
        // 注意判断i!=numsSize-1
        if (stepLeft == 0 && i != numsSize - 1)
            return 0;
    }
    return 1;
}
```

### 45.跳跃游戏 II（middle） ###

给定一个长度为 `n` 的 **0 索引**整数数组 `nums`。初始位置为 `nums[0]`。

每个元素 `nums[i]` 表示从索引 `i` 向前跳转的最大长度。换句话说，如果你在 `nums[i]` 处，你可以跳转到任意 `nums[i + j]` 处:

- `0 <= j <= nums[i]` 
- `i + j < n`

返回到达 `nums[n - 1]` 的最小跳跃次数。生成的测试用例可以到达 `nums[n - 1]`。

**示例 1:**

```
输入: nums = [2,3,1,1,4]
输出: 2
解释: 跳到最后一个位置的最小跳跃数是 2。
     从下标为 0 跳到下标为 1 的位置，跳 1 步，然后跳 3 步到达数组的最后一个位置。
```

**示例 2:**

```
输入: nums = [2,3,0,1,4]
输出: 2
```

**代码示例：**

```c
int jump(int* nums, int numsSize) {
    if (numsSize == 1)
        return 0;
    int curDis = 0; // 当前这一步能走到的最远距离
    int nxtDis = 0; // 当前这一步范围内的下一步的最远距离
    int step = 0;   // 步数，初始时为第0步
    for (int i = 0; i < numsSize; i++) {
        // nxtDis保持已经走到的范围内的最远可达距离
        nxtDis = fmax(nxtDis, nums[i] + i);
        if (i == curDis) { // 这一步走完了
            step++;        // 下一步开始了
            curDis = nxtDis;
            if (curDis >= numsSize - 1)
                break;
        }
    }
    return step;
}
```

### 1005.K 次取反后最大化的数组和（easy） ###

给你一个整数数组 `nums` 和一个整数 `k` ，按以下方法修改该数组：

- 选择某个下标 `i` 并将 `nums[i]` 替换为 `-nums[i]` 。

重复这个过程恰好 `k` 次。可以多次选择同一个下标 `i` 。

以这种方式修改数组后，返回数组 **可能的最大和** 。

**示例 1：**

```
输入：nums = [4,2,3], k = 1
输出：5
解释：选择下标 1 ，nums 变为 [4,-2,3] 。
```

**示例 2：**

```
输入：nums = [3,-1,0,2], k = 3
输出：6
解释：选择下标 (1, 2, 2) ，nums 变为 [3,1,0,2] 。
```

**示例 3：**

```
输入：nums = [2,-3,-1,5,-4], k = 2
输出：13
解释：选择下标 (1, 4) ，nums 变为 [2,3,-1,5,4] 。
```

**代码示例1：**

```c
// 笨方法：每次都转换最小的，这样每次都需要排序
int cmp(void* a, void* b) { return *(int*)a - *(int*)b; }
int largestSumAfterKNegations(int* nums, int numsSize, int k) {
    qsort(nums, numsSize, sizeof(int), cmp);
    while (k--) {
        nums[0] *= -1;
        qsort(nums, numsSize, sizeof(int), cmp);
    }
    int sum = 0;
    for (int i = 0; i < numsSize; i++) {
        sum += nums[i];
    }
    return sum;
}
```

**代码示例2：**

```c
// 只排序一次,按照绝对值大小进行排序,从大到小！！
int cmp(void* a, void* b) { 
    return -(abs(*(int*)a) - abs(*(int*)b)); // 比普通的多个负号
}
int largestSumAfterKNegations(int* nums, int numsSize, int k) {
    qsort(nums, numsSize, sizeof(int), cmp);
    // 排序过后，负数之间，绝对值更大的在前面
    for (int i = 0; i < numsSize; i++) {
        // 优先将负数变正数
        if (k > 0 && nums[i] < 0) {
            nums[i] *= -1;
            k--;
        }
    }
    // k还有剩余，如果是k此时为偶数无所谓
    // 如果是奇数，将最小的正数反复取反，最后会变成负数
    if (k % 2 == 1)
        nums[numsSize - 1] *= -1;
    int sum = 0;
    for (int i = 0; i < numsSize; i++) {
        sum += nums[i];
    }
    return sum;
}
```

### 134.加油站（middle） ###

在一条环路上有 `n` 个加油站，其中第 `i` 个加油站有汽油 `gas[i]` 升。

你有一辆油箱容量无限的的汽车，从第 `i` 个加油站开往第 `i+1` 个加油站需要消耗汽油 `cost[i]` 升。你从其中的一个加油站出发，开始时油箱为空。

给定两个整数数组 `gas` 和 `cost` ，如果你可以按顺序绕环路行驶一周，则返回出发时加油站的编号，否则返回 `-1` 。如果存在解，则 **保证** 它是 **唯一** 的。

**示例 1:**

```
输入: gas = [1,2,3,4,5], cost = [3,4,5,1,2]
输出: 3
解释:
从 3 号加油站(索引为 3 处)出发，可获得 4 升汽油。此时油箱有 = 0 + 4 = 4 升汽油
开往 4 号加油站，此时油箱有 4 - 1 + 5 = 8 升汽油
开往 0 号加油站，此时油箱有 8 - 2 + 1 = 7 升汽油
开往 1 号加油站，此时油箱有 7 - 3 + 2 = 6 升汽油
开往 2 号加油站，此时油箱有 6 - 4 + 3 = 5 升汽油
开往 3 号加油站，你需要消耗 5 升汽油，正好足够你返回到 3 号加油站。
因此，3 可为起始索引。
```

**示例 2:**

```
输入: gas = [2,3,4], cost = [3,4,3]
输出: -1
解释:
你不能从 0 号或 1 号加油站出发，因为没有足够的汽油可以让你行驶到下一个加油站。
我们从 2 号加油站出发，可以获得 4 升汽油。 此时油箱有 = 0 + 4 = 4 升汽油
开往 0 号加油站，此时油箱有 4 - 3 + 2 = 3 升汽油
开往 1 号加油站，此时油箱有 3 - 3 + 3 = 3 升汽油
你无法返回 2 号加油站，因为返程需要消耗 4 升汽油，但是你的油箱只有 3 升汽油。
因此，无论怎样，你都不可能绕环路行驶一周。
```

**代码示例：**

```c
// 总油量大于总消耗就一定能跑完
// 即总剩余油量>=0就一定能跑完
int canCompleteCircuit(int* gas, int gasSize, int* cost, int costSize) {
    // 从0开始到了某一点剩的油变成了负数，那么整个区间都不能是起点
    int start = 0;
    int rest = 0;  // 当前节点剩余油量
    int total = 0; // 总剩余油量
    for (int i = 0; i < gasSize; i++) {
        rest += gas[i] - cost[i];
        total += gas[i] - cost[i];
        if (rest <= 0) { // 说明要么跑不完，就算能跑完起点也不在0~i
            start = (i + 1) % gasSize; // 假设起点在下一个
            rest = 0;
        }
    }
    // 如果跑的完那么起点就是start
    if (total < 0)
        return -1;
    return start;
}
```

## 五、动态规划 ##

### 343.整数拆分（middle） ###

给定一个正整数 `n` ，将其拆分为 `k` 个 **正整数** 的和（ `k >= 2` ），并使这些整数的乘积最大化。

返回 *你可以获得的最大乘积* 。

**示例 1:**

```
输入: n = 2
输出: 1
解释: 2 = 1 + 1, 1 × 1 = 1。
```

**示例 2:**

```
输入: n = 10
输出: 36
解释: 10 = 3 + 3 + 4, 3 × 3 × 4 = 36。
```

**代码示例：**

```c
int integerBreak(int n) {
    int dp[n + 1]; // dp[i]:将i拆分得到的最大乘积
    memset(dp, 0, sizeof(dp));
    dp[2] = 1; // dp[0]和dp[1]没法拆分
    for (int i = 3; i <= n; i++) {
        for (int j = 1; j < i; j++) {
            dp[i] = fmax(dp[i], fmax(j * (i - j), j * dp[i - j]));
        }
    }
    return dp[n];
}
```

### 96.不同的二叉搜索树（middle） ###

给你一个整数 `n` ，求恰由 `n` 个节点组成且节点值从 `1` 到 `n` 互不相同的 **二叉搜索树** 有多少种？返回满足题意的二叉搜索树的种数。

**示例 1：**

![img](D:/typoraphotos/uniquebstn3.jpg)

```
输入：n = 3
输出：5
```

**示例 2：**

```
输入：n = 1
输出：1
```

**代码示例：**

```c
int numTrees(int n) {
    int dp[n + 1]; // dp[i]:i个不同元素组成的BST的数量
    memset(dp, 0, sizeof(dp));
    dp[0] = 1; // 空节点也是一棵BST
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= i; j++) {
            // 以j为头结点，j-1为其左子树，i-j为其右子树
            // 1~j轮流成为头结点，计算其左子树个数*右子树个数
            dp[i] += dp[j - 1] * dp[i - j];
        }
    }
    return dp[n];
}
```

### 背包问题 ###

**0/1背包：**先遍历物品，再遍历背包，物品正序，背包要倒序(num[i]~target)

**完全背包：**在0/1背包基础上，物品有无限个，所以背包遍历时用正序即可

**组合问题：**先遍历物品，再遍历背包

**排列问题：**先遍历背包，再遍历物品（一般在递推式之前需要有条件判断）

****

### 416.分割等和子集（middle） ###

一个 **只包含正整数** 的 **非空** 数组 `nums` 。请你判断是否可以将这个数组分割成两个子集，使得两个子集的元素和相等。

**示例 1：**

```
输入：nums = [1,5,11,5]
输出：true
解释：数组可以分割成 [1, 5, 5] 和 [11] 。
```

**示例 2：**

```
输入：nums = [1,2,3,5]
输出：false
解释：数组不能分割成两个元素和相等的子集。
```

**代码示例：**

```c
bool canPartition(int* nums, int numsSize) {
    int sum = 0;
    for (int i = 0; i < numsSize; i++) {
        sum += nums[i];
    }
    if (sum % 2 == 1)
        return 0;
    int target = sum / 2;
    // 转换为背包问题
    // 一个背包能否价值达到sum/2
    int dp[20001]; // dp[j]:容量为j的背包的最大价值
    memset(dp, 0, sizeof(dp));
    dp[0] = 0;
    // 先遍历物品，再遍历背包，顺序不可改变！
    for (int i = 0; i < numsSize; i++) {
        // 0/1背包要倒序遍历！
        // j是背包容量，j<nums[i]时这个物品一定放不下去
        for (int j = target; j >= nums[i]; j--) {
            dp[j] = fmax(dp[j], dp[j - nums[i]] + nums[i]);
        }
    }
    if (dp[target] == target)
        return 1;
    return 0;
}
```

### 1049.最后一块石头的重量 II（middle） ###

有一堆石头，用整数数组 `stones` 表示。其中 `stones[i]` 表示第 `i` 块石头的重量。

每一回合，从中选出**任意两块石头**，然后将它们一起粉碎。假设石头的重量分别为 `x` 和 `y`，且 `x <= y`。那么粉碎的可能结果如下：

- 如果 `x == y`，那么两块石头都会被完全粉碎；
- 如果 `x != y`，那么重量为 `x` 的石头将会完全粉碎，而重量为 `y` 的石头新重量为 `y-x`。

最后，**最多只会剩下一块** 石头。返回此石头 **最小的可能重量** 。如果没有石头剩下，就返回 `0`。

**示例 1：**

```
输入：stones = [2,7,4,1,8,1]
输出：1
解释：
组合 2 和 4，得到 2，所以数组转化为 [2,7,1,8,1]，
组合 7 和 8，得到 1，所以数组转化为 [2,1,1,1]，
组合 2 和 1，得到 1，所以数组转化为 [1,1,1]，
组合 1 和 1，得到 0，所以数组转化为 [1]，这就是最优值。
```

**示例 2：**

```
输入：stones = [31,26,33,21,40]
输出：5
```

**代码示例：**

```c
// 把石头分成两堆，两边重量差值最小
// 转化为背包问题
#define max(a, b) ((a > b) ? a : b)
int lastStoneWeightII(int* stones, int stonesSize) {
    int sum = 0;
    for (int i = 0; i < stonesSize; i++) {
        sum += stones[i];
    }
    int target = sum / 2;
    int dp[30001]; // dp[j]:容量为j的背包的最大价值(重量)
    memset(dp, 0, sizeof(dp));
    dp[0] = 0;
    for (int i = 0; i < stonesSize; i++) {          // 遍历物品
        for (int j = target; j >= stones[i]; j--) { // 遍历背包容量
            dp[j] = max(dp[j], dp[j - stones[i]] + stones[i]);
        }
    }
    return sum - dp[target] - dp[target];
}
```

### 494.目标和（middle） ###

给你一个非负整数数组 `nums` 和一个整数 `target` 。

向数组中的每个整数前添加 `'+'` 或 `'-'` ，然后串联起所有整数，可以构造一个 **表达式** ：

- 例如，`nums = [2, 1]` ，可以在 `2` 之前添加 `'+'` ，在 `1` 之前添加 `'-'` ，然后串联起来得到表达式 `"+2-1"` 。

返回可以通过上述方法构造的、运算结果等于 `target` 的不同 **表达式** 的数目。

**示例 1：**

```
输入：nums = [1,1,1,1,1], target = 3
输出：5
解释：一共有 5 种方法让最终目标和为 3 。
-1 + 1 + 1 + 1 + 1 = 3
+1 - 1 + 1 + 1 + 1 = 3
+1 + 1 - 1 + 1 + 1 = 3
+1 + 1 + 1 - 1 + 1 = 3
+1 + 1 + 1 + 1 - 1 = 3
```

**示例 2：**

```
输入：nums = [1], target = 1
输出：1
```

**代码示例：**

```c
// 动态规划解决组合问题
// nums分为两组，总和为a和b
// a-b=target,a+b=sum，则a=(target+sum)/2，转换为背包问题
// a和b必须是整数，所以target+sum必须是偶数
int findTargetSumWays(int* nums, int numsSize, int target) {
    int sum = 0;
    for (int i = 0; i < numsSize; i++) {
        sum += nums[i];
    }
    if ((target + sum) % 2 == 1 || abs(target) > sum)
        return 0;
    int a = (target + sum) / 2;
    int dp[a + 1]; // dp[j]:背包内数字的总和达到j,有dp[j]中方法
    memset(dp, 0, sizeof(dp));
    dp[0] = 1;
    for (int i = 0; i < numsSize; i++) {
        for (int j = a; j >= nums[i]; j--) {
            dp[j] += dp[j - nums[i]];
        }
    }
    return dp[a];
}
```

### 474.一和零（middle） ###

给你一个二进制字符串数组 `strs` 和两个整数 `m` 和 `n` 。

请你找出并返回 `strs` 的最大子集的长度，该子集中 **最多** 有 `m` 个 `0` 和 `n` 个 `1` 。

如果 `x` 的所有元素也是 `y` 的元素，集合 `x` 是集合 `y` 的 **子集** 。

**示例 1：**

```
输入：strs = ["10", "0001", "111001", "1", "0"], m = 5, n = 3
输出：4
解释：最多有 5 个 0 和 3 个 1 的最大子集是 {"10","0001","1","0"} ，因此答案是 4 。
其他满足题意但较小的子集包括 {"0001","1"} 和 {"10","1","0"} 。{"111001"} 不满足题意，因为它含 4 个 1 ，大于 n 的值 3 。
```

**示例 2：**

```
输入：strs = ["10", "0", "1"], m = 1, n = 1
输出：2
解释：最大的子集是 {"0", "1"} ，所以答案是 2 。
```

**代码示例：**

```c
// 二维背包，一个背包装0，一个背包装1
int findMaxForm(char** strs, int strsSize, int m, int n) {
    int dp[m + 1][n + 1]; // dp[i][j]:装i个0和j个1的背包的最大容量(子集长度)
    memset(dp, 0, sizeof(dp));
    for (int x = 0; x < strsSize; x++) { // 遍历物品(每个子串)
        int zeroNum = 0, oneNum = 0;
        // 记录该子串0和1的个数
        for (int t = 0; strs[x][t] != '\0'; t++) {
            if (strs[x][t] == '0')
                zeroNum++;
            else
                oneNum++;
        }
        // 遍历两个背包，倒序！！
        for (int i = m; i >= zeroNum; i--) {
            for (int j = n; j >= oneNum; j--) {
                dp[i][j] = fmax(dp[i][j], dp[i - zeroNum][j - oneNum] + 1);
            }
        }
    }
    return dp[m][n];
}
```

### 518.零钱兑换 II（middle） ###

给你一个整数数组 `coins` 表示不同面额的硬币，另给一个整数 `amount` 表示总金额。

请你计算并返回可以凑成总金额的硬币组合数。如果任何硬币组合都无法凑出总金额，返回 `0` 。

假设每一种面额的硬币有无限个。 

题目数据保证结果符合 32 位带符号整数。

**示例 1：**

```
输入：amount = 5, coins = [1, 2, 5]
输出：4
解释：有四种方式可以凑成总金额：
5=5
5=2+2+1
5=2+1+1+1
5=1+1+1+1+1
```

**示例 2：**

```
输入：amount = 3, coins = [2]
输出：0
解释：只用面额 2 的硬币不能凑成总金额 3 。
```

**示例 3：**

```
输入：amount = 10, coins = [10] 
输出：1
```

**代码示例：**

```c
// 完全背包问题，即物品有无限个
// 在遍历背包时，0/1背包用倒序，完全背包用顺序
int change(int amount, int* coins, int coinsSize) {
    int dp[amount + 1]; // dp[j]:价值为j的背包,有dp[j]种组合
    memset(dp, 0, sizeof(dp));
    dp[0] = 1;
    for (int i = 0; i < coinsSize; i++) {          // 遍历物品
        for (int j = coins[i]; j <= amount; j++) { // 遍历背包,顺序！！
            dp[j] += dp[j - coins[i]];
        }
    }
    return dp[amount];
}
```

### 377.组合总和 Ⅳ（middle） ###

给你一个由 **不同** 整数组成的数组 `nums` ，和一个目标整数 `target` 。请你从 `nums` 中找出并返回总和为 `target` 的元素组合的个数。

题目数据保证答案符合 32 位整数范围。

**示例 1：**

```
输入：nums = [1,2,3], target = 4
输出：7
解释：
所有可能的组合为：
(1, 1, 1, 1)
(1, 1, 2)
(1, 2, 1)
(1, 3)
(2, 1, 1)
(2, 2)
(3, 1)
请注意，顺序不同的序列被视作不同的组合。
```

**示例 2：**

```
输入：nums = [9], target = 3
输出：0
```

**代码示例：**

```c
// 求组合先遍历物品，求排列先遍历背包！！
int combinationSum4(int* nums, int numsSize, int target) {
    int dp[target + 1]; // dp[j]:总和为j的背包，有dp[j]种排列！！
    memset(dp, 0, sizeof(dp));
    dp[0] = 1; // 无意义，但是如果等于0后面的数都是0
    for (int j = 0; j <= target; j++) {      // 遍历背包
        for (int i = 0; i < numsSize; i++) { // 遍历物品
            if (j - nums[i] >= 0 && dp[j] < INT_MAX - dp[j - nums[i]])
                dp[j] += dp[j - nums[i]];
        }
    }
    return dp[target];
}
```

### 322.零钱兑换（middle） ###

给你一个整数数组 `coins` ，表示不同面额的硬币；以及一个整数 `amount` ，表示总金额。

计算并返回可以凑成总金额所需的 **最少的硬币个数** 。如果没有任何一种硬币组合能组成总金额，返回 `-1` 。

你可以认为每种硬币的数量是无限的。

**示例 1：**

```
输入：coins = [1, 2, 5], amount = 11
输出：3 
解释：11 = 5 + 5 + 1
```

**示例 2：**

```
输入：coins = [2], amount = 3
输出：-1
```

**示例 3：**

```
输入：coins = [1], amount = 0
输出：0
```

**代码示例：**

```c
// 完全背包：意味着内循环正序
// 无所谓排列还是组合，所以背包和物品内外顺序无所谓
int coinChange(int* coins, int coinsSize, int amount) {
    int dp[amount + 1]; // dp[j]：凑成价值为j的背包，最少要dp[j]个硬币
    // 这里可以使用memset(dp,0x3f,sizeof(dp))
    // 这是将dp[i]的每个字节设置为3f，最终为0x3f3f3f3f
    // 这只是表示一个很大的数，而不是真正的
    // 因为memset是对每个字节赋值，所以做不到将每个数直接赋值为INT_MAX
    for (int i = 1; i <= amount; i++) {
        dp[i] = INT_MAX;
    }
    dp[0] = 0;
    for (int i = 0; i < coinsSize; i++) {          // 遍历物品
        for (int j = coins[i]; j <= amount; j++) { // 遍历背包
            // j-cooins[i]可能还没更新，此时要跳过
            if (dp[j - coins[i]] != INT_MAX)
                dp[j] = fmin(dp[j], dp[j - coins[i]] + 1);
        }
    }
    if (dp[amount] == INT_MAX)
        return -1;
    return dp[amount];
}
```

### 139.单词拆分（middle） ###

给你一个字符串 `s` 和一个字符串列表 `wordDict` 作为字典。如果可以利用字典中出现的一个或多个单词拼接出 `s` 则返回 `true`。

**注意：**不要求字典中出现的单词全部都使用，并且字典中的单词可以重复使用。

**示例 1：**

```
输入: s = "leetcode", wordDict = ["leet", "code"]
输出: true
解释: 返回 true 因为 "leetcode" 可以由 "leet" 和 "code" 拼接成。
```

**示例 2：**

```
输入: s = "applepenapple", wordDict = ["apple", "pen"]
输出: true
解释: 返回 true 因为 "applepenapple" 可以由 "apple" "pen" "apple" 拼接成。
     注意，你可以重复使用字典中的单词。
```

**示例 3：**

```
输入: s = "catsandog", wordDict = ["cats", "dog", "sand", "and", "cat"]
输出: false
```

**代码示例：**

```c
// 完全背包+排列问题
bool wordBreak(char* s, char** wordDict, int wordDictSize) {
    int len = strlen(s);
    bool dp[len + 1]; // dp[j]:长度为j的字符串，是否能拆分为字典中的单词
    memset(dp, 0, sizeof(dp));
    dp[0] = 1; // 无实际意义，递推公式需要
    // dp[j]只有在dp[i]=1(这一段表示0~i-1)
    // 且i~j-1这段能在字典中找到的时候才为1
    for (int j = 1; j <= len; j++) {  // 遍历背包
        for (int i = 0; i < j; i++) { // 遍历物品 0~i-1 i~j-1
            // 把i~j-1截取下来
            char word[j - i + 1];
            int l = 0;
            for (int k = i; k < j; k++, l++) {
                word[l] = s[k];
            }
            word[l] = '\0';
            // 查找word是不是在字典里面
            bool flag = 0;
            for (int i = 0; i < wordDictSize; i++) {
                if (strcmp(wordDict[i], word) == 0) {
                    flag = 1;
                    break;
                }
            }
            if (dp[i] && flag) {
                dp[j] = 1;
            }
        }
    }
    return dp[len];
}
```

### 198.打家劫舍（middle） ###

你是一个专业的小偷，计划偷窃沿街的房屋。每间房内都藏有一定的现金，影响你偷窃的唯一制约因素就是相邻的房屋装有相互连通的防盗系统，**如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警**。

给定一个代表每个房屋存放金额的非负整数数组，计算你 **不触动警报装置的情况下** ，一夜之内能够偷窃到的最高金额。

**示例 1：**

```
输入：[1,2,3,1]
输出：4
解释：偷窃 1 号房屋 (金额 = 1) ，然后偷窃 3 号房屋 (金额 = 3)。
     偷窃到的最高金额 = 1 + 3 = 4 。
```

**示例 2：**

```
输入：[2,7,9,3,1]
输出：12
解释：偷窃 1 号房屋 (金额 = 2), 偷窃 3 号房屋 (金额 = 9)，接着偷窃 5 号房屋 (金额 = 1)。
     偷窃到的最高金额 = 2 + 9 + 1 = 12 。
```

**注意两种代码思路的区别在于dp[j]的定义！！！！这导致了代码递推逻辑的改变**

**代码示例1：**

```c
int rob(int* nums, int numsSize) {
    int dp[numsSize]; // dp[i]:偷窃到j号房间时(不是一定要偷i)，最大能拿dp[i]
    memset(dp, 0, sizeof(dp));
    dp[0] = nums[0];
    if (numsSize == 1)
        return dp[0];
    dp[1] = fmax(nums[0], nums[1]); // 0号和1号能偷多少取决于谁更大
    for (int i = 2; i < numsSize; i++) { // 从2号房间开偷
        // 当偷到j号房间的时候，可以考虑偷或者不偷
        // 偷的话上一个偷的就是j-2
        // 不偷的话dp[j]=dp[j-1]，这里不代表一定偷了dp[j-1]
        // 可能在dp[j-1]那层逻辑里，偷的其实是j-2
        dp[i] = fmax(dp[i - 2] + nums[i], dp[i - 1]);
    }
    return dp[numsSize - 1];
}
```

**代码示例2：**

```c
int rob(int* nums, int numsSize) {
    int max;
    int dp[numsSize]; // dp[i]:偷到i号房间时(一定要偷dp[i])，最大能拿dp[i]
    memset(dp, 0, sizeof(dp));
    dp[0] = nums[0];
    if (numsSize == 1)
        return dp[0];
    dp[1] = nums[1]; // 0号和1号能偷多少很固定
    max = fmax(dp[0], dp[1]);
    for (int i = 2; i < numsSize; i++) { // 从2号房间开偷
        // 当偷到i号房间的时候，上一个偷的可以是i-2
        // 也可以是i-3，但不可能是i-4或者更往前了
        // 因为如果能偷i-4和i，就一定能偷i-2
        if (i - 3 >= 0)
            dp[i] = fmax(dp[i - 3] + nums[i], dp[i - 2] + nums[i]);
        else
            dp[i] = dp[i - 2] + nums[i];
        max = fmax(max, dp[i]);
    }
    return max;
}
```

### 213.打家劫舍 II（middle） ###

你是一个专业的小偷，计划偷窃沿街的房屋，每间房内都藏有一定的现金。这个地方所有的房屋都 **围成一圈** ，这意味着第一个房屋和最后一个房屋是紧挨着的。同时，相邻的房屋装有相互连通的防盗系统，**如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警** 。

给定一个代表每个房屋存放金额的非负整数数组，计算你 **在不触动警报装置的情况下** ，今晚能够偷窃到的最高金额。

**示例 1：**

```
输入：nums = [2,3,2]
输出：3
解释：你不能先偷窃 1 号房屋（金额 = 2），然后偷窃 3 号房屋（金额 = 2）, 因为他们是相邻的。
```

**示例 2：**

```
输入：nums = [1,2,3,1]
输出：4
解释：你可以先偷窃 1 号房屋（金额 = 1），然后偷窃 3 号房屋（金额 = 3）。
     偷窃到的最高金额 = 1 + 3 = 4 。
```

**示例 3：**

```
输入：nums = [1,2,3]
输出：3
```

**代码示例：**

```c
// 考虑：如果抢了第一家(0)就不能抢最后一家(n-1)，反之亦然
// 那么：分两次抢，0~n-2和1~n-1
// 则有：这两次分别抢相当于普通的打家劫舍
// 这是普通版的打家劫舍逻辑
int robCommon(int* nums, int start, int end) {
    int len = end - start + 1;
    if (len == 1)
        return nums[start];
    int dp[end - start +
           2]; // dp[i]:偷到第i家(第i家不一定偷!)的时候最多能偷dp[i]
    memset(dp, 0, sizeof(dp));
    dp[start] = nums[start];
    dp[start + 1] = fmax(nums[start], nums[start + 1]);
    for (int i = start + 2; i <= end; i++) { // 从start+2家开始打劫
        dp[i] = fmax(dp[i - 1], dp[i - 2] + nums[i]);
    }
    return dp[end];
}
int rob(int* nums, int numsSize) {
    if (numsSize == 1)
        return nums[0];
    return fmax(robCommon(nums, 0, numsSize - 2),
                robCommon(nums, 1, numsSize - 1));
}
```

### 718.最长重复子数组（middle） ###

给两个整数数组 `nums1` 和 `nums2` ，返回 *两个数组中 **公共的** 、长度最长的子数组的长度* 。

**示例 1：**

```
输入：nums1 = [1,2,3,2,1], nums2 = [3,2,1,4,7]
输出：3
解释：长度最长的公共子数组是 [3,2,1] 。
```

**示例 2：**

```
输入：nums1 = [0,0,0,0,0], nums2 = [0,0,0,0,0]
输出：5
```

**代码示例：**

```c
int findLength(int* nums1, int nums1Size, int* nums2, int nums2Size) {
    int max = 0;
    int dp[nums1Size][nums2Size]; // dp[i][j]:num1[i]和nums2[j]结尾的子串长度
    for (int i = 0; i < nums1Size; i++) {
        if (nums1[i] == nums2[0]) {
            dp[i][0] = 1;
            max = 1; // 下面的双层for循环是从1开始的，这里必须更新max的值
        } else
            dp[i][0] = 0;
    }
    for (int j = 0; j < nums2Size; j++) {
        if (nums1[0] == nums2[j]) {
            dp[0][j] = 1;
            max = 1; // 如果下面的双层for从0开始，那么这里就不用更新max
        } else
            dp[0][j] = 0;
    }
    for (int i = 1; i < nums1Size; i++) {
        for (int j = 1; j < nums2Size; j++) {
            if (nums1[i] == nums2[j])
                dp[i][j] = dp[i - 1][j - 1] + 1;
            else
                dp[i][j] = 0;
            max = max > dp[i][j] ? max : dp[i][j];
        }
    }
    return max;
}
```

### 1143.最长公共子序列（middle） ###

给定两个字符串 `text1` 和 `text2`，返回这两个字符串的最长 **公共子序列** 的长度。如果不存在 **公共子序列** ，返回 `0` 。

一个字符串的 **子序列** 是指这样一个新的字符串：它是由原字符串在不改变字符的相对顺序的情况下删除某些字符（也可以不删除任何字符）后组成的新字符串。

- 例如，`"ace"` 是 `"abcde"` 的子序列，但 `"aec"` 不是 `"abcde"` 的子序列。

两个字符串的 **公共子序列** 是这两个字符串所共同拥有的子序列。

**示例 1：**

```
输入：text1 = "abcde", text2 = "ace" 
输出：3  
解释：最长公共子序列是 "ace" ，它的长度为 3 。
```

**示例 2：**

```
输入：text1 = "abc", text2 = "abc"
输出：3
解释：最长公共子序列是 "abc" ，它的长度为 3 。
```

**示例 3：**

```
输入：text1 = "abc", text2 = "def"
输出：0
解释：两个字符串没有公共子序列，返回 0 。
```

**代码示例：**

```c
int longestCommonSubsequence(char* text1, char* text2) {
    int len1 = strlen(text1);
    int len2 = strlen(text2);
    int dp[len1 + 1][len2 + 1];
    // dp[i][j]:text1[0, i - 1]与text2[0, j - 1]的最长公共子序列
    int max = 0;
    memset(dp, 0, sizeof(dp));
    for (int i = 1; i < len1 + 1; i++) {
        for (int j = 1; j < len2 + 1; j++) {
            if (text1[i - 1] == text2[j - 1])
                dp[i][j] = dp[i - 1][j - 1] + 1;
            else // 注意这里递推方程的理解
                dp[i][j] = fmax(dp[i - 1][j], dp[i][j - 1]);
        }
    }
    return dp[len1][len2];
}
```





## 六、哈希表 ##

### 454.四数相加 II（middle） ###

给你四个整数数组 `nums1`、`nums2`、`nums3` 和 `nums4` ，数组长度都是 `n` ，请你计算有多少个元组 `(i, j, k, l)` 能满足：

- `0 <= i, j, k, l < n`
- `nums1[i] + nums2[j] + nums3[k] + nums4[l] == 0`

**示例 1：**

```
输入：nums1 = [1,2], nums2 = [-2,-1], nums3 = [-1,2], nums4 = [0,2]
输出：2
解释：
两个元组如下：
1. (0, 0, 0, 1) -> nums1[0] + nums2[0] + nums3[0] + nums4[1] = 1 + (-2) + (-1) + 2 = 0
2. (1, 1, 0, 0) -> nums1[1] + nums2[1] + nums3[0] + nums4[0] = 2 + (-1) + (-1) + 0 = 0
```

**示例 2：**

```
输入：nums1 = [0], nums2 = [0], nums3 = [0], nums4 = [0]
输出：1
```

**代码示例：**

```c
struct hashTable {
    int key;
    int value; // 记录key出现的次数
    UT_hash_handle hh;
};
int fourSumCount(int* nums1, int nums1Size, int* nums2, int nums2Size,int* nums3, int nums3Size, int* nums4, int nums4Size) {
    struct hashTable* hash = NULL;
    for (int i = 0; i < nums1Size; i++) {
        for (int j = 0; j < nums2Size; j++) {
            int sum = nums1[i] + nums2[j];
            struct hashTable* temp;
            HASH_FIND_INT(hash, &sum, temp);
            if (temp == NULL) {
                temp = malloc(sizeof(struct hashTable));
                temp->key = sum, temp->value = 1;
                HASH_ADD_INT(hash, key, temp);
            } else {
                temp->value++;
            }
        }
    }
    int res = 0;
    for (int i = 0; i < nums3Size; i++) {
        for (int j = 0; j < nums4Size; j++) {
            int sum = -nums3[i] - nums4[j];
            struct hashTable* temp;
            HASH_FIND_INT(hash, &sum, temp);
            if (temp != NULL) {
                res += temp->value;
            }
        }
    }
    return res;
}
```

### 791.自定义字符串排序（middle） ###

给定两个字符串 `order` 和 `s` 。`order` 的所有字母都是 **唯一** 的，并且以前按照一些自定义的顺序排序。

对 `s` 的字符进行置换，使其与排序的 `order` 相匹配。更具体地说，如果在 `order` 中的字符 `x` 出现字符 `y` 之前，那么在排列后的字符串中， `x` 也应该出现在 `y` 之前。

返回 *满足这个性质的 `s` 的任意一种排列* 。

**示例 1:**

```
输入: order = "cba", s = "abcd"
输出: "cbad"
解释: 
"a"、"b"、"c"是按顺序出现的，所以"a"、"b"、"c"的顺序应该是"c"、"b"、"a"。
因为"d"不是按顺序出现的，所以它可以在返回的字符串中的任何位置。"dcba"、"cdba"、"cbda"也是有效的输出。
```

**示例 2:**

```
输入: order = "cbafg", s = "abcd"
输出: "cbad"
解释：字符 "b"、"c" 和 "a" 规定了 s 中字符的顺序。s 中的字符 "d" 没有在 order 中出现，所以它的位置是弹性的。

按照出现的顺序，s 中的 "b"、"c"、"a" 应排列为"b"、"c"、"a"。"d" 可以放在任何位置，因为它没有按顺序排列。输出 "bcad" 遵循这一规则。其他排序如 "bacd" 或 "bcda" 也是有效的，只要维持 "b"、"c"、"a" 的顺序。
```

**代码示例：**

```c
// 哈希表加冒泡排序
char* customSortString(char* order, char* s) {
    int len1 = strlen(order);
    int len2 = strlen(s);
    int hash[27];
    for (int i = 0; i < 26; i++) {
        hash[i] = 27;
    }
    for (int i = 0; i < len1; i++) {
        hash[order[i] - 'a'] = i;
    }
    for (int i = 0; i < len2; i++) {
        bool ifsorted = 0;
        for (int j = 1; j < len2; j++) {
            if (hash[s[j] - 'a'] < hash[s[j - 1] - 'a']) {
                ifsorted = 1;
                char tmp = s[j];
                s[j] = s[j - 1];
                s[j - 1] = tmp;
            }
        }
        if (ifsorted == 0)
            break;
    }
    return s;
}
```



## 七、二叉树 ##

### 144.二叉树的前序遍历（easy） ###

给你二叉树的根节点 `root` ，返回它节点值的 **前序** 遍历。

**示例 1：**

![img](D:/typoraphotos/inorder_1.jpg)

```
输入：root = [1,null,2,3]
输出：[1,2,3]
```

**示例 2：**

```
输入：root = []
输出：[]
```

- 树中节点数目在范围 `[0, 100]` 内

**代码示例1：**

```c
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     struct TreeNode *left;
 *     struct TreeNode *right;
 * };
 */
// 递归实现
 void preorder(struct TreeNode* root,int* res,int* returnSize){
    if(root==NULL) return;
    res[(*returnSize)++]=root->val;
    preorder(root->left,res,returnSize);
    preorder(root->right,res,returnSize);
 }
int* preorderTraversal(struct TreeNode* root, int* returnSize) {
    int* res=(int*)malloc(sizeof(int)*101);
    *returnSize=0;
    preorder(root,res,returnSize);
    return res;
}
```

**代码实现2：**

```c
// 用栈实现
// 入栈顺序：先压入右节点再压入左节点
int* preorderTraversal(struct TreeNode* root, int* returnSize) {
    int* res = (int*)malloc(sizeof(int) * 101);
    *returnSize = 0;
    if (root == NULL)
        return res;
    struct TreeNode* stk[101]; // 栈
    int stkTop = 0;            // 栈顶指针
    stk[stkTop++] = root;
    while (stkTop != 0) {
        struct TreeNode* node = stk[--stkTop]; // 出栈
        if (node->right != NULL)
            stk[stkTop++] = node->right;
        if (node->left != NULL)
            stk[stkTop++] = node->left;
        res[(*returnSize)++] = node->val;
    }
    return res;
}
```

### 94.二叉树的中序遍历（easy） ###

```c
// 用栈实现中序遍历
int* inorderTraversal(struct TreeNode* root, int* returnSize) {
    int* res = (int*)malloc(sizeof(int) * 101);
    *returnSize = 0;
    if (root == NULL)
        return res;
    struct TreeNode* stk[101];
    int stkTop = 0;
    // 从根节点开始一直往左走，所有遇到的结点入栈
    while (root != NULL) {
        stk[stkTop++] = root;
        root = root->left;
    }
    while (stkTop != 0) {
        struct TreeNode* node = stk[--stkTop];
        res[(*returnSize)++] = node->val;
        // 当前结点被访问，说明它一定没有左孩子了
        // 如果它还有右孩子，将它的右孩子当作新的树根
        if (node->right != NULL) {
            node = node->right;
            while (node != NULL) {
                stk[stkTop++] = node;
                node = node->left;
            }
        }
    }
    return res;
}
```

### 797.所有可能的路径（middle—DFS） ###

给你一个有 `n` 个节点的 **有向无环图（DAG）**，请你找出所有从节点 `0` 到节点 `n-1` 的路径并输出（**不要求按特定顺序**）

 `graph[i]` 是一个从节点 `i` 可以访问的所有节点的列表（即从节点 `i` 到节点 `graph[i][j]`存在一条有向边）。

 

**示例 1：**

![img](D:/typoraphotos/all_1.jpg)

```
输入：graph = [[1,2],[3],[3],[]]
输出：[[0,1,3],[0,2,3]]
解释：有两条路径 0 -> 1 -> 3 和 0 -> 2 -> 3
```

**示例 2：**

![img](D:/typoraphotos/all_2.jpg)

```
输入：graph = [[4,3,1],[3,2,4],[3],[4],[]]
输出：[[0,4],[0,3,4],[0,1,3,4],[0,1,2,3,4],[0,1,4]]
```

**代码示例：**

```c
// 全局变量
int** res;
int path[15]; // 记录当前路径，实质上是一个stack
int stkTop;   // 栈顶指针
void dfs(int** graph, int graphSize, int* graphColSize, int node,
         int* returnSize, int** returnColumnSizes) {
    if (node == graphSize - 1) {
        // 不能直接写res[(*returnSize)]=path!!
        int* tmp = malloc(sizeof(int) * stkTop);
        memcpy(tmp, path, sizeof(int) * stkTop);
        res[(*returnSize)] = tmp;
        (*returnColumnSizes)[(*returnSize)++] = stkTop;
        return;
    }
    // node为当前结点
    // 遍历当前结点的邻接点
    for (int i = 0; i < graphColSize[node]; i++) {
        path[stkTop++] = graph[node][i];
        dfs(graph, graphSize, graphColSize, graph[node][i], returnSize,
            returnColumnSizes);
        stkTop--; // 回溯
    }
}
int** allPathsSourceTarget(int** graph, int graphSize, int* graphColSize,int* returnSize, int** returnColumnSizes) {
    res = malloc(sizeof(int*) * 100000);
    *returnSize = 0;
    *returnColumnSizes = malloc(sizeof(int) * 100000);
    stkTop = 0;
    path[stkTop++] = 0; // 永远从0开始
    dfs(graph, graphSize, graphColSize, 0, returnSize, returnColumnSizes);
    return res;
}
```

### 199.二叉树的右视图（middle） ###

给定一个二叉树的 **根节点** `root`，想象自己站在它的右侧，按照从顶部到底部的顺序，返回从右侧所能看到的节点值。

**示例 1:**

![img](D:/typoraphotos/tree.jpg)

```
输入: [1,2,3,null,5,null,4]
输出: [1,3,4]
```

**示例 2:**

```
输入: [1,null,3]
输出: [1,3]
```

**示例 3:**

```
输入: []
输出: []
```

**代码示例1：**

```c
// 先序或者后序遍历，只要先左再右就行，遍历的时候记录层数
// 每次遍历都存储该值，存储位置的下标就是深度
// 后出现的一定会覆盖先出现的，所以最后保留的就是最右边的
void preOrder(struct TreeNode* root, int* res, int* returnSize, int deep) {
    if (root == NULL)
        return;
    // 将结点值存放在deep-1处
    res[deep - 1] = root->val;
    (*returnSize) = fmax(deep, (*returnSize));
    preOrder(root->left, res, returnSize, deep + 1);
    preOrder(root->right, res, returnSize, deep + 1);
}
int* rightSideView(struct TreeNode* root, int* returnSize) {
    int* res = (int*)malloc(sizeof(int) * 101);
    *returnSize = 0;
    preOrder(root, res, returnSize, 1);
    return res;
}
```

**代码示例2：**

```c
// 层序遍历，返回每一层最右边的节点
int* rightSideView(struct TreeNode* root, int* returnSize) {
    int levelNUm[101] = {0}; // 记录每层的顶点个数
    int layer = 0;
    int* res = (int*)malloc(sizeof(int) * 101);
    *returnSize = 0;
    struct TreeNode* quque[101];
    int front = 0, rear = 0;
    if (root == NULL)
        return res;
    quque[rear++] = root;
    levelNUm[layer++]++;
    while (front != rear) {
        struct TreeNode* p = quque[front++];
        if (levelNUm[layer - 1] == 1) {
            res[(*returnSize)++] = p->val;
        }
        if (p->left != NULL) {
            quque[rear++] = p->left;
            levelNUm[layer]++;
        }
        if (p->right != NULL) {
            quque[rear++] = p->right;
            levelNUm[layer]++;
        }
        levelNUm[layer - 1]--;
        if (levelNUm[layer - 1] == 0)
            layer++;
    }
    return res;
}
```

## 八、图 ##

### 200.岛屿数量（middle） ###

给你一个由 `'1'`（陆地）和 `'0'`（水）组成的的二维网格，请你计算网格中岛屿的数量。

岛屿总是被水包围，并且每座岛屿只能由水平方向和/或竖直方向上相邻的陆地连接形成。

此外，你可以假设该网格的四条边均被水包围。

**示例 1：**

```
输入：grid = [
  ["1","1","1","1","0"],
  ["1","1","0","1","0"],
  ["1","1","0","0","0"],
  ["0","0","0","0","0"]
]
输出：1
```

**示例 2：**

```
输入：grid = [
  ["1","1","0","0","0"],
  ["1","1","0","0","0"],
  ["0","0","1","0","0"],
  ["0","0","0","1","1"]
]
输出：3
```

**代码示例1：**

```c
// dfs：遇到没访问过且为"1"的结点就count++，然后用dfs沉没该岛屿
// 访问一个节点原本可以用visited[][]去标识
// 但这里直接使得原来的岛屿沉没变成海洋达到了这一效果
void dfs(char** grid, int gridSize, int* gridColSize, int x, int y) {
    int move[4][2] = {0, 1, 1, 0, 0, -1, -1, 0}; // 移动方向：上右下左
    // 当前坐标(x,y),通过for循环去找下一个坐标(nextx,nexty)
    int nextx, nexty;
    for (int i = 0; i < 4; i++) {
        nextx = x + move[i][0];
        nexty = y + move[i][1];
        if (nextx < 0 || nextx >= gridSize || nexty < 0 || nexty >= gridColSize[0])
            continue; // 越界了，直接跳过
        if (grid[nextx][nexty] == '1') {
            grid[nextx][nexty] = '0'; // 沉没
            dfs(grid, gridSize, gridColSize, nextx, nexty);
        }
    }
}
int numIslands(char** grid, int gridSize, int* gridColSize) {
    int count = 0;
    for (int i = 0; i < gridSize; i++) {
        for (int j = 0; j < gridColSize[0]; j++) {
            if (grid[i][j] == '1') {
                grid[i][j] == '0'; // 沉没
                count++;
                dfs(grid, gridSize, gridColSize, i, j);
            }
        }
    }
    return count;
}
```

**代码示例2：**

```c
// bfs:一旦入队立马沉没，而不是等到出队才沉没
// 后者会导致某些结点重复入队，提高时间复杂度
typedef struct Coordinate {
    int x;
    int y;
} Coordinate; // 表示坐标的结构体
int numIslands(char** grid, int gridSize, int* gridColSize) {
    int count = 0;
    Coordinate queue[gridSize * gridColSize[0] + 1];
    int front = 0, rear = 0;
    for (int i = 0; i < gridSize; i++) {
        for (int j = 0; j < gridColSize[0]; j++) {
            if (grid[i][j] == '1') {
                Coordinate n = {i, j};
                queue[rear++] = n;
                grid[i][j] = '0';
                count++;
            }
            while (front != rear) {
                int move[4][2] = {0, 1, 1, 0, 0, -1, -1, 0};
                int x = queue[front].x, y = queue[front].y;
                front++; // 出队
                int nextx, nexty;
                // 遍历四个方向
                for (int i = 0; i < 4; i++) {
                    nextx = x + move[i][0];
                    nexty = y + move[i][1];
                    if (nextx < 0 || nexty < 0 || nextx >= gridSize || nexty >= gridColSize[0] || grid[nextx][nexty] == '0')
                        continue;
                    Coordinate tmp = {nextx, nexty};
                    queue[rear++] = tmp;
                    grid[nextx][nexty] = '0'; // 沉没
                }
            }
        }
    }
    return count;
}
```

### 994.腐烂的橘子（middle） ###

在给定的 `m x n` 网格 `grid` 中，每个单元格可以有以下三个值之一：

- 值 `0` 代表空单元格；
- 值 `1` 代表新鲜橘子；
- 值 `2` 代表腐烂的橘子。

每分钟，腐烂的橘子 **周围 4 个方向上相邻** 的新鲜橘子都会腐烂。

返回 *直到单元格中没有新鲜橘子为止所必须经过的最小分钟数。如果不可能，返回 `-1`* 。

**示例 1：**

**![img](D:/typoraphotos/oranges.png)**

```
输入：grid = [[2,1,1],[1,1,0],[0,1,1]]
输出：4
```

**示例 2：**

```
输入：grid = [[2,1,1],[0,1,1],[1,0,1]]
输出：-1
解释：左下角的橘子（第 2 行， 第 0 列）永远不会腐烂，因为腐烂只会发生在 4 个方向上。
```

**示例 3：**

```
输入：grid = [[0,2]]
输出：0
解释：因为 0 分钟时已经没有新鲜橘子了，所以答案就是 0 。
```

**代码示例：**

```c
// bfs
int orangesRotting(int** grid, int gridSize, int* gridColSize) {
    int time = 0;                         // 记录最终结果
    int minute[gridSize][(*gridColSize)]; // 记录每个格子上的橘子被污染的时间
    memset(minute, 0, sizeof(minute));
    int count = 0; // 记录好橘子的个数
    int* queue = malloc(sizeof(int) * 300);
    int front = 0, rear = 0;                     // 队列指针
    int move[4][2] = {0, 1, 1, 0, 0, -1, -1, 0}; // 上下左右移动
    for (int i = 0; i < gridSize; i++) {
        for (int j = 0; j < (*gridColSize); j++) {
            // 最开始的坏橘子全部入队
            if (grid[i][j] == 2) {
                queue[rear++] = i;
                queue[rear++] = j;
            } else if (grid[i][j] == 1) {
                count++;
            }
        }
    }
    while (front != rear) {
        int x, y, nextx, nexty; // 当前和下一步的坐标值
        // 出队
        x = queue[front++];
        y = queue[front++];
        // 遍历四个方向
        for (int i = 0; i < 4; i++) {
            nextx = x + move[i][0];
            nexty = y + move[i][1];
            // 越界处理
            if (nextx < 0 || nexty < 0 || nextx >= gridSize ||
                nexty >= (*gridColSize))
                continue;
            // 污染遇到的好橘子
            if (grid[nextx][nexty] == 1) {
                minute[nextx][nexty] = minute[x][y] + 1;
                // 记录时间，这里没必要比大小取最大值
                time = minute[nextx][nexty]; 
                grid[nextx][nexty] = 2;
                count--;
                if (count == 0)
                    return time;
                queue[rear++] = nextx;
                queue[rear++] = nexty;
            }
        }
    }
    return count > 0 ? -1 : time;
}
```

## 九、链表 ##

### 206.反转链表（easy） ###

给你单链表的头节点 `head` ，请你反转链表，并返回反转后的链表。

**示例 1：**

![img](D:/typoraphotos/rev1ex1.jpg)

```
输入：head = [1,2,3,4,5]
输出：[5,4,3,2,1]
```

**示例 2：**

![img](D:/typoraphotos/rev1ex2.jpg)

```
输入：head = [1,2]
输出：[2,1]
```

**示例 3：**

```
输入：head = []
输出：[]
```

**代码示例1：**

```c
// 双指针
struct ListNode* reverseList(struct ListNode* head) {
    if (head == NULL)
        return head;
    struct ListNode* p = NULL;
    struct ListNode* q = head;
    while (q != NULL) {
        struct ListNode* tmp = q->next;
        q->next = p;
        p = q;
        q = tmp;
    }
    return p;
}
```

**代码示例2：**

```c
// 设置虚拟头结点
struct ListNode* reverseList(struct ListNode* head) {
    struct ListNode* virtualHead =
        (struct ListNode*)malloc(sizeof(struct ListNode));
    virtualHead->next = NULL;
    while (head != NULL) {
        struct ListNode* p = virtualHead->next;
        struct ListNode* q = head->next;
        virtualHead->next = head;
        head->next = p;
        head = q;
    }
    head = virtualHead->next;
    free(virtualHead);
    return head;
}
```

### 24.两两交换链表中的节点（middle） ###

给你一个链表，两两交换其中相邻的节点，并返回交换后链表的头节点。你必须在不修改节点内部的值的情况下完成本题（即，只能进行节点交换）。

**示例 1：**

![img](D:/typoraphotos/swap_ex1.jpg)

```
输入：head = [1,2,3,4]
输出：[2,1,4,3]
```

**示例 2：**

```
输入：head = []
输出：[]
```

**示例 3：**

```
输入：head = [1]
输出：[1]
```

**代码示例：**

```c
struct ListNode* swapPairs(struct ListNode* head) {
    if (head == NULL || head->next == NULL)
        return head;
    struct ListNode* virtualHead =
        (struct ListNode*)malloc(sizeof(struct ListNode));
    virtualHead->next = head;
    struct ListNode* p = virtualHead;
    while (p->next != NULL && p->next->next != NULL) {
        struct ListNode* q = p->next;
        struct ListNode* t = p->next->next->next;
        // 理清楚这个逻辑
        p->next = q->next;
        q->next->next = q;
        q->next = t;
        p = p->next->next;
    }
    return virtualHead->next;
}
```

### 19.删除链表的倒数第 N 个结点（middle） ###

给你一个链表，删除链表的倒数第 `n` 个结点，并且返回链表的头结点。

**示例 1：**

![img](D:/typoraphotos/remove_ex1.jpg)

```
输入：head = [1,2,3,4,5], n = 2
输出：[1,2,3,5]
```

**示例 2：**

```
输入：head = [1], n = 1
输出：[]
```

**示例 3：**

```
输入：head = [1,2], n = 1
输出：[1]
```

 **代码示例：**

```c
// 双指针，关键在于先让p走几步
// 如果走n-1步，q会指向待删除，不成立
// 如果走n步，n=1时不成立
// 走n+1步并且让p一直走到null，成立
struct ListNode* removeNthFromEnd(struct ListNode* head, int n) {
    struct ListNode* virturalHead =
        (struct ListNode*)malloc(sizeof(struct ListNode));
    virturalHead->next = head;
    struct ListNode* p;
    struct ListNode* q;
    p = virturalHead;
    q = virturalHead;
    int x = n + 1;
    while (x-- && p != NULL) {
        p = p->next;
    }
    while (p != NULL) {
        p = p->next;
        q = q->next;
    } // 此时q的下一个就是待删除节点
    q->next = q->next->next;
    head = virturalHead->next;
    return head;
}
```

### 142.环形链表 II（middle） ###

给定一个链表的头节点  `head` ，返回链表开始入环的第一个节点。 *如果链表无环，则返回 `null`。*

如果链表中有某个节点，可以通过连续跟踪 `next` 指针再次到达，则链表中存在环。 为了表示给定链表中的环，评测系统内部使用整数 `pos` 来表示链表尾连接到链表中的位置（**索引从 0 开始**）。如果 `pos` 是 `-1`，则在该链表中没有环。**注意：`pos` 不作为参数进行传递**，仅仅是为了标识链表的实际情况。

**不允许修改** 链表。

**示例 1：**

![img](D:/typoraphotos/circularlinkedlist.png)

```
输入：head = [3,2,0,-4], pos = 1
输出：返回索引为 1 的链表节点
解释：链表中有一个环，其尾部连接到第二个节点。
```

**示例 2：**

![img](D:/typoraphotos/circularlinkedlist_test2.png)

```
输入：head = [1,2], pos = 0
输出：返回索引为 0 的链表节点
解释：链表中有一个环，其尾部连接到第一个节点。
```

**示例 3：**

![img](D:/typoraphotos/circularlinkedlist_test3.png)

```
输入：head = [1], pos = -1
输出：返回 null
解释：链表中没有环。
```

**代码示例：**

```c
// 快指针一次两步，慢指针一次一不
// 如果有环，二者一定会在环中相遇
// 如何确定环入口的位置需要一定的数学推导，见代码随想录
struct ListNode* detectCycle(struct ListNode* head) {
    struct ListNode* fast = head;
    struct ListNode* slow = head;
    while (fast != NULL && fast->next != NULL) {
        fast = fast->next->next;
        slow = slow->next;
        if (fast == slow) { // 有环
            struct ListNode* index1 = fast;
            struct ListNode* index2 = head;
            while (index1 != index2) {
                index1 = index1->next;
                index2 = index2->next;
            }
            return index1;
        }
    }
    return NULL;
}
```

## 十、回溯 ##

### 77.组合（middle） ###

给定两个整数 `n` 和 `k`，返回范围 `[1, n]` 中所有可能的 `k` 个数的组合。

你可以按 **任何顺序** 返回答案。

**示例 1：**

```
输入：n = 4, k = 2
输出：
[
  [2,4],
  [3,4],
  [2,3],
  [1,2],
  [1,3],
  [1,4],
]
```

**示例 2：**

```
输入：n = 1, k = 1
输出：[[1]]
```

**代码示例：**

```c
// 回溯：递归的副产品，用树形结构理解
int** res;
int* path;
int pathTop; // 为了达到回溯的效果，需要对path中进行pop()操作
void backtracking(int n, int k, int startIndex, int* returnSize, int** returnColumnSizes) {
    if (pathTop == k) { // 已经找到一个组合
        // 这里必须malloc新的空间tmp，不能直接用path
        int* tmp = (int*)malloc(sizeof(int) * k);
        for (int i = 0; i < k; i++) {
            tmp[i] = path[i];
        }
        res[(*returnSize)] = tmp;
        (*returnColumnSizes)[(*returnSize)++] = k;
        return;
    }
    // 这里for循环采用剪枝优化
    // 原本可以是i<=n，但要注意优化的值
    for (int i = startIndex; i <= n - (k - pathTop) + 1; i++) {
        path[pathTop++] = i;	
        backtracking(n, k, i + 1, returnSize, returnColumnSizes);
        pathTop--;
    }
}

int** combine(int n, int k, int* returnSize, int** returnColumnSizes) {
    res = (int**)malloc(sizeof(int*) * 1000000);
    path = (int*)malloc(sizeof(int) * k);
    *returnSize = 0;
    *returnColumnSizes = malloc(sizeof(int) * 1000000);
    pathTop = 0;
    if (k > n)
        return res;
    backtracking(n, k, 1, returnSize, returnColumnSizes);
    return res;
}
```

### 216.组合总和 III（middle） ###

找出所有相加之和为 `n` 的 `k` 个数的组合，且满足下列条件：

- 只使用数字1到9
- 每个数字 **最多使用一次** 

返回 *所有可能的有效组合的列表* 。该列表不能包含相同的组合两次，组合可以以任何顺序返回。

**示例 1:**

```
输入: k = 3, n = 7
输出: [[1,2,4]]
解释:
1 + 2 + 4 = 7
没有其他符合的组合了。
```

**示例 2:**

```
输入: k = 3, n = 9
输出: [[1,2,6], [1,3,5], [2,3,4]]
解释:
1 + 2 + 6 = 9
1 + 3 + 5 = 9
2 + 3 + 4 = 9
没有其他符合的组合了。
```

**示例 3:**

```
输入: k = 4, n = 1
输出: []
解释: 不存在有效的组合。
在[1,9]范围内使用4个不同的数字，我们可以得到的最小和是1+2+3+4 = 10，因为10 > 1，没有有效的组合。
```

**代码示例：**

```c
int** res;
int* path;
int pathTop;
void backtracking(int k, int n, int startIndex, int* returnSize,
                  int** returnColumnSizes) {
    int sum = 0;
    for (int i = 0; i < pathTop; i++) {
        sum += path[i];
    }
    if (sum > n) return;
    if (pathTop == k) {
        if (sum == n) {
            int* tmp = (int*)malloc(sizeof(int) * k);
            for (int i = 0; i < k; i++) {
                tmp[i] = path[i];
            }
            res[(*returnSize)] = tmp;
            (*returnColumnSizes)[(*returnSize)++] = k;
        }
        return;
    }
    for (int i = startIndex; i <= 9 - (k - pathTop) + 1; i++) {
        path[pathTop++] = i;
        backtracking(k, n, i + 1, returnSize, returnColumnSizes);
        pathTop--;
    }
}
int** combinationSum3(int k, int n, int* returnSize, int** returnColumnSizes) {
    res = (int**)malloc(sizeof(int*) * 10001);
    path = (int*)malloc(sizeof(int) * k);
    *returnSize = 0;
    *returnColumnSizes = malloc(sizeof(int) * 10001);
    pathTop = 0;
    backtracking(k, n, 1, returnSize, returnColumnSizes);
    return res;
}
```

## 39.组合总和（middle） ##

给你一个 **无重复元素** 的整数数组 `candidates` 和一个目标整数 `target` ，找出 `candidates` 中可以使数字和为目标数 `target` 的 所有 **不同组合** ，并以列表形式返回。你可以按 **任意顺序** 返回这些组合。

`candidates` 中的 **同一个** 数字可以 **无限制重复被选取** 。如果至少一个数字的被选数量不同，则两种组合是不同的。 

对于给定的输入，保证和为 `target` 的不同组合数少于 `150` 个。

**示例 1：**

```
输入：candidates = [2,3,6,7], target = 7
输出：[[2,2,3],[7]]
解释：
2 和 3 可以形成一组候选，2 + 2 + 3 = 7 。注意 2 可以使用多次。
7 也是一个候选， 7 = 7 。
仅有这两种组合。
```

**示例 2：**

```
输入: candidates = [2,3,5], target = 8
输出: [[2,2,2,2],[2,3,3],[3,5]]
```

**示例 3：**

```
输入: candidates = [2], target = 1
输出: []
```

 **代码示例：**

```c
int** res;
int* path;
int pathTop;
void backtracking(int* candidates, int candidatesSize, int startIndex, int target, int* returnSize, int** returnColumnSizes) {
    int sum = 0;
    for (int i = 0; i < pathTop; i++) {
        sum += path[i];
    }
    if (sum > target)
        return;
    if (target == sum) {
        int* tmp = (int*)malloc(sizeof(int) * pathTop);
        for (int i = 0; i < pathTop; i++) {
            tmp[i] = path[i];
        }
        res[(*returnSize)] = tmp;
        (*returnColumnSizes)[(*returnSize)++] = pathTop;
        return;
    }
    for (int i = startIndex; i < candidatesSize; i++) {
        path[pathTop++] = candidates[i];
        backtracking(candidates, candidatesSize, i, target, returnSize,
                     returnColumnSizes);
        pathTop--;
    }
}
int** combinationSum(int* candidates, int candidatesSize, int target,
                     int* returnSize, int** returnColumnSizes) {
    res = (int**)malloc(sizeof(int*) * 1001);
    path = (int*)malloc(sizeof(int) * 10000);
    *returnSize = 0;
    *returnColumnSizes = malloc(sizeof(int) * 1001);
    pathTop = 0;
    backtracking(candidates, candidatesSize, 0, target, returnSize,
                 returnColumnSizes);
    return res;
}
```

### 17.电话号码的字母组合（middle） ###

给定一个仅包含数字 `2-9` 的字符串，返回所有它能表示的字母组合。答案可以按 **任意顺序** 返回。

给出数字到字母的映射如下（与电话按键相同）。注意 1 不对应任何字母。

![img](D:/typoraphotos/200px-telephone-keypad2svg.png)

**示例 1：**

```
输入：digits = "23"
输出：["ad","ae","af","bd","be","bf","cd","ce","cf"]
```

**示例 2：**

```
输入：digits = ""
输出：[]
```

**示例 3：**

```
输入：digits = "2"
输出：["a","b","c"]
```

**代码示例：**

```c
char** res;
char* path;
int pathTop;
char phoneMap[11][5] = {"\0",    "\0",    "abc\0",  "def\0", "ghi\0",
                        "jkl\0", "mno\0", "pqrs\0", "tuv\0", "wxyz\0"};
// index指示的是digits字符串中的下标
void backtracking(char* digits, int* returnSize,int index) {
    if(pathTop==strlen(digits)){
        char* tmp=(char*)malloc(sizeof(char)*(pathTop+1));
        for(int i=0;i<pathTop;i++){
            tmp[i]=path[i];
        }
        tmp[pathTop]='\0';
        res[(*returnSize)++]=tmp;
        return;
    }
    int digit=digits[index]-'0';
    char* s=phoneMap[digit];
    for(int i=0;i<strlen(s);i++){
        path[pathTop++]=s[i];
        backtracking(digits,returnSize,index+1);
        pathTop--;
    }
}
char** letterCombinations(char* digits, int* returnSize) {
    int len = strlen(digits);
    res = (char**)malloc(sizeof(char*) * 10001);
    path = (char*)malloc(sizeof(char) * (len+1));
    *returnSize = 0;
    pathTop = 0;
    if(len==0) return res;
    backtracking(digits,returnSize,0);
    return res;
}
```

### 40.组合总和 II（middle） ###

给定一个候选人编号的集合 `candidates` 和一个目标数 `target` ，找出 `candidates` 中所有可以使数字和为 `target` 的组合。

`candidates` 中的每个数字在每个组合中只能使用 **一次** 。

**注意：**解集不能包含重复的组合。 

**示例 1:**

```
输入: candidates = [10,1,2,7,6,1,5], target = 8,
输出:
[
[1,1,6],
[1,2,5],
[1,7],
[2,6]
]
```

**示例 2:**

```
输入: candidates = [2,5,2,1,2], target = 5,
输出:
[
[1,2,2],
[5]
]
```

**代码示例：**

```c
// 每个path不可重复，需要先排序再去重
// 去重：每层选择元素的时候先判断“在该层”是否已经选过
int** res;
int* path;
int pathTop;
void backtracking(int* candidates, int candidatesSize, int target, int startIndex, int* returnSize, int** returnColumnSizes) {
    int sum = 0;
    for (int i = 0; i < pathTop; i++) {
        sum += path[i];
    }
    if (sum > target)
        return;
    if (sum == target) {
        int* tmp = (int*)malloc(sizeof(int) * pathTop);
        for (int i = 0; i < pathTop; i++) {
            tmp[i] = path[i];
        }
        res[(*returnSize)] = tmp;
        (*returnColumnSizes)[(*returnSize)++] = pathTop;
        return;
    }
    for (int i = startIndex; i < candidatesSize; i++) {
        if (i > startIndex && candidates[i] == candidates[i - 1])
            continue;
        path[pathTop++] = candidates[i];
        backtracking(candidates, candidatesSize, target, i + 1, returnSize,
                     returnColumnSizes);
        pathTop--;
    }
}
int cmp(const void* a, const void* b) { return *(int*)a - *(int*)b; }
int** combinationSum2(int* candidates, int candidatesSize, int target, int* returnSize, int** returnColumnSizes) {
    // 排序
    qsort(candidates, candidatesSize, sizeof(int), cmp);
    res = (int**)malloc(sizeof(int*) * 1001);
    path = (int*)malloc(sizeof(int) * 10000);
    *returnSize = 0;
    *returnColumnSizes = malloc(sizeof(int) * 1001);
    pathTop = 0;
    backtracking(candidates, candidatesSize, target, 0, returnSize,
                 returnColumnSizes);
    return res;
}
```

### 131.分割回文串（middle） ###

给你一个字符串 `s`，请你将 `s` 分割成一些子串，使每个子串都是 

**回文串**

 。返回 `s` 所有可能的分割方案。

**示例 1：**

```
输入：s = "aab"
输出：[["a","a","b"],["aa","b"]]
```

**示例 2：**

```
输入：s = "a"
输出：[["a"]]
```

**代码示例：**

```c
char*** res;
char** path;
int pathTop;
bool ifHuiWen(char* s, int start, int end) {
    while (start < end) {
        if (s[start++] != s[end--])
            return 0;
    }
    return 1;
}
void backtracking(char* s, int startIndex, int* returnSize, int** returnColumnSizes) {
    if (startIndex >= strlen(s)) {
        char** tmp = (char**)malloc(sizeof(char*) * (pathTop + 1));
        for (int i = 0; i < pathTop; i++) {
            tmp[i] = path[i];
        }
        tmp[pathTop] = '\0';
        res[(*returnSize)] = tmp;
        (*returnColumnSizes)[(*returnSize)++] = pathTop;
        return;
    }
    for (int i = startIndex; i < strlen(s); i++) {
        if (ifHuiWen(s, startIndex, i)) {
            // 这里必须先分配空间再赋值
            path[pathTop] = malloc(sizeof(char) * (i - startIndex + 2));
            for (int j = startIndex, k = 0; j <= i; j++, k++) {
                path[pathTop][k] = s[j];
            }
            path[pathTop][i - startIndex + 1] = '\0';
            pathTop++;
        } else {
            continue;
        }
        backtracking(s, i + 1, returnSize, returnColumnSizes);
        pathTop--;
    }
}
char*** partition(char* s, int* returnSize, int** returnColumnSizes) {
    res = malloc(sizeof(char**) * 1000000);
    path = malloc(sizeof(char*) * 1000000);
    *returnSize = 0;
    *returnColumnSizes = malloc(sizeof(int) * 1000000);
    pathTop = 0;
    backtracking(s, 0, returnSize, returnColumnSizes);
    return res;
}
```

### 78.子集（middle） ###

给你一个整数数组 `nums` ，数组中的元素 **互不相同** 。返回该数组所有可能的子集

解集 **不能** 包含重复的子集。你可以按 **任意顺序** 返回解集。

**示例 1：**

```
输入：nums = [1,2,3]
输出：[[],[1],[2],[1,2],[3],[1,3],[2,3],[1,2,3]]
```

**示例 2：**

```
输入：nums = [0]
输出：[[],[0]]
```

**代码示例：**

```c
// 子集问题，区别于组合：每个结点都要收集
int** res;
int* path;
int pathTop = 0;
void backtracking(int* nums, int numsSize, int* returnSize,
                  int** returnColumnSizes, int startIndex) {
    // 必须在每次回溯开始的时候就把当前节点收入res
    // 不用等到叶子结点再收集
    if (pathTop != 0) {
        int* tmp = (int*)malloc(sizeof(int) * pathTop);
        for (int s = 0; s < pathTop; s++) {
            tmp[s] = path[s];
        }
        res[(*returnSize)] = tmp;
        (*returnColumnSizes)[(*returnSize)++] = pathTop;
    }

    for (int i = startIndex; i < numsSize; i++) {
        path[pathTop++] = nums[i];
        backtracking(nums, numsSize, returnSize, returnColumnSizes, i + 1);
        pathTop--;
    }
}
int** subsets(int* nums, int numsSize, int* returnSize,
              int** returnColumnSizes) {
    res = malloc(sizeof(int*) * 100000);
    path = malloc(sizeof(int) * numsSize);
    *returnSize = 1;
    *returnColumnSizes = malloc(sizeof(int) * 100000);
    int* t = (int*)malloc(sizeof(int));
    res[0] = t;
    (*returnColumnSizes)[0] = 0;
    backtracking(nums, numsSize, returnSize, returnColumnSizes, 0);
    return res;
}
```

### 90.子集 II（middle） ###

给你一个整数数组 `nums` ，其中可能包含重复元素，请你返回该数组所有可能的 子集

解集 **不能** 包含重复的子集。返回的解集中，子集可以按 **任意顺序** 排列。

**示例 1：**

```
输入：nums = [1,2,2]
输出：[[],[1],[1,2],[1,2,2],[2],[2,2]]
```

**示例 2：**

```
输入：nums = [0]
输出：[[],[0]]
```

**代码示例：**

```c
// 在同一层进行横向选择时要进行去重
// 同一层意味着在for循环中去重
// 去重要先排序！！！！
int** res;
int* path;
int pathTop = 0;
void backtracking(int* nums, int numsSize, int* returnSize,
                  int** returnColumnSizes, int startIndex) {
    if (pathTop != 0) {
        int* tmp = (int*)malloc(sizeof(int) * pathTop);
        for (int i = 0; i < pathTop; i++) {
            tmp[i] = path[i];
        }
        res[(*returnSize)] = tmp;
        (*returnColumnSizes)[(*returnSize)++] = pathTop;
    }
    for (int i = startIndex; i < numsSize; i++) {
        if (i > startIndex && nums[i] == nums[i - 1])
            continue;
        path[pathTop++] = nums[i];
        backtracking(nums, numsSize, returnSize, returnColumnSizes, i + 1);
        pathTop--;
    }
}
int cmp(const void* a, const void* b) { return *(int*)a - *(int*)b; }
int** subsetsWithDup(int* nums, int numsSize, int* returnSize,
                     int** returnColumnSizes) {
    qsort(nums, numsSize, sizeof(int), cmp);
    res = malloc(sizeof(int*) * 100000);
    path = malloc(sizeof(int) * numsSize);
    *returnSize = 1;
    *returnColumnSizes = malloc(sizeof(int) * 100000);
    int* t = (int*)malloc(sizeof(int));
    res[0] = t;
    (*returnColumnSizes)[0] = 0;
    backtracking(nums, numsSize, returnSize, returnColumnSizes, 0);
    return res;
}
```

### 491.非递减子序列（middle） ###

给你一个整数数组 `nums` ，找出并返回所有该数组中不同的递增子序列，递增子序列中 **至少有两个元素** 。你可以按 **任意顺序** 返回答案。

数组中可能含有重复元素，如出现两个整数相等，也可以视作递增序列的一种特殊情况。

**示例 1：**

```
输入：nums = [4,6,7,7]
输出：[[4,6],[4,6,7],[4,6,7,7],[4,7],[4,7,7],[6,7],[6,7,7],[7,7]]
```

**示例 2：**

```
输入：nums = [4,4,3,2,1]
输出：[[4,4]]
```

**提示：**

- `1 <= nums.length <= 15`
- `-100 <= nums[i] <= 100`

**代码示例：**

```c
int** res;
int* path;
int pathTop = 0;
void backtracking(int* nums, int numsSize, int* returnSize,
                  int** returnColumnSizes, int startIndex) {
    if (pathTop >= 2) {
        int* tmp = (int*)malloc(sizeof(int) * pathTop);
        for (int i = 0; i < pathTop; i++) {
            tmp[i] = path[i];
        }
        res[(*returnSize)] = tmp;
        (*returnColumnSizes)[(*returnSize)++] = pathTop;
        // 这里不能有return
    }
    // 在每一层定义一个数组记录是否使用过
    // 这个数组的定义必须是在回溯函数内，for循环之前
    bool ifused[201] = {0};
    for (int i = startIndex; i < numsSize; i++) {
        if (ifused[nums[i] + 100]) // 消除负数的影响
            continue;
        if (pathTop == 0) {
            path[pathTop++] = nums[i];
            ifused[nums[i] + 100] = 1;
        } else if (nums[i] < path[pathTop - 1]) {
            continue;
        } else {
            path[pathTop++] = nums[i];
            ifused[nums[i] + 100] = 1;
        }
        backtracking(nums, numsSize, returnSize, returnColumnSizes, i + 1);
        pathTop--;
    }
}
int** findSubsequences(int* nums, int numsSize, int* returnSize,
                       int** returnColumnSizes) {
    res = malloc(sizeof(int*) * 1000000);
    path = malloc(sizeof(int) * numsSize);
    *returnSize = 0;
    *returnColumnSizes = malloc(sizeof(int) * 1000000);
    backtracking(nums, numsSize, returnSize, returnColumnSizes, 0);
    return res;
}
```

### 46.全排列（middle） ###

给定一个不含重复数字的数组 `nums` ，返回其 *所有可能的全排列* 。你可以 **按任意顺序** 返回答案。

**示例 1：**

```
输入：nums = [1,2,3]
输出：[[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]
```

**示例 2：**

```
输入：nums = [0,1]
输出：[[0,1],[1,0]]
```

**示例 3：**

```
输入：nums = [1]
输出：[[1]]
```

**代码示例：**

```c
int** res;
int* path;
int pathTop = 0;
void backtracking(int* nums, int numsSize, int* returnSize,
                  int** returnColumnSizes, bool* used) {
    if (pathTop == numsSize) {
        int* tmp = (int*)malloc(sizeof(int) * pathTop);
        for (int i = 0; i < pathTop; i++) {
            tmp[i] = path[i];
        }
        res[(*returnSize)] = tmp;
        (*returnColumnSizes)[(*returnSize)++] = pathTop;
        return;
    }
    for (int i = 0; i < numsSize; i++) {
        if (!used[i]) {
            path[pathTop++] = nums[i];
            used[i] = 1;
            backtracking(nums, numsSize, returnSize, returnColumnSizes, used);
            pathTop--;
            used[i] = 0;
        }
    }
}
int** permute(int* nums, int numsSize, int* returnSize,
              int** returnColumnSizes) {
    res = malloc(sizeof(int*) * 10000000);
    path = malloc(sizeof(int) * numsSize);
    bool* used = (bool*)malloc(sizeof(bool) * numsSize);
    // 注意！数组名和指针在使用sizeof的时候有区别
    // 这里sizeof(bool) * numsSize能代表整个数组的大小
    // 但是sizeof(used)只能代表这个指针的大小
    // bool used[numSize];这种时候sizeof(used)就能代表整个数组大小
    memset(used, 0, sizeof(bool) * numsSize);
    *returnSize = 0;
    *returnColumnSizes = malloc(sizeof(int) * 10000000);
    backtracking(nums, numsSize, returnSize, returnColumnSizes, used);
    return res;
}
```

### 47.全排列 II（middle） ###

给定一个可包含重复数字的序列 `nums` ，***按任意顺序*** 返回所有不重复的全排列。

**示例 1：**

```
输入：nums = [1,1,2]
输出：
[[1,1,2],
 [1,2,1],
 [2,1,1]]
```

**示例 2：**

```
输入：nums = [1,2,3]
输出：[[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]
```

**代码示例：**

```c
// 给定数组内含重复元素，但返回数组不能有一样的全排列
// 需要在同一层进行去重操作，意味着需要排序
// 注意去重思路，略有不同
int** res;
int* path;
int pathTop = 0;
int cmp(const void* a, const void* b) { return *(int*)a - *(int*)b; }
void backtracking(int* nums, int numsSize, int* returnSize, int** returnColumnSizes,bool* used){
    if(pathTop==numsSize){
        int* tmp = (int*)malloc(sizeof(int) * pathTop);
        for (int i = 0; i < pathTop; i++) {
            tmp[i] = path[i];
        }
        res[(*returnSize)] = tmp;
        (*returnColumnSizes)[(*returnSize)++] = pathTop;
        return;
    }
    for(int i=0;i<numsSize;i++){
        // 这里去重必须要有used[i-1]==0
        // 因为每一层遍历的时候，其前一个相同数字一定是未使用状态
        // 而如果是已使用状态说明这两个数不是在同一层遍历的
        if(i>0&&nums[i]==nums[i-1]&&used[i-1]==0) continue;
        if(!used[i]){
            path[pathTop++] = nums[i];
            used[i] = 1;
            backtracking(nums, numsSize, returnSize, returnColumnSizes, used);
            pathTop--;
            used[i] = 0;
        }
    }
}
int** permuteUnique(int* nums, int numsSize, int* returnSize, int** returnColumnSizes) {
    qsort(nums, numsSize, sizeof(int), cmp);
    res = malloc(sizeof(int*) * 100000);
    path = malloc(sizeof(int) * numsSize);
    bool used[numsSize];
    memset(used,0,sizeof(used));
    *returnSize = 0;
    *returnColumnSizes = malloc(sizeof(int) * 100000);
    backtracking(nums, numsSize, returnSize, returnColumnSizes, used);
    return res;
}
```


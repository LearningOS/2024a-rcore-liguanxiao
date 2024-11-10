# 实现功能

对于前几张的内容比较关键的问题时这里的take_current_task()会把所有所有权进行转移,这点需要尤其注意,本人在这卡了很久,其次是本节的实现swapn是将fork和exec的关键部分融合在一块,另外一个是调度问题,设置prio和stride属性,然后在Manage中进行操作即可.

# 简答题

一.

调度器通常会选择stride值最小的进程来执行下一个时间片。在这种情况下，p2的stride值为4，而p1的stride值为255。因此，调度器会选择p2而不是p1来执行下一个时间片。

二.

进程优先级 >= 2：这意味着每个进程的 stride 值至少为 2。因此，即使经过多次调度，stride 值的增长速度也会相对较慢。STRIDE_MAX – STRIDE_MIN <= BigStride / 2：这里的 BigStride 是指所有进程的最大步长。由于每个进程的 stride 值至少为 2，所以在多个时间片内，stride 值的增长是可控的。因此，最大和最小 stride 值之间的差值不会超过 BigStride / 2。



```rust
use core::cmp::Ordering;

const STRIDE_MAX: u64 = u64::MAX; // 最大值，对于 u64 是 2^64 - 1
const STRIDE_MIN: u64 = 2; // 最小值，根据题目要求

struct Stride(u64);

impl PartialOrd for Stride {
    fn partial_cmp(&self, other: &Self) -> Option<Ordering> {
        // 计算两个 stride 值的差值
        let diff = self.0.wrapping_sub(other.0);

        // 如果差值小于 STRIDE_MAX / 2，则 self 小于 other
        // 否则，self 大于 other
        if diff <= STRIDE_MAX / 2 {
            Some(Ordering::Less)
        } else {
            Some(Ordering::Greater)
        }
    }
}

impl PartialEq for Stride {
    fn eq(&self, other: &Self) -> bool {
        false // 根据题目要求，两个 Stride 永远不会相等
    }
}
```



# 荣耀准则

1. 在完成本次实验的过程（含此前学习的过程）中，我曾分别与 **以下各位** 就（与本次实验相关的）以下方面做过交流，还在代码中对应的位置以注释形式记录了具体的交流对象及内容：

   > *无*

2. 无料** ，还在代码中对应的位置以注释形式记录了具体的参考来源及内容：

   > *公告文档*

3. 我独立完成了本次实验除以上方面之外的所有工作，包括代码与文档。 我清楚地知道，从以上方面获得的信息在一定程度上降低了实验难度，可能会影响起评分。

4. 我从未使用过他人的代码，不管是原封不动地复制，还是经过了某些等价转换。 我未曾也不会向他人（含此后各届同学）复制或公开我的实验代码，我有义务妥善保管好它们。 我提交至本实验的评测系统的代码，均无意于破坏或妨碍任何计算机系统的正常运转。 我清楚地知道，以上情况均为本课程纪律所禁止，若违反，对应的实验成绩将按“-100”分计。
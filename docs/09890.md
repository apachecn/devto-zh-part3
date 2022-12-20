# LeetCode in Ruby: 56。合并间隔

> 原文：<https://dev.to/algobot76/leetcode-in-ruby-56-merge-intervals-492>

# 排序

```
def merge(intervals)
  merged = []
  intervals.sort_by! { |interval| interval.start }

  intervals.each do |interval|
    if merged.empty? || merged.last.end < interval.start
      merged << interval
    else
      merged.last.end = interval.end if interval.end > merged.last.end
    end
  end

  return merged
end 
```

首先我们根据列表中每个区间的`start`值对`intervals`列表进行排序。然后，我们遍历列表，并将当前间隔追加到`merged`列表，如果:

1.  `merged`列表为空，或者
2.  `last`间隔与当前间隔不重叠，即`last.end < interval.start`。

否则我们应该更新`merged`列表的`last`区间的`end`值。更新后的`end`值应该大于之前的值。

*时间复杂度* : `O(nlgn)`

*额外内存* : `O(1)`
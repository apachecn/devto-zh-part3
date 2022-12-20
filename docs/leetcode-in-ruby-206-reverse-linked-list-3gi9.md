# Ruby 中的 leet code:206 反向链表

> 原文：<https://dev.to/algobot76/leetcode-in-ruby-206-reverse-linked-list-3gi9>

# 迭代

```
def reverse_list(head)
  prev = nil
  curr = head
  until curr.nil?
    temp = curr.next
    curr.next = prev
    prev = curr
    curr = temp
  end
  prev
end 
```

这种方法的目的是将一个节点的`next`指针改为指向它的前一个节点。在我们开始遍历每个节点之前，我们需要指针`prev`来存储前一个节点。此外，当我们遍历节点时，我们需要`temp`节点，这样我们可以用`cur.next`节点替换`cur`节点，这将是我们在下一次迭代中处理的节点。

*时间复杂度* : `O(n)`

*额外内存* : `O(1)`

# 递归

```
def reverse_list(head)
  return head if head.nil? || head.next.nil?

  p = reverse_list(head.next)
  head.next.next = head
  head.next = nil
  p
end 
```

虽然递归方法和迭代方法一样有效，但是很难搞清楚。让我们假设我们有一个链表`n1 -> n2 -> n3 -> nill`。假设后两个已经反过来了。也就是`n1 -> n2 -> n3 <- nill`，你在`n2`。你希望`n3`的`next`指向`n2`，即`n2.next.next = n2`。顺着这个逻辑，我们就可以出来上面的代码了。

*时间复杂度* : `O(n)`

*额外内存* : `O(1)`
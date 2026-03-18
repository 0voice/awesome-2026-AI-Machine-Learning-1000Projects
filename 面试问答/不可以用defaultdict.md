# 手撕：无重复字符的最长连续子串（禁用 defaultdict 版）
**纯基础字典（dict）实现**的最优解法（滑动窗口+哈希表），全程不依赖 `defaultdict`，逻辑和性能与原版一致，同时保留核心注释和关键细节。

## 核心说明
原版代码本就使用普通 `dict`（而非 `defaultdict`），因为我们仅需「判断键是否存在」+「赋值/取值」，完全不需要 `defaultdict` 的默认值特性。以下是纯净版代码：

## 代码实现（Python，无 defaultdict，最优版）
```python
def lengthOfLongestSubstring(s: str) -> int:
    # 普通字典：key=字符，value=字符最后出现的索引（无defaultdict，纯基础dict）
    char_index = {}  # 仅初始化空字典，无任何额外依赖
    left = 0         # 滑动窗口左指针
    max_len = 0      # 记录最长无重复子串长度
    
    # 右指针遍历字符串，enumerate获取索引和字符
    for right, char in enumerate(s):
        # 关键判断：字符已存在 且 最后出现位置在当前窗口内（≥left）→ 更新左指针
        if char in char_index and char_index[char] >= left:
            left = char_index[char] + 1
        
        # 更新当前字符的最后出现索引（普通dict直接赋值，无需defaultdict）
        char_index[char] = right
        
        # 计算当前窗口长度，更新最大值
        current_len = right - left + 1
        if current_len > max_len:
            max_len = current_len
    
    return max_len

# 测试用例（覆盖常规/边界/易错场景）
if __name__ == "__main__":
    assert lengthOfLongestSubstring("abcabcbb") == 3  # 常规场景
    assert lengthOfLongestSubstring("bbbbb") == 1     # 全重复
    assert lengthOfLongestSubstring("pwwkew") == 3    # 中间重复
    assert lengthOfLongestSubstring("") == 0          # 空字符串
    assert lengthOfLongestSubstring("abba") == 2      # 易错用例（左指针不回退）
    assert lengthOfLongestSubstring("dvdf") == 3      # 非连续重复
    print("所有测试用例通过！")
```

## 关键细节（为什么无需 defaultdict）
1. `char_index` 仅存储「出现过的字符」：遍历到新字符时，直接 `char_index[char] = right` 赋值，无需默认值；
2. 判断重复时，先通过 `char in char_index` 检查键是否存在，再取值，完全规避「键不存在报错」的问题；
3. 普通 `dict` 的性能与 `defaultdict` 一致，且更轻量化，符合面试「最小依赖」的要求。

## 面试避坑点（禁用 defaultdict 时的注意事项）
- 不要尝试用 `char_index.get(char, -1)` 替代 `in` 判断（虽然可行，但可读性差），面试中优先用 `char in char_index` 明确判断；
- 避免手动初始化所有可能的字符（如英文字母），只需动态赋值即可，保持代码简洁。

### 总结
1. 核心解法仍为**滑动窗口+普通字典**，时间复杂度 $O(n)$、空间复杂度 $O(1)$，无性能损失；
2. 禁用 `defaultdict` 后，仅需用基础 `dict` 的「存在性判断+赋值」即可实现核心逻辑；
3. 易错用例（如 `"abba"`）的处理逻辑不变，关键是保证左指针不回退。

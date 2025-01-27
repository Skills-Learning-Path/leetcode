# [444. 序列重建](https://leetcode.cn/problems/sequence-reconstruction)

[English Version](/solution/0400-0499/0444.Sequence%20Reconstruction/README_EN.md)

## 题目描述

<!-- 这里写题目描述 -->

<p>验证原始的序列&nbsp;<code>org</code>&nbsp;是否可以从序列集&nbsp;<code>seqs</code>&nbsp;中唯一地重建。序列&nbsp;<code>org</code>&nbsp;是 <code>1</code> 到 <code>n</code> 整数的排列，其中 <code>1 ≤ n ≤ 10<sup>4</sup></code> 。重建是指在序列集 <code>seqs</code> 中构建最短的公共超序列。（即使得所有 &nbsp;<code>seqs</code>&nbsp;中的序列都是该最短序列的子序列）。请你确定是否只可以从 <code>seqs</code> 重建唯一的序列，且该序列就是 <code>org</code> 。</p>

<p>&nbsp;</p>

<p><strong>示例 1：</strong></p>

<pre>
<strong>输入：</strong>org = [1,2,3], seqs = [[1,2],[1,3]]
<strong>输出：</strong>false
<strong>解释：</strong>[1,2,3] 不是唯一的可重建序列，[1,3,2] 也是一个有效的可重建序列。
</pre>

<p><strong>示例 2：</strong></p>

<pre>
<strong>输入：</strong>org = [1,2,3], seqs = [[1,2]]
<strong>输出：</strong>false
<strong>解释：</strong>可重建序列只能是 [1,2] 。
</pre>

<p><strong>示例 3：</strong></p>

<pre>
<strong>输入：</strong>org = [1,2,3], seqs = [[1,2],[1,3],[2,3]]
<strong>输出：</strong>true
<strong>解释：</strong>序列 [1,2]、[1,3] 和 [2,3] 可以从原始序列 [1,2,3] 唯一地重建。
</pre>

<p><strong>示例 4：</strong></p>

<pre>
<strong>输入：</strong>org = [4,1,5,2,6,3], seqs = [[5,2,6,3],[4,1,5,2]]
<strong>输出：</strong>true
</pre>

<p>&nbsp;</p>

<p><strong>提示：</strong></p>

<ul>
	<li><code>1 &lt;= n &lt;= 10^4</code></li>
	<li><code>org</code> 是 <code>{1,2,...,n}</code> 的一个排列</li>
	<li><code>1 &lt;= segs[i].length &lt;= 10^5</code></li>
	<li><code>seqs[i][j]</code> 符合 32 位整数范围</li>
</ul>

## 解法

<!-- 这里可写通用的实现逻辑 -->

拓扑排序，BFS 实现。

<!-- tabs:start -->

### **Python3**

<!-- 这里可写当前语言的特殊实现逻辑 -->

```python
class Solution:
    def sequenceReconstruction(self, org: List[int], seqs: List[List[int]]) -> bool:
        n = len(org)
        nums = set()
        for seq in seqs:
            for num in seq:
                if num < 1 or num > n:
                    return False
                nums.add(num)
        if len(nums) < n:
            return False

        edges = defaultdict(list)
        indegree = [0] * (n + 1)
        for seq in seqs:
            i = seq[0]
            for j in seq[1:]:
                edges[i].append(j)
                indegree[j] += 1
                i = j
        q = deque()
        for i in range(1, n + 1):
            if indegree[i] == 0:
                q.append(i)
        cnt = 0
        while q:
            if len(q) > 1 or org[cnt] != q[0]:
                return False
            i = q.popleft()
            cnt += 1
            for j in edges[i]:
                indegree[j] -= 1
                if indegree[j] == 0:
                    q.append(j)
        return cnt == n
```

### **Java**

<!-- 这里可写当前语言的特殊实现逻辑 -->

```java
class Solution {
    public boolean sequenceReconstruction(int[] org, List<List<Integer>> seqs) {
        int n = org.length;
        Set<Integer> nums = new HashSet<>();
        for (List<Integer> seq : seqs) {
            for (int num : seq) {
                if (num < 1 || num > n) {
                    return false;
                }
                nums.add(num);
            }
        }
        if (nums.size() < n) {
            return false;
        }
        List<Integer>[] edges = new List[n + 1];
        for (int i = 0; i < edges.length; ++i) {
            edges[i] = new ArrayList<>();
        }
        int[] indegree = new int[n + 1];
        for (List<Integer> seq : seqs) {
            int i = seq.get(0);
            for (int j = 1; j < seq.size(); ++j) {
                edges[i].add(seq.get(j));
                ++indegree[seq.get(j)];
                i = seq.get(j);
            }
        }
        Queue<Integer> q = new LinkedList<>();
        for (int i = 1; i <= n; ++i) {
            if (indegree[i] == 0) {
                q.offer(i);
            }
        }
        int cnt = 0;
        while (!q.isEmpty()) {
            if (q.size() > 1 || q.peek() != org[cnt]) {
                return false;
            }
            ++cnt;
            int i = q.poll();
            for (int j : edges[i]) {
                --indegree[j];
                if (indegree[j] == 0) {
                    q.offer(j);
                }
            }
        }
        return cnt == n;
    }
}
```

### **C++**

```cpp
class Solution {
public:
    bool sequenceReconstruction(vector<int>& org, vector<vector<int>>& seqs) {
        int n = org.size();
        unordered_set<int> nums;
        for (auto& seq : seqs)
        {
            for (int num : seq)
            {
                if (num < 1 || num > n) return false;
                nums.insert(num);
            }
        }
        if (nums.size() < n) return false;
        vector<vector<int>> edges(n + 1);
        vector<int> indegree(n + 1);
        for (auto& seq : seqs)
        {
            int i = seq[0];
            for (int j = 1; j < seq.size(); ++j)
            {
                edges[i].push_back(seq[j]);
                ++indegree[seq[j]];
                i = seq[j];
            }
        }
        queue<int> q;
        for (int i = 1; i <= n; ++i)
        {
            if (indegree[i] == 0) q.push(i);
        }
        int cnt = 0;
        while (!q.empty())
        {
            if (q.size() > 1 || q.front() != org[cnt]) return false;
            ++cnt;
            int i = q.front();
            q.pop();
            for (int j : edges[i])
            {
                --indegree[j];
                if (indegree[j] == 0) q.push(j);
            }
        }
        return cnt == n;
    }
};
```

### **Go**

```go
func sequenceReconstruction(org []int, seqs [][]int) bool {
	n := len(org)
	nums := make(map[int]bool)
	for _, seq := range seqs {
		for _, num := range seq {
			if num < 1 || num > n {
				return false
			}
			nums[num] = true
		}
	}
	if len(nums) < n {
		return false
	}
	edges := make([][]int, n+1)
	indegree := make([]int, n+1)
	for _, seq := range seqs {
		i := seq[0]
		for _, j := range seq[1:] {
			edges[i] = append(edges[i], j)
			indegree[j]++
			i = j
		}
	}
	var q []int
	for i := 1; i <= n; i++ {
		if indegree[i] == 0 {
			q = append(q, i)
		}
	}
	cnt := 0
	for len(q) > 0 {
		if len(q) > 1 || org[cnt] != q[0] {
			return false
		}
		i := q[0]
		q = q[1:]
		cnt++
		for _, j := range edges[i] {
			indegree[j]--
			if indegree[j] == 0 {
				q = append(q, j)
			}
		}
	}
	return cnt == n
}
```

### **...**

```

```

<!-- tabs:end -->

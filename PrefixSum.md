# PREFIX SUM – COMPLETE CLASSWORK NOTES

---

[![Watch on YouTube](https://img.youtube.com/vi/v5Z2_9ApE2g/maxresdefault.jpg)](https://youtu.be/v5Z2_9ApE2g "Prefix Sum | Silent Code Journal")

## 1. What is Prefix Sum?

**Prefix Sum** is a technique where we **precompute cumulative sums** of an array so that we can answer **range-based questions efficiently**.

<img src="https://miro.medium.com/1%2A3j7gkjyRLd0oL86kCN8bRQ.jpeg">
<img src="https://labuladong.online/algo/images/difference/1.jpeg">

### Core Idea

Instead of repeatedly summing elements every time we need a range sum, we **store partial sums once** and reuse them.

---

## 2. Why Prefix Sum Exists (The Motivation)

Consider this problem:

> Given an array, answer multiple queries asking for the sum of elements between index `L` and `R`.

### Brute Force Approach

For each query:

* Loop from `L` to `R`
* Add all elements

**Time Complexity:**
`O(N)` per query
If there are `Q` queries → `O(N × Q)` (very slow)

### Optimization Goal

We want:

* **One-time preprocessing**
* **Fast queries afterward**

This is exactly what Prefix Sum provides.

---

## 3. Definition (Formal)

For an array `arr` of size `n`, the **prefix sum array** `prefix` is defined as:

```
prefix[i] = arr[0] + arr[1] + ... + arr[i]
```

---

## 4. How Prefix Sum Works (Step-by-Step)

### Example Array

```
arr = [2, 4, 1, 3, 6]
```

### Build Prefix Sum

```
prefix[0] = 2
prefix[1] = 2 + 4 = 6
prefix[2] = 6 + 1 = 7
prefix[3] = 7 + 3 = 10
prefix[4] = 10 + 6 = 16
```

### Final Prefix Array

```
prefix = [2, 6, 7, 10, 16]
```

---

## 5. How to Use Prefix Sum (Range Sum Formula)

### Question

Find sum from index `L` to `R`.

### Formula

```
sum(L, R) = prefix[R] - prefix[L - 1]
```

### Special Case

If `L == 0`:

```
sum(0, R) = prefix[R]
```

---

## 6. Prefix Sum in Java (Core Template)

```java
int n = arr.length;
int[] prefix = new int[n];

prefix[0] = arr[0];
for (int i = 1; i < n; i++) {
    prefix[i] = prefix[i - 1] + arr[i];
}
```

---

## 7. Time and Space Complexity

| Phase        | Complexity |
| ------------ | ---------- |
| Build Prefix | O(N)       |
| Each Query   | O(1)       |
| Extra Space  | O(N)       |

This tradeoff (extra space for speed) is **intentional and powerful**.

---

## 8. Types of Problems Solved Using Prefix Sum

### 1️⃣ Range Sum Queries (Most Basic)

**Problem**

* Find sum between `L` and `R` many times.

**Used In**

* Competitive programming
* Databases
* Analytics

---

### 2️⃣ Subarray Sum Problems

**Typical Question**

> Find number of subarrays whose sum equals `K`.

#### Key Insight

If:

```
prefix[j] - prefix[i] = K
```

Then:

```
prefix[i] = prefix[j] - K
```

#### Solution Technique

* Use **HashMap**
* Store frequency of prefix sums

```java
Map<Integer, Integer> map = new HashMap<>();
map.put(0, 1);

int sum = 0, count = 0;
for (int num : arr) {
    sum += num;
    if (map.containsKey(sum - k)) {
        count += map.get(sum - k);
    }
    map.put(sum, map.getOrDefault(sum, 0) + 1);
}
```

---

### 3️⃣ Check if Any Subarray Has Sum = 0

**Logic**
If two prefix sums are equal:

```
prefix[i] == prefix[j]
```

Then sum between them is `0`.

Use a **Set** to detect duplicates.

---

### 4️⃣ Prefix Sum with Modulo

**Problem**

> Count subarrays where sum % K == 0

**Important Concept**

```
(a - b) % k == 0  →  a % k == b % k
```

Used heavily in:

* Advanced LeetCode problems
* Number theory problems

---

### 5️⃣ Prefix Sum on Binary Arrays (0s and 1s)

Examples:

* Longest subarray with equal 0s and 1s
* Replace 0 with -1 and use prefix sum

---

### 6️⃣ Prefix Sum for Difference of Counts

Transform array:

* +1 for one condition
* -1 for another condition

Then analyze prefix sums.

---

## 9. 2D Prefix Sum (Matrix Prefix Sum)

### Use Case

Find sum of elements inside a submatrix **very fast**.

### Formula

```
prefix[i][j] =
matrix[i][j]
+ prefix[i-1][j]
+ prefix[i][j-1]
- prefix[i-1][j-1]
```

### Query

```
sum = p[r2][c2]
    - p[r1-1][c2]
    - p[r2][c1-1]
    + p[r1-1][c1-1]
```

Used in:

* Image processing
* Heatmaps
* Game grids

---

## 10. Prefix Sum vs Sliding Window

| Prefix Sum           | Sliding Window                   |
| -------------------- | -------------------------------- |
| Works with negatives | Fails with negatives             |
| Used for queries     | Used for contiguous optimization |
| Preprocessing needed | No preprocessing                 |

Prefix Sum is **more general**.

---

## 11. When Should You Think of Prefix Sum?

Immediately consider Prefix Sum when you see:

* “Range”
* “Subarray”
* “Continuous segment”
* “Sum equals K”
* “Multiple queries”
* “Count number of subarrays”

---

## 12. Common Mistakes

1. Forgetting `prefix[0]`
2. Off-by-one errors
3. Not handling `L = 0`
4. Forgetting to initialize HashMap with `{0:1}`
5. Integer overflow (use `long` if needed)

---

## 13. Mental Checklist Before Solving Problems

Ask yourself:

* Can I reuse previous sums?
* Is this a range-based problem?
* Are there multiple queries?
* Does subarray sum matter?

If **YES**, think **PREFIX SUM**.

---

## 14. Summary 

* Prefix Sum stores cumulative sums
* Converts `O(N)` range queries to `O(1)`
* Foundation for many advanced algorithms
* Combines well with HashMaps
* Extremely important for interviews

---

# PREFIX SUM + HASHMAP (DEEP DIVE CLASSWORK)

---

## 1. Why Prefix Sum Alone Is Not Enough

Prefix Sum alone helps with:

* Fast **range sum queries**
* Preprocessing based problems

But many real problems ask:

* Count number of subarrays
* Detect existence of subarrays
* Handle **negative numbers**
* Work without fixed window size

Prefix Sum alone **cannot count efficiently**.
That is where **HashMap** enters.

---

## 2. The Core Observation (VERY IMPORTANT)

Let:

```
prefixSum[i] = sum of elements from index 0 to i
```

For any subarray `(i+1 → j)`:

```
subarraySum = prefixSum[j] - prefixSum[i]
```

If we want:

```
subarraySum = K
```

Then:

```
prefixSum[i] = prefixSum[j] - K
```

### Meaning (in plain English)

If we already saw a prefix sum equal to `(currentPrefix - K)`,
then there exists a subarray ending at the current index with sum `K`.

---

## 3. Why HashMap Is Required

We need to:

* **Store previous prefix sums**
* **Count how many times they occurred**
* **Check instantly (O(1))**

That is exactly what HashMap does.

```
HashMap<PrefixSum, Frequency>
```

---

## 4. The Golden Template (Memorize This)

This template solves **90% of prefix + hashmap problems**.

```java
Map<Integer, Integer> map = new HashMap<>();
map.put(0, 1);   // VERY IMPORTANT

int prefixSum = 0;
int answer = 0;

for (int num : arr) {
    prefixSum += num;

    if (map.containsKey(prefixSum - K)) {
        answer += map.get(prefixSum - K);
    }

    map.put(prefixSum, map.getOrDefault(prefixSum, 0) + 1);
}
```

---

## 5. Why `map.put(0, 1)` Is Mandatory

This represents:

> “There is one prefix sum of 0 before the array starts.”

### Example

```
arr = [3]
K = 3
```

At index 0:

```
prefixSum = 3
prefixSum - K = 0
```

If we don’t store `{0:1}`, we miss this valid subarray.

---

## 6. Problem Type 1: Count Subarrays with Sum = K

### Example

```
arr = [1, 2, 3]
K = 3
```

### Walkthrough

| Index | Value | PrefixSum | Needed | Map           | Count |
| ----- | ----- | --------- | ------ | ------------- | ----- |
| 0     | 1     | 1         | -2     | {0:1}         | 0     |
| 1     | 2     | 3         | 0      | {0:1,1:1}     | 1     |
| 2     | 3     | 6         | 3      | {0:1,1:1,3:1} | 2     |

Answer = 2
Subarrays: `[1,2]`, `[3]`

---

## 7. Why Sliding Window Fails Here

Sliding Window:

* Works only when **all numbers are positive**
* Breaks when negatives exist

Prefix + HashMap:

* Works with **positive, zero, negative**
* Order independent

That is why interviewers prefer this pattern.

---

## 8. Problem Type 2: Check If Subarray with Sum = K Exists

Just change **count** to **boolean**.

```java
Map<Integer, Integer> map = new HashMap<>();
map.put(0, 1);

int sum = 0;
for (int num : arr) {
    sum += num;
    if (map.containsKey(sum - K)) {
        return true;
    }
    map.put(sum, map.getOrDefault(sum, 0) + 1);
}
return false;
```

---

## 9. Problem Type 3: Subarray Sum Equals 0

### Key Idea

If:

```
prefixSum repeats → subarray sum is 0
```

```java
Set<Integer> set = new HashSet<>();
int sum = 0;
set.add(0);

for (int num : arr) {
    sum += num;
    if (set.contains(sum)) return true;
    set.add(sum);
}
```

---

## 10. Problem Type 4: Longest Subarray with Sum = K

### Trick

* Store **first occurrence** of each prefix sum
* Do NOT overwrite existing values

```java
Map<Integer, Integer> map = new HashMap<>();
map.put(0, -1);

int sum = 0, maxLen = 0;

for (int i = 0; i < arr.length; i++) {
    sum += arr[i];

    if (map.containsKey(sum - K)) {
        maxLen = Math.max(maxLen, i - map.get(sum - K));
    }

    map.putIfAbsent(sum, i);
}
```

---

## 11. Problem Type 5: Count Subarrays with Sum % K == 0

### Mathematical Insight

```
(a - b) % k == 0  ⇨  a % k == b % k
```

### Code

```java
Map<Integer, Integer> map = new HashMap<>();
map.put(0, 1);

int sum = 0, count = 0;

for (int num : arr) {
    sum += num;
    int mod = ((sum % K) + K) % K;

    count += map.getOrDefault(mod, 0);
    map.put(mod, map.getOrDefault(mod, 0) + 1);
}
```

---

## 12. Binary Array Problems (0s and 1s)

### Longest Subarray with Equal 0s and 1s

Step 1: Convert

```
0 → -1
1 → +1
```

Step 2: Find longest subarray with sum = 0

Same prefix + hashmap logic.

---

## 13. Pattern Recognition (Interview Gold)

If you see:

* “Count subarrays”
* “Sum equals K”
* “Continuous”
* “Negative numbers”
* “Multiple answers”

Immediately think:

```
Prefix Sum + HashMap
```

---

## 14. Common Interview Mistakes

1. Forgetting `map.put(0,1)`
2. Overwriting first prefix index (for longest problems)
3. Using sliding window incorrectly
4. Ignoring negative numbers
5. Integer overflow (use `long`)

---

## 15. Complexity Analysis

| Metric               | Value     |
| -------------------- | --------- |
| Time                 | O(N)      |
| Space                | O(N)      |
| Works with negatives | Yes       |
| Interview friendly   | Extremely |

---

## 16. Mental Flow (Train This)

At every index:

1. Update prefix sum
2. Ask: “Have I seen `prefixSum - K` before?”
3. Update answer
4. Store prefix sum

This **mental flow must become automatic**.

---

## 17. Summary (One-Line Mastery)

> Prefix Sum converts subarray problems into difference problems, and HashMap makes those differences searchable in O(1).

---
**top LeetCode problems that use the Prefix Sum + HashMap pattern** 

### **1. Core Prefix + HashMap Problems**

1. **Subarray Sum Equals K** — count subarrays with sum exactly `k`.
   [LeetCode 560. Subarray Sum Equals K](https://leetcode.com/problems/subarray-sum-equals-k/?utm_source=chatgpt.com)

2. **Subarray Sums Divisible by K** — count subarrays whose sum % `k` == 0.
   [LeetCode 974. Subarray Sums Divisible by K](https://leetcode.com/problems/subarray-sums-divisible-by-k/?utm_source=chatgpt.com)

3. **Continuous Subarray Sum** — check if a subarray of at least length 2 has sum multiple of `k`.
   [LeetCode 523. Continuous Subarray Sum](https://leetcode.com/problems/continuous-subarray-sum/?utm_source=chatgpt.com)

4. **Maximum Size Subarray Sum Equals k** — longest subarray with sum exactly `k`.
   [LeetCode 325. Maximum Size Subarray Sum Equals k](https://algo.monster/liteproblems/325?utm_source=chatgpt.com)

5. **Contiguous Array** — longest balanced subarray (equal number of 0’s and 1’s).
   (Discussed in prefix sum pattern guides — problem uses prefix sum + map for difference balance) ([LeetCode][1])

---

### **2. Related & Useful Practice Patterns**

These also involve prefix sums and maps or similar logic:

6. **303. Range Sum Query – Immutable** (simple prefix sum, not hash map) — prefix sum fundamentals.
   [LeetCode 303. Range Sum Query – Immutable](https://leetcode.com/discuss/post/5119937/prefix-sum-problems-by-c0d3m-08l9/?utm_source=chatgpt.com)

7. **930. Binary Subarrays With Sum = Goal** — count binary subarrays with sum = `goal` (hash map, prefix sum).
   (See pattern examples in prefix + hashmap guides) ([LeetCode][1])

8. **Subarray Product Less Than K** — not purely prefix sum but related sliding/prefix logic (good crossover).
   (Search platform list — strong practice companion) ([LeetCode][2])

9. **Count of Range Sum** — count subarrays in range using prefix sums + advanced trees.
   (Often asked variation beyond simple prefix + map) ([LeetCode][2])

10. **Longest Subarray With Sum 0** — depth of prefix + map pattern.
    (Common prefix/map variant) ([LeetCode][3])

---

### **3. Extended/Advanced Variants (Often Asked in Interviews)**

11. **Subarrays with XOR = K** — XOR prefix + map (similar logic with XOR instead of sum)
    (Listed in hashing + prefix sum guides) ([LeetCode][3])

12. **Smallest Subarray with Sum >= K** — different approach (deque) but often contrasted with prefix logic.
    (Seen in prefix pattern discussions) ([LeetCode][3])

13. **Number of Nice Subarrays** — prefix/map for odd/even counts (LeetCode variation).
    (Prefix sum + frequency logic) ([Reddit][4])

14. **Longest Subarray With Sum Divisible by K** — find longest subarray divisible by `k`.
    (GeeksforGeeks practice) ([GeeksforGeeks][5])

15. **Range Sum Query 2D – Immutable** — extension to 2D prefix sums (practice dimensions).
    [LeetCode Prefix Sum Problem List (1D & 2D)](https://leetcode.com/problem-list/prefix-sum/?utm_source=chatgpt.com)

---

### **Guided Lists for More Problems**

* **LeetCode Prefix Sum problem list** — a curated list of prefix-based problems.
  [LeetCode Prefix Sum Problem List](https://leetcode.com/problem-list/prefix-sum/?utm_source=chatgpt.com)

* **Prefix Sum & HashMap study guides** with multiple examples.
  (Discuss guides that cover many problems together) ([LeetCode][1])

---

### **Practice Strategy**

* Start with **560**, **974**, and **523** — core prefix + map logic.
* Progress to **325** and **Contiguous Array** for **longest subarray** variants.
* Solve binary and modulo variations (930, modulo counts).
* Use the curated LeetCode list to branch into 2D and advanced forms.

---

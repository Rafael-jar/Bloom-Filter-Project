# Bloom Filter Implementation and Analysis

This repo is a summary of my project for the **INFO-F413: Randomized Algorithms** course at ULB (Ecole Polytechnique de Bruxelles).

**Note:** The C++ source code isn't public to respect the university's academic integrity policy.

---

### 🎯 Project Goal

The main goal was to build a Bloom filter from scratch in C++ and then run a bunch of experiments on it. I wanted to see how the *actual* false positive rate compared to the theoretical one.

Specifically, I was testing the well-known formula for the optimal number of hash functions, *k*:
$$k=\frac{m}{n}ln(2)$$
...where *m* is the array size and *n* is the number of items.

### 🛠️ How It Works

I built two main C++ classes:

1.  **`HashFactory`**: This class creates the *k* hash functions. Instead of generating *k* totally separate (and computationally expensive) hashes, I used **double hashing**. This common technique just needs two base hash functions ($h_1$ and $h_2$) from a universal family. Then, it can generate all the other hashes with a simple formula:
    $h_i(x) = (h_1(x) + i \cdot h_2(x)) \pmod m$.
    This is way more efficient.

2.  **`BloomFilter`**: This is the data structure itself. It uses the `HashFactory` and has the two main methods:
    * `add(item)`: Hashes the item *k* times and flips the corresponding bits in the array to `true`.
    * `contains(item)`: Hashes the item *k* times. If *any* bit is `false`, it's a guaranteed "not in set." If *all* bits are `true`, it reports "in set" (which might be a false positive).

### 🔬 Key Findings

To get the false positive rate, I'd add *n* items to the filter, then test 200,000 *new* items (that were definitely not in the set) and count how many came back as `true`.

For the most part, my experimental results (which I plotted in the full report) matched the theory. The optimal *k* I found in my tests tracked the theoretical formula very closely.

But, I also found two interesting edge cases where the theory didn't quite match:

* **For very small *n*** ($n \le 200$): The results were kind of all over the place. This makes sense, as the probabilistic model's assumptions don't hold up very well with such a small sample.
* **For very large *n*** ($n \ge 500,000$): When the filter was "saturated" (too full), the *best* *k* was always *lower* than what the formula predicted. It seems that when the filter is that full, the best strategy is to use a tiny *k* (like 1 or 2) just to avoid flipping even more bits, which is the opposite of what the standard theory suggests.

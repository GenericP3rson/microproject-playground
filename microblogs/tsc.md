# Theoretical CS

## Rank Finding

Problem: Given a set S of numbers, find the ith smallest number. (This is a more general form of the "median finding" problem.)

There exists a solution in O(n) time, as introduced by [Blum, Floyd, Pratt, Rivest, Tarjan 1973]! The algorithm is a bit tricky, but let's just go for figuring it out.

### Algorithm

To find the ith smallest elements of an array...

1. Break the array into $\frac{n}{5}$ groups of 5 elements each. 
2. Sort each group of 5 elements
3. Recursively find the median of the medians of the groups. Let x = median of medians.
4. Find sets of L and G where L has all elements less than x and G has all elements greater than x
5. If the rank(x)...
  * = i, return x
  * \> i, search for i in the L array
  * < i, search for i - rank(x) in the G array

### Runtime Analysis

Find, let's state the obvious:
* Step #1 will take O(n) time
* Step #2 will take O(n) time because sorting 5 elements is constants and we do this for all n/5 elements
* Step #4 will take O(n) time (since we can search element-by-elements and do a check if it's greater than or less than x then add it to the appropriate array)

Next, the less obvious:
* Step #3 will take T(n/5) (we're recursing on the algorithm for n/5 elements)
* Step #5 will either take T(|L|) or T(|G|) (or O(1), I suppose) (we're recursing on one of the arrays)

Here's the big claim:

> Step #5 is 7/10-balanced, so it is T(7n/10)

### Wait, what does that mean?

Assume we knew that for some c < 1, max({rank(x), n-rank(x)}) ≤ cn. (This means x is **c-balanced**—regardless of which way we go, we'll cut at least cn elements!)

Generally, this means that the recurrance could evaluate out to be $T(n) = T(cn) + O(n)$. 

$cn + c^2n + c^3n + ... = \frac{n}{1-c} = O(n)$ (which is linear!)

### Okay... how are we c-balanced?

We have the median of all medians as our x. This means that half the medians (n/10 elements in total) are bigger than the median. Furthermore, an addition 2n/10 are also greater (since there are 2 more points in each group that are greater than the median by definition of the median).

This means that rank(x) > 3n/10 => rank(x) ≤ 7/10. We can do a similar argument to show that the same is true for n-rank(x). This means that we're c-bounded!

### Finishing the Runtime Analysis

Let's wrap up the runtime analysis and solve out the recursion.

T(n) = T(n/5) + T(7n/10) + O(n)

Looking at this, since 1/5 + 7/10 < 1, it's likely O(n). We can check that though.

Assume T(k) ≤ ck for all k > n.

$T(k) = c/5 k + 7/10 ck + c_1 k \leq cn$

$c (9/10 k) + c_1 k \leq ck$

$10c_1 \leq c$

With the base case, $T(1) \leq c$. Therefore, max($10c_1, T(1)$) ≤ c, so the function is indeed in O(1) time!


## Randomized Algorithms

In the real world, there are often tradeoffs one must make. For example, we might trade accuracy for speed or worst-case runtime for average runtime. These tradeoffs are what makes randomized algorithms so useful. 

There are two types of randomized algorithms: Monte Carlo and Las Vegas algorithms. Monte Carlo may not get the correct answer but will always have a good runtime. Las Vegas will always get the right answer but may have a very large runtime. 

One example of a Monte Carlo algorithm that I've used for test cases is, for checking if a parametrixed circuit is correct by passing a few random test vectors instead of matrix multiplying with a variable. While I suppose this isn't the best of examples, if there was a parameter, it might be harder for the program to keep track of it. However, with setting the parameters to be random and sending in a few random test vectors, we can ensure the output is the same. This means that there can be incorrect matrices that we deem as correct; however, the runtime is better (I would imagine since just computation is easier than dealing with variables, though I don't know the exact comparison). Therefore, it's a Monte Carlo algorithm. 

## Amortized Time—is it worst-case or average?

Before the most recent lecture on amortized runtime, I wasn't completely certain if amortized time was worst-case or average. Turns out, it's both (sort of). The idea of amortized time is while a single operation can take a massive amount of time, we can guarantee that over k steps, this will only happen at most once. In this sense, yes, it's average since we're considering the runtime over several steps, but it's also worst case because the amortized runtime is the worst it can be over those steps. 

## Online vs. Offline Algorithms

In the "real world," it's rare to have *all* the data before making a decision. Instead, a decision often has to be made based on what we've seen thus far and updated with the new data we get. This is what we would call an online algorithm. As opposed to an offline algorithm where there is a fixed input and a decision is made after reading that input, online algorithms gets pieces of the input at a time and must update its decision based on the new inputs. Note that once an online algorithm made a decision, it cannot be undone; instead, the algorithm just has to work from where its previous decision left it. 

## Hashing!

Hashing—an unordered_map for C++ users or a dict for Python users—is a way to achieve an *expected* O(1) lookup time for values. The way hashing works is a value is put through a hash function which directly maps to a value. Since we generally want to store any number in a universe U into a smaller area m, hashing will almost certainly have overlaps (by the Pigeonhole Principle); to accomodate for this, one can chain values. So as long as the chains don't grow too big (we can bound it by a constant), the lookup time can still be constant. The key way to achieve this is by picking a good hashing function. One family of hash functions is the dot product in which one selects a random vector a and the hash function is the dot product between a and the vector of interest to hash. 

## Perfect Hashing

The core idea of perfect hashing is being able to search in a static dictionary in O(1) time with a structure that takes O(n) space. First, some terminology: a static dictionary is a dictionary which doesn't change its points, so it can only really support the search operation. The runtime to do this will take expected polynomial time, so we'll be creating a Las Vegas algorithm! The core idea is we will have a hash function map to other hash functions. If we can bound the number of items that are mapped to each hash map in the first level, then the number of hash functions in the second level can have a little more space to have the total space still be O(n). We can bound that probability that two hash functions collide to calculate the expected value of the number of hash functions we must try before finding a hash function that works (mapping a bounded number to each section in the first level and mapping exactly one to each cell in each of the second levels). 


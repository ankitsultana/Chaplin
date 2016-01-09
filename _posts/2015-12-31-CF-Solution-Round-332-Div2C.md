---
title: CF Solution
---
Problem Statement: [CF Link](http://codeforces.com/contest/599/problem/C)

First of all, let's do a [Coordinate Compression](https://www.quora.com/What-is-coordinate-compression), so that the given array (say):

{% highlight text %}
5
1 100 200 300 100
{% endhighlight %}

becomes

{% highlight text %}
5
1 2 3 4 2
{% endhighlight %}

**Note:** Henceforth, I will refer to the Coordinate Compressed array as the "Original Array"

The answer for the above test case is `3`:

{% highlight text %}
[1][2][3 4 2]
{% endhighlight %}

First of all we define blocks according to the question as:

> A Block is a continuos sequence of integers such that  for a given range, the elements in the original array are a permutation of the elements in the sorted array for the same range

In simple terms, considering the previous example, the sorted array is:

{% highlight text %}
1 2 2 3 4
{% endhighlight %}

With Blocks (Note the square brackets don't change positions):

{% highlight text %}
[1][2][3 4 2]  # Original array
[1][2][2 3 4]  # Sorted array
{% endhighlight %}

Note that each block in the original array is a permutation of the elements of the *same block* in the sorted array.

Notice an interesting property, if we combine *any* two adjacent blocks, the resulting "block" also satisfies our definition of a block.

So if we merge second and third block in the original array, we get:

{% highlight text %}
[1][2 3 4 2]
{% endhighlight %}

where the resulting blocks satisfy our definition of blocks.

An even more interesting property is that, if we have an array with 2 blocks like shown below:

{% highlight text %}
a b c... [p q] r s t...
{% endhighlight %}

`[p q]` is a block
{% highlight text %}
a b c... [p q r s] t...
{% endhighlight %}

`[p q r s]` is also a block

then
{% highlight text %}
a b c... p q [r s] t...
{% endhighlight %}

`[r s]` is also a block

This can be proved using our definition of blocks.


We can use the second property to our advantage. I will explain that later. But first, let me state an algorithm:

> Start scanning the original array from left to right, if current element can be the last element of a block whose first element is the first element of the original array then increase a counter that is initially set to zero.

In the end the counter will be our answer.

How?

Well let me write the five iterations for our initial test-case:

Also let `counter = 0`

* Iteration One: The first element can be the last element of a block whose first element is the first element of the original array.
   
{% highlight text %}
[1] 2 3 4 2  # Original Array
[1] 2 2 3 4  # Sorted Array
{% endhighlight %}

{% highlight text %}
counter++; // counter is now 1
{% endhighlight %}

* Iteration Two: The second element can be the last element of a block whose first element is the first element of the original array.

{% highlight text %}
[1 2] 3 4 2  # Original Array
[1 2] 2 3 4  # Sorted Array
{% endhighlight %}

{% highlight text %}
counter++; // counter is now 2
{% endhighlight %}
* Iteration Three: The third element **cannot** be the last element of a block whose first element is the first element of the original array, *since* if we were to create a block that started at the first element and had the third element as its last element:

{% highlight text %}
[1 2 3] 4 2  # Original Array
[1 2 2] 3 4  # Sorted Array
{% endhighlight %}

`[1 2 3]` is **not** a permutation of `[1 2 2]`

* Iteration Four: Again the fourth element **cannot** be the last element, same reason as point 3.

{% highlight text %}
[1 2 3 4] 2  # Original Array
[1 2 2 3] 4  # Sorted Array
{% endhighlight %}

`[1 2 3 4]` is **not** a permutation of `[1 2 2 3]`

* Iteration Five: This element can be the last element, since:

{% highlight text %}
[1 2 3 4 2]  # Original Array
[1 2 2 3 4]  # Sorted Array
{% endhighlight %}

`[1 2 3 4 2]` is a permutation of `[1 2 2 3 4]`.

{% highlight text %}
counter++; // counter is now 3
{% endhighlight %}

> But how do all these iterations relate to our original question?

Well consider the iterations when we increment the counter, i.e. iterations *one*, *two*, *five*.

Notice the blocks formed after the respective iterations:

{% highlight text %}
a. [1] 2 3 4 5
b. [1 2] 3 4 2
c. [1 2 3 4 2]
{% endhighlight %}

Start applying the second property from the bottom.
Initially we have block *c*:

{% highlight text %}
[1 2 3 4 2]
{% endhighlight %}

Applying the second property on blocks *b* and *c*, we have the following blocks:

{% highlight text %}
[1 2][3 4 2]
{% endhighlight %}

Now apply it to block *a* and *b*:

{% highlight text %}
[1][2][3 4 2]
{% endhighlight %}

Voila!!

But wait, how do I code *the algorithm* ?

Well let me restate the algorithm:

> Scan from left to right, if the current element can be the last element of a block which opens before the first element, then increase a counter initially initialized to zero

This is the same as checking if the prefix of the *Original Array* is a permutation of the prefix of the *Sorted Array*. How? Well 

> We increase the counter *iff* the continuos sequence of integers starting at index `1` and ending at index `i` in the Original Array is a permutation of the same in the Sorted Array.

So the algorithm roughly translates to:

{% highlight cpp %}
int counter = 0;
for(int i = 1; i <= n; i++) {
    if (original_array[1..i] is a permutation of sorted_array[1..i])
      counter++
}
{% endhighlight %}

How to implement that? Well figure out yourself! 

Here is the code for reference:

{% highlight cpp %}

/*  Author: Ankit Sultana
 * * * * * * * * * * * * * * * * */

#include <iostream>
#include <cmath>
#include <fstream>
#include <map>
#include <algorithm>
#include <iterator>
#include <vector>
#include <set>
#include <cassert>
#define LL long long
#define PB push_back
#define SWAP(a,b) a^=b,b^=a,a^=b
#define MP make_pair
#define vi vector<int>
#define vll vector<LL>
#define REP(i,n)    for(__typeof(n) i = 0; i < n; i++)
#define REP1(i,n)   for(__typeof(n) i = 1; i <= n; i++)
#define REPc(i,j,n) for(__typeof(n) i = j; i <= n; i++)
#define FOR(i,c) for(__typeof(c.begin()) i = c.begin(); i != c.end(); i++)
#define F first
#define S second
#define smax(a, b) a = max(a, b)
#define smin(a, b) a = min(a, b)
#define mod 1000000007ll
#define SZ(a) int(a.size())
#define SZll(a) (LL)(a.size())
#define DECODE(x) int(x-'0')
#define ENCODE(x) char(x+'0')
#define INF 1<<28
#define what_is(x) cout<<#x<<" is "<<x<<'\n'
#define pii pair<int, int>
#define pll pair<LL, LL>
#define pil pair<int, LL>
#define pli pair<LL, int>
#define MAXN 100002

float x = 3.41;

using namespace std;

template<typename T> T gcd(T a, T b) { return b == 0?a: gcd(b, a % b); }
template<typename T> T LCM(T a, T b) { return a*(b/gcd(a, b)); }
template<typename T> T expo(T b, T e, const T &m){if(e <= 1)return e == 0?1: b;\
    return (e&1) == 0?expo((b*b)%m, e>>1, m): (b*expo((b*b)%m, e>>1, m))%m;}
template<typename T> T modinv(T a) { return expo(a, mod-2, mod); }
template<class T, class S> std::ostream& operator<<(std::ostream &os, const std::pair<T, S> &t) {
    os<<"("<<t.first<<", "<<t.second<<")";  
    return os;
}
template<class T> std::ostream& operator<<(std::ostream &os, const std::vector<T> &t) {
    os<<"["; FOR(it,t) { if(it != t.begin()) os<<", "; os<<*it; } os<<"]";
    return os;
}

int n, arr[MAXN], dupl[MAXN];
map<int, int> compress;
vi pos[MAXN];

int main()
{
    ios_base::sync_with_stdio(false);
    cin>>n;
    REP1(i,n){
        cin>>arr[i];  
        compress[arr[i]] = 0;
    }
    int cnt = 0;
    FOR(it,compress){
        it->S = ++cnt;
    }
    REP1(i,n){
        arr[i] = compress[arr[i]];  
        dupl[i] = arr[i];
    }
    sort(dupl+1, dupl+1+n);
    set<int> s;
    for(int i = n; i > 0; i--){
        pos[dupl[i]].PB(i);
    }
    int res = 0;
    REP1(i,n){
        assert(pos[arr[i]].empty() == false);
        s.insert(pos[arr[i]].back());
        pos[arr[i]].pop_back();
        if(*(s.rbegin()) == i && int(s.size()) == i){
            res++;  
        }
    }
    cout<<res<<'\n';
    return 0;
}
{% endhighlight %}

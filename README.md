# smallsort
Optimized sorts on small arrays 

The goal is to extend the Rust/C++ standard libraries with opitimized sorts.

[Optimal sorting networks](https://stackoverflow.com/questions/19790522/very-fast-sorting-of-fixed-length-arrays-using-comparator-networks) can be serialized at each level to create a total order.

First pass will probably be "godbolting" the permutations without actually running them to see what the compiler optimization state space is. Then running each class to see how they run and come up with a model of good vs bad permutations.


Branch free tricks from [GNU libc++](https://github.com/gcc-mirror/gcc/blob/16e2427f50c208dfe07d07f18009969502c25dc8/gcc/sort.cc#L160)

```c++
template<typename sort_ctx>
noinline static intptr_t
cmp1 (char *e0, char *e1, sort_ctx *c)
{
  intptr_t x = (intptr_t)e0 ^ (intptr_t)e1;
  return x & (c->cmp (e0, e1) >> 31);
}

#define CMP(e0, e1)                   \
do {                                  \
  intptr_t x = cmp1 (e1, e0, c);      \
  e0 = (char *)((intptr_t)e0 ^ x);    \
  e1 = (char *)((intptr_t)e1 ^ x);    \
} while (0)
```

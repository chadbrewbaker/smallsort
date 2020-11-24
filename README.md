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

[Vectorized](https://github.com/Vectorized/Static-Sort/blob/91ba3750205439a7b668d9e6042d993ffc2afb27/include/static_sort.h#L36) uses
```c++
template <class A, class C, int I0, int I1> struct Swap
	{
		template <class T> inline void s(T &v0, T &v1, C c)
		{
			// Explicitly code out the Min and Max to nudge the compiler
			// to generate branchless code where applicable.
			T t = c(v0, v1) ? v0 : v1; // Min
			v1 = c(v0, v1) ? v1 : v0; // Max
			v0 = t;
		}
		
		inline Swap(A &a, C c) { s(a[I0], a[I1], c); }
	};

```

(cpp-sort)[https://github.com/Morwenn/cpp-sort)
(perl generation)[http://pages.ripco.net/~jgamble/nw.html]


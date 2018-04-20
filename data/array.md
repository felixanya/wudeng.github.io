array


shuffer:

Fisher-Yates shuffle

```
-- To shuffle an array a of n elements (indices 0..n-1):
for i from n−1 downto 1 do
     j ← random integer such that 0 ≤ j ≤ i
     exchange a[j] and a[i]
```


* https://en.wikipedia.org/wiki/Fisher%E2%80%93Yates_shuffle

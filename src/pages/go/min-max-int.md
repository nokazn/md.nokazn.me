---
layout: ../../layouts/index.astro
---

# int の最大値と最小値

```go
// 符号なし 0 のビットをすべて反転 (すべて 1 にする)
const MaxUint32 = ^uint32(0)
// 符号なし 0 のビット反転させたものを右へシフトし (先頭が 0 になる)、符号付き int32 に変換
const MaxInt32 = int(^uint32(0) >> 1)

const MinUint32 = 0
const MaxInt32 = -int(^uint32(0) >> 1) - 1
```

- int32 は -2147483648 ~ 2147483647
- uint32 は 0 ~ 4294967295

## 参考

[numbers - The maximum value for an int type in Go - Stack Overflow](https://stackoverflow.com/questions/6878590/the-maximum-value-for-an-int-type-in-go)
[[go-nuts] MAX/MIN NUMBER of each integer type](https://groups.google.com/g/golang-nuts/c/a9PitPAHSSU/m/ziQw1-QHw3EJ)

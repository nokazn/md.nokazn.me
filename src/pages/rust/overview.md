---
layout: ../../layouts/index.astro
---

# Rust ことはじめ

## 配列とスライスとタプル

### 配列

- 固定長
- コンパイル時に長さが決まる
- usize 型のインデックス (0 始まり)

```rust
fn main() {
    let nums: [i32; 3] = [1, 2, 3];
    println!("{:?}", nums);
    println!("{}", nums[1]);
}
```

### スライス

- 可変長
- 実行時に長さが決まる
- usize 型のインデックス (0 始まり)

```rust

```

### タプル

```rust
fn swap(x: i32, y: i32) -> (i32, i32) {
    return (y, x);
}

fn main() {
  let result = swap(123, 321);
  println!("{} {}", result.0, result.1);
  // => 321 123

  let (a, b) = swap(result.0, result.1);
  println!("{} {}", a, b);
  // => 123 321
}
```

## パッケージとクレートとモジュール

### パッケージ

- 1つ以上のクレートを持つ

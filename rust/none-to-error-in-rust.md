---
title: "Option で None の場合に Error を返す"
description: "None の場合に Error を返す方法"
date: 2021-02-23
tags: ["Rust"]
---

`None` が発生した場合にエラーを発生させてエラー移譲したい

## `anyhow` を使う場合
`anyhow` で新しくエラーを作って返す
```rust
use anyhow::{Result, anyhow};

fn return_option() -> Option<_> {
    /* returns Option<_> type object */
}

fn return_result() -> Result<()> {

    let opt: Option<_> = return_option();
    
    // if the option is None, then return error
    let val = match opt {
        Some(v) => v,
        None => {
            // return Error
            return Err(anyhow!("There is no value."))
        }
    }

    Ok(())
}

```

## `anyhow` を使わない場合
そのうち書く

## 参考
* [anyhow - Github](https://github.com/dtolnay/anyhow#details)

# compiler-v1 work log


## イテレータからデータ構造の変更

`Iter` (さらに言えば `Peekable<Chars>`) を使って 入力から `Node` への構文解析を実装していたが、コンパイラが進展するにつれて `next` / `peek` メソッドの 1字ずつの解析に限界が出てきた. 例えば、`assign` における `=` (代入) と `equality` における `==` (同等) を 1字ずつの解析だと区別がつかない. 

なので、構文解析用のデータ構造 `Consumer` を作った.

```rust
pub struct Consumer {
    queue: Vec<char>,
    pos: usize,
}


impl Consumer {
    pub fn new(s: &str) -> Self {
        let vec = s.chars().collect::<Vec<char>>();
        Self {
            queue: vec,
            pos: 0,
        }
    }

    pub fn next(&mut self) -> Option<String> {
        if let Some(c) = self.next_char() {
            Some(c.to_string())
        } else {
            None
        }
    }

    fn next_char(&mut self) -> Option<char> {
        let res: char;
        if self.pos < self.queue.len() {
            res = self.queue[self.pos];
            self.pos +=1;
            Some(res)
        } else {
            None
        }
    }

    pub fn next_n(&mut self, n: usize) -> Option<String> {
        let mut vec = Vec::new();
        for _ in 0..n {
            if let Some(c) = self.next_char() {
                vec.push(c);
            } else {
                return None;
            }
        }
        let res = vec.into_iter().collect::<String>();
        Some(res)

    }

    pub fn next_until_space(&mut self) -> Option<String> {
        let mut vec = Vec::new();
        loop {
            match self.peek_char() {
                Some(c) => {
                    match c {
                        ' ' | '\t' => {
                            break;
                        }
                        _ => {
                            self.next();
                            vec.push(c)
                        }
                    }
                },
                None => {
                    break;
                }
            }
        }
        let res = vec.into_iter().collect::<String>();
        Some(res)
    }

    pub fn peek(&self) -> Option<String> {
        if let Some(c) = self.peek_char() {
            Some(c.to_string())
        } else {
            None
        }

    }

    fn peek_char(&self) -> Option<char> {
        let res: char;
        if self.pos < self.queue.len() {
            res = self.queue[self.pos];
            Some(res)
        } else {
            None
        }
    }

    pub fn peek_n(&self, n: usize) -> Option<String> {
        let mut vec = Vec::new();
        for i in 0..n {
            if self.pos+i < self.queue.len() {
                vec.push(self.queue[self.pos+i]);
            } else {
                return None;
            }
        }
        let res = vec.iter().collect::<String>();
        Some(res)
    }
    
    pub fn to_usize(&mut self) -> Option<usize> {
        let mut result: usize = 0;

        // check whether the first char is number
        match self.peek_char() {
            Some(c) => {
                match c {
                    '0'..='9' => {
                    }
                    _ => {
                        return None;
                    }
                }
            }
            None => {
                return None;
            }
        }

        loop {
            match self.peek_char() {
                Some(c) => {
                    match c {
                        '0'..='9' => {
                            self.next();
                            let n = c.to_digit(10).unwrap() as usize;
                            result = result*10 + n;
                        }
                        _ => {
                            break;
                        }
                    }
                }
                None => {
                    break;
                }
            }
        }
        return Some(result);
    }

    pub fn skip_space(&mut self) {
        loop {
            if let Some(c) = self.peek_char() {
                if " \t".contains(c) {
                    self.next_char();
                } else {
                    break;
                }
            } else {
                break;
            }
        }
    }

}

```

`Iter` の `peek` / `next` では 1字先までしか対応できなかったため、それらを拡張した `peek_n` / `next_n` を実装し、複数文字を一度に `peek` / `next` できるようにした.

また、それに伴って、 
- 文字列先頭の空白文字をスキップする `skip_space`
- 空白文字まで読み進める `next_until_space` 
- 文字列先頭の数字を  `usize` へ変換して読み進める`to_usize` 
といった便利メソッドも実装した.

## tokenize と解析

tokenize と構文木の組み立てが別れていたほうがいいような気がする

## `String` と `&str` と `Vec<char>` の相互変換

いままで自分のメモがなかったので

`String` -> `&str`
```rust
let s1 = String::from("Hi~");
let s2: &str = &s1; // String to &str
let s3 = &s1 as &str; // String to &str
```

`&str` -> `String`
```rust 
let s1 = "Hi~";
let s2 = String::from(s1); // &str to String
let s3 = s1.to_string(); // &str to String
```

`String` -> `Vec<char>`
```rust
let s1 = String::from("Hi~");
let s2 = s1.chars().collect::<Vec<char>>();
```

`&str` -> `Vec<char>`
```rust
let s1 = "Hi~";
let s2 = s1.chars().collect::<Vec<char>>();
```

`Vec<char>` -> `String`
```rust
let s1 = vec!['H', 'i', '~'];
let s2 = s1.iter().collect::<String>();
```

#### 参考
- [Rustのcharと&strとStringが難しい](https://qiita.com/kujirahand/items/fcb4f75dbdbfaf36aa75)



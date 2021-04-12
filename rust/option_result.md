

## `Option<T>`

値が存在しない場合も考慮する必要のある値を表す列挙型.  

入力に依存する場合に活用可能.  
```rust
fn main() {
    let test_str = String::from(""); 
    let value = receive_value(test_str);
    match_string(value);
    
    let test_str = String::from("Hello");
    let value = receive_value(test_str);
    match_string(value);
}

fn match_string(v: Option<String>) {
    match v {
        Some(result) => println!("{}", result),
        None => println!("detected empty string."),
    }
}

fn receive_value(s: String) -> Option<String> {
    if s.len() != 0 {
        Some(s)
    } else {
        None
    }
}
```

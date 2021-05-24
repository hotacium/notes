---
title: "Rust でパスワードによるファイル暗号化"
description: "Rust による、 PBKDF2 と AES-GCM を用いたファイル暗号化についてのメモです."
date: 2021-02-20
tags: ["Rust", "Cryptography"]
---

このメモでは PBKDF2 や AES-GCM のアルゴリズム自体の実装は取り扱いません. Rust の外部クレートを利用したプログラムです. 


免責事項: このメモを参考にしたことによる損害に対して一切責任を負いません.  

## `Cargo.toml`
以下のクレートを導入しています.

`Cargo.toml`
```toml
[dependencies]
# AES-GCM
aes-gcm = { version = "0.8.0", features = ["std"] }
# PBKDF2
pbkdf2 = { version = "0.7", features = ["std"] }
password-hash = { version = "0.1.1", features = ["std"] } # in order to use std feature
# PRNG
rand = "0.8.3"
anyhow = "1.0"
```
`aes-gcm` ([Github](https://github.com/RustCrypto/AEADs/tree/master/aes-gcm)), `pbkdf2` ([Github](https://github.com/RustCrypto/password-hashes/tree/master/pbkdf2)) クレートは [Rust Crypto](https://github.com/RustCrypto) のものを使用しています.  

`rand` クレートは乱数を生成するために使用しています. `rand` の標準的な暗号生成器 (`rand::rngs::StdRng`) は現時点 (2021 年 2 月) において、CSPRNG (暗号論的疑似乱数生成器) となっているため、そのまま用いています. しかし、アルゴリズムは将来変更される可能性もあります.  
詳しくは、[rand::rngs::StdRng](https://rust-random.github.io/rand/rand/rngs/struct.StdRng.html).  

`anyhow` クレートは Rust でのエラー処理をより容易にするためのクレートです.  
[anyhow](https://github.com/dtolnay/anyhow)

## Password-Based Key Derivation Function Version 2.0 (PBKDF2)
PBKDF2 は鍵導出関数です. AES-GCM の暗号化鍵を生成するために使用します. PBKDF2 を使う意義などについて詳細は割愛しますが、 AES-GCM の暗号化鍵をユーザーのパスワードを用いて生成することが可能になる他、AES-GCM の安全性の向上に寄与します.  

参考: [PBKDF2 - Wikipedia](https://ja.wikipedia.org/wiki/PBKDF2)

```rust
use pbkdf2::{password_hash::PasswordHasher, Pbkdf2};
use anyhow::Result;

fn pbkdf(passwd: &str, salt: &str) -> Result<(Vec<u8>, String)> {
    // password to byte data
    let passwd = passwd.as_bytes();
    
    // generate password hash
    let password_hash = Pbkdf2.hash_password_simple(passwd, salt)?;
    
    // get hash itself from struct
    let hash = password_hash.hash.unwrap();
    let hash = hash.as_bytes();
    let hash_vec = out_hash.to_vec();
    
    // get salt itself from struct
    let salt = password_hash.salt.unwrap().as_str();
    let salt = String::from(salt);
    
    // return 
    Ok((hash_vec, salt))
}
```

実装の参考
* [pbkdf2](https://docs.rs/pbkdf2/0.7.3/pbkdf2/)
* [simple.rs](https://github.com/RustCrypto/password-hashes/blob/master/pbkdf2/tests/simple.rs)

## Advanced Encryption Standard - Galois/Counter Mode (AES-GCM)
AES-GCM は AES 暗号の亜種で、CTR を用いた暗号化と GMAC による完全性の確認を行う暗号化方法です. 

参照:
* [Advanced Encryption Standard - Wikipedia](https://ja.wikipedia.org/wiki/Advanced_Encryption_Standard)
* [Galois/Counter Mode - Wikipedia](https://ja.wikipedia.org/wiki/Galois/Counter_Mode)

#### 暗号化
```rust
use aes_gcm::{
    Aes256Gcm,
    aead::{Aead, NewAead, generic_array::GenericArray},
};
// error handling crate
use anyhow::Result;

// AES-GCM
fn aes_encrypt(contents: &[u8], key: &[u8], nonce: &[u8]) -> Result<Vec<u8>> {
    let key = GenericArray::from_slice(key);
    let nonce = GenericArray::from_slice(nonce);
    
    // encryption
    let cipher = Aes256Gcm::new(key);
    let cipherdata = cipher.encrypt(nonce, contents.as_ref())?;
    
    // return
    Ok(cipherdata)
}
```
#### 復号(化)
```rust
use aes_gcm::{
    Aes256Gcm,
    aead::{Aead, NewAead, generic_array::GenericArray},
};
// error handling crate
use anyhow::Result;


// AES-GCM decryption
fn aes_decrypt(cipher_data: &[u8], key: &[u8], nonce: &[u8]) -> Result<Vec<u8>> {
    let key = GenericArray::from_slice(key);
    let nonce = GenericArray::from_slice(nonce);
    
    // decryption
    let cipher = Aes256Gcm::new(key);
    let data = cipher.decrypt(nonce, cipher_data)?;
    Ok(data)
}
```
## 実装

今回は nonce と salt を暗号化ファイルと同じ場所に保存しています. 保存場所については [Using Salts, Nonces, and initialization Vectors](https://www.oreilly.com/library/view/secure-programming-cookbook/0596003943/ch04s09.html) を参考にしました. 

```rust
use aes_gcm::Aes256Gcm;
use aes_gcm::aead::{
    Aead, NewAead, generic_array::GenericArray
};
use pbkdf2::{
    password_hash::PasswordHasher,
    Pbkdf2
};
use rand::{thread_rng, Rng, distributions::Alphanumeric};
use std::fs;
use anyhow::Result;
use std::str;

const NONCE_LEN: usize = 12;
const SALT_LEN: usize = 32;

fn random_string(length: usize) -> String {
    thread_rng()
        .sample_iter(&Alphanumeric)
        .take(length)
        .map(char::from)
        .collect()
}

fn read_file(src: &str) -> Result<Vec<u8>> {
    let contents: Vec<u8> = fs::read(src).unwrap();
    Ok(contents)
}

fn write_file(dst: &str, contents: &[u8]) -> Result<()> {
    fs::write(dst, contents)?;
    Ok(())
}

fn pbkdf(passwd: &str, salt: &str) -> Result<(Vec<u8>, String)> {
    // input 
    let passwd = passwd.as_bytes();
    
    // generate password hash
    let password_hash = Pbkdf2.hash_password_simple(passwd, salt)?;
    
    // get hash itself from struct
    let out_hash = password_hash.hash.unwrap();
    let out_hash = out_hash.as_bytes();
    let hash_vec = out_hash.to_vec();
    
    // get salt itself from struct
    let out_salt = password_hash.salt.unwrap().as_str();
    let salt_string = String::from(out_salt);
    
    // return 
    Ok((hash_vec, salt_string))
}

fn aes_encrypt(contents: &[u8], key: &[u8], nonce: &[u8]) -> Result<Vec<u8>> {
    let key = GenericArray::from_slice(key);
    let nonce = GenericArray::from_slice(nonce);
    
    let cipher = Aes256Gcm::new(key);
    let cipherdata = cipher.encrypt(nonce, contents)?;
    Ok(cipherdata)
}

fn encrypt(src: &str, dst: &str, passwd: &str) -> Result<()>{
    // read file
    let contents = read_file(src)?;
    
    // generate salt
    let salt_string = random_string(SALT_LEN);
    let salt = salt_string.as_bytes();
    
    // generate key
    let pbkdf_tuple = pbkdf(passwd, &salt_string)?;
    let key = pbkdf_tuple.0;
    
    // generate nonce
    let nonce_string = random_string(NONCE_LEN);
    let nonce = nonce_string.as_bytes();

    // encrypt contents with AES-GCM 
    let mut cipher_data = aes_encrypt(&contents, &key, nonce)?;
    
    // append nonce and salt
    let mut nonce_vec = nonce.to_vec();
    cipher_data.append(&mut nonce_vec);
    let mut salt_vec = salt.to_vec();
    cipher_data.append(&mut salt_vec);
    
    // write
    write_file(dst, &cipher_data)?;
    
    // return 
    Ok(())
}


// AES-GCM decryption
fn aes_decrypt(cipher_data: &[u8], key: &[u8], nonce: &[u8]) -> Result<Vec<u8>> {
    let key = GenericArray::from_slice(key);
    let nonce = GenericArray::from_slice(nonce);
    
    // decryption
    let cipher = Aes256Gcm::new(key);
    let data = cipher.decrypt(nonce, cipher_data)?;
    Ok(data)
}

fn decrypt(src: &str, dst: &str, passwd: &str) -> Result<()> {
    // read file
    let mut encrypted_data = read_file(src)?;
    
    // extract salt and nonce from encrypted data
    let mut appended_data = encrypted_data.split_off(encrypted_data.len()-SALT_LEN-NONCE_LEN);
    // salt
    let salt_vec = appended_data.split_off(appended_data.len()-SALT_LEN);
    let salt_string = std::str::from_utf8(&salt_vec).unwrap();
    // nonce
    let nonce = appended_data;
    
    // generate key from password
    let pbkdf_tuple = pbkdf(passwd, &salt_string)?;
    let key = pbkdf_tuple.0;
    
    // decrypt with AES-GCM
    let data = aes_decrypt(&encrypted_data, &key, &nonce)?;

    // write
    write_file(dst, &data)?;
    
    // return 
    Ok(())
}

fn main() {
    write_file("test.txt", "Cryptography! This is a test file. オラーーーーーーーーーーーーーーーー！！！！！！！！！！！！！！！！！！！".as_ref()).unwrap();
    println!("[*] Encrypting...");
    encrypt("test.txt", "test.txt.cry", "passwd").unwrap();
    println!("[*] Decrypting...");
    decrypt("test.txt.cry", "test.txt", "passwd").unwrap();
}
```

## TODO
* 他の暗号化アルゴリズムに対応させるために抽象化を行う
* インターフェースを実装する
* 複数ファイルへの並行暗号化を実装する
* エラーハンドリングを強化する

## 参考
* 暗号技術入門 秘密の国のアリス
* [Crate aes_gcm](https://docs.rs/aes-gcm/0.8.0/aes_gcm/)
* [Crate pbkdf2](https://docs.rs/pbkdf2/0.7.3/pbkdf2/)
* [Using Salts, Nonces, and initialization Vectors](https://www.oreilly.com/library/view/secure-programming-cookbook/0596003943/ch04s09.html)

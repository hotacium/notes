---
title: "Rust のサウンドライブラリ"
description: "Rust のサウンドライブラリについてのメモ"
date: 2021-03-20
tags: ["Rust"]
---

Rust で音声を再生するときに使うクレートのメモです.  

以下のクレートについての記載があります. 
* soloud
* rodio

## soloud
C/C++ のポータブルなオーディオエンジンの Rust バインディング. soloud のページ ([here](https://sol.gfxile.net/soloud/index.html)) にも easy to use と書かれている通り、音声関連の知識がなくても使い勝手のいいエンジン. 

#### 例
ピンクノイズを再生
```rust
use soloud::*;

fn main() -> Result<(), Box<dyn std::error::Error>> {
    
    let mut sl = Soloud::default()?;
    
    // ノイズ音源の作成
    let mut noise = audio::Noise::default();
    // ピンクノイズをセット
    noise.set_type(audio::NoiseType::Pink);
    
    // 再生
    let handle = sl.play(&noise);
    // 音量の調節
    sl.set_volume(handle, 0.1);

    // 再生終了まで main スレッドが終了しないようループ
    while sl.voice_count() > 0 {
        // CTRL-C to terminate
        std::thread::sleep(std::time::Duration::from_millis(100));
    }

    Ok(())
}

```

音声ファイルを再生
```rust
use soloud::*;

fn main() -> Result<(), Box<dyn std::error::Error>> {
    let mut sl = Soloud::default()?;

    let mut wav = audio::Wav::default();
    
    // wav ファイルの再生
    wav.load(&std::path::Path::new("sample.wav"))?;
    
    // 再生
    sl.play(&wav); 

    // calls to play are non-blocking, so we put the thread to sleep
    // main スレッドが終了しないようループ
    while sl.voice_count() > 0 {
        std::thread::sleep(std::time::Duration::from_millis(100));
    }
    
    // mp3 ファイルの再生
    wav.load(&std::path::Path::new("Recording.mp3"))?;
    
    sl.play(&wav);
    while sl.voice_count() > 0 {
        std::thread::sleep(std::time::Duration::from_millis(100));
    }

    Ok(())
}
```

#### 参照
* [soloud-rs github](https://github.com/MoAlyousef/soloud-rs)
* [soloud-rs docs](https://docs.rs/soloud/0.3.4/soloud/index.html)
* [soloud](https://sol.gfxile.net/soloud/index.html)

## rodio
Rust で音を扱う際におそらく最もポピュラーなクレート.  
Ubuntu では `libasound2-dev` をインストールする必要がある (依存クレートである `capl` に必要なため). Windows ではデフォルトのオーディオエンジンを使用する限り他のパッケージのインストールは不要 (cpal の readme.md より. 筆者未検証).

#### 例

* mp3, flac ファイルの再生
* `rodio::source::from_iter` を用いて無限の長さの音を生成可能 (参照: [rodio::source::SineWave](https://docs.rs/rodio/0.13.0/rodio/source/struct.SineWave.html))

#### 参照
* [github](https://github.com/RustAudio/rodio)
* [docs](https://docs.rs/rodio/0.13.0/rodio/)




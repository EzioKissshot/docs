# Rust

## 设置代理

tag: macos windows

`~/.cargo/config`中设置

```
[http]
proxy = "127.0.0.1:1080"

[https]
proxy = "127.0.0.1:1080"
```

or

```
[http]
proxy = "socks5://127.0.0.1:1080"

[https]
proxy = "socks5://127.0.0.1:1080"
```

## 设置国内源

!> 国内源可能存在某些包没有的情况，最好还是走代理

`~/.cargo/config`中设置

```
[source.crates-io]
registry = "https://github.com/rust-lang/crates.io-index"
replace-with = 'ustc'
[source.ustc]
registry = "git://mirrors.ustc.edu.cn/crates.io-index"
```
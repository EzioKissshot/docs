# MacOS

## 命令行代理
.zshrc
```
alias enable_proxyhttp="export http_proxy=http://127.0.0.1:1087;export https_proxy=https://127.0.0.1:1087"
alias disable_proxyhttp="unset http_proxy;unset https_proxy"

alias enable_proxy="export ALL_PROXY=socks5://127.0.0.1:1086"
alias disable_proxy="unset ALL_PROXY"
```

## oh-my-zsh配置
.zshrc
```
ZSH_THEME="agnoster"

plugins=(
  git
  z
  d
  zsh-syntax-highlighting
  zsh-autosuggestions
  extract
  colored-man-pages
)
```
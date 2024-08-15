# macOS Terminal 设置代理

## 背景
今天接触了新工具——fnm，打算尝试下，使用 `brew install fnm` 时发现报错，无法安装。
![fnm install error](<install-fnm-error.png>)

`curl: (28) Failed to connect to formulae.brew.sh port 443 after 300008 ms: Couldn't connect to server`

## 排查过程
首先 google 和问 chatgpt，问的方向不对，是直接把这段报错丢进去问，只看到怎么解决 github 连接失败的问题，可见《git 推送代码到 github 报错问题解决》。

浏览器访问 `formulae.brew.sh` 也是正常的，此时还是一脸懵。

又搜了一会，灵光乍现，是不是由于 Terminal 没有设置代理的原因，于是 google :
"terminal proxy mac 设置", 找到[正确答案](https://limbopro.com/archives/MacOS_terminal_proxy_setting.html)。

## 解决问题
跟着 [正确答案](https://limbopro.com/archives/MacOS_terminal_proxy_setting.html) 来就行了。
1. 由于我终端用的是 `zsh`。打开 Terminal，输入：
`vi ~/.zshrc `

2. 粘贴如下代码：（其中 alias proxy 的 proxy 与 unproxy 可以修改成你喜欢的代称）
```
alias proxy="
    export http_proxy=socks5://127.0.0.1:7890;
    export https_proxy=socks5://127.0.0.1:7890;
    export all_proxy=socks5://127.0.0.1:7890;
    export no_proxy=socks5://127.0.0.1:7890;
    export HTTP_PROXY=socks5://127.0.0.1:7890;
    export HTTPS_PROXY=socks5://127.0.0.1:7890;
    export ALL_PROXY=socks5://127.0.0.1:7890;
    export NO_PROXY=socks5://127.0.0.1:7890;"
alias unproxy="
    unset http_proxy;
    unset https_proxy;
    unset all_proxy;
    unset no_proxy;
    unset HTTP_PROXY;
    unset HTTPS_PROXY;
    unset ALL_PROXY;
    unset NO_PROXY"
```
然后按 `esc`，输入 `:wq` 保存退出。

3. 激活当前配置，使其生效。
`source ~/.zshrc`

4. 测试效果
```
proxy # 开启代理
brew install fnm
```
![fnm install success](<install-fnm-success.png>)
成功！





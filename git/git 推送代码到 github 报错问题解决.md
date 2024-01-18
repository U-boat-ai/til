# git 推送代码到 github 报错问题解决

浏览器访问 github 仓库是可以正常访问的，就是死活推送不上去，报错信息如下：
`Failed to connect to github.com port 443 after 75026 ms: Couldn't connect to server`

google 搜了下，发现是由于命令行在拉取、推送代码时并没有使用代理的原因。

配置 `Http` 代理:
```
git config --global http.proxy 127.0.0.1:7890
git config --global https.proxy 127.0.0.1:7890
```

其中端口号 `7890` 是需要根据代理工具配置上的来。

### 参考资料
- [Git报错： Failed to connect to github.com port 443 解决方案](https://blog.csdn.net/zpf1813763637/article/details/128340109)
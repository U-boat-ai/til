# Android 13 WebView 下载附件报错问题解决

### 背景
项目中发现 Android 13 的机型下载文件会报错，具体报错如下：
`W/System.err: java.io.FileNotFoundException: /storage/emulated/0/xx/Download/File/nt;filename*=UTF-8''升级功能清单.doc: open failed: EPERM (Operation not permitted)`

这个报错很好理解，从 Android 11（API level 30）开始，由于强制分区存储，之前的相关API：`Environment.getExternalStorageDirectory`、`Environment.getExternalStoragePublicDirectory` 无法使用了。

### 解决
针对这个问题，我的解决方法是在 Android 10 之前，还是用之前的 File API：`Environment.getExternalStorageDirectory`，对于 Android 10 及之后的版本，使用 `Context.getExternalFilesDir` API。


```java
/**
 * 获取文件本地路径
 */
private String getFilePath(String fileName) {
        String dirPath = "";
        if (Build.VERSION.SDK_INT > 29) {
            dirPath = mContext.getExternalFilesDir(null).getAbsolutePath() + File.separator + "xx" + File.separator + "oa";
        } else {
            dirPath = Environment.getExternalStorageDirectory().getAbsolutePath() + File.separator + "xx" + File.separator + "oa";
        }

        File dir = new File(dirPath);
        if (!dir.exists()) {
            dir.mkdirs();
        }

        return dir.getAbsolutePath() + File.separator + fileName;
    }
```

但是随即会引出另外一个问题，我想下载之后，能够通过合适的 App 打开下载完成的文件，方便用户查看。这时候打开文件就是报错，提示“文件不存在”，这也很好理解，我们上面采用的是非共享存储空间，其他应用访问不了。

解决方法是通过 Android FileProvider 生成内容 URI，赋予接收应用临时访问权限，实现安全共享文件。

```java
public static void openFile(Context context, String filePath) {
        File file = new File(filePath);
        Intent intent = new Intent();
        // 这里 Flag 也必须加上，否则会提示：请在文件管理器中选择正确的文件打开
        intent.addFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION | Intent.FLAG_GRANT_WRITE_URI_PERMISSION);
        intent.setAction(Intent.ACTION_VIEW);
        String type = getMIMEType(file);
        intent.setDataAndType(toUri(context, filePath), type);
        try {
            context.startActivity(intent);
        } catch (Exception e) {
            ToastUtil.show("没有合适的应用打开此类型文件！");
        }
    }

public static Uri toUri(Context context,String filePath) {
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.N) {
            return FileProvider7.getUriForFile(context,  new File(filePath));
        }
        return Uri.fromFile(new File(filePath));
    }
```

上面的 FileProvider7 是项目中定义的，也可以使用以下写法：
```java
public static Uri toUri(Context context,String filePath) {
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.N) {
            return FileProvider.getUriForFile(context, context.getApplicationInfo().packageName + ".fileprovider", new File(filePath));
        }
        return Uri.fromFile(new File(filePath));
    }
```

### 意外收获
在解决这个问题的时候，发现之前获取文件名是有待优化的。之前就是通过 `DownloadListener.onDownloadStart(String url, String userAgent, String contentDisposition, String mimetype,long contentLength)` 中的 `contentDisposition` 去解析文件名的，实际项目中就很可能返回空字符串，导致无法获取到文件名。

随手进行了一下优化：
```java
String fileName  = URLUtil.guessFileName(url, contentDisposition, mimeType);
```

### 参考资料
- https://www.jianshu.com/p/75ae35d95b6d

- [Android存储权限适配与读写媒体文件](https://medium.com/@wanxiao1994/android%E5%AD%98%E5%82%A8%E6%9D%83%E9%99%90%E9%80%82%E9%85%8D%E4%B8%8E%E8%AF%BB%E5%86%99%E5%AA%92%E4%BD%93%E6%96%87%E4%BB%B6-5c2004a62dfa)

- [Android 文件共享](https://blog.csdn.net/yingaizhu/article/details/118708571)

- [webview进行下载踩坑记录](https://blog.csdn.net/azhoup/article/details/89533697)

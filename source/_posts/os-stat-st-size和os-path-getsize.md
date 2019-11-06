---
title: os.stat().st_size 和 os.path.getsize()
comments: true
categories: 笔记
tags: python
abbrlink: 435f58aa
date: 2019-11-04 13:28:20
---

---

## os.stat(file).st_size

os.stat()是返回读取指定文件的相关属性，然后用stat模块来处理。

## os.path.getsize(file)

返回指定文件 file 的大小，当指定的路径不存在或者不可访问，将会抛出异常 os.error。
实现形式：
```
def getsize(filename):
    """Return the size of a file, reported by os.stat()."""
    return os.stat(filename).st_size
```

## 总结
os.stat().st_size 和 os.path.getsize()这两种方法实际上并没有什么不同
如果你想使你的性能最优，使用 os.stat() ,先检查路径是否是文件，再调用 st_size，这样只是用了一次stat指令。
如果想要用 os.path.getsize() ,则必须要使用os.path.isfile()来判断是不是文件，再去使用。
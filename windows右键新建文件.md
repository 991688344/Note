# win10 右键新建文件添加自定义文件

[参考链接](http://www.pianshen.com/article/9969623602/)

1. win+r 打开regedit
2. 进入HKEY_CLASSES_ROOT 中的 .md (MarkDown文件实例，添加其他格式的文件就找其他文件的后缀)
3. 右击.md 新建项 `ShellNew`
4. 点击`ShellNew`，右边空白处新建字符串值，名为`NullFile`



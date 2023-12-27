# 我的vscode写作环境

首先隐藏主侧栏，并把关闭主侧栏的快捷键设为`ctrl+alt+/`，这样就可以更好的写作了。`ctrl+0`可以打开主侧栏。

设置打开大纲的快捷键为`ctrl+alt+t`。

`ctrl+1`打开编辑器左侧，即你正在写的文章。

使用的扩展有：
- Markdown All in One
- Paste Image

## Markdown All in One
这个不用怎么配置。

了解一下编辑快捷键：
- `ctrl+b`：加粗
- `ctrl+i`：斜体
- `alt+S`：删除线
- `ctrl+shift+]`：标题加大
- `ctrl+shift+[`：标题减小
- `ctrl+M`：插入数学公式
- `ctrl+K+V`：侧边预览


## Paste Image
这个需要配置一下，配置如下：
```json
    "pasteImage.basePath": "${projectRoot}",
    "pasteImage.path": "${projectRoot}/assets$/{currentFileNameWithoutExt}",
    "pasteImage.prefix": "../"
```
`pasteImage.basePath`是图片的根目录，这里设置为项目根目录。

`pasteImage.path`是图片的存放路径，图片统一放在`assets`目录下，然后再根据文章的名称创建一个目录，该文章的图片都放在该目录下。

`pasteImage.prefix`是图片的前缀，这里设置为`../`。因为当粘贴图片时，图片存在了`assets`目录下，而前面设置了`pasteImage.basePath`为项目根目录，所以出来的结果是 `assets/${currentFileNameWithoutExt}`，而我们需要的是`../assets/${currentFileNameWithoutExt}`，所以需要设置`pasteImage.prefix`为`../`。

最后，把快捷键设置为`ctrl+alt+v`。


后续可以尝试加入 vim 插件，这样就可以纯键盘操作了。
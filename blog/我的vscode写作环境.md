# 我的vscode写作环境

工作区
- blog——各种md文章
  - assets
    - 文章同名文件夹——文章图片
- daily——日常笔记 ignore
  - assets
    - 文章同名文件夹——文章图片
- readme.md——文章分类定位


实现以下功能：
- GitHub仓库同步，其他设备可以查看
- vscode的copilot插件，提供AI提示！！
- vscode各种补全和快捷键 

首先隐藏主侧栏，并把关闭主侧栏的快捷键设为`ctrl+alt+/`，这样就可以更好的写作了。`ctrl+0`可以打开主侧栏。

设置打开大纲的快捷键为`ctrl+alt+t`。

`ctrl+shift+.`聚焦导航，方向右展开

`ctrl+1`打开编辑器左侧，即你正在写的文章。

使用的扩展有：
- Markdown All in One
- Paste Image
- Markdown Preview Mermaid Support
- Markdown Preview Github Styling
- Markdown Footnotes

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
"pasteImage.insertPattern": "<img src=\"${imageFilePath}\" alt=\"${imageFileName}\" style=\"zoom:50%\" />",   // 50%缩放
"pasteImage.basePath": "${currentFileDir}",
"pasteImage.path": "${currentFileDir}/assets/${currentFileNameWithoutExt}",
```
`pasteImage.basePath`是图片的根目录，这里设置为项目根目录。

`pasteImage.path`是图片的存放路径，图片统一放在`assets`目录下，然后再根据文章的名称创建一个目录，该文章的图片都放在该目录下。


最后，把快捷键设置为`ctrl+alt+v`。

## Markdown Preview Mermaid Support
为了让markdown展示支持mermaid，插件不用配置，不需要这个功能的话可以不安装。

## Markdown Preview Github Styling
这个插件可以让markdown的展示效果为github的样式，我发现没有它的时候markdown预览和编辑焦点位置有点不一样，安装了这个插件之后就一致了。而且GitHub的样式也很好看。不需要可以不安装。

> 好像有问题，导致前面的mermaid要刷新才能显示。

## Markdown Footnotes
这个插件可以让markdown支持脚注，不需要可以不安装。

---
后续可以尝试加入 vim 插件，这样就可以纯键盘操作了。
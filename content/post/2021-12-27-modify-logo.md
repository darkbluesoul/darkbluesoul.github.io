---
title:  inkscape修改logo
date:   2021-12-27 19:32:10 +0800
description: 第一次用inkscape，做了个很简单的logo.
tags: [logo, inkscape, AI, path]
categories: [Jekyll]
key: 2021-12-27-modify-logo

---
建好博客第一件事情就是改logo，毕竟不好顶着别人的名字啊，一开始想着简单弄个好了。

SVG不知道用什么打开，搜了下是AI，再搜Linux替代AI的软件，就找到inkscape了。之前只用过PS，好在是中文的？想不起来是不是中文的了，就按着原logo.svg的尺寸新建了个，文本输入DARKBLUESOUL，改了个字体，保存替换了原来的文件。`jekyll
 serve`，本地看看很满意，就push上去了。

<!--more-->

![DARKBLUESOULlast.png](https://s2.loli.net/2021/12/28/RBtfcoFNXbVxQjJ.png)

换台电脑看就又不一样了，字体没了，变成黑体的样子了，想着也无所谓，先这样吧。

![DARKBLUESOUL.png](https://s2.loli.net/2021/12/28/y17JN6VQifajCkD.png)

过了几天无聊了，就仿着原来的logo也弄了个圈圈，里面么就要把D、B、S都要包含进去，就想了上面这样的图。字也分成两排，不会配色，就把原来的logo打开吸点颜色，弄好了看看也还行。结果同事过来一看，“哟，这是SB嘛，可以可以”。不管他，我还是放上去了，又是没字体，字跟图都挤一块儿了，那我有点受不了了。想着是不是删格化图层可以试试，可是inkscape没有这功能，就以为AI会有，又在另外电脑下了个AI，可惜也没找到删除化功能。

既然下了AI，那就在AI上弄呗。配合以前做的favicon的黑红做了下面这张，其实图片本来还挺满意的，只是存好再看还是因为字体丢失就很难看。无聊点了个右键，发现了新大陆，可以把对象导出的哦，就把字啊图啊，全部导出PNG，然后再插回到SVG里，字的样子是不变了，只是，好像加载很慢啊？

![MVM-logo-full.png](https://s2.loli.net/2021/12/28/6Dpia73tCUN5HLs.png)

搜了下字体丢失，说是要转路径，忘了哪篇了，本来想着所有搜索的都要存着，只是找东西的时候就忘了。

AI应该是叫建立轮廓，终于可以了，字体都在了，本地看看也还行，只是等我push到github多看了几次，就又觉得难看了。

两台电脑我用坚果云同步的，已经拷贝到img目录我就懒得再弄回去了，直接用inkscape编辑了，已经弄的很烦了，还是用了最早的字体全名，想着找轮廓，结果没找到，再仔细一看，菜单里本来就有转换路径，很方便的，只是原来我不知道这个干嘛用的。

![DARKBLUESOULlast.png](https://s2.loli.net/2021/12/28/RBtfcoFNXbVxQjJ.png)

所以现在我又回到最早的版本了，现在是路径了，不会再丢字体了。也好的，兜来兜去，知道的又多了点，不是么？

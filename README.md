# SI 100B Recitation SOP

## 基本信息

为了方便标准化所有习题课slides，本仓库提供了一个基于reveal-md的slides工作流。该工作流可提供高效、自动排版及风格统一的slides制作。

## 如何使用

只需按照Markdown语法（和极少html语法）制作slides，随后git push即可一键渲染slides到GitHub Pages。也可以使用pdf版本。

仓库中提供了一个Example文件夹，可以参考示例快速上手。以下提供了基本功能的使用介绍：

### 使用模板

直接将示例的`Example.md`复制一份即可。

### 修改标题页

模板开头的`makeTitle`处可以自定义slides的标题页。

### 创建一页（小）标题slide

空行输入`<!--s-->`，随后使用一级标题表示当页标题。

### 创建一页内容slide

空行输入`<!--v-->`，随后使用二级标题表示当页标题。随后直接用正常Markdown语法撰写正文内容即可。

### 插入图片

`<img src="图片目录名/图片名.图片后缀" width="一个百分数" style="display: block; margin: 0 auto;">`

### 逐行动画

逐行动画可能是slides中需求最高的动画。本仓库的工作流允许你使用非常简单的语法实现这一特性。~~可能不是特别完美~~

如果你希望整页slide的内容都有逐行动画，则只需要在slide的分隔符（`<!--v-->`等）后添加一行`<!-- .slide: class="fragmented-lists" -->`即可。

如果你希望自定义：

- 对于正常的一行，在行末添加`<!-- .element: class="fragment" -->`即可。
- 对于使用了**加粗**、~~删除线~~等语法的情况，可以使用如下形式添加动画：

  ```html
  <span>内容~~和被删除的内容~~</span
  ><!-- .element: class="fragment" -->
  ```

## 写好slides之后

直接push你的更新，等待渲染，几分钟之后你的slides会在 https://si100b-26spring.github.io/SI100B_Spring_2026_PP_Recitation/ 出现。

## FAQ

### 如何打印去掉逐行动画的pdf

去掉链接末尾可能出现的 `/#/` 部分，加上 `?print-pdf`，然后使用浏览器的打印功能，可以将网页保存为 PDF 文件。

### 自定义主题

我们可以尝试写一个新的主题一起用。需要修改assets/custom.css，有意戳我。

> This document is written by Yuhan Cao. The workflow is developed by Hengyu Ai and Zebang He. This project is modified by Coinred5965 for SI100B 2025 Fall. Many thanks for their contribution.

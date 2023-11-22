# 实验三

## 时间

*直至期末考试前，具体时间待确定。*

## 说明

- 完成选做内容将标记上 bonus，作为期末评分的参考之一；
- 对输入和输出格式不做要求，你既可以按照题目描述的（如果有）来实现，也可以用自己喜欢的方式；
- 在完整实现功能要求的前提下，技术细节不必拘泥于题中描述的要求，合理即可；
- **本次实验需要提交实验报告，具体要求请参照 QQ 群内的说明。**

## Huffman 文件压缩解压程序

### 基本说明

你需要利用 Huffman 编码原理实现一个支持压缩和解压的程序。针对一个输入的文件（例如 `image.png`），它能将其压缩为 `image.png.huff`；将 `image.png.huff` 解压后，能获得和原始文件一样的文件。

### 接口模式

你可以从以下两种接口（interface）中选择其一。

**交互式**

这个是以往实验中同学们常用的方式。大致逻辑类似于，黑框框中显示命令菜单，输入 1 选择压缩，然后输入原始文件的路径（包含文件名）、目标文件的路径（包含文件名）……

**命令行式**

你的程序需支持以下命令行选项：

```
-c: 表示使用压缩功能
-x: 表示使用解压功能
-i: 后面加上输入文件路径
-o: 后面加上输出文件路径
```

假设你的代码编译后的可执行文件是 `huffman.exe`，则

```
./huffman.exe -c -i testcase/image.png -o compressed/image.png.huff
```

表示将 `testcase/image.png` 压缩为 `compressed/image.png.huff`。

```
./huffman.exe -x -i compressed/image.png.huff -o uncompressed/image.png
```

表示将 `compressed/image.png.huff` 解压至 `uncompressed/image.png`。

一般情况下，这种命令行风格有一些大家约定俗成的原则，例如：

- 可交换顺序：`-c -i a.png -o a.png.huff` 等效于 `-c -o a.png.huff -i a.png `；
- 可合并：`-c -i a.png -o a.png.huff` 等效于 `-ci a.png -o a.png.huff `；

- 支持缩略和全称：`-c -i a.png -o a.png.huff` 等效于 `--compress --input a.png --output a.png.huff `；

我们对支持这些原则不做要求，以上列出来仅是为了让大家感受一下这种风格。

### 实现提示

**压缩**

1. 统计元素频次（以 1 byte / 8 bit 为基本单位）；
2. 构建 Huffman 树，生成 Huffman 编码表；
3. 利用 Huffman 编码表“翻译”原文件，写入到目标文件中。注意，你必须同时将 Huffman 编码表也写入到目标文件中，否则将无法解压。为此，你需要自己设计合理的方式来存储这两项内容到一个文件中。

**解压**

按照你自己设计的方式从压缩文件中读取 Huffman 编码表和编码后的内容，利用 Huffman 编码表复原内容。

### 评测

从以下任选一个地址下载 `testcase.zip`：
- [下载地址1: GitHub CDN](https://github.com/ustc-ds-2023/ustc-ds-2023.github.io/files/13434839/testcase.zip)
- [下载地址2: USTC FTP](http://home.ustc.edu.cn/~liulangcao/testcase.zip)
- [下载地址3: 阿里云](https://storage.yusanshi.com/testcase.zip)

解压缩，获得以下 6 个测试样例：
```
testcase
├── 1.MIT-license.txt
├── 2.hamlet.txt
├── 3.USTC-logo.bmp
├── 4.USTC-logo.png
├── 5.data-structure.pdf
└── 6.MV.mp4
```

针对 `testcase` 目录中的每一个测试样例，将其压缩存至 `compressed` 目录，再解压至 `uncompressed` 目录。

`uncompressed` 目录中的每个文件应能正常打开，且和 `testcase` 中对应文件的内容一致。考虑到肉眼观测一致性可能存在疏漏，我们要求你使用文件/文件夹对比软件来检验一致性。

例如，你可以下载 [WinMerge](https://github.com/WinMerge/winmerge) 或 [Beyond Compare](https://www.scootersoftware.com/)，然后使用它们的“文件夹对比”功能，对比 `testcase` 文件夹和 `uncompressed` 文件夹。

### 选做内容

**针对英文自然语言纯文本文件的压缩率优化**：在基本要求中，我们以 1 byte / 8 bit 为基本单位，对于英文纯文本文件，“以 1 byte / 8 bit 为基本单位”约等于“以单个字符作为基本单位”。考虑到在英文自然语言中，同一个单词可出现多次，且不同单词的频次差异很大，当我们将单词作为基本单位时，有可能可以实现更高的压缩率。

实现此优化策略，针对 `2.hamlet.txt` （《哈姆雷特》全书）文件，计算采用这种优化策略的压缩率，对比没有采用优化策略时的压缩率。

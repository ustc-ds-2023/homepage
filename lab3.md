# 实验三

## 时间

*直至期末考试前，具体时间待确定。*

## 说明

- 以下两题二选一（**注：第一题有选做内容且选做内容 2 分，故最高可得 12 分；第二题难度相对更低，无选做，最高 10分**）；
- 对输入和输出格式不做要求，你既可以按照题目描述的（如果有）来实现，也可以用自己喜欢的方式；
- 在完整实现功能要求的前提下，技术细节不必拘泥于题中描述的要求，合理即可；
- **本次实验需要提交实验报告，具体要求请参照 QQ 群内的说明。**

## 1. Huffman 文件压缩解压程序

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
./huffman.exe -x -i compressed/image.png.huff -o decompressed/image.png
```

表示将 `compressed/image.png.huff` 解压至 `decompressed/image.png`。

一般情况下，这种命令行风格有一些大家约定俗成的原则，例如：

- 可交换顺序：`-c -i a.png -o a.png.huff` 等效于 `-c -o a.png.huff -i a.png `；
- 可合并：`-c -i a.png -o a.png.huff` 等效于 `-ci a.png -o a.png.huff `；

- 支持缩略和全称：`-c -i a.png -o a.png.huff` 等效于 `--compress --input a.png --output a.png.huff `；

我们对支持这些原则不做要求，以上列出来仅是为了让大家感受一下这种风格。

> 对命令行参数的解析较复杂，我们鼓励你直接用第三方轮子。例如，针对 C++，你可以在这里找到一些开源库 <https://github.com/fffaraz/awesome-cpp#cli>。

### 实现提示

**压缩**

1. 统计元素频次（以 1 byte / 8 bit 为基本单位）；
2. 构建 Huffman 树，生成 Huffman 编码表；
3. 利用 Huffman 编码表“翻译”原文件，写入到目标文件中。注意，你必须同时将 Huffman 编码表也写入到目标文件中，否则将无法解压。为此，你需要自己设计合理的方式来存储这两项内容到一个文件中。

**解压**

按照你自己设计的方式从压缩文件中读取 Huffman 编码表和编码后的内容，利用 Huffman 编码表复原内容。

### 评测

从以下任选一个地址下载 `testcase.zip`：
- [下载地址1: GitHub](https://github.com/ustc-ds-2023/ustc-ds-2023.github.io/files/13434839/testcase.zip)
- [下载地址2: 阿里云](https://storage.yusanshi.com/testcase.zip)

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

针对 `testcase` 目录中的每一个测试样例，将其压缩存至 `compressed` 目录，再解压至 `decompressed` 目录。

`decompressed` 目录中的每个文件应能正常打开，且和 `testcase` 中对应文件的内容一致。考虑到肉眼观测一致性可能存在疏漏，我们要求你使用文件/文件夹对比软件来检验一致性。

例如，你可以下载 [WinMerge](https://github.com/WinMerge/winmerge) 或 [Beyond Compare](https://www.scootersoftware.com/)，然后使用它们的“文件夹对比”功能，对比 `testcase` 文件夹和 `decompressed` 文件夹。

### 选做内容

**针对英文自然语言纯文本文件的压缩率优化**

在基本要求中，我们以 1 byte / 8 bit 为基本单位，对于英文纯文本文件，“以 1 byte / 8 bit 为基本单位”等价于“以单个字符作为基本单位”。考虑到在英文自然语言中，同一个单词可出现多次，且不同单词的频次差异很大，当我们将单词作为基本单位时，有可能可以实现更高的压缩率。

但另一方面，以单词作为基本单位可能导致元素种类过多，这一方面会让存储的 Huffman 编码表过大，另一方面会导致很低频的单词的编码过长，对提高压缩率无益。

为此，我们采用这样的优化策略：当某单词出现频次超过某一个阈值时，我们取单词作为基本单位；否则，取组成它的各个字符作为基本单位。其中，阈值的取值由你自行设定（实际上，当这个阈值无限大时，这种策略实际上退化成了原始的以单个字符作为基本单位的情况）。

请实现此优化策略，针对 `2.hamlet.txt` （《哈姆雷特》全书）文件，计算采用这种优化策略的压缩率，对比没有采用优化策略时的压缩率。

> 经过实际测试该纯文本优化方案确实是能起效果的：当阈值设为 4 时压缩率最高，此时该文件被压缩到 91 KB（无此优化时是 104 KB 左右）。在我的实现中，使用了 [Variable-length Quantity](https://en.wikipedia.org/wiki/Variable-length_quantity)，未使用 [Canonical Huffman](https://en.wikipedia.org/wiki/Canonical_Huffman_code)。
>
> 如果你的实现中，该优化策略没能起效，可能是因为存储编码表的方式不够高效。你可以自行找一个更大的英文自然语言纯文本文件（当然，直接把 `2.hamlet.txt` 自身复制很多遍也是可以的），只要你能找到一个 testcase 让你的优化方案实际起效，该选做就视为通过 :)

## 2. 基于最短路径算法的导航软件

### 基本说明

在所给的数据集上建立图结构，实现 Dijkstra 算法求解任意两点之间的最短路径。

### 评测

从以下任选一个地址下载数据集文件 `data.txt`：

- [下载地址1: GitHub](https://github.com/ustc-ds-2023/ustc-ds-2023.github.io/files/13441102/data.txt)
- [下载地址2: 阿里云](https://storage.yusanshi.com/data.txt)

它包含若干行数据，每行数据形式如下：

```
src dst distance
```

其中 `src` 表示源点，`dst` 表示目标点，`distance` 表示从源点到目标点有直接连接的路径且路径长度为 `distance`。

程序运行后，它应首先自行读取数据集并构建图。接着，程序接受多次查询，在每次查询中，我们输入 `src` 和 `dst`，程序输出最短路径长度和其中一条最短路径（如：`src->p1->...->dst`）。


# PreTeXtBook 详细使用说明

> PreTeXt 是一个基于 XML 的学术写作系统，能将同一份源文件转换为 HTML、PDF、EPUB、Braille 等多种格式，特别适合编写数学教材、技术文档和学术书籍。

---

## 目录

1. [什么是 PreTeXt](#1-什么是-pretext)
2. [安装与环境配置](#2-安装与环境配置)
3. [项目基本结构](#3-项目基本结构)
4. [文档骨架：书、章、节](#4-文档骨架书章节)
5. [数学公式排版](#5-数学公式排版)
6. [定理、定义、例题环境](#6-定理定义例题环境)
7. [代码块与程序清单](#7-代码块与程序清单)
8. [图形与图像](#8-图形与图像)
9. [表格排版](#9-表格排版)
10. [交叉引用与超链接](#10-交叉引用与超链接)
11. [习题与解答](#11-习题与解答)
12. [交互式元素（HTML 输出）](#12-交互式元素html-输出)
13. [创意排版示例集](#13-创意排版示例集)
14. [构建与输出](#14-构建与输出)
15. [完整示例项目](#15-完整示例项目)

---

## 1. 什么是 PreTeXt

PreTeXt（前身为 MathBook XML）由 Rob Beezer 开发，其核心理念是：

- **一次编写，多处发布**：同一 XML 源码可生成网页版（含交互）、印刷版 PDF、电子书 EPUB 等。
- **语义优先**：描述"这是一个定理"而非"这段文字加粗居中"，格式由样式表控制。
- **数学友好**：深度集成 MathJax/LaTeX，数学公式渲染效果出色。
- **无障碍访问**：生成的 HTML 自动包含 ARIA 标签，支持屏幕阅读器。

---

## 2. 安装与环境配置

### 2.1 前置要求

| 工具 | 版本要求 | 用途 |
|------|----------|------|
| Python | ≥ 3.8 | pretext CLI 运行环境 |
| LaTeX（TeX Live / MiKTeX） | 最新版 | 生成 PDF |
| Git | 任意版本 | 版本管理 |

### 2.2 安装 PreTeXt CLI

```bash
# 使用 pip 安装
pip install pretext

# 验证安装
pretext --version
```

### 2.3 创建新项目

```bash
# 新建项目（交互式向导）
pretext new

# 或者直接指定项目类型
pretext new book --title "我的第一本书" --author "张三"
```

向导结束后，目录结构如下：

```
my-book/
├── project.ptx          ← 项目配置文件
├── source/
│   ├── main.ptx         ← 主入口文件
│   └── chapters/        ← 各章节文件
├── publication/
│   └── publication.ptx  ← 发布配置（页眉、页脚、编号方式等）
└── output/              ← 构建输出目录（自动生成）
```

---

## 3. 项目基本结构

### 3.1 project.ptx（项目配置）

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project ptx-version="2">
  <targets>
    <target name="html">
      <format>html</format>
      <source>source/main.ptx</source>
      <publication>publication/publication.ptx</publication>
      <output-dir>output/html</output-dir>
    </target>
    <target name="pdf">
      <format>pdf</format>
      <source>source/main.ptx</source>
      <publication>publication/publication.ptx</publication>
      <output-dir>output/pdf</output-dir>
    </target>
  </targets>
</project>
```

### 3.2 main.ptx（主文件）

```xml
<?xml version="1.0" encoding="UTF-8"?>
<pretext xmlns:xi="http://www.w3.org/2001/XInclude">
  <docinfo>
    <macros>
      <!-- 全局 LaTeX 宏定义 -->
      \newcommand{\RR}{\mathbb{R}}
      \newcommand{\NN}{\mathbb{N}}
      \newcommand{\ZZ}{\mathbb{Z}}
      \newcommand{\CC}{\mathbb{C}}
      \newcommand{\abs}[1]{\left|#1\right|}
      \newcommand{\norm}[1]{\left\|#1\right\|}
      \newcommand{\inner}[2]{\langle #1,\, #2 \rangle}
    </macros>
    <latex-image-preamble>
      \usepackage{pgfplots}
      \usepackage{tikz}
      \usetikzlibrary{arrows.meta, positioning, shapes}
      \pgfplotsset{compat=1.18}
    </latex-image-preamble>
  </docinfo>

  <book xml:id="my-book">
    <title>线性代数入门</title>
    <subtitle>从直觉到严格证明</subtitle>

    <frontmatter xml:id="frontmatter">
      <titlepage>
        <author>
          <personname>张三</personname>
          <institution>某大学数学系</institution>
        </author>
        <date><today /></date>
      </titlepage>
      <abstract>
        <p>本书面向大学一年级学生，以几何直觉引导严格的代数推导，
        覆盖向量空间、线性映射、行列式、特征值等核心主题。</p>
      </abstract>
    </frontmatter>

    <xi:include href="chapters/ch01-vectors.ptx" />
    <xi:include href="chapters/ch02-matrices.ptx" />
    <xi:include href="chapters/ch03-determinants.ptx" />

    <backmatter xml:id="backmatter">
      <index />
      <colophon>
        <p>本书使用 PreTeXt 排版系统编写。</p>
      </colophon>
    </backmatter>
  </book>
</pretext>
```

---

## 4. 文档骨架：书、章、节

PreTeXt 的层级结构（从上到下）：

```
book（书）
  └── chapter（章）
        └── section（节）
              └── subsection（小节）
                    └── subsubsection（小小节）
```

### 示例：一章的完整结构

```xml
<!-- source/chapters/ch01-vectors.ptx -->
<chapter xml:id="ch-vectors">
  <title>向量与向量空间</title>

  <introduction>
    <p>本章从几何直觉出发，介绍欧氏空间中的向量，
    并逐步抽象为一般向量空间的公理体系。</p>
  </introduction>

  <section xml:id="sec-euclidean-vectors">
    <title>欧氏空间中的向量</title>

    <introduction>
      <p>在平面和三维空间中，向量是同时具有大小和方向的量。</p>
    </introduction>

    <subsection xml:id="subsec-vector-addition">
      <title>向量加法</title>
      <p>两个向量 <m>\mathbf{u}</m> 和 <m>\mathbf{v}</m> 的和
      按分量定义：</p>
      <!-- 正文内容 -->
    </subsection>

    <subsection xml:id="subsec-scalar-mult">
      <title>数乘</title>
      <p>标量 <m>c \in \RR</m> 与向量 <m>\mathbf{v}</m> 的数乘 <m>c\mathbf{v}</m>
      将向量缩放 <m>|c|</m> 倍。</p>
    </subsection>

    <conclusion>
      <p>本节介绍了向量的两种基本运算，下一节将讨论内积结构。</p>
    </conclusion>
  </section>

  <section xml:id="sec-abstract-vector-space">
    <title>抽象向量空间</title>
    <p>……</p>
  </section>
</chapter>
```

---

## 5. 数学公式排版

### 5.1 行内公式

使用 `<m>...</m>` 标签：

```xml
<p>
  设 <m>f: \RR^n \to \RR^m</m> 是线性映射，
  则对任意 <m>\mathbf{x}, \mathbf{y} \in \RR^n</m>
  和标量 <m>c \in \RR</m>，有
  <m>f(c\mathbf{x} + \mathbf{y}) = cf(\mathbf{x}) + f(\mathbf{y})</m>。
</p>
```

### 5.2 独立公式（无编号）

使用 `<me>` (math equation)：

```xml
<me>
  \int_a^b f(x)\,dx = F(b) - F(a)
</me>
```

### 5.3 独立公式（有编号，可交叉引用）

使用 `<men xml:id="...">` (math equation numbered)：

```xml
<men xml:id="eq-cauchy-schwarz">
  \abs{\inner{\mathbf{u}}{\mathbf{v}}}
  \leq \norm{\mathbf{u}} \cdot \norm{\mathbf{v}}
</men>

<!-- 在正文中引用 -->
<p>由不等式 <xref ref="eq-cauchy-schwarz" text="global"/> 可知……</p>
```

### 5.4 多行对齐公式

使用 `<md>` 或 `<mdn>`（有编号）配合 `<mrow>`：

```xml
<md>
  <mrow>
    \det(AB) \amp= \det(A) \cdot \det(B)
  </mrow>
  <mrow>
    \amp= \det(B) \cdot \det(A)
  </mrow>
  <mrow>
    \amp= \det(BA)
  </mrow>
</md>
```

> **注意**：对齐符号 `&` 在 XML 中需写为 `\amp`。

### 5.5 矩阵

```xml
<me>
  A = \begin{pmatrix}
    a_{11} \amp a_{12} \amp \cdots \amp a_{1n} \\
    a_{21} \amp a_{22} \amp \cdots \amp a_{2n} \\
    \vdots \amp \vdots \amp \ddots \amp \vdots \\
    a_{m1} \amp a_{m2} \amp \cdots \amp a_{mn}
  \end{pmatrix}
</me>
```

---

## 6. 定理、定义、例题环境

PreTeXt 内置了丰富的学术写作环境，每种都自动编号并可交叉引用。

### 6.1 定义（Definition）

```xml
<definition xml:id="def-linear-independence">
  <title>线性无关</title>
  <statement>
    <p>向量组 <m>\{\mathbf{v}_1, \mathbf{v}_2, \ldots, \mathbf{v}_k\}</m>
    称为<term>线性无关</term>（linearly independent），若方程
    <me>c_1\mathbf{v}_1 + c_2\mathbf{v}_2 + \cdots + c_k\mathbf{v}_k = \mathbf{0}</me>
    的唯一解为 <m>c_1 = c_2 = \cdots = c_k = 0</m>。</p>
  </statement>
</definition>
```

### 6.2 定理（Theorem）+ 证明

```xml
<theorem xml:id="thm-basis-dimension">
  <title>基的等势性</title>
  <statement>
    <p>有限维向量空间 <m>V</m> 的任意两组基具有相同的元素个数。</p>
  </statement>
  <proof>
    <p>设 <m>\mathcal{B} = \{\mathbf{b}_1, \ldots, \mathbf{b}_n\}</m>
    和 <m>\mathcal{C} = \{\mathbf{c}_1, \ldots, \mathbf{c}_m\}</m>
    都是 <m>V</m> 的基。</p>

    <p>由于 <m>\mathcal{B}</m> 是基，<m>\mathcal{C}</m> 中每个向量
    均可由 <m>\mathcal{B}</m> 线性表示，故由替换定理得 <m>m \leq n</m>。
    对称地，<m>n \leq m</m>，从而 <m>n = m</m>。</p>
  </proof>
</theorem>
```

### 6.3 命题、推论、引理

```xml
<proposition xml:id="prop-rank-nullity">
  <title>秩-零化度定理</title>
  <statement>
    <p>设 <m>T: V \to W</m> 是线性映射，<m>V</m> 有限维，则
    <me>\dim V = \dim \ker T + \dim \operatorname{im} T.</me></p>
  </statement>
</proposition>

<corollary xml:id="cor-injective-surjective">
  <statement>
    <p>若 <m>\dim V = \dim W</m>，则 <m>T</m> 单射当且仅当 <m>T</m> 满射。</p>
  </statement>
</corollary>

<lemma xml:id="lem-exchange">
  <title>替换引理</title>
  <statement>
    <p>……</p>
  </statement>
</lemma>
```

### 6.4 例题（Example）

```xml
<example xml:id="ex-gram-schmidt">
  <title>Gram-Schmidt 正交化</title>
  <statement>
    <p>对向量组
    <m>\mathbf{v}_1 = (1,1,0)^T</m>，
    <m>\mathbf{v}_2 = (1,0,1)^T</m>，
    <m>\mathbf{v}_3 = (0,1,1)^T</m>
    施行 Gram-Schmidt 正交化。</p>
  </statement>
  <solution>
    <p><em>第一步</em>：令 <m>\mathbf{e}_1 = \mathbf{v}_1 / \norm{\mathbf{v}_1}
    = (1,1,0)^T / \sqrt{2}</m>。</p>

    <p><em>第二步</em>：从 <m>\mathbf{v}_2</m> 中减去在 <m>\mathbf{e}_1</m>
    方向的投影：
    <md>
      <mrow>
        \mathbf{u}_2 \amp= \mathbf{v}_2
          - \inner{\mathbf{v}_2}{\mathbf{e}_1}\mathbf{e}_1
      </mrow>
      <mrow>
        \amp= (1,0,1)^T - \frac{1}{\sqrt{2}} \cdot \frac{1}{\sqrt{2}}(1,1,0)^T
      </mrow>
      <mrow>
        \amp= \left(\tfrac{1}{2}, -\tfrac{1}{2}, 1\right)^T
      </mrow>
    </md>
    </p>

    <p>最终得到标准正交基
    <m>\{\mathbf{e}_1, \mathbf{e}_2, \mathbf{e}_3\}</m>。</p>
  </solution>
</example>
```

### 6.5 注记与警告

```xml
<remark xml:id="rem-zero-vector">
  <p>零向量 <m>\mathbf{0}</m> 不能加入任何线性无关集，
  因为 <m>1 \cdot \mathbf{0} = \mathbf{0}</m> 提供了非平凡线性组合。</p>
</remark>

<warning>
  <p>线性相关 <em>不等于</em> 某个向量是另一个向量的倍数；
  后者是两个向量线性相关的特殊情形。</p>
</warning>

<insight>
  <p>可以将线性无关性理解为：向量组中每个向量都提供了其他向量
  无法覆盖的"新方向"。</p>
</insight>
```

---

## 7. 代码块与程序清单

### 7.1 行内代码

```xml
<p>在 Python 中，使用 <c>numpy.linalg.eig()</c> 函数计算特征值。</p>
```

### 7.2 代码块（program listing）

```xml
<listing xml:id="lst-numpy-eigenvalue">
  <caption>用 NumPy 计算特征值与特征向量</caption>
  <program language="python">
    <input>
import numpy as np

# 定义矩阵
A = np.array([[4, 1],
              [2, 3]])

# 计算特征值和特征向量
eigenvalues, eigenvectors = np.linalg.eig(A)

print("特征值:", eigenvalues)
print("特征向量（列向量）:")
print(eigenvectors)

# 验证：A @ v = λ * v
for i in range(len(eigenvalues)):
    lam = eigenvalues[i]
    v   = eigenvectors[:, i]
    print(f"\n验证 λ={lam:.1f}: A@v = {A @ v}, λ*v = {lam * v}")
    </input>
  </program>
</listing>
```

### 7.3 支持的语言高亮

PreTeXt 通过 Pygments 支持数十种语言：

```xml
<!-- C++ 示例 -->
<program language="cpp">
  <input>
#include &lt;iostream&gt;
#include &lt;vector&gt;

// 高斯消元（行阶梯形）
void gaussian_elimination(std::vector&lt;std::vector&lt;double&gt;&gt;&amp; A) {
    int n = A.size();
    for (int col = 0; col &lt; n; ++col) {
        // 选主元
        int pivot = col;
        for (int row = col + 1; row &lt; n; ++row)
            if (std::abs(A[row][col]) &gt; std::abs(A[pivot][col]))
                pivot = row;
        std::swap(A[col], A[pivot]);

        // 消元
        for (int row = col + 1; row &lt; n; ++row) {
            double factor = A[row][col] / A[col][col];
            for (int k = col; k &lt;= n; ++k)
                A[row][k] -= factor * A[col][k];
        }
    }
}

int main() {
    // 增广矩阵 [A | b]
    std::vector&lt;std::vector&lt;double&gt;&gt; Ab = {
        {2, 1, -1,  8},
        {-3, -1, 2, -11},
        {-2, 1, 2,  -3}
    };
    gaussian_elimination(Ab);
    std::cout &lt;&lt; "消元完成" &lt;&lt; std::endl;
    return 0;
}
  </input>
</program>

<!-- Julia 示例 -->
<program language="julia">
  <input>
using LinearAlgebra

A = [4.0  1.0;
     2.0  3.0]

# 特征值分解
F = eigen(A)
println("特征值: ", F.values)
println("特征向量:\n", F.vectors)

# SVD 分解
U, Σ, V = svd(A)
println("\nSVD 奇异值: ", Σ)
  </input>
</program>
```

---

## 8. 图形与图像

### 8.1 插入外部图片

```xml
<figure xml:id="fig-vector-addition">
  <caption>向量加法的几何解释（平行四边形法则）</caption>
  <image source="images/vector-addition.png" width="60%">
    <description>
      平面上两个向量 u 和 v，以及它们的和向量 u+v，
      构成一个平行四边形。
    </description>
  </image>
</figure>
```

### 8.2 内嵌 TikZ 图形（推荐！）

TikZ 图形由 PreTeXt 自动转换为 SVG（HTML）或直接嵌入 PDF：

```xml
<figure xml:id="fig-basis-2d">
  <caption>平面直角坐标系中的标准基向量 <m>\mathbf{e}_1</m> 和
  <m>\mathbf{e}_2</m></caption>
  <image xml:id="img-basis-2d" width="50%">
    <description>二维坐标系，标注了两个基向量 e1=(1,0) 和 e2=(0,1)</description>
    <latex-image>
      \begin{tikzpicture}[scale=2,
          vector/.style={-Stealth, very thick, blue!70!black},
          axis/.style={-Stealth, thin, gray}]

        % 坐标轴
        \draw[axis] (-0.3,0) -- (1.5,0) node[right] {$x$};
        \draw[axis] (0,-0.3) -- (0,1.5) node[above] {$y$};

        % 基向量
        \draw[vector] (0,0) -- (1,0)
          node[below right] {$\mathbf{e}_1 = (1,0)$};
        \draw[vector, red!70!black] (0,0) -- (0,1)
          node[above left] {$\mathbf{e}_2 = (0,1)$};

        % 原点标记
        \fill (0,0) circle (1.5pt) node[below left] {$O$};
      \end{tikzpicture}
    </latex-image>
  </image>
</figure>
```

### 8.3 pgfplots 绘制函数图像

```xml
<figure xml:id="fig-sine-cosine">
  <caption>正弦函数与余弦函数在 <m>[0, 2\pi]</m> 上的图像</caption>
  <image xml:id="img-sine-cosine" width="70%">
    <description>正弦和余弦函数的周期图像</description>
    <latex-image>
      \begin{tikzpicture}
        \begin{axis}[
          width=10cm, height=6cm,
          xmin=0, xmax=6.4,
          ymin=-1.3, ymax=1.3,
          xtick={0,1.5708,3.1416,4.7124,6.2832},
          xticklabels={$0$, $\frac{\pi}{2}$, $\pi$,
                        $\frac{3\pi}{2}$, $2\pi$},
          ytick={-1,0,1},
          grid=both,
          grid style={line width=0.2pt, draw=gray!30},
          legend pos=north east,
          legend style={font=\small},
          axis lines=center,
        ]
          \addplot[blue, thick, domain=0:6.4, samples=200]
            {sin(deg(x))};
          \addlegendentry{$\sin x$}

          \addplot[red, thick, domain=0:6.4, samples=200,
                   dashed]
            {cos(deg(x))};
          \addlegendentry{$\cos x$}
        \end{axis}
      \end{tikzpicture}
    </latex-image>
  </image>
</figure>
```

---

## 9. 表格排版

### 9.1 基本表格

```xml
<table xml:id="table-vector-space-axioms">
  <title>向量空间的八条公理</title>
  <tabular halign="left" top="major" bottom="major">
    <col width="10%" />
    <col width="35%" />
    <col width="55%" />
    <row header="yes" bottom="medium">
      <cell>编号</cell>
      <cell>名称</cell>
      <cell>公理</cell>
    </row>
    <row>
      <cell>A1</cell>
      <cell>加法封闭性</cell>
      <cell><m>\mathbf{u} + \mathbf{v} \in V</m></cell>
    </row>
    <row>
      <cell>A2</cell>
      <cell>加法交换律</cell>
      <cell><m>\mathbf{u} + \mathbf{v} = \mathbf{v} + \mathbf{u}</m></cell>
    </row>
    <row>
      <cell>A3</cell>
      <cell>加法结合律</cell>
      <cell><m>(\mathbf{u}+\mathbf{v})+\mathbf{w} =
        \mathbf{u}+(\mathbf{v}+\mathbf{w})</m></cell>
    </row>
    <row>
      <cell>A4</cell>
      <cell>零元素存在</cell>
      <cell><m>\exists\, \mathbf{0} \in V,\ \mathbf{v}+\mathbf{0}=\mathbf{v}</m></cell>
    </row>
    <row>
      <cell>A5</cell>
      <cell>加法逆元存在</cell>
      <cell><m>\forall\,\mathbf{v},\,\exists\,{-\mathbf{v}},\
        \mathbf{v}+(-\mathbf{v})=\mathbf{0}</m></cell>
    </row>
    <row>
      <cell>S1</cell>
      <cell>数乘封闭性</cell>
      <cell><m>c\mathbf{v} \in V</m></cell>
    </row>
    <row>
      <cell>S2</cell>
      <cell>数乘结合律</cell>
      <cell><m>(cd)\mathbf{v} = c(d\mathbf{v})</m></cell>
    </row>
    <row>
      <cell>S3</cell>
      <cell>分配律（向量）</cell>
      <cell><m>c(\mathbf{u}+\mathbf{v}) = c\mathbf{u}+c\mathbf{v}</m></cell>
    </row>
    <row>
      <cell>S4</cell>
      <cell>分配律（标量）</cell>
      <cell><m>(c+d)\mathbf{v} = c\mathbf{v}+d\mathbf{v}</m></cell>
    </row>
    <row bottom="none">
      <cell>S5</cell>
      <cell>单位元</cell>
      <cell><m>1 \cdot \mathbf{v} = \mathbf{v}</m></cell>
    </row>
  </tabular>
</table>
```

### 9.2 跨列合并

```xml
<tabular halign="center" top="major" bottom="major">
  <col /><col /><col />
  <row header="yes" bottom="minor">
    <cell colspan="3">矩阵运算速查表</cell>
  </row>
  <row header="yes" bottom="medium">
    <cell>运算</cell>
    <cell>记号</cell>
    <cell>条件</cell>
  </row>
  <row>
    <cell>加法</cell>
    <cell><m>A + B</m></cell>
    <cell><m>A, B</m> 同型</cell>
  </row>
  <row>
    <cell>乘法</cell>
    <cell><m>AB</m></cell>
    <cell><m>A</m> 的列数 = <m>B</m> 的行数</cell>
  </row>
  <row bottom="none">
    <cell>转置</cell>
    <cell><m>A^T</m></cell>
    <cell>无限制</cell>
  </row>
</tabular>
```

---

## 10. 交叉引用与超链接

### 10.1 内部引用

```xml
<!-- 引用定理（显示编号） -->
<p>由<xref ref="thm-basis-dimension"/>，维数是向量空间的不变量。</p>

<!-- 引用带自定义文本 -->
<p>正如<xref ref="def-linear-independence" text="title"/>中定义的，……</p>

<!-- 引用范围 -->
<p>详见<xref first="sec-euclidean-vectors" last="sec-abstract-vector-space"/>。</p>
```

`text` 属性的可选值：

| 值 | 显示效果 |
|----|---------|
| `global` | 定理 2.3.1 |
| `local` | 定理 3 |
| `title` | 基的等势性 |
| `phrase-global` | 定理 2.3.1（基的等势性）|
| `type-global` | Theorem 2.3.1 |

### 10.2 外部链接

```xml
<p>PreTeXt 官方文档见
<url href="https://pretextbook.org/doc/guide/html/guide-toc.html"
     visual="pretextbook.org">PreTeXt 作者指南</url>。</p>
```

---

## 11. 习题与解答

### 11.1 章末习题集

```xml
<exercises xml:id="exercises-ch01">
  <title>第一章习题</title>

  <introduction>
    <p>以下习题难度从 ★ 到 ★★★ 逐步递增。</p>
  </introduction>

  <!-- 填空/计算题 -->
  <exercise xml:id="ex-linear-combo">
    <statement>
      <p>（★）设 <m>\mathbf{u} = (2, -1, 3)</m>，<m>\mathbf{v} = (1, 4, -2)</m>，
      计算 <m>3\mathbf{u} - 2\mathbf{v}</m>。</p>
    </statement>
    <answer>
      <p><m>(4, -11, 13)</m></p>
    </answer>
    <solution>
      <p>
        <md>
          <mrow>
            3\mathbf{u} - 2\mathbf{v}
            \amp= 3(2,-1,3) - 2(1,4,-2)
          </mrow>
          <mrow>
            \amp= (6,-3,9) - (2,8,-4)
          </mrow>
          <mrow>
            \amp= (4,-11,13)
          </mrow>
        </md>
      </p>
    </solution>
  </exercise>

  <!-- 证明题 -->
  <exercise xml:id="ex-subspace-proof">
    <statement>
      <p>（★★）证明：若 <m>W_1</m> 和 <m>W_2</m> 都是向量空间 <m>V</m> 的子空间，
      则 <m>W_1 \cap W_2</m> 也是 <m>V</m> 的子空间。</p>
    </statement>
    <hint>
      <p>验证子空间的三个判定条件：含零向量、加法封闭、数乘封闭。</p>
    </hint>
    <solution>
      <p>设 <m>W = W_1 \cap W_2</m>。</p>
      <p><em>含零向量</em>：因 <m>\mathbf{0} \in W_1</m> 且
      <m>\mathbf{0} \in W_2</m>，故 <m>\mathbf{0} \in W</m>。</p>
      <p><em>加法封闭</em>：设 <m>\mathbf{u}, \mathbf{v} \in W</m>，
      则 <m>\mathbf{u}, \mathbf{v} \in W_1</m>，故 <m>\mathbf{u}+\mathbf{v} \in W_1</m>；
      类似地 <m>\mathbf{u}+\mathbf{v} \in W_2</m>，从而 <m>\mathbf{u}+\mathbf{v} \in W</m>。</p>
      <p><em>数乘封闭</em>：类似可证。</p>
    </solution>
  </exercise>

  <!-- 计算机实验题 -->
  <exercise xml:id="ex-computer-gram-schmidt">
    <statement>
      <p>（★★★ 计算机实验）使用以下代码框架，实现 Gram-Schmidt
      正交化算法，并对随机生成的 <m>5\times 5</m> 矩阵列向量组进行正交化。</p>
      <listing>
        <program language="python">
          <input>
import numpy as np

def gram_schmidt(V):
    """
    输入: V 是 n×k 矩阵，列向量为待正交化的向量组
    输出: Q 是 n×k 矩阵，列向量为标准正交向量组
    """
    n, k = V.shape
    Q = np.zeros((n, k))
    for i in range(k):
        q = V[:, i].copy()
        for j in range(i):
            q -= np.dot(Q[:, j], V[:, i]) * Q[:, j]
        Q[:, i] = q / np.linalg.norm(q)
    return Q

# 测试
np.random.seed(42)
A = np.random.randn(5, 5)
Q = gram_schmidt(A)

# 验证正交性：Q^T Q 应接近单位矩阵
print("Q^T Q =")
print(np.round(Q.T @ Q, 8))
          </input>
        </program>
      </listing>
    </statement>
    <answer>
      <p>正确实现后，<c>Q.T @ Q</c> 应输出接近 <m>5\times 5</m> 单位矩阵的结果。</p>
    </answer>
  </exercise>
</exercises>
```

---

## 12. 交互式元素（HTML 输出）

### 12.1 Sage 交互单元格

HTML 输出中，SageMath 代码块可以直接在浏览器中运行（通过 SageMathCell 服务）：

```xml
<sage>
  <input>
# 在浏览器中可直接运行！
A = matrix(QQ, [[2, 1, 0],
                [1, 3, 1],
                [0, 1, 2]])

print("矩阵 A =")
print(A)
print("\n行列式:", A.det())
print("\n特征多项式:", A.charpoly('x'))
print("\n特征值:", A.eigenvalues())

# 可视化特征向量
show(A)
  </input>
  <output>
矩阵 A =
[2 1 0]
[1 3 1]
[0 1 2]

行列式: 7
  </output>
</sage>
```

### 12.2 WeBWorK 在线作业题

```xml
<exercise>
  <webwork>
    <pg-code>
      $a = random(2, 9, 1);
      $b = random(2, 9, 1);
      $ans = $a * $b;
    </pg-code>
    <statement>
      <p>计算 <m><var name="$a" /> \times <var name="$b" /></m> = 
      <var name="$ans" width="5" /></p>
    </statement>
  </webwork>
</exercise>
```

### 12.3 GeoGebra 动态图形

```xml
<interactive xml:id="ggb-vector-addition"
             platform="geogebra"
             width="90%" aspect="4:3">
  <slate surface="geogebra">
    setCoordSystem(-1, 4, -1, 4);
    a = Vector((0,0),(2,1));
    b = Vector((2,1),(1,2));
    c = Vector((0,0),(3,3));
    SetColor(a,"Blue");
    SetColor(b,"Red");
    SetColor(c,"Green");
  </slate>
  <instructions>
    <p>拖动向量的端点，观察向量加法 <m>\mathbf{a}+\mathbf{b}</m>（绿色）的变化。</p>
  </instructions>
</interactive>
```

---

## 13. 创意排版示例集

### 13.1 侧边注释（Margin Note）

```xml
<p>
  Cauchy-Schwarz 不等式
  <men xml:id="eq-cs-ineq">
    \abs{\inner{\mathbf{u}}{\mathbf{v}}}^2
    \leq \norm{\mathbf{u}}^2 \norm{\mathbf{v}}^2
  </men>
  是分析学中最重要的不等式之一。
  <fn>不等式由 Augustin-Louis Cauchy（1821）和
  Hermann Amandus Schwarz（1888）分别独立发现。</fn>
</p>
```

### 13.2 并排子图（Subfigures）

```xml
<figure xml:id="fig-transformations">
  <caption>三种基本线性变换</caption>
  <sidebyside widths="30% 30% 30%" margins="3%">
    <figure xml:id="fig-rotation">
      <caption>旋转</caption>
      <image xml:id="img-rotation">
        <description>平面旋转变换</description>
        <latex-image>
          \begin{tikzpicture}[scale=1.2]
            \draw[-Stealth,gray] (-1.3,0)--(1.3,0) node[right]{$x$};
            \draw[-Stealth,gray] (0,-1.3)--(0,1.3) node[above]{$y$};
            \draw[blue,thick,-Stealth] (0,0)--(1,0)
              node[below right]{\small$\mathbf{v}$};
            \draw[red,thick,-Stealth] (0,0)--(0.707,0.707)
              node[above right]{\small$R\mathbf{v}$};
            \draw[gray,dashed] (0.5,0) arc(0:45:0.5)
              node[midway, right]{\small$45°$};
          \end{tikzpicture}
        </latex-image>
      </image>
    </figure>

    <figure xml:id="fig-reflection">
      <caption>反射</caption>
      <image xml:id="img-reflection">
        <description>关于 x 轴的反射变换</description>
        <latex-image>
          \begin{tikzpicture}[scale=1.2]
            \draw[-Stealth,gray] (-1.3,0)--(1.3,0) node[right]{$x$};
            \draw[-Stealth,gray] (0,-1.3)--(0,1.3) node[above]{$y$};
            \draw[blue,thick,-Stealth] (0,0)--(0.8,0.6)
              node[above right]{\small$\mathbf{v}$};
            \draw[red,thick,-Stealth] (0,0)--(0.8,-0.6)
              node[below right]{\small$R\mathbf{v}$};
            \draw[gray,dashed] (0.8,0.6)--(0.8,-0.6);
          \end{tikzpicture}
        </latex-image>
      </image>
    </figure>

    <figure xml:id="fig-projection">
      <caption>投影</caption>
      <image xml:id="img-projection">
        <description>向 x 轴的正交投影</description>
        <latex-image>
          \begin{tikzpicture}[scale=1.2]
            \draw[-Stealth,gray] (-0.3,0)--(1.3,0) node[right]{$x$};
            \draw[-Stealth,gray] (0,-0.3)--(0,1.3) node[above]{$y$};
            \draw[blue,thick,-Stealth] (0,0)--(0.8,0.8)
              node[above right]{\small$\mathbf{v}$};
            \draw[red,thick,-Stealth] (0,0)--(0.8,0)
              node[below]{\small$P\mathbf{v}$};
            \draw[gray,dashed] (0.8,0.8)--(0.8,0);
            \draw (0.75,0) rectangle (0.8,0.05);
          \end{tikzpicture}
        </latex-image>
      </image>
    </figure>
  </sidebyside>
</figure>
```

### 13.3 算法伪代码（有序列表模拟）

```xml
<algorithm xml:id="alg-gaussian-elim">
  <title>高斯消元法</title>
  <statement>
    <p><em>输入</em>：<m>m \times n</m> 增广矩阵 <m>[A \mid \mathbf{b}]</m></p>
    <p><em>输出</em>：行阶梯形矩阵</p>
    <ol marker="1.">
      <li>
        <p>令 <m>r \leftarrow 0</m>（当前主元行），<m>c \leftarrow 0</m>（当前主元列）。</p>
      </li>
      <li>
        <p>当 <m>r \leq m</m> 且 <m>c \leq n</m> 时，执行：</p>
        <ol marker="a.">
          <li>
            <p>在第 <m>c</m> 列，从第 <m>r</m> 行向下找第一个非零元素，
            设其在第 <m>k</m> 行。若找不到，令 <m>c \leftarrow c+1</m>，继续。</p>
          </li>
          <li><p>交换第 <m>r</m> 行和第 <m>k</m> 行。</p></li>
          <li>
            <p>对所有 <m>i > r</m>，令
            <m>R_i \leftarrow R_i - \dfrac{a_{ic}}{a_{rc}} R_r</m>。</p>
          </li>
          <li><p><m>r \leftarrow r+1</m>，<m>c \leftarrow c+1</m>。</p></li>
        </ol>
      </li>
      <li><p>输出当前矩阵。</p></li>
    </ol>
  </statement>
</algorithm>
```

### 13.4 彩色框（Colored Boxes）与自定义环境

在 `publication.ptx` 中可定制颜色主题：

```xml
<!-- publication/publication.ptx 片段 -->
<publication>
  <html>
    <css style="default" colors="pastel"/>
    <!-- 或选择: classic, winnipeg, pastel, tonal -->
  </html>
</publication>
```

### 13.5 数学历史注记（传记框）

```xml
<biographical xml:id="bio-gauss">
  <title>Carl Friedrich Gauss（1777–1855）</title>
  <p>德国数学家，被称为"数学王子"。高斯在数论、统计学、分析学、
  微分几何和物理学等领域做出了开创性贡献。高斯消元法以其命名，
  但实际上中国数学家在《九章算术》（约公元前 200 年）中
  已使用类似方法。</p>
</biographical>
```

### 13.6 警示/提示盒子

```xml
<convention>
  <p>本书中，向量用粗体小写字母表示（如 <m>\mathbf{v}</m>），
  矩阵用大写字母表示（如 <m>A</m>），
  标量用普通小写字母表示（如 <m>c</m>）。</p>
</convention>

<technology xml:id="tech-python-setup">
  <title>Python 环境配置</title>
  <p>建议使用 Anaconda 发行版，它预装了
  NumPy、SciPy 和 Matplotlib。安装后在终端运行：</p>
  <program language="bash">
    <input>
conda create -n linalg python=3.11
conda activate linalg
conda install numpy scipy matplotlib jupyter
    </input>
  </program>
</technology>
```

---

## 14. 构建与输出

### 14.1 常用构建命令

```bash
# 构建 HTML（开发模式，支持热重载）
pretext build html

# 构建 PDF
pretext build pdf

# 构建并在浏览器中预览
pretext view html

# 同时构建多个目标
pretext build html pdf

# 查看所有可用目标
pretext targets
```

### 14.2 发布到 GitHub Pages

```bash
# 构建并推送到 gh-pages 分支
pretext deploy

# 或指定目标
pretext deploy --target html
```

### 14.3 publication.ptx 发布配置详解

```xml
<?xml version="1.0" encoding="UTF-8"?>
<publication>
  <!-- HTML 输出配置 -->
  <html>
    <!-- 数学渲染引擎 -->
    <math-cdn preference="mathjax"/>

    <!-- 目录深度（0=不显示，3=显示到小节） -->
    <toc level="3"/>

    <!-- 知名度（影响搜索和分享） -->
    <knowl theorem="no"
           proof="yes"
           definition="no"
           example="yes"
           exercise-inline="no"
           exercise-divisional="yes"/>

    <!-- 版权和访问控制 -->
    <index-page ref="frontmatter"/>
  </html>

  <!-- PDF/LaTeX 输出配置 -->
  <latex>
    <!-- 纸张大小 -->
    <page right-margin="1in" left-margin="1.25in"/>
    <!-- 字体大小 -->
    <font-size>11</font-size>
    <!-- 章节样式 -->
    <division chapter-start="1"/>
  </latex>

  <!-- 全局编号方式 -->
  <numbering>
    <theorems level="2"/>   <!-- 按"章.节"编号 -->
    <equations level="2"/>
    <figures level="1"/>    <!-- 按"章"编号 -->
    <footnotes start="1"/>
  </numbering>
</publication>
```

---

## 15. 完整示例项目

以下是一个可以直接测试的最小化完整项目：

### 文件 1：`source/main.ptx`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<pretext xmlns:xi="http://www.w3.org/2001/XInclude">
  <docinfo>
    <macros>
      \newcommand{\RR}{\mathbb{R}}
      \newcommand{\vv}[1]{\mathbf{#1}}
      \newcommand{\abs}[1]{\left|#1\right|}
    </macros>
    <latex-image-preamble>
      \usepackage{pgfplots, tikz}
      \usetikzlibrary{arrows.meta}
      \pgfplotsset{compat=1.18}
    </latex-image-preamble>
  </docinfo>

  <book xml:id="sample-book">
    <title>PreTeXt 功能演示</title>

    <frontmatter xml:id="front">
      <titlepage>
        <author><personname>示例作者</personname></author>
        <date><today /></date>
      </titlepage>
    </frontmatter>

    <xi:include href="ch-demo.ptx" />

    <backmatter>
      <colophon><p>使用 PreTeXt 排版。</p></colophon>
    </backmatter>
  </book>
</pretext>
```

### 文件 2：`source/ch-demo.ptx`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<chapter xml:id="ch-demo">
  <title>功能演示章</title>

  <!-- ===== 1. 文字与数学 ===== -->
  <section xml:id="sec-math">
    <title>数学公式</title>

    <p>设 <m>A \in \RR^{n \times n}</m> 是方阵。
    <term>特征值</term>（eigenvalue）<m>\lambda</m> 满足：</p>

    <men xml:id="eq-eigen">
      \det(A - \lambda I) = 0
    </men>

    <p>这是 <m>\lambda</m> 的 <m>n</m> 次多项式方程，
    故恰好有 <m>n</m> 个（含重数）复数根（参见公式<nbsp/><xref ref="eq-eigen"/>）。</p>

    <example xml:id="ex-2x2-eigen">
      <title>2×2 矩阵的特征值</title>
      <statement>
        <p>求 <m>A = \begin{pmatrix}3 \amp 1 \\ 1 \amp 3\end{pmatrix}</m>
        的特征值。</p>
      </statement>
      <solution>
        <p>特征多项式为：
        <md>
          <mrow>
            \det(A-\lambda I)
            \amp= \det\begin{pmatrix}3-\lambda \amp 1 \\ 1 \amp 3-\lambda\end{pmatrix}
          </mrow>
          <mrow>
            \amp= (3-\lambda)^2 - 1
          </mrow>
          <mrow>
            \amp= \lambda^2 - 6\lambda + 8
          </mrow>
          <mrow>
            \amp= (\lambda-2)(\lambda-4)
          </mrow>
        </md>
        故特征值为 <m>\lambda_1 = 2</m>，<m>\lambda_2 = 4</m>。</p>
      </solution>
    </example>
  </section>

  <!-- ===== 2. 图形 ===== -->
  <section xml:id="sec-graphics">
    <title>图形示例</title>

    <figure xml:id="fig-parabola">
      <caption>抛物线 <m>y = x^2 - 2x + 1 = (x-1)^2</m></caption>
      <image xml:id="img-parabola" width="55%">
        <description>顶点在 (1,0) 的抛物线</description>
        <latex-image>
          \begin{tikzpicture}
            \begin{axis}[
              width=8cm, height=6cm,
              xmin=-1, xmax=3, ymin=-0.5, ymax=4,
              axis lines=center,
              xlabel={$x$}, ylabel={$y$},
              xtick={-1,0,1,2,3},
              ytick={0,1,2,3,4},
              grid=both,
              grid style={draw=gray!20},
            ]
              \addplot[blue,thick,domain=-0.8:2.8,samples=100]
                {(x-1)^2};
              \addplot[red,only marks,mark=*] coordinates {(1,0)};
              \node[red,above right] at (axis cs:1,0) {顶点 $(1,0)$};
            \end{axis}
          \end{tikzpicture}
        </latex-image>
      </image>
    </figure>
  </section>

  <!-- ===== 3. 代码 ===== -->
  <section xml:id="sec-code">
    <title>代码示例</title>

    <listing xml:id="lst-eigen-python">
      <caption>Python 验证特征值</caption>
      <program language="python">
        <input>
import numpy as np

A = np.array([[3, 1],
              [1, 3]])

vals, vecs = np.linalg.eig(A)
print("特征值:", vals)          # 期待: [2. 4.]
print("特征向量:\n", vecs)

# 手动验证
for i, lam in enumerate(vals):
    v = vecs[:, i]
    Av = A @ v
    print(f"A @ v{i+1} = {Av}, {lam:.0f} * v{i+1} = {lam * v}")
        </input>
      </program>
    </listing>

    <sage xml:id="sage-eigen">
      <input>
# Sage 代码（HTML 中可直接运行）
A = matrix(QQ, [[3, 1], [1, 3]])
print("特征值:", A.eigenvalues())
print("特征向量:")
for val, vecs, mult in A.eigenvectors_right():
    print(f"  λ={val}: {vecs}")
      </input>
    </sage>
  </section>

  <!-- ===== 4. 习题 ===== -->
  <exercises xml:id="ex-ch-demo">
    <title>练习</title>

    <exercise xml:id="exer-trace-det">
      <statement>
        <p>设 <m>A = \begin{pmatrix}a \amp b \\ c \amp d\end{pmatrix}</m>，
        证明：<m>A</m> 的特征值之和等于迹 <m>\operatorname{tr}(A) = a+d</m>，
        特征值之积等于行列式 <m>\det(A) = ad-bc</m>。</p>
      </statement>
      <hint>
        <p>展开特征多项式 <m>\det(A-\lambda I)</m>，
        与 <m>(\lambda - \lambda_1)(\lambda - \lambda_2)</m> 比较系数。</p>
      </hint>
      <solution>
        <p>特征多项式为
        <me>(a-\lambda)(d-\lambda) - bc
           = \lambda^2 - (a+d)\lambda + (ad-bc).</me>
        由韦达定理，两根之和为 <m>a+d = \operatorname{tr}(A)</m>，
        两根之积为 <m>ad-bc = \det(A)</m>。</p>
      </solution>
    </exercise>
  </exercises>
</chapter>
```

### 文件 3：`publication/publication.ptx`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<publication>
  <html>
    <math-cdn preference="mathjax"/>
    <toc level="2"/>
    <knowl proof="yes" example="yes" exercise-divisional="yes"/>
  </html>
  <latex>
    <page right-margin="1in" left-margin="1.25in"/>
  </latex>
  <numbering>
    <theorems level="2"/>
    <equations level="2"/>
  </numbering>
</publication>
```

### 构建测试步骤

```bash
# 1. 进入项目目录
cd my-book

# 2. 构建 HTML（速度较快，推荐先用这个测试）
pretext build html

# 3. 在浏览器中查看
pretext view html
# 浏览器自动打开 http://localhost:8128

# 4. 确认无误后构建 PDF
pretext build pdf
# 输出文件在 output/pdf/ 目录下
```

---

## 附录：常见问题与调试

### Q1: XML 特殊字符如何处理？

| 字符 | XML 转义 | 说明 |
|------|----------|------|
| `<`  | `&lt;`   | 用于代码中的小于号 |
| `>`  | `&gt;`   | 用于代码中的大于号 |
| `&`  | `&amp;`  | 用于文字中的 & 符号 |
| `"` | `&quot;` | 在属性值中使用 |
| `&`（数学） | `\amp` | 仅在 `<m>`, `<me>` 等数学环境中 |

### Q2: 如何给 HTML 添加自定义 CSS？

在 `publication.ptx` 中加入：

```xml
<html>
  <css style="default" colors="pastel"/>
  <extra-css filename="custom.css"/>
</html>
```

然后在项目根目录创建 `custom.css`：

```css
/* 自定义样式示例：加宽内容区 */
:root {
  --content-width: 900px;
}

/* 自定义定理框颜色 */
.theorem-like {
  border-left: 4px solid #1a73e8;
}
```

### Q3: 图片编译报错怎么办？

```bash
# 单独生成所有 TikZ/pgfplots 图形
pretext generate latex-image --target html

# 查看详细日志
pretext build html -v
```

### Q4: 如何添加索引项？

```xml
<p>向量空间<index><main>向量空间</main></index>
是满足八条公理的代数结构。</p>

<!-- 子条目 -->
<p>向量<index><main>向量</main><sub>内积</sub></index>
的内积……</p>
```

---

## 参考资源

- **PreTeXt 官方文档**：[https://pretextbook.org/doc/guide/](https://pretextbook.org/doc/guide/)
- **PreTeXt 示例库**：[https://pretextbook.org/examples.html](https://pretextbook.org/examples.html)
- **社区论坛**：[https://groups.google.com/g/pretext-support](https://groups.google.com/g/pretext-support)
- **GitHub 仓库**：[https://github.com/PreTeXtBook/pretext](https://github.com/PreTeXtBook/pretext)
- **在线编辑器**：[https://runestone.academy](https://runestone.academy)（支持 PreTeXt 在线编写）

---

*本文档使用 Markdown 编写，可将其中的 XML 代码片段直接复制到 PreTeXt 项目中测试。如有问题，欢迎在仓库 Issues 中反馈。*

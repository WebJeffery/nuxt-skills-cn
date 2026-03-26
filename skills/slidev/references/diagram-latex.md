---
name: latex
description: 使用 KaTeX 渲染数学公式
---

# LaTeX

渲染数学公式。由 KaTeX 提供支持。

## 行内数学公式

```md
$\sqrt{3x-1}+(1+x)^2$
```

## 块级数学公式

```md
$$
\begin{aligned}
\nabla \cdot \vec{E} &= \frac{\rho}{\varepsilon_0} \\
\nabla \cdot \vec{B} &= 0
\end{aligned}
$$
```

## 行高亮

```md
$$ {1|3|all}
\begin{aligned}
\nabla \cdot \vec{E} &= \frac{\rho}{\varepsilon_0} \\
\nabla \cdot \vec{B} &= 0 \\
\nabla \times \vec{E} &= -\frac{\partial\vec{B}}{\partial t}
\end{aligned}
$$
```

## 化学方程式

在 `vite.config.ts` 中启用 mhchem 扩展：

```ts
import 'katex/contrib/mhchem'

export default {}
```

然后使用：

```md
$$
\ce{B(OH)3 + H2O <--> B(OH)4^- + H+}
$$
```

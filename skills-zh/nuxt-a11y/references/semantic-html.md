# 语义化 HTML 最佳实践

## 为什么需要语义化 HTML

语义化 HTML 对于无障碍至关重要,因为:
1. 屏幕阅读器依赖语义化元素来理解页面结构
2. 提供更好的导航体验(如快速跳转到特定区域)
3. 改善 SEO 和代码可维护性

## 核心语义化元素

### 页面结构

```vue
<template>
  <!-- ✅ 完整的页面结构 -->
  <body>
    <!-- 页眉 -->
    <header>
      <h1>网站名称</h1>
      <nav aria-label="主导航">
        <ul>
          <li><a href="/">首页</a></li>
          <li><a href="/about">关于</a></li>
          <li><a href="/contact">联系</a></li>
        </ul>
      </nav>
    </header>

    <!-- 主要内容 (每页只有一个) -->
    <main>
      <article>
        <h1>文章标题</h1>
        <p>文章内容...</p>
      </article>

      <aside>
        <h2>侧边栏</h2>
        <p>相关内容...</p>
      </aside>
    </main>

    <!-- 页脚 -->
    <footer>
      <p>&copy; 2024 版权信息</p>
      <nav aria-label="页脚导航">
        <ul>
          <li><a href="/privacy">隐私政策</a></li>
          <li><a href="/terms">服务条款</a></li>
        </ul>
      </nav>
    </footer>
  </body>
</template>
```

### header 元素

```vue
<template>
  <!-- 页面级 header -->
  <header>
    <logo />
    <nav>...</nav>
  </header>

  <!-- section 级 header -->
  <article>
    <header>
      <h1>文章标题</h1>
      <p class="byline">作者: 张三 | 2024-01-01</p>
    </header>

    <p>文章内容...</p>
  </article>
</template>
```

### nav 元素

```vue
<template>
  <!-- 主导航 -->
  <nav aria-label="主导航">
    <ul>
      <li><a href="/">首页</a></li>
      <li><a href="/products">产品</a></li>
      <li><a href="/about">关于</a></li>
    </ul>
  </nav>

  <!-- 次要导航 -->
  <nav aria-label="页面导航">
    <ul>
      <li><a href="#section1">第一部分</a></li>
      <li><a href="#section2">第二部分</a></li>
    </ul>
  </nav>
</template>
```

### main 元素

```vue
<template>
  <!-- ✅ 正确: 每页只有一个 main -->
  <main>
    <h1>页面标题</h1>
    <p>主要内容...</p>
  </main>

  <!-- ❌ 错误: 多个 main 元素 -->
  <main>
    <section>...</section>
  </main>
  <main>
    <section>...</section>
  </main>
</template>
```

### article 元素

```vue
<template>
  <!-- 独立的内容单元 -->
  <article>
    <header>
      <h1>如何学习 Vue 3</h1>
      <time datetime="2024-01-01">2024年1月1日</time>
    </header>

    <p>Vue 3 是一个渐进式 JavaScript 框架...</p>

    <footer>
      <p>作者: 张三</p>
    </footer>
  </article>

  <!-- 嵌套 article (评论) -->
  <article>
    <h1>主文章</h1>
    <p>内容...</p>

    <section class="comments">
      <h2>评论</h2>

      <article class="comment">
        <p>很好的文章!</p>
        <footer>评论者: 李四</footer>
      </article>
    </section>
  </article>
</template>
```

### section 元素

```vue
<template>
  <article>
    <h1>文章标题</h1>

    <!-- 使用 section 组织相关内容 -->
    <section>
      <h2>第一章</h2>
      <p>内容...</p>
    </section>

    <section>
      <h2>第二章</h2>
      <p>内容...</p>
    </section>

    <!-- section vs div -->
    <!-- ✅ section: 有语义,包含标题 -->
    <section>
      <h2>章节标题</h2>
      <p>内容...</p>
    </section>

    <!-- ❌ div: 无语义,用于样式 -->
    <div class="card">
      <h2>标题</h2>
      <p>内容...</p>
    </div>
  </article>
</template>
```

### aside 元素

```vue
<template>
  <main>
    <article>
      <h1>主文章</h1>
      <p>内容...</p>
    </article>

    <!-- 侧边栏 -->
    <aside>
      <h2>相关文章</h2>
      <ul>
        <li><a href="/article1">文章1</a></li>
        <li><a href="/article2">文章2</a></li>
      </ul>
    </aside>
  </main>

  <!-- 文章内的侧边说明 -->
  <article>
    <h1>主标题</h1>
    <p>主要内容...</p>

    <aside>
      <p>注: 这是一个补充说明。</p>
    </aside>
  </article>
</template>
```

### footer 元素

```vue
<template>
  <!-- 页面级 footer -->
  <footer>
    <p>&copy; 2024 公司名称</p>
    <nav aria-label="页脚导航">
      <a href="/privacy">隐私政策</a>
    </nav>
  </footer>

  <!-- section 级 footer -->
  <article>
    <h1>文章标题</h1>
    <p>内容...</p>

    <footer>
      <p>发布于: <time datetime="2024-01-01">2024年1月1日</time></p>
      <p>标签: Vue, Nuxt</p>
    </footer>
  </article>
</template>
```

## 标题层级

### 正确的标题结构

```vue
<template>
  <!-- ✅ 正确: 层级清晰 -->
  <h1>网站标题 (每页一个)</h1>

  <section>
    <h2>主要章节</h2>
    <p>内容...</p>

    <section>
      <h3>子章节</h3>
      <p>内容...</p>
    </section>
  </section>

  <section>
    <h2>另一个主要章节</h2>
    <p>内容...</p>

    <article>
      <h3>文章标题</h3>
      <p>内容...</p>
    </article>
  </section>
</template>
```

### ❌ 常见错误

```vue
<template>
  <!-- ❌ 跳过层级 -->
  <h1>标题 1</h1>
  <h3>跳过 h2</h3>

  <!-- ❌ 使用标题控制样式 -->
  <h1 class="small">小标题</h1>
  <h6 class="big">大标题</h6>

  <!-- ✅ 使用 CSS 控制样式 -->
  <h1>主标题</h1>
  <h2 style="font-size: 1rem;">副标题</h2>
</template>
```

## 列表

### 无序列表

```vue
<template>
  <ul>
    <li>第一项</li>
    <li>第二项</li>
    <li>第三项</li>
  </ul>
</template>
```

### 有序列表

```vue
<template>
  <ol>
    <li>第一步: 注册账户</li>
    <li>第二步: 验证邮箱</li>
    <li>第三步: 完善资料</li>
  </ol>
</template>
```

### 定义列表

```vue
<template>
  <dl>
    <dt>HTML</dt>
    <dd>超文本标记语言</dd>

    <dt>CSS</dt>
    <dd>层叠样式表</dd>

    <dt>JavaScript</dt>
    <dd>一种脚本语言</dd>
  </dl>
</template>
```

### 嵌套列表

```vue
<template>
  <ul>
    <li>
      水果
      <ul>
        <li>苹果</li>
        <li>香蕉</li>
      </ul>
    </li>
    <li>
      蔬菜
      <ul>
        <li>胡萝卜</li>
        <li>白菜</li>
      </ul>
    </li>
  </ul>
</template>
```

## 表格

### 基础表格

```vue
<template>
  <table>
    <caption>月销售数据</caption>
    <thead>
      <tr>
        <th scope="col">产品</th>
        <th scope="col">一月</th>
        <th scope="col">二月</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <th scope="row">产品 A</th>
        <td>100</td>
        <td>120</td>
      </tr>
      <tr>
        <th scope="row">产品 B</th>
        <td>80</td>
        <td>90</td>
      </tr>
    </tbody>
    <tfoot>
      <tr>
        <th scope="row">总计</th>
        <td>180</td>
        <td>210</td>
      </tr>
    </tfoot>
  </table>
</template>
```

### 复杂表格

```vue
<template>
  <table>
    <caption>员工信息表</caption>

    <thead>
      <tr>
        <th colspan="2" scope="colgroup">姓名</th>
        <th rowspan="2" scope="col">部门</th>
        <th rowspan="2" scope="col">职位</th>
      </tr>
      <tr>
        <th scope="col">名</th>
        <th scope="col">姓</th>
      </tr>
    </thead>

    <tbody>
      <tr>
        <td>张</td>
        <td>三</td>
        <td>技术部</td>
        <td>工程师</td>
      </tr>
    </tbody>
  </table>
</template>
```

## 引用和标记

### blockquote

```vue
<template>
  <!-- 长引用 -->
  <blockquote cite="https://example.com/source">
    <p>
      "学习编程最好的方法就是动手实践。"
    </p>
    <footer>
      <cite>— 某程序员</cite>
    </footer>
  </blockquote>
</template>
```

### q 和 cite

```vue
<template>
  <p>
    正如 Tim Berners-Lee 所说:
    <q cite="https://www.w3.org/People/Berners-Lee/">
      "The Web as I envisaged it, we have not seen it yet."
    </q>
  </p>

  <p>
    参考文档: <cite>HTML5 规范</cite>
  </p>
</template>
```

### code 和 pre

```vue
<template>
  <p>
    使用 <code>const</code> 声明常量。
  </p>

  <pre><code>function hello() {
  console.log('Hello, World!')
}</code></pre>
</template>
```

## 其他语义化元素

### figure 和 figcaption

```vue
<template>
  <figure>
    <img
      src="/chart.jpg"
      alt="2024年销售增长趋势图"
    />
    <figcaption>
      图1: 2024年销售数据增长30%
    </figcaption>
  </figure>

  <figure>
    <pre><code>const x = 10</code></pre>
    <figcaption>代码示例1</figcaption>
  </figure>
</template>
```

### mark

```vue
<template>
  <p>
    在 Vue 3 中,<mark>Composition API</mark> 提供了更灵活的代码组织方式。
  </p>
</template>
```

### time

```vue
<template>
  <article>
    <header>
      <h1>文章标题</h1>
      <p>
        发布于:
        <time datetime="2024-01-01T10:00:00+08:00">
          2024年1月1日 上午10点
        </time>
      </p>
    </header>
  </article>

  <p>
    会议时间:
    <time datetime="2024-06-01T14:00">2024年6月1日下午2点</time>
  </p>
</template>
```

### address

```vue
<template>
  <footer>
    <address>
      <p>联系方式:</p>
      <p>
        <a href="mailto:info@example.com">info@example.com</a><br />
        电话: +86 400-xxx-xxxx
      </p>
    </address>
  </footer>
</template>
```

## 语义化 vs 非语义化

### 何时使用 div

```vue
<template>
  <!-- ✅ div 用于样式或脚本钩子,不提供语义 -->
  <div class="container">
    <header>...</header>
    <main>...</main>
    <footer>...</footer>
  </div>

  <!-- ❌ 避免用 div 替代语义化元素 -->
  <div class="header">...</div>
  <div class="main">...</div>
  <div class="footer">...</div>

  <!-- ✅ 使用语义化元素 -->
  <header>...</header>
  <main>...</main>
  <footer>...</footer>
</template>
```

### 何时使用 span

```vue
<template>
  <!-- ✅ span 用于样式化文本片段 -->
  <p>
    这是<span class="highlight">重要的</span>内容。
  </p>

  <!-- ❌ 避免用 span 替代语义化元素 -->
  <span class="button">点击</span>

  <!-- ✅ 使用 button 元素 -->
  <button>点击</button>
</template>
```

## ARIA 地标角色

虽然语义化元素已经包含隐式角色,但有时需要明确指定:

```vue
<template>
  <!-- 隐式角色 (不需要额外 aria) -->
  <header>     <!-- role="banner" -->
  <nav>        <!-- role="navigation" -->
  <main>       <!-- role="main" -->
  <aside>      <!-- role="complementary" -->
  <footer>     <!-- role="contentinfo" -->

  <!-- 明确指定角色 (当使用 div 时) -->
  <div role="banner">
    <h1>网站标题</h1>
  </div>

  <div role="navigation" aria-label="主导航">
    <!-- 导航链接 -->
  </div>

  <div role="main">
    <!-- 主要内容 -->
  </div>

  <div role="complementary">
    <!-- 侧边栏 -->
  </div>

  <div role="contentinfo">
    <!-- 页脚信息 -->
  </div>
</template>
```

## 最佳实践清单

- [ ] 使用 HTML5 语义化元素(header, nav, main, article, section, aside, footer)
- [ ] 每页只有一个 `<main>` 元素
- [ ] 标题层级清晰(h1 → h2 → h3),不跳级
- [ ] 所有图片有 alt 属性
- [ ] 表单元素有关联的 label
- [ ] 表格有 caption 和 th scope
- [ ] 使用 landmark 角色(或语义化元素)
- [ ] 避免仅用于样式的 div/span
- [ ] 代码使用 code/pre, 引用使用 blockquote

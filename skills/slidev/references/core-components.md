---
name: components
description: Slidev 中可用的组件
---

# 内置组件

Slidev 中可用的组件。

## 导航

### Link

导航到幻灯片：
```md
<Link to="5">跳转到第 5 张幻灯片</Link>
<Link to="intro">跳转到介绍</Link>  <!-- 使用 routeAlias -->
```

### SlideCurrentNo / SlidesTotal

```md
幻灯片 <SlideCurrentNo /> / <SlidesTotal />
```

### Toc（目录）

```md
<Toc />
<Toc maxDepth="2" />
<Toc columns="2" />
```

属性：
- `columns` - 列数
- `maxDepth` / `minDepth` - 标题深度过滤器
- `mode` - 'all' | 'onlyCurrentTree' | 'onlySiblings'

### TitleRenderer

渲染幻灯片标题：
```md
<TitleRenderer no="3" />
```

## 动画

### VClick / VClicks

```md
<VClick>点击时显示</VClick>

<VClicks>

- 项目 1
- 项目 2

</VClicks>
```

### VAfter

```md
<VClick>第一个</VClick>
<VAfter>与第一个一起显示</VAfter>
```

### VSwitch

```md
<VSwitch>
  <template #1>状态 1</template>
  <template #2>状态 2</template>
</VSwitch>
```

## 绘图

### Arrow

```md
<Arrow x1="10" y1="10" x2="100" y2="100" />
<Arrow x1="10" y1="10" x2="100" y2="100" two-way />
```

属性：`x1`、`y1`、`x2`、`y2`、`width`、`color`、`two-way`

### VDragArrow

可拖动箭头：
```md
<VDragArrow />
```

## 布局

### Transform

缩放元素：
```md
<Transform :scale="0.5">
  <LargeContent />
</Transform>
```

属性：`scale`、`origin`

### AutoFitText

自动调整文本大小：
```md
<AutoFitText :max="200" :min="50" modelValue="Hello" />
```

## 媒体

### SlidevVideo

```md
<SlidevVideo v-click autoplay controls>
  <source src="/video.mp4" type="video/mp4" />
</SlidevVideo>
```

属性：`controls`、`autoplay`、`autoreset`、`poster`、`timestamp`

### Youtube

```md
<Youtube id="dQw4w9WgXcQ" />
<Youtube id="dQw4w9WgXcQ" width="600" height="400" />
```

### Tweet

```md
<Tweet id="1423789844234231808" />
<Tweet id="1423789844234231808" :scale="0.8" />
```

## 条件渲染

### LightOrDark

```md
<LightOrDark>
  <template #dark>暗色模式内容</template>
  <template #light>亮色模式内容</template>
</LightOrDark>
```

### RenderWhen

```md
<RenderWhen context="presenter">
  仅在演讲者模式下显示
</RenderWhen>
```

上下文值：
- `main` - 主演示视图
- `visible` - 可见幻灯片
- `print` - 打印/导出模式
- `slide` - 正常幻灯片视图
- `overview` - 概览模式
- `presenter` - 演讲者模式
- `previewNext` - 下一张幻灯片预览

## 品牌展示

### PoweredBySlidev

```md
<PoweredBySlidev />
```

## 可拖动

### VDrag

```md
<VDrag pos="myElement">
  可拖动内容
</VDrag>
```

详情参见 [draggable](draggable.md)。

## 组件自动导入

来自以下来源的组件会自动导入：
1. 内置组件
2. 主题组件
3. 插件组件
4. `./components/` 目录

无需导入语句。
